# ContactAgent 代码解析

## 文件位置
`samples/agent/adk/contact_lookup/agent.py`

## 技术栈分析

### 1. 核心框架与依赖

| 组件 | 用途 | 来源 |
|------|------|------|
| **LlmAgent** | LLM 智能体核心类 | google.adk |
| **Runner** | ADK 运行器，处理消息流转 | google.adk |
| **LiteLLM** | 统一 LLM 调用接口，支持多模型 | google.adk |
| **A2uiSchemaManager** | A2UI 模式管理器 | a2ui |
| **InMemoryArtifactService** | 内存 Artifact 存储 | google.adk |
| **InMemorySessionService** | 内存 Session 存储 | google.adk |
| **InMemoryMemoryService** | 内存记忆存储 | google.adk |
| **jsonschema** | JSON Schema 验证 | 第三方库 |

### 2. 模型配置

```python
LITELLM_MODEL = os.getenv("LITELLM_MODEL", "gemini/gemini-2.5-flash")
```

支持通过环境变量配置，默认使用 Gemini 2.5 Flash。

---

## 核心类：ContactAgent

### 类结构

```
ContactAgent
├── __init__(base_url, use_ui)
│   ├── 初始化 Schema 管理器
│   ├── 构建 LLM Agent
│   └── 创建 Runner
├── get_agent_card()
├── get_processing_message()
├── _build_agent(use_ui)
└── stream(query, session_id)
```

---

## 业务逻辑详解

### 1. 初始化流程

```python
def __init__(self, base_url: str, use_ui: bool = False):
    self.base_url = base_url
    self.use_ui = use_ui
    
    # 根据 use_ui 决定是否启用 A2UI 模式
    self._schema_manager = (
        A2uiSchemaManager(
            version=VERSION_0_8,
            catalogs=[BasicCatalog.get_config(...)]
        )
        if use_ui
        else None
    )
    
    # 构建底层 LLM Agent
    self._agent = self._build_agent(use_ui)
    
    # 创建 ADK Runner
    self._runner = Runner(...)
```

**关键设计**：
- `use_ui` 参数控制是否启用 UI 模式
- UI 模式：使用 A2UI Schema 验证返回内容
- 非 UI 模式：纯文本返回

### 2. Agent Card 定义

```python
def get_agent_card(self) -> AgentCard:
    return AgentCard(
        name="Contact Lookup Agent",
        capabilities=AgentCapabilities(
            streaming=True,
            extensions=[get_a2ui_agent_extension(...)]
        ),
        skills=[AgentSkill(
            id="find_contact",
            name="Find Contact Tool",
            description="Helps find contact information...",
            tags=["contact", "directory", "people", "finder"],
            examples=[...]
        )]
    )
```

**能力说明**：
- **流式输出**：支持流式响应
- **A2UI 扩展**：支持 A2UI 协议扩展
- **技能标签**：联系人查找相关

### 3. Agent 构建逻辑

```python
def _build_agent(self, use_ui: bool) -> LlmAgent:
    # 根据模式选择不同的 prompt
    instruction = (
        self._schema_manager.generate_system_prompt(...)  # UI 模式
        if use_ui
        else get_text_prompt()  # 非 UI 模式
    )
    
    return LlmAgent(
        model=LiteLlm(model=LITELLM_MODEL),
        name="contact_agent",
        instruction=instruction,
        tools=[get_contact_info],  # 绑定工具
    )
```

---

## 核心方法：stream()

这是最核心的方法，处理用户查询并流式返回结果。

### 流程图

```
┌─────────────────────────────────────────────────────────────────┐
│                      stream(query, session_id)                  │
├─────────────────────────────────────────────────────────────────┤
│  1. 获取或创建 Session                                            │
│     └── session = _runner.session_service.get_session(...)      │
│                                                                     │
│  2. 检查 Schema 是否加载                                          │
│     └── if use_ui and not catalog_schema:                         │
│          └── yield error, return                                   │
│                                                                     │
│  3. 进入重试循环 (max_retries = 1, 共2次尝试)                      │
│     ┌──────────────────────────────────────┐                      │
│     │  3.1 发送消息给 LLM                   │                      │
│     │      async for event in runner.run_async  │                   │
│     │                                      │                      │
│     │  3.2 获取最终响应                      │                      │
│     │      final_response_content = ...    │                      │
│     │                                      │                      │
│     │  3.3 如果 use_ui: 验证响应            │                      │
│     │      - parse_response()              │                      │
│     │      - validator.validate()         │                      │
│     │      - 处理验证失败的重试              │                      │
│     │                                      │                      │
│     │  3.4 如果验证成功:                     │                      │
│     │      yield final response, return    │                      │
│     │                                      │                      │
│     │  3.5 如果验证失败:                     │                      │
│     │      构建重试提示, 继续循环            │                      │
│     └──────────────────────────────────────┘                      │
│                                                                     │
│  4. 重试次数用尽                                                    │
│     └── yield error message, return                               │
└─────────────────────────────────────────────────────────────────┘
```

### 详细步骤

#### Step 1: Session 管理

```python
session = await self._runner.session_service.get_session(
    app_name=self._agent.name,
    user_id=self._user_id,
    session_id=session_id,
)

if session is None:
    session = await self._runner.session_service.create_session(...)
elif "base_url" not in session.state:
    session.state["base_url"] = self.base_url
```

- 查找现有 session，不存在则创建
- 确保 session 包含 base_url 状态

#### Step 2: Schema 加载检查

```python
selected_catalog = self._schema_manager.get_selected_catalog()
if self.use_ui and not selected_catalog.catalog_schema:
    yield error message
    return
```

- 检查 A2UI Schema 是否加载
- 未加载则直接返回错误

#### Step 3: LLM 调用与响应获取

```python
async for event in self._runner.run_async(
    user_id=self._user_user_id,
    session_id=session.id,
    new_message=current_message,
):
    if event.is_final_response():
        final_response_content = ...
        break
    else:
        yield {"is_task_complete": False, "updates": ...}
```

- 使用 ADK Runner 异步执行
- 中间事件yield 进度更新
- 最终响应提取 text 内容

#### Step 4: UI 响应验证 (核心逻辑)

```python
if self.use_ui:
    try:
        # 1. 解析响应，提取 A2UI JSON
        response_parts = parse_response(final_response_content)
        
        for part in response_parts:
            if not part.a2ui_json:
                continue
            
            parsed_json_data = part.a2ui_json
            
            # 2. 处理空结果
            if parsed_json_data == []:
                is_valid = True
            else:
                # 3. JSON Schema 验证
                selected_catalog.validator.validate(parsed_json_data)
                is_valid = True
                
    except (ValueError, JSONDecodeError, ValidationError) as e:
        error_message = f"Validation failed: {e}."
        is_valid = False
```

**验证流程**：
1. `parse_response()` - 解析文本中的 A2UI JSON
2. `validator.validate()` - JSON Schema 验证
3. 支持空结果列表（表示"无结果"）

#### Step 5: 重试机制

```python
if not is_valid and attempt <= max_retries:
    current_query_text = (
        f"Your previous response was invalid. {error_message} "
        "You MUST generate a valid response that strictly follows "
        "the A2UI JSON SCHEMA..."
    )
    continue  # 重试
```

- 验证失败时构建重试提示
- 告知 LLM 需要生成符合 Schema 的响应

---

## A2UI 标签格式

代码中定义的标签：

```python
from a2ui.core.schema.constants import A2UI_OPEN_TAG, A2UI_CLOSE_TAG
```

LLM 响应格式：
```json
<a2ui>
[{"component": "text", "content": "..."}]
</a2ui>
```

---

## 与其他模块的关系

```
┌─────────────────────────────────────────────────────────────────┐
│                         ContactAgent                            │
├─────────────────────────────────────────────────────────────────┤
│  依赖:                                                          │
│  ├── tools.py              → get_contact_info (工具)           │
│  ├── prompt_builder.py     → 系统提示词构建                     │
│  └── __main__.py           → A2AStarletteApplication           │
│                                                                   │
│  A2UI 依赖:                                                     │
│  ├── A2uiSchemaManager     → Schema 管理与验证                  │
│  ├── BasicCatalog          → 基础组件目录                       │
│  └── parse_response        → 响应解析                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## 总结

| 特性 | 说明 |
|------|------|
| **架构模式** | ADK LLM Agent + A2UI Schema 验证 |
| **流式支持** | ✅ 通过 async generator 实现 |
| **UI 模式** | 可选，通过 use_ui 参数控制 |
| **重试机制** | 1 次重试，共 2 次尝试 |
| **错误处理** | JSON Schema 验证失败重试 |
| **Session 管理** | 内存存储，跨请求保持状态 |
