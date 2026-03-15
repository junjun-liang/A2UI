# Contact Lookup Agent 软件架构与业务逻辑分析

> 本文件分析 `/samples/agent/adk/contact_lookup` 目录的 Agent 软件架构和业务流程

---

## 一、项目结构

```
contact_lookup/
├── agent.py                  # 核心 Agent 逻辑
├── agent_executor.py         # ADK 执行器
├── tools.py                  # 业务工具（联系人查询）
├── prompt_builder.py         # Prompt 构建
├── contact_data.json         # 联系人数据
├── examples/                 # A2UI 示例
│   ├── action_confirmation.json
│   ├── contact_card.json
│   ├── contact_list.json
│   └── follow_success.json
└── images/                   # 示例图片
```

---

## 二、整体架构

### 2.1 系统架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                           客户端                                │
│  ┌─────────────────┐                                            │
│  │   用户界面       │                                            │
│  └────────┬────────┘                                            │
└───────────┼─────────────────────────────────────────────────────┘
            │ A2A 消息
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Agent 服务器                               │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    AgentExecutor                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                           │                                     │
│                           ▼                                     │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    ContactAgent                           │   │
│  │  ┌──────────────────────────────────────────────────┐    │   │
│  │  │           A2uiSchemaManager                     │    │   │
│  │  │  ┌─────────────┐  ┌────────────┐  ┌───────────┐  │    │   │
│  │  │  │ BasicCatalog│  │  Parser    │  │ Validator │  │    │   │
│  │  │  └─────────────┘  └────────────┘  └───────────┘  │    │   │
│  │  └──────────────────────────────────────────────────┘    │   │
│  └─────────────────────────────────────────────────────────┘   │
│                           │                                     │
│                           ▼                                     │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                      ADK Runner                          │   │
│  │  ┌──────────────┐  ┌───────────────┐  ┌──────────────┐  │   │
│  │  │SessionService│  │ ArtifactService│  │MemoryService │  │   │
│  │  └──────────────┘  └───────────────┘  └──────────────┘  │   │
│  └─────────────────────────────────────────────────────────┘   │
│                           │                                     │
│                           ▼                                     │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                  LLM Model (Gemini)                     │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    业务工具                               │   │
│  │  ┌─────────────────────────────────────────────────┐     │   │
│  │  │            get_contact_info                    │     │   │
│  │  │         ┌──────────────────┐                   │     │   │
│  │  │         │  contact_data   │                   │     │   │
│  │  │         │      .json      │                   │     │   │
│  │  │         └──────────────────┘                   │     │   │
│  │  └─────────────────────────────────────────────────┘     │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 三、核心组件

### 3.1 ContactAgent 类

```python
class ContactAgent:
    def __init__(self, base_url: str, use_ui: bool = False):
        # 1. 初始化 A2UI Schema 管理器
        self._schema_manager = A2uiSchemaManager(
            version=VERSION_0_8,
            catalogs=[BasicCatalog.get_config(version=VERSION_0_8)]
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

### 3.2 核心方法

| 方法 | 作用 |
|------|------|
| `__init__` | 初始化 Agent、Schema Manager、Runner |
| `get_agent_card` | 生成 A2A Agent Card |
| `get_processing_message` | 返回处理中的提示消息 |
| `_build_agent` | 构建 LLM Agent |
| `stream` | 流式处理用户请求 |

---

## 四、业务流程

### 4.1 主流程图

```
┌─────────────────────────────────────────────────────────────────┐
│                         用户请求                                  │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    使用 UI 模式?                                  │
│                   ┌─────────────┐                                 │
│                   │   Yes / No  │                                 │
│                   └──────┬──────┘                                 │
│            ┌──────────────┘  └───────────────┐                    │
│            ▼                                    ▼                │
│   ┌─────────────────┐              ┌─────────────────┐            │
│   │ 初始化 A2UI     │              │  使用纯文本     │            │
│   │ Schema Manager  │              │    模式         │            │
│   └────────┬────────┘              └────────┬────────┘            │
│            │                                 │                    │
│            └─────────────┬───────────────────┘                    │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │    构建 Agent        │                             │
│               │  + Schema Manager   │                             │
│               └──────────┬──────────┘                             │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │  获取/创建 Session  │                             │
│               └──────────┬──────────┘                             │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │  Runner.run_async   │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │   LLM 生成响应       │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │  parse_response    │                             │
│               │    解析响应         │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │  包含 A2UI JSON?   │                             │
│               └──────────┬──────────┘                             │
│            ┌──────────────┘  └───────────────┐                    │
│            ▼                                    ▼                │
│   ┌─────────────────┐              ┌─────────────────┐            │
│   │   纯文本响应     │              │  Schema 校验    │            │
│   └────────┬────────┘              └────────┬────────┘            │
│            │                                 │                    │
│            │         ┌──────────────┐        │                    │
│            │         │  校验成功?   │        │                    │
│            │         └──────┬───────┘        │                    │
│            │        ┌───────┴───────┐        │                    │
│            │        ▼               ▼        │                    │
│            │  ┌─────────┐   ┌──────────┐     │                    │
│            │  │   是    │   │   否     │     │                    │
│            │  └───┬─────┘   └────┬─────┘     │                    │
│            │      │              │            │                    │
│            │      │              ▼            │                    │
│            │      │     ┌────────────────┐    │                    │
│            │      │     │   重试        │    │                    │
│            │      │     │  (最多2次)    │    │                    │
│            │      │     └───────┬──────┘    │                    │
│            │      │             │            │                    │
│            │      │             └─────┬──────┘                    │
│            │      │                   │                           │
│            │      │                   ▼                           │
│            │      │          ┌────────────────┐                   │
│            │      │          │  返回错误消息  │                   │
│            │      │          └───────┬───────┘                   │
│            │      │                  │                           │
│            │      │                  │                           │
│            │      │                  │                           │
│            │      │                  │                           │
│            └──────┴──────────────────┘                           │
│                          │                                       │
│                          ▼                                       │
│               ┌─────────────────────┐                             │
│               │     返回响应         │                             │
│               └─────────────────────┘                             │
```

### 4.2 Schema 校验流程

```
┌─────────────────────────────────────────────────────────────────┐
│                         输入                                      │
│                   ┌─────────────┐                                 │
│                   │ A2UI JSON   │                                 │
│                   └──────┬──────┘                                 │
└───────────────────────────┼───────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     校验步骤 1                                    │
│               ┌─────────────────────┐                             │
│               │   检查 JSON 格式     │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│              ┌───────────┴───────────┐                            │
│              │         失败          │                            │
│              └───────────┬───────────┘                            │
│                          │ 失败                                   │
│                          ▼                                       │
│              ┌─────────────────────┐                             │
│              │     返回错误         │                             │
│              └─────────────────────┘                             │
                            │
                            │ 成功
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     校验步骤 2                                    │
│               ┌─────────────────────┐                             │
│               │   匹配 JSON Schema  │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│              ┌───────────┴───────────┐                            │
│              │         失败          │                            │
│              └───────────┬───────────┘                            │
│                          │ 失败                                   │
│                          ▼                                       │
│              ┌─────────────────────┐                             │
│              │     返回错误         │                             │
│              └─────────────────────┘                             │
                            │
                            │ 成功
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     校验步骤 3                                    │
│               ┌─────────────────────┐                             │
│               │   验证组件合法性    │                             │
│               └──────────┬──────────┘                             │
│                          │                                       │
│              ┌───────────┴───────────┐                            │
│              │         失败          │                            │
│              └───────────┬───────────┘                            │
│                          │ 失败                                   │
│                          ▼                                       │
│              ┌─────────────────────┐                             │
│              │     返回错误         │                             │
│              └─────────────────────┘                             │
                            │
                            │ 成功
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                         输出                                      │
│               ┌───────────────┐                                  │
│               │   校验通过    │                                  │
│               └───────────────┘                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 五、核心代码解析

### 5.1 Agent 初始化 (`agent.py`)

```python
def __init__(self, base_url: str, use_ui: bool = False):
    self.base_url = base_url
    self.use_ui = use_ui
    
    # 关键：初始化 A2UI Schema Manager
    self._schema_manager = (
        A2uiSchemaManager(
            version=VERSION_0_8,
            catalogs=[
                BasicCatalog.get_config(
                    version=VERSION_0_8,
                    examples_path="examples"  # 加载示例
                )
            ],
        )
        if use_ui
        else None
    )
    
    # 构建 Agent
    self._agent = self._build_agent(use_ui)
    
    # 初始化 Runner
    self._runner = Runner(...)
```

**关键点**：
- `use_ui` 参数决定是否启用 A2UI
- `BasicCatalog` 包含基础组件定义
- `examples_path` 加载 A2UI 示例

### 5.2 Agent Card 生成

```python
def get_agent_card(self) -> AgentCard:
    # 1. 构造能力声明
    capabilities = AgentCapabilities(
        streaming=True,
        extensions=[
            # 2. 添加 A2UI 扩展
            get_a2ui_agent_extension(
                self._schema_manager.accepts_inline_catalogs,
                self._schema_manager.supported_catalog_ids,
            )
        ],
    )
    
    # 3. 定义技能
    skill = AgentSkill(
        id="find_contact",
        name="Find Contact Tool",
        description="Helps find contact information...",
        examples=[
            "Who is David Chen in marketing?",
            "Find Sarah Lee from engineering",
        ],
    )
    
    return AgentCard(
        name="Contact Lookup Agent",
        capabilities=capabilities,
        skills=[skill],
    )
```

### 5.3 流式处理核心逻辑

```python
async def stream(self, query, session_id):
    # 1. 获取或创建 Session
    session = await self._runner.session_service.get_session(...)
    
    # 2. 最多重试 2 次
    max_retries = 1  # 总共 2 次尝试
    attempt = 0
    
    while attempt <= max_retries:
        attempt += 1
        
        # 3. 调用 LLM
        async for event in self._runner.run_async(...):
            if event.is_final_response():
                final_response_content = event.content.parts[0].text
                break
        
        if self.use_ui:
            # 4. 解析 A2UI JSON
            response_parts = parse_response(final_response_content)
            
            for part in response_parts:
                if not part.a2ui_json:
                    continue
                
                # 5. Schema 校验
                try:
                    selected_catalog.validator.validate(part.a2ui_json)
                    is_valid = True
                except ValidationError as e:
                    # 6. 校验失败，准备重试
                    error_message = f"Validation failed: {e}"
                    current_query_text = f"请生成有效的 A2UI JSON..."
                    break
        
        # 7. 返回响应
        yield {"is_task_complete": True, "parts": final_parts}
```

---

## 六、工具定义

### 6.1 get_contact_info 工具

```python
@tool
def get_contact_info(
    name: str,                    # 必填：姓名
    tool_context: ToolContext,    # 工具上下文
    department: str = ""           # 可选：部门筛选
) -> str:
    """根据姓名（和可选部门）查询联系人信息"""
    
    # 1. 读取联系人数据
    with open("contact_data.json") as f:
        all_contacts = json.loads(f.read())
    
    # 2. 按姓名筛选
    results = [
        contact for contact in all_contacts
        if name.lower() in contact["name"].lower()
    ]
    
    # 3. 按部门进一步筛选
    if department:
        results = [
            contact for contact in results
            if department.lower() in contact["department"].lower()
        ]
    
    # 4. 返回 JSON 字符串
    return json.dumps(results)
```

### 6.2 数据结构

`contact_data.json` 示例：

```json
[
  {
    "name": "David Chen",
    "email": "david.chen@company.com",
    "department": "Marketing",
    "location": "Building A, Floor 3",
    "team": "Digital Marketing",
    "profile_image": "http://localhost:10002/images/profile1.png"
  },
  {
    "name": "Sarah Lee",
    "email": "sarah.lee@company.com",
    "department": "Engineering",
    "location": "Building B, Floor 5",
    "team": "Backend",
    "profile_image": "http://localhost:10002/images/profile2.png"
  }
]
```

---

## 七、A2UI 消息示例

### 7.1 联系人列表 (`contact_list.json`)

```json
[
  {
    "createSurface": {
      "surfaceId": "contact_list_surface",
      "root": "root"
    }
  },
  {
    "surfaceUpdate": {
      "surfaceId": "contact_list_surface",
      "components": [
        {
          "id": "root",
          "component": {
            "Column": {
              "children": ["header", "contact_list"]
            }
          }
        },
        {
          "id": "header",
          "component": {
            "Text": {
              "text": { "literalString": "Search Results" },
              "usageHint": "h2"
            }
          }
        },
        {
          "id": "contact_list",
          "component": {
            "List": {
              "children": {
                "template": {
                  "componentId": "contact_card",
                  "dataBinding": "/contacts"
                }
              }
            }
          }
        }
      ]
    }
  }
]
```

### 7.2 联系人卡片 (`contact_card.json`)

```json
[
  {
    "surfaceUpdate": {
      "surfaceId": "contact_list_surface",
      "components": [
        {
          "id": "contact_card",
          "component": {
            "Card": {
              "child": "card_content"
            }
          }
        },
        {
          "id": "card_content",
          "component": {
            "Row": {
              "children": ["avatar", "info"]
            }
          }
        },
        {
          "id": "avatar",
          "component": {
            "Image": {
              "url": { "path": "/profile_image" },
              "variant": "avatar"
            }
          }
        },
        {
          "id": "info",
          "component": {
            "Column": {
              "children": ["name", "email", "department"]
            }
          }
        }
      ]
    }
  }
]
```

---

## 八、Prompt 构建

### 8.1 Prompt 组成

```python
# prompt_builder.py

ROLE_DESCRIPTION = """
You are a contact lookup assistant. Your task is to help users find 
contact information for people in the organization.
"""

WORKFLOW_DESCRIPTION = """
1. When a user asks to find someone, use the get_contact_info tool
2. Format the results as A2UI components
3. Display contacts in a list with avatars and details
"""

UI_DESCRIPTION = """
Generate A2UI messages to display:
- A list of contacts with name, email, department, location
- Each contact should be in a card with profile image
- Include action buttons for email or message
"""
```

---

## 九、错误处理与重试

### 9.1 重试机制

```python
max_retries = 1  # 总共 2 次尝试

while attempt <= max_retries:
    try:
        # 校验
        validator.validate(a2ui_json)
        is_valid = True
        break
    except ValidationError as e:
        # 重试
        current_query_text = (
            f"Your previous response was invalid. {e} "
            "Please generate valid A2UI JSON..."
        )
        # 继续循环
```

### 9.2 错误类型

| 错误类型 | 处理方式 |
|---------|---------|
| JSON 解析失败 | 重试 |
| Schema 验证失败 | 重试并告知错误 |
| 无响应 | 重试 |
| 重试耗尽 | 返回错误消息 |

---

## 十、部署架构

```
┌─────────────────────────────────────────────────────────────────┐
│                          前端                                    │
│  ┌─────────────────┐       ┌─────────────────┐                  │
│  │   Web 应用       │       │   A2A Client   │                  │
│  └────────┬────────┘       └────────┬────────┘                  │
└───────────┼─────────────────────────┼─────────────────────────────┘
            │ HTTP                   │ A2A
            ▼                        ▼
┌─────────────────────────────────────────────────────────────────┐
│                          后端                                    │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    Gunicorn 服务器                         │   │
│  │  ┌─────────────────────────────────────────────────┐    │   │
│  │  │              AgentExecutor                         │    │   │
│  │  └─────────────────────────────────────────────────┘    │   │
│  │                         │                                 │   │
│  │                         ▼                                 │   │
│  │  ┌─────────────────────────────────────────────────┐    │   │
│  │  │               ContactAgent                        │    │   │
│  │  │  ┌─────────────┐  ┌────────────┐  ┌───────────┐  │    │   │
│  │  │  │SchemaManager│  │   Parser   │  │ Validator │  │    │   │
│  │  │  └─────────────┘  └────────────┘  └───────────┘  │    │   │
│  │  └─────────────────────────────────────────────────┘    │   │
│  └─────────────────────────────────────────────────────────┘   │
│                           │                                      │
│                           ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    基础设施                                │   │
│  │  ┌──────────────┐  ┌───────────────┐  ┌────────────┐  │   │
│  │  │   Redis      │  │   LLM API     │  │  文件系统  │  │   │
│  │  │ (Session)    │  │   (Gemini)    │  │   (数据)   │  │   │
│  │  └──────────────┘  └───────────────┘  └────────────┘  │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 十一、总结

### 11.1 核心特点

1. **双模式支持**：可选择纯文本或 A2UI 模式
2. **Schema 校验**：确保 A2UI 消息有效性
3. **重试机制**：自动重试无效响应
4. **工具集成**：通过 `get_contact_info` 查询数据
5. **Agent Card**：标准化服务发现

### 11.2 数据流

```
用户请求 → AgentExecutor → ContactAgent.stream 
    → Runner.run_async → LLM → 解析/校验 
    → DataPart 封装 → 返回客户端
```

### 11.3 关键技术

| 技术 | 作用 |
|------|------|
| Google ADK | Agent 框架 |
| LiteLlm | LLM 调用 |
| A2UI | UI 协议 |
| JSON Schema | 消息验证 |
| async/await | 异步流式处理 |
