# ContactAgent 核心逻辑分析

## 文件位置
`samples/agent/adk/contact_lookup/agent.py`

---

## 概述

`ContactAgent` 是联系人查询 Agent 的核心类，负责：
1. 管理 A2UI Schema 和提示词
2. 构建 ADK LLM Agent
3. 处理用户查询并返回流式响应

---

## 类结构

```python
class ContactAgent:
    """An agent that finds contact info for colleagues."""

    def __init__(self, base_url: str, use_ui: bool = False):
        # 初始化 Schema 管理器、Agent、Runner

    def get_agent_card(self) -> AgentCard:
        # 返回 Agent 能力卡片

    def get_processing_message(self) -> str:
        # 返回处理中消息

    def _build_agent(self, use_ui: bool) -> LlmAgent:
        # 构建 ADK LLM Agent

    async def stream(self, query, session_id) -> AsyncIterable[dict]:
        # 流式处理查询
```

---

## 整体流程图

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ContactAgent                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌──────────────────┐    ┌──────────────────┐    ┌──────────────┐   │
│  │ __init__()      │───▶│ get_agent_card() │───▶│ _build_agent │   │
│  │                  │    │                  │    │   ()         │   │
│  │ - Schema Manager │    │ - capabilities  │    │              │   │
│  │ - LLM Agent      │    │ - skills        │    │ - prompt     │   │
│  │ - Runner        │    │ - version       │    │ - tools      │   │
│  └──────────────────┘    └──────────────────┘    └──────────────┘   │
│           │                                                    │       │
│           └──────────────────┬───────────────────────────────────┘       │
│                              ▼                                          │
│                    ┌──────────────────┐                                  │
│                    │    stream()      │                                  │
│                    │                  │                                  │
│                    │ 1. Session 管理  │                                  │
│                    │ 2. LLM 调用      │                                  │
│                    │ 3. 响应验证      │                                  │
│                    │ 4. 重试逻辑      │                                  │
│                    │ 5. 返回流式响应  │                                  │
│                    └──────────────────┘                                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 初始化流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                     __init__(base_url, use_ui)                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. 保存参数                                                         │
│     self.base_url = base_url                                        │
│     self.use_ui = use_ui                                            │
│                                                                      │
│  2. 创建 Schema Manager (仅 UI 模式)                                │
│     ┌────────────────────────────────────────────────────────┐       │
│     │ if use_ui:                                            │       │
│     │   self._schema_manager = A2uiSchemaManager(           │       │
│     │       version=VERSION_0_8,                            │       │
│     │       catalogs=[BasicCatalog.get_config(...)]          │       │
│     │   )                                                   │       │
│     │ else:                                                 │       │
│     │   self._schema_manager = None                         │       │
│     └────────────────────────────────────────────────────────┘       │
│                                                                      │
│  3. 构建 LLM Agent                                                  │
│     self._agent = self._build_agent(use_ui)                         │
│                                                                      │
│  4. 创建 ADK Runner                                                 │
│     self._runner = Runner(                                          │
│         app_name=self._agent.name,                                  │
│         agent=self._agent,                                          │
│         artifact_service=InMemoryArtifactService(),                  │
│         session_service=InMemorySessionService(),                    │
│         memory_service=InMemoryMemoryService(),                      │
│     )                                                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Agent 构建流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                      _build_agent(use_ui)                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. 获取模型配置                                                     │
│     LITELLM_MODEL = os.getenv("LITELLM_MODEL",                      │
│                           "gemini/gemini-2.5-flash")                │
│                                                                      │
│  2. 生成指令 (Prompt)                                               │
│     ┌────────────────────────────────────────────────────────┐       │
│     │ if use_ui:                                            │       │
│     │   instruction = schema_manager.generate_system_prompt(│       │
│     │       role_description=ROLE_DESCRIPTION,              │       │
│     │       workflow_description=WORKFLOW_DESCRIPTION,      │       │
│     │       ui_description=UI_DESCRIPTION,                   │       │
│     │       include_schema=True,                             │       │
│     │       include_examples=True,                           │       │
│     │   )                                                   │       │
│     │ else:                                                 │       │
│     │   instruction = get_text_prompt()                     │       │
│     └────────────────────────────────────────────────────────┘       │
│                                                                      │
│  3. 创建 LlmAgent                                                   │
│     return LlmAgent(                                                │
│         model=LiteLlm(model=LITELLM_MODEL),                         │
│         name="contact_agent",                                        │
│         instruction=instruction,                                      │
│         tools=[get_contact_info],                                    │
│     )                                                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## stream() 核心流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                     async stream(query, session_id)                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ Step 1: Session 管理                                           │ │
│  │                                                                  │ │
│  │ session = runner.session_service.get_session(...)              │ │
│  │                                                                  │ │
│  │ if session is None:                                            │ │
│  │     session = runner.session_service.create_session(...)       │ │
│  │ elif "base_url" not in session.state:                         │ │
│  │     session.state["base_url"] = self.base_url                 │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                              │                                       │
│                              ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ Step 2: Schema 检查 (UI 模式)                                 │ │
│  │                                                                  │ │
│  │ selected_catalog = self._schema_manager.get_selected_catalog() │ │
│  │                                                                  │ │
│  │ if use_ui and not selected_catalog.catalog_schema:             │ │
│  │     yield error message                                        │ │
│  │     return                                                    │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                              │                                       │
│                              ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ Step 3: 重试循环 (max_retries = 1, 共 2 次尝试)              │ │
│  │                                                                  │ │
│  │ while attempt <= max_retries:                                  │ │
│  │     ┌───────────────────────────────────────────────────────┐   │ │
│  │     │ Step 3.1: 调用 LLM                                    │   │ │
│  │     │                                                        │   │ │
│  │     │ current_message = types.Content(                      │   │ │
│  │     │     role="user",                                      │   │ │
│  │     │     parts=[types.Part.from_text(text=query)]          │   │ │
│  │     │ )                                                      │   │ │
│  │     │                                                        │   │ │
│  │     │ async for event in runner.run_async(...):              │   │ │
│  │     │     if event.is_final_response():                      │   │ │
│  │     │         final_response_content = ...                  │   │ │
│  │     │         break                                          │   │ │
│  │     │     else:                                              │   │ │
│  │     │         yield {"is_task_complete": False, ...}        │   │ │
│  │     └───────────────────────────────────────────────────────┘   │ │
│  │                              │                                   │ │
│  │                              ▼                                   │ │
│  │     ┌───────────────────────────────────────────────────────┐   │ │
│  │     │ Step 3.2: 验证响应 (UI 模式)                          │   │ │
│  │     │                                                        │   │ │
│  │     │ if use_ui:                                            │   │ │
│  │     │     response_parts = parse_response(content)           │   │ │
│  │     │                                                        │   │ │
│  │     │     for part in response_parts:                        │   │ │
│  │     │         if part.a2ui_json:                            │   │ │
│  │     │             validator.validate(part.a2ui_json)         │   │ │
│  │     │                                                        │   │ │
│  │     │ except ValidationError:                                │   │ │
│  │     │     is_valid = False                                   │   │ │
│  │     │     error_message = ...                                 │   │ │
│  │     └───────────────────────────────────────────────────────┘   │ │
│  │                              │                                   │ │
│  │                              ▼                                   │ │
│  │     ┌───────────────────────────────────────────────────────┐   │ │
│  │     │ Step 3.3: 返回结果 / 重试                             │   │ │
│  │     │                                                        │   │ │
│  │     │ if is_valid:                                           │   │ │
│  │     │     final_parts = parse_response_to_parts(...)         │   │ │
│  │     │     yield {"is_task_complete": True, "parts": ...}    │   │ │
│  │     │     return                                             │   │ │
│  │     │ else:                                                   │   │ │
│  │     │     current_query_text = "Your response invalid..."    │   │ │
│  │     │     # 继续循环                                         │   │ │
│  │     └───────────────────────────────────────────────────────┘   │ │
│  │                                                                  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                              │                                       │
│                              ▼                                       │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ Step 4: 重试耗尽                                               │ │
│  │                                                                  │ │
│  │ yield error message                                            │ │
│  │ return                                                        │ │
│  │                                                                  │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 验证与重试逻辑

### 验证流程

```
LLM 响应内容
    │
    ▼
parse_response(content)
    │
    ▼
┌─────────────────────┐
│ 遍历 ResponseParts  │
└─────────────────────┘
    │
    ├─ part.text ──▶ 保留为 TextPart
    │
    └─ part.a2ui_json
        │
        ├─ 空列表 [] ──▶ 视为有效 (无结果)
        │
        └─ 非空 ──▶ validator.validate()
                        │
                        ├─ 通过 ──▶ is_valid = True
                        │
                        └─ 失败 ──▶ ValidationError
                                    │
                                    ▼
                                is_valid = False
                                error_message
```

### 重试提示

```python
current_query_text = (
    f"Your previous response was invalid. {error_message} "
    "You MUST generate a valid response that strictly follows the A2UI JSON SCHEMA. "
    "The response MUST be a JSON list of A2UI messages. "
    "Ensure each JSON part is wrapped in '<a2ui-json>' and '</a2ui-json>' tags. "
    f"Please retry the original request: '{query}'"
)
```

---

## 数据流

```
User Query
    │
    ▼
┌─────────────────┐
│ ContactAgent    │
│  .stream()      │
└─────────────────┘
    │
    ▼
┌─────────────────┐     ┌─────────────────┐
│ ADK Runner      │────▶│ LLM (Gemini)   │
│ runner.run_async│◀────│                 │
└─────────────────┘     └─────────────────┘
    │                         │
    │ Tool Call               │ Response
    ▼                         ▼
┌─────────────────┐     ┌─────────────────┐
│ get_contact_info│     │ parse_response │
│ tool            │     │ + validate     │
└─────────────────┘     └─────────────────┘
    │                         │
    │ JSON Results             │ Valid A2UI JSON
    ▼                         ▼
┌─────────────────┐     ┌─────────────────┐
│ LLM receives    │────▶│ A2A Parts      │
│ tool results    │     │ (response)      │
└─────────────────┘     └─────────────────┘
```

---

## 与其他模块的关系

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ContactAgent                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  依赖:                                                               │
│  ├── prompt_builder.py                                               │
│  │   ├── ROLE_DESCRIPTION                                           │
│  │   ├── WORKFLOW_DESCRIPTION                                       │
│  │   ├── UI_DESCRIPTION                                             │
│  │   └── get_text_prompt()                                          │
│  │                                                                  │
│  ├── tools.py                                                        │
│  │   └── get_contact_info()  ← ADK Tool                             │
│  │                                                                  │
│  ├── a2ui.core.schema.manager                                        │
│  │   └── A2uiSchemaManager                                          │
│  │                                                                  │
│  ├── a2ui.core.parser.parser                                         │
│  │   └── parse_response()                                            │
│  │                                                                  │
│  ├── a2ui.basic_catalog.provider                                     │
│  │   └── BasicCatalog                                               │
│  │                                                                  │
│  └── a2ui.a2a                                                       │
│      ├── create_a2ui_part()                                          │
│      ├── get_a2ui_agent_extension()                                  │
│      └── parse_response_to_parts()                                    │
│                                                                      │
│  被依赖:                                                             │
│  ├── agent_executor.py                                               │
│  │   └── ContactAgentExecutor                                        │
│  │       ├── ui_agent: ContactAgent(use_ui=True)                    │
│  │       └── text_agent: ContactAgent(use_ui=False)                 │
│  │                                                                  │
│  └── __main__.py                                                    │
│      └── A2AStarletteApplication                                     │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 关键配置

### 模型配置

```python
LITELLM_MODEL = os.getenv("LITELLM_MODEL", "gemini/gemini-2.5-flash")
```

### 重试配置

```python
max_retries = 1  # 总共 2 次尝试 (1 次重试)
```

### 内容类型

```python
SUPPORTED_CONTENT_TYPES = ["text", "text/plain"]
```

---

## 总结

| 阶段 | 核心组件 | 说明 |
|------|----------|------|
| **初始化** | `A2uiSchemaManager` | 加载 A2UI Schema + Catalog |
| **构建** | `LlmAgent` | 配置 Prompt + Tools |
| **执行** | `Runner` | ADK 运行器，管理会话 |
| **验证** | `A2uiValidator` | JSON Schema 验证 |
| **重试** | 自定义逻辑 | 验证失败时重试 |
| **响应** | `parse_response_to_parts` | 转换为 A2A Parts |
