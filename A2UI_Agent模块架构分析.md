# A2UI Agent 模块架构分析

> 本文件分析 `/samples/agent` 目录的软件架构和业务逻辑代码

---

## 一、整体目录结构

```
samples/agent/
├── adk/                              # 基于 Google ADK 的 Agent 示例
│   ├── component_gallery/            # 组件库展示示例
│   ├── contact_lookup/               # 联系人查找示例
│   ├── contact_multiple_surfaces/    # 多 Surface 场景示例
│   ├── orchestrator/                 # 多 Agent 调度器示例
│   ├── restaurant_finder/           # 餐厅查找预订示例
│   ├── rizzcharts/                  # 电商仪表盘示例（图表/地图）
│   └── pyproject.toml
│
└── mcp/                             # 基于 MCP 协议的示例
    ├── apps/                        # 嵌入式前端应用
    ├── server.py                    # MCP 服务器实现
    └── recipe_a2ui.json            # 示例数据
```

---

## 二、技术栈

### 1. 核心框架

| 技术 | 作用 |
|------|------|
| **Google ADK** | Agent 开发框架 |
| **LiteLlm** | LLM 调用封装 |
| **A2UI Core** | A2UI 协议处理 |

### 2. A2UI 相关库

| 库 | 作用 |
|------|------|
| `a2ui.core.schema` | Schema 管理与验证 |
| `a2ui.a2a` | A2A 协议集成 |
| `a2ui.basic_catalog` | 基础组件目录 |

---

## 三、ADK 示例模块详解

### 3.1 Component Gallery Agent（组件库示例）

**功能**：展示 A2UI 组件库并响应用户 UI 动作

**核心代码** (`agent.py`)：

```python
class ComponentGalleryAgent:
    def stream(self, query, context_id):
        if query == "WHO_ARE_YOU" or query == "START":
            # 初始加载 Gallery
            gallery_json = get_gallery_json()
            # 使用 DataPart 封装 A2UI JSON
            yield DataPart(data={...})
        elif query.startswith("ACTION:"):
            # 处理用户 UI 动作
            response_update = {...}  # surfaceUpdate
            yield create_a2ui_part(response_update)
```

**特点**：
- 使用 `A2UI_OPEN_TAG` / `A2UI_CLOSE_TAG` 标记混合文本与 A2UI JSON
- 通过 `DataPart` 中的 `userAction` 响应用户操作

---

### 3.2 Contact Lookup Agent（联系人查找）

**功能**：基于文本/A2UI 的联系人查找

**架构**：

```
┌─────────────────────────────────────────────────────────────┐
│                    ContactAgent                              │
├─────────────────────────────────────────────────────────────┤
│  __init__                                                   │
│    ├── A2uiSchemaManager ←── 加载 A2UI Schema              │
│    ├── BasicCatalog ←─────── 基础组件目录                    │
│    └── Runner ←───────────── ADK 运行器                     │
├─────────────────────────────────────────────────────────────┤
│  stream()                                                   │
│    ├── Runner.run_async() ←─── 调用 LLM                     │
│    ├── parse_response() ←──── 解析 A2UI JSON                │
│    ├── validator.validate() ← 校验 Schema                   │
│    └── DataPart ←──────────── 封装响应                       │
└─────────────────────────────────────────────────────────────┘
```

**核心流程**：

```python
async def stream(self, query, session_id):
    # 1. 调用 LLM
    response = await self._runner.run_async(user_message)
    
    # 2. 解析 A2UI JSON
    parsed = parse_response(response)
    
    # 3. Schema 校验
    for a2ui_json in parsed.a2ui_json:
        try:
            validator.validate(a2ui_json)
            # 4. 封装为 DataPart
            yield DataPart(data={"a2ui": True, "instances": [a2ui_json]})
        except ValidationError:
            # 重试逻辑
            yield error
```

---

### 3.3 Restaurant Finder Agent（餐厅查找）

**功能**：餐厅搜索与预订

**与 ContactAgent 的对比**：

| 特性 | ContactAgent | RestaurantAgent |
|------|-------------|-----------------|
| 场景 | 联系人查找 | 餐厅查找预订 |
| 工具 | get_contact_info | get_restaurants |
| UI | 联系人卡片 | 餐厅列表+表单 |

**工具定义** (`tools.py`)：

```python
@tool
def get_restaurants(
    cuisine: str,
    location: str,
    count: int = 5
) -> str:
    """获取指定 cuisine、location 和数量的餐厅列表"""
    # 从数据库查询
    results = query_restaurants(cuisine, location, count)
    return json.dumps(results)
```

---

### 3.4 Orchestrator Agent（调度器）

**功能**：多 Agent 调度，可选 A2UI 能力

**架构**：

```
┌─────────────────────────────────────────────────────────────┐
│                  OrchestratorAgent                          │
├─────────────────────────────────────────────────────────────┤
│  SubagentRouteManager ←── 管理子 Agent 路由                  │
│  A2ACardResolver ←─────── 获取子 Agent Card                 │
├─────────────────────────────────────────────────────────────┤
│  核心逻辑                                                    │
│  1. 从子 Agent 收集 A2UI capabilities                       │
│  2. 聚合 supported_catalog_ids                              │
│  3. 生成聚合的 AgentCard                                    │
│  4. 根据 userAction + surfaceId 路由到子 Agent             │
└─────────────────────────────────────────────────────────────┘
```

**路由逻辑** (`agent.py`)：

```python
def programmtically_route_user_action_to_subagent(
    callback_context, before_model_callback
):
    # 检查是否是 A2UI part
    if contains_a2ui_part(callback_context):
        # 提取 surfaceId 和 userAction
        surface_id = extract_surface_id(callback_context)
        user_action = extract_user_action(callback_context)
        
        # 路由到对应子 Agent
        target_agent = route_manager.get_route(surface_id)
        return FunctionCall(name="transfer_to_agent", args={"agent_name": target_agent})
```

---

### 3.5 Rizzcharts Agent（电商仪表盘）

**功能**：销售数据可视化（图表/地图）

**特点**：
- 使用自定义 A2UI Catalog (`rizzcharts_catalog_definition.json`)
- 使用 `SendA2uiToClientToolset` 发送 A2UI JSON
- 展示图表、地图等复杂可视化组件

**工具集**：

```python
# 注册的工具
tools = [
    get_store_sales,      # 获取门店销售数据
    get_sales_data,       # 获取销售数据
    SendA2uiToClientToolset(...)  # 发送 A2UI 到客户端
]
```

---

## 四、MCP 示例模块

### 4.1 MCP Server 架构

```
┌─────────────────────────────────────────────────────────────┐
│                      MCP Server                             │
├─────────────────────────────────────────────────────────────┤
│  工具 (Tools)                                               │
│  ├── get_recipe_a2ui ←─────── 获取食谱 A2UI                 │
│  ├── send_a2ui_user_action ←─ 发送用户动作                  │
│  └── send_a2ui_error ←──────── 发送错误                     │
├─────────────────────────────────────────────────────────────┤
│  Schema 校验                                                │
│  ├── server_to_client.json ←─ 验证 A2UI 消息               │
│  └── client_to_server.json ←─ 验证客户端消息                │
└─────────────────────────────────────────────────────────────┘
```

---

## 五、核心代码文件职责

### 5.1 文件对应关系

| 文件 | 作用 |
|------|------|
| `agent.py` | Agent 核心逻辑（状态机、响应生成） |
| `agent_executor.py` | ADK 执行器（请求解析、响应封装） |
| `tools.py` | 业务工具（数据查询） |
| `prompt_builder.py` | Prompt 构建 |
| `a2ui_examples.py` | A2UI 示例数据 |

### 5.2 AgentExecutor 模式

```python
class ComponentGalleryExecutor(AgentExecutor):
    def execute(self, request, task):
        # 1. 解析请求
        query = extract_query(request)
        
        # 2. 调用 Agent
        for part in self.agent.stream(query, task.context_id):
            # 3. 封装响应
            yield TaskUpdater(part=part)
```

---

## 六、A2UI 与 Agent 集成模式

### 6.1 生成阶段

```
LLM 输出 → parse_response() → Schema 验证 → DataPart 封装
```

```python
# 1. LLM 生成响应
response = await llm.generate(prompt)

# 2. 解析 A2UI JSON
parsed = parse_response(response)

# 3. Schema 校验
validator.validate(parsed.a2ui_json)

# 4. 封装为 DataPart
data_part = DataPart(
    data={
        "a2ui": True,
        "jsonSchema": schema,
        "instances": [a2ui_json]
    }
)
```

### 6.2 消费阶段

```
DataPart 解析 → Schema 验证 → 渲染 UI
```

```python
# 1. 解析 DataPart
if part.data.get("a2ui"):
    a2ui_json = part.data["instances"][0]
    
# 2. 校验 Schema
validator.validate(a2ui_json)

# 3. 渲染 UI
renderer.render(a2ui_json)
```

### 6.3 AgentCard 扩展声明

```python
def get_agent_card():
    return AgentCard(
        name="Restaurant Agent",
        capabilities=AgentCapabilities(
            streaming=True,
            extensions=[
                get_a2ui_agent_extension(
                    accepts_inline_catalogs=True,
                    supported_catalog_ids=[
                        "https://a2ui.org/specification/v0_10/basic_catalog.json"
                    ]
                )
            ]
        )
    )
```

---

## 七、Schema 校验与错误处理

### 7.1 校验流程

```python
async def validate_and_retry(a2ui_json, max_retries=2):
    for attempt in range(max_retries):
        try:
            validator.validate(a2ui_json)
            return a2ui_json
        except ValidationError as e:
            logger.error(f"Validation failed: {e}")
            if attempt == max_retries - 1:
                raise
            # 重试
            a2ui_json = await regenerate()
```

### 7.2 错误类型

| 错误类型 | 处理方式 |
|---------|---------|
| Schema 验证失败 | 重试或返回错误 |
| 组件不存在 | 回退到基础组件 |
| 数据绑定失败 | 使用默认值 |

---

## 八、示例对比

### 8.1 纯文本 vs A2UI 模式

| 特性 | 纯文本模式 | A2UI 模式 |
|------|-----------|----------|
| 输出格式 | 纯文本 | A2UI JSON |
| 交互方式 | 对话 | 交互式 UI |
| Schema 校验 | 无 | 有 |
| 重试机制 | 无 | 有 |

### 8.2 各 Agent 复杂度

| Agent | 复杂度 | 特点 |
|-------|-------|------|
| Component Gallery | ⭐ | 基础展示 |
| Contact Lookup | ⭐⭐ | 单 Agent + 工具 |
| Restaurant Finder | ⭐⭐ | 预订流程 |
| Orchestrator | ⭐⭐⭐⭐ | 多 Agent 调度 |
| Rizzcharts | ⭐⭐⭐ | 自定义 Catalog |

---

## 九、总结

### 9.1 架构模式

1. **LLM Agent 模式**：LLM 生成 A2UI JSON → 解析/校验 → 返回 DataPart
2. **调度器模式**：聚合子 Agent 能力 → 根据 surfaceId 路由
3. **MCP 模式**：通过工具协议交换 A2UI 消息

### 9.2 关键组件

| 组件 | 作用 |
|------|------|
| `A2uiSchemaManager` | 加载和管理 Schema |
| `parse_response` | 解析 LLM 响应 |
| `validator` | Schema 校验 |
| `DataPart` | A2UI 消息载体 |
| `get_a2ui_agent_extension` | AgentCard 扩展声明 |

### 9.3 适用场景

| 场景 | 推荐 Agent |
|------|-----------|
| 组件展示 | Component Gallery |
| 数据查询 + 列表 | Contact/Restaurant |
| 多业务线 | Orchestrator |
| 数据可视化 | Rizzcharts |
| 自定义协议 | MCP Server |
