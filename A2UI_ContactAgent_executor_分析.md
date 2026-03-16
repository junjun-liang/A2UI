# ContactAgentExecutor 业务逻辑分析

## 文件位置
`samples/agent/adk/contact_lookup/agent_executor.py`

---

## 核心职责

`ContactAgentExecutor` 是 A2A 协议的 `AgentExecutor` 实现，负责：
1. 根据客户端请求选择合适的 Agent（UI 模式 / 文本模式）
2. 处理客户端事件（UI 交互）
3. 协调 Agent 执行并返回结果

---

## 类结构

```python
class ContactAgentExecutor(AgentExecutor):
    def __init__(self, ui_agent: ContactAgent, text_agent: ContactAgent):
        # 两个 Agent 实例：UI 模式和文本模式

    async def execute(self, context: RequestContext, event_queue: EventQueue):
        # 主执行逻辑

    async def cancel(self, request: RequestContext, event_queue: EventQueue):
        # 取消操作（不支持）
```

---

## 业务流程图

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ContactAgentExecutor.execute()                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. 检查扩展激活状态                                                  │
│     └── try_activate_a2ui_extension(context)                         │
│          ├── A2UI 激活 → ui_agent                                   │
│          └── 未激活 → text_agent                                     │
│                                                                      │
│  2. 解析消息 Parts                                                   │
│     └── 遍历 context.message.parts                                   │
│          ├── DataPart → 检查 userAction                             │
│          └── TextPart → 提取文本                                     │
│                                                                      │
│  3. 处理 UI 事件 (如果有)                                            │
│     └── ui_event_part.get("name")                                    │
│          ├── view_profile → "WHO_IS: {name} from {dept}"            │
│          ├── send_email → "USER_WANTS_TO_EMAIL: {email}"            │
│          ├── send_message → "USER_WANTS_TO_MESSAGE: {name}"         │
│          ├── follow_contact → "ACTION: follow_contact"               │
│          └── view_full_profile → "USER_WANTS_FULL_PROFILE: {name}" │
│                                                                      │
│  4. 创建/更新 Task                                                   │
│     └── new_task() / TaskUpdater                                    │
│                                                                      │
│  5. 调用 Agent.stream()                                             │
│     └── 获取流式响应                                                  │
│                                                                      │
│  6. 处理中间状态                                                     │
│     └── TaskState.working + TextMessage                             │
│                                                                      │
│  7. 返回最终结果                                                    │
│     └── TaskState.completed / input_required                        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 详细流程

### Step 1: 选择 Agent 模式

```python
use_ui = try_activate_a2ui_extension(context)

if use_ui:
    agent = self.ui_agent  # 使用 UI Agent
else:
    agent = self.text_agent  # 使用文本 Agent
```

**判断逻辑**：
- 如果客户端请求包含 A2UI 扩展 → 使用 `ui_agent`
- 否则 → 使用 `text_agent`

---

### Step 2: 解析消息 Parts

```python
for i, part in enumerate(context.message.parts):
    if isinstance(part.root, DataPart):
        if "userAction" in part.root.data:
            ui_event_part = part.root.data["userAction"]
    elif isinstance(part.root, TextPart):
        # 提取文本
```

**Part 类型**：
| 类型 | 说明 |
|------|------|
| `DataPart` | 包含 A2UI 客户端事件 (`userAction`) |
| `TextPart` | 普通文本输入 |

---

### Step 3: 处理 UI 事件

```python
if ui_event_part:
    action = ui_event_part.get("name")
    ctx = ui_event_part.get("context", {})

    if action == "view_profile":
        query = f"WHO_IS: {contact_name} from {department}"

    elif action == "send_email":
        query = f"USER_WANTS_TO_EMAIL: {contact_name} at {email}"

    elif action == "send_message":
        query = f"USER_WANTS_TO_MESSAGE: {contact_name}"

    elif action == "follow_contact":
        query = "ACTION: follow_contact"

    elif action == "view_full_profile":
        query = f"USER_WANTS_FULL_PROFILE: {contact_name}"
```

**事件映射表**：

| UI Action | 转换为 Query | 说明 |
|-----------|--------------|------|
| `view_profile` | `WHO_IS: {name} from {dept}` | 查看联系人详情 |
| `send_email` | `USER_WANTS_TO_EMAIL: {name} at {email}` | 发送邮件 |
| `send_message` | `USER_WANTS_TO_MESSAGE: {name}` | 发送消息 |
| `follow_contact` | `ACTION: follow_contact` | 关注联系人 |
| `view_full_profile` | `USER_WANTS_FULL_PROFILE: {name}` | 查看完整档案 |

---

### Step 4: 处理文本输入

```python
else:
    # 没有 UI 事件，使用普通文本输入
    query = context.get_user_input()
```

---

### Step 5: Agent 执行

```python
async for item in agent.stream(query, task.context_id):
    is_task_complete = item["is_task_complete"]

    if not is_task_complete:
        # 中间状态
        await updater.update_status(
            TaskState.working,
            new_agent_text_message(item["updates"], ...),
        )
        continue

    # 最终状态
    final_parts = item["parts"]
```

**流式处理**：
- `is_task_complete=False` → 发送进度消息 (`TaskState.working`)
- `is_task_complete=True` → 发送最终结果

---

### Step 6: 确定最终状态

```python
final_state = TaskState.input_required  # 默认

# 部分操作直接完成
if action in ["send_email", "send_message", "view_full_profile"]:
    final_state = TaskState.completed

await updater.update_status(
    final_state,
    new_agent_parts_message(final_parts, ...),
    final=(final_state == TaskState.completed),
)
```

**状态映射**：

| 操作 | 最终状态 |
|------|----------|
| `send_email` | `TaskState.completed` |
| `send_message` | `TaskState.completed` |
| `view_full_profile` | `TaskState.completed` |
| 其他（如 view_profile） | `TaskState.input_required` |

---

## 与 A2A 协议集成

### 整体架构

```
┌──────────────┐     ┌─────────────────────┐     ┌──────────────┐
│   Client     │────▶│  A2A Server         │────▶│  Contact    │
│  (React UI)  │◀────│  (Starlette)        │◀────│  Agent       │
└──────────────┘     └─────────────────────┘     └──────────────┘
                              │
                              ▼
                     ┌─────────────────────┐
                     │ ContactAgentExecutor │
                     │  (本文件)           │
                     └─────────────────────┘
```

### 消息流程

```
Client                                    Server
  │                                          │
  │── JSON-RPC: tasks/send ─────────────────▶│
  │   {                                     │
  │     message: {                         │
  │       parts: [                         │
  │         {DataPart: {userAction: {...}}} │
  │       ]                                │
  │     }                                  │
  │   }                                     │
  │                                          │
  │◀── Stream Events ──────────────────────│
  │   TaskState.working (进度)              │
  │   TaskState.completed (完成)            │
  │                                          │
```

---

## 核心代码片段

### 初始化

```python
def __init__(self, ui_agent: ContactAgent, text_agent: ContactAgent):
    self.ui_agent = ui_agent    # 启用 A2UI 模式
    self.text_agent = text_agent  # 纯文本模式
```

### 事件转换

```python
# 将 UI 事件转换为 LLM 可理解的查询
action_mapping = {
    "view_profile": "WHO_IS: {name} from {department}",
    "send_email": "USER_WANTS_TO_EMAIL: {name} at {email}",
    "send_message": "USER_WANTS_TO_MESSAGE: {name}",
    "follow_contact": "ACTION: follow_contact",
    "view_full_profile": "USER_WANTS_FULL_PROFILE: {name}",
}
```

---

## 总结

| 阶段 | 职责 |
|------|------|
| **1. 模式选择** | 根据 A2UI 扩展是否激活选择 Agent |
| **2. 消息解析** | 区分 DataPart (UI事件) 和 TextPart (文本) |
| **3. 事件转换** | 将 UI action 转换为 LLM 查询 |
| **4. Agent 执行** | 调用 ContactAgent.stream() 获取响应 |
| **5. 状态管理** | 处理 working / completed / input_required |

**关键设计**：
- 双 Agent 模式：UI 模式 + 文本模式
- UI 事件抽象：客户端事件 → LLM 查询
- 流式响应支持
- Task 状态管理
