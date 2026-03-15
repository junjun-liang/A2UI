# ContactAgent 核心逻辑分析

> 本文件解析 `/samples/agent/adk/contact_lookup/agent.py` 的业务逻辑和技术栈

---

## 一、整体架构

### 1.1 类结构概览

```
┌─────────────────────────────────────────────────────────────────┐
│                       ContactAgent 类                             │
├─────────────────────────────────────────────────────────────────┤
│  属性                                                            │
│  ├── base_url: str              - 服务基地址                     │
│  ├── use_ui: bool               - 是否启用 A2UI 模式            │
│  ├── _schema_manager            - A2UI Schema 管理器            │
│  ├── _agent                    - ADK LLM Agent                 │
│  ├── _runner                   - ADK Runner (执行器)           │
│  └── _user_id                  - 用户ID                         │
├─────────────────────────────────────────────────────────────────┤
│  方法                                                            │
│  ├── __init__()                 - 初始化                         │
│  ├── get_agent_card()           - 获取 Agent 服务卡片           │
│  ├── get_processing_message()   - 获取处理中提示                │
│  ├── _build_agent()             - 构建 LLM Agent               │
│  └── stream()                   - 流式处理请求                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 二、技术栈

### 2.1 依赖的库

| 库 | 作用 |
|------|------|
| **google.adk** | Agent 开发框架 |
| `google.adk.agents.llm_agent.LlmAgent` | LLM Agent |
| `google.adk.runners.Runner` | Agent 运行器 |
| `google.adk.sessions.InMemorySessionService` | 会话管理 |
| `google.adk.artifacts.InMemoryArtifactService` | 产物存储 |
| `google.adk.memory.InMemoryMemoryService` | 内存服务 |
| `google.genai.types` | Google GenAI 类型 |
| **a2a.types** | A2A 协议类型 |
| `a2ui.core.schema` | A2UI Schema 管理 |
| `a2ui.basic_catalog` | A2UI 基础组件目录 |
| `jsonschema` | JSON Schema 验证 |

---

## 三、核心方法解析

### 3.1 `__init__` 初始化方法

```python
def __init__(self, base_url: str, use_ui: bool = False):
    self.base_url = base_url
    self.use_ui = use_ui

    # 1. 初始化 A2UI Schema Manager
    self._schema_manager = (
        A2uiSchemaManager(
            version=VERSION_0_8,
            catalogs=[
                BasicCatalog.get_config(
                    version=VERSION_0_8,
                    examples_path="examples"
                )
            ],
        )
        if use_ui
        else None
    )

    # 2. 构建 LLM Agent
    self._agent = self._build_agent(use_ui)

    # 3. 初始化 ADK Runner
    self._runner = Runner(
        app_name=self._agent.name,
        agent=self._agent,
        artifact_service=InMemoryArtifactService(),
        session_service=InMemorySessionService(),
        memory_service=InMemoryMemoryService(),
    )
```

**关键点**：
- `use_ui=True` 时加载 A2UI Schema 和组件目录
- 使用 InMemory 服务（适合开发/测试）

---

### 3.2 `get_agent_card` 获取服务卡片

```python
def get_agent_card(self) -> AgentCard:
    # 1. 构造能力声明
    capabilities = AgentCapabilities(
        streaming=True,
        extensions=[
            get_a2ui_agent_extension(
                self._schema_manager.accepts_inline_catalogs,
                self._schema_manager.supported_catalog_ids,
            )
        ],
    )

    # 2. 定义技能
    skill = AgentSkill(
        id="find_contact",
        name="Find Contact Tool",
        description="Helps find contact information...",
        examples=[
            "Who is David Chen in marketing?",
            "Find Sarah Lee from engineering",
        ],
    )

    # 3. 返回 Agent Card
    return AgentCard(
        name="Contact Lookup Agent",
        url=self.base_url,
        capabilities=capabilities,
        skills=[skill],
    )
```

**Agent Card 作用**：
- 服务发现：客户端通过它了解服务能力
- 声明 A2UI 支持：扩展中包含 A2UI 配置

---

### 3.3 `_build_agent` 构建 LLM Agent

```python
def _build_agent(self, use_ui: bool) -> LlmAgent:
    LITELLM_MODEL = os.getenv("LITELLM_MODEL", "gemini/gemini-2.5-flash")

    # 根据模式生成不同的指令
    instruction = (
        self._schema_manager.generate_system_prompt(
            role_description=ROLE_DESCRIPTION,
            workflow_description=WORKFLOW_DESCRIPTION,
            ui_description=UI_DESCRIPTION,
            include_schema=True,
            include_examples=True,
        )
        if use_ui
        else get_text_prompt()
    )

    return LlmAgent(
        model=LiteLlm(model=LITELLM_MODEL),
        name="contact_agent",
        instruction=instruction,
        tools=[get_contact_info],
    )
```

**关键点**：
- 使用 LiteLLM 调用 Gemini 模型
- A2UI 模式下生成带 Schema 的 Prompt
- 注册 `get_contact_info` 工具

---

### 3.4 `stream` 流式处理核心逻辑

```python
async def stream(self, query, session_id) -> AsyncIterable[dict[str, Any]]:
    # 1. 获取或创建 Session
    session = await self._runner.session_service.get_session(...)

    # 2. 最多重试 2 次
    max_retries = 1  # Total 2 attempts
    attempt = 0
    current_query_text = query

    while attempt <= max_retries:
        attempt += 1

        # 3. 调用 LLM
        async for event in self._runner.run_async(...):
            if event.is_final_response():
                final_response_content = event.content.parts[0].text
                break

        # 4. A2UI 模式：解析和验证
        if self.use_ui:
            response_parts = parse_response(final_response_content)

            for part in response_parts:
                if part.a2ui_json:
                    # 5. Schema 验证
                    selected_catalog.validator.validate(part.a2ui_json)
                    is_valid = True

        # 6. 返回响应
        yield {
            "is_task_complete": True,
            "parts": final_parts,
        }
```

---

## 四、业务流程图

### 4.1 完整请求处理流程

```
┌─────────────────────────────────────────────────────────────────┐
│                         用户请求                                  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    1. Session 管理                               │
│  - 获取或创建 Session                                           │
│  - 存储 base_url 到 session state                              │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    2. LLM 调用循环                               │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Attempt 1                                              │  │
│  │   ├── Runner.run_async() → LLM                           │  │
│  │   ├── 获取响应                                            │  │
│  │   ├── parse_response() 解析                               │  │
│  │   ├── validator.validate() 验证                          │  │
│  │   └── ✅ 成功 → 返回响应                                  │  │
│  │         ❌ 失败 → 重试                                   │  │
│  └─────────────────────────────────────────────────────────┘  │
│                              │                                   │
│                              ▼                                   │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Attempt 2 (如果验证失败)                                  │  │
│  │   - 拼接重试 Prompt                                       │  │
│  │   - 重新调用 LLM                                         │  │
│  │   - 验证                                                  │  │
│  └─────────────────────────────────────────────────────────┘  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    3. 响应处理                                    │
│  - parse_response_to_parts() 转换格式                           │
│  - 返回 is_task_complete + parts                                │
└─────────────────────────────────────────────────────────────────┘
```

---

### 4.2 Schema 验证流程

```
LLM 响应文本
    │
    ▼
parse_response()
    │
    ├── 提取 a2ui_json
    │
    ▼
验证通过? ──否──> 记录错误 ──> 重试
    │
   是│
    ▼
validator.validate(a2ui_json)
    │
    ├── 验证失败? ──是──> 记录错误 ──> 重试
    │
   否│
    ▼
验证通过
    │
    ▼
封装 DataPart 返回
```

---

## 五、关键代码片段

### 5.1 A2UI Prompt 生成

```python
instruction = self._schema_manager.generate_system_prompt(
    role_description=ROLE_DESCRIPTION,      # 角色描述
    workflow_description=WORKFLOW_DESCRIPTION,  # 工作流
    ui_description=UI_DESCRIPTION,          # UI 描述
    include_schema=True,                  # 包含 Schema
    include_examples=True,                 # 包含示例
    validate_examples=False,
)
```

### 5.2 响应解析与验证

```python
# 解析
response_parts = parse_response(final_response_content)

# 遍历每个 part
for part in response_parts:
    if part.a2ui_json:
        # 验证 Schema
        selected_catalog.validator.validate(part.a2ui_json)

        # 封装为 DataPart
        final_parts.append(
            Part(root=DataPart(data={...}))
        )
```

### 5.3 重试机制

```python
if attempt <= max_retries:
    current_query_text = (
        f"Your previous response was invalid. {error_message} "
        "You MUST generate a valid response that strictly follows "
        f"the A2UI JSON SCHEMA. "
        f"'{A2UI_OPEN_TAG}' and '{A2UI_CLOSE_TAG}' tags."
    )
```

---

## 六、数据流总结

### 6.1 输入

```python
query = "Who is Sarah Lee?"
session_id = "session-123"
```

### 6.2 处理

```
query + session_id
    │
    ▼
Runner.run_async()
    │
    ▼
LLM (Gemini) + get_contact_info 工具
    │
    ▼
LLM 返回文本（包含 A2UI JSON）
    │
    ▼
parse_response() → 提取 a2ui_json
    │
    ▼
validator.validate() → Schema 验证
    │
    ▼
parse_response_to_parts() → 转换格式
```

### 6.3 输出

```python
{
    "is_task_complete": True,
    "parts": [
        Part(root=TextPart(text="Here are the results...")),
        Part(root=DataPart(data={
            "a2ui": True,
            "jsonSchema": {...},
            "instances": [{...}]  # A2UI JSON
        }))
    ]
}
```

---

## 七、关键技术点

### 7.1 双模式支持

| 模式 | 特点 |
|------|------|
| `use_ui=True` | A2UI 模式，返回富文本UI |
| `use_ui=False` | 纯文本模式，简单对话 |

### 7.2 Schema 验证

- 使用 JSON Schema 验证 A2UI 消息
- 确保 LLM 输出符合规范

### 7.3 重试机制

- 最多 2 次尝试
- 验证失败时自动重试
- 重试时提供错误信息和规范提示

### 7.4 工具集成

```python
tools=[get_contact_info]
```

Agent 可以调用 `get_contact_info` 工具查询联系人数据。

---

## 八、总结

ContactAgent 是一个典型的 **A2UI + ADK** 示例，展示了：

1. **ADK Runner** - 管理 LLM Agent 生命周期
2. **A2UI Schema** - 生成和验证 UI 定义
3. **工具调用** - 通过 LLM 调用业务工具
4. **重试机制** - 保证输出质量
5. **Agent Card** - 服务能力声明
