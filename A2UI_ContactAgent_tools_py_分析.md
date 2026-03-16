# ContactAgent Tools 模块分析

## 文件位置
`samples/agent/adk/contact_lookup/tools.py`

---

## 核心功能

`get_contact_info` 是一个 ADK 工具函数，用于根据姓名（和可选部门）查询联系人信息。

---

## 函数定义

```python
def get_contact_info(
    name: str,
    tool_context: ToolContext,
    department: str = ""
) -> str:
```

### 参数说明

| 参数 | 类型 | 说明 |
|------|------|------|
| `name` | str | 要搜索的联系人姓名（必填） |
| `tool_context` | ToolContext | ADK 工具上下文，包含 state 等 |
| `department` | str | 可选的部门筛选条件 |

### 返回值

返回 JSON 字符串，包含匹配的联系人数组。

---

## 业务流程图

```
┌─────────────────────────────────────────────────────────────────┐
│                    get_contact_info()                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. 接收参数                                                    │
│     └── name, tool_context, department                          │
│                                                                 │
│  2. 读取联系人数据                                              │
│     └── 从 contact_data.json 文件加载                           │
│                                                                 │
│  3. 动态替换 base_url                                           │
│     └── tool_context.state.get("base_url")                     │
│         替换 JSON 中的 "http://localhost:10002"                 │
│                                                                 │
│  4. 按姓名过滤                                                  │
│     └── name_lower in contact["name"].lower()                   │
│                                                                 │
│  5. 按部门过滤 (如果提供)                                        │
│     └── department_lower in contact["department"].lower()      │
│                                                                 │
│  6. 返回 JSON 结果                                              │
│     └── json.dumps(results)                                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 详细代码逻辑

### Step 1: 读取数据文件

```python
script_dir = os.path.dirname(__file__)
file_path = os.path.join(script_dir, "contact_data.json")
with open(file_path) as f:
    contact_data_str = f.read()
```

- 获取当前文件所在目录
- 读取 `contact_data.json` 文件内容

### Step 2: 动态替换 base_url

```python
if base_url := tool_context.state.get("base_url"):
    contact_data_str = contact_data_str.replace(
        "http://localhost:10002", 
        base_url
    )
```

- 从 tool_context.state 获取 base_url
- 替换 JSON 中的默认 URL 为实际值
- 用于处理不同部署环境的 URL

### Step 3: 解析 JSON

```python
all_contacts = json.loads(contact_data_str)
```

- 将文件内容解析为 Python 列表

### Step 4: 姓名过滤

```python
name_lower = name.lower()
results = [
    contact for contact in all_contacts 
    if name_lower in contact["name"].lower()
]
```

- 将搜索词转为小写
- 模糊匹配（包含关系）
- 不区分大小写

### Step 5: 部门过滤（可选）

```python
dept_lower = department.lower() if department else ""
if dept_lower:
    results = [
        contact for contact in results 
        if dept_lower in contact["department"].lower()
    ]
```

- 如果提供了 department 参数
- 进一步过滤结果

### Step 6: 返回结果

```python
return json.dumps(results)
```

- 返回 JSON 字符串格式的结果
- 方便 LLM 解析

---

## 数据结构

### 输入: contact_data.json

```json
[
  {
    "name": "David Chen",
    "title": "Engineering Manager",
    "department": "Engineering",
    "email": "david.chen@example.com",
    "location": "Building A",
    "mobile": "+1-555-0101",
    "calendar": "https://calendar.example.com/david.chen",
    "imageUrl": "https://example.com/images/david.jpg"
  },
  ...
]
```

### 输出: JSON 字符串

```json
[
  {
    "name": "David Chen",
    "title": "Engineering Manager",
    "department": "Engineering",
    "email": "david.chen@example.com",
    ...
  }
]
```

---

## 与 Agent 的集成

### 在 agent.py 中绑定工具

```python
def _build_agent(self, use_ui: bool) -> LlmAgent:
    return LlmAgent(
        model=LiteLlm(model=LITELLM_MODEL),
        name="contact_agent",
        instruction=instruction,
        tools=[get_contact_info],  # 绑定工具
    )
```

### LLM 调用流程

```
┌─────────────────────────────────────────────────────────────────┐
│                         LLM                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  用户: "Who is David Chen?"                                     │
│                                                                 │
│  1. LLM 分析意图 → 需要调用 get_contact_info                     │
│                                                                 │
│  2. ADK 自动调用工具                                            │
│     └── get_contact_info(name="David Chen", ...)                │
│                                                                 │
│  3. 工具返回 JSON 字符串                                        │
│     └── '[{"name": "David Chen", ...}]'                         │
│                                                                 │
│  4. LLM 读取工具返回                                            │
│                                                                 │
│  5. LLM 生成响应                                                │
│     └── 使用 A2UI JSON 格式返回联系人卡片                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 错误处理

| 错误类型 | 处理方式 |
|----------|----------|
| `FileNotFoundError` | 记录错误日志，返回空列表 |
| `json.JSONDecodeError` | 记录错误日志，返回空列表 |

```python
try:
    # ... 业务逻辑
except FileNotFoundError:
    logger.error(f"  - Error: contact_data.json not found at {file_path}")
except json.JSONDecodeError:
    logger.error(f"  - Error: Failed to decode JSON from {file_path}")

# 没有显式 return，默认返回 None 或空结果
```

---

## 搜索示例

| 用户输入 | name 参数 | department 参数 | 匹配结果 |
|----------|-----------|----------------|----------|
| "Who is David?" | "David" | "" | 所有名字包含 "david" 的联系人 |
| "Find Sarah from Engineering" | "Sarah" | "Engineering" | 名字含 "sarah" 且部门是 "Engineering" |
| "Show me marketing team" | "marketing" | "" | 名字含 "marketing" 的联系人 |

---

## 总结

| 特性 | 说明 |
|------|------|
| **搜索方式** | 模糊匹配（包含关系） |
| **大小写** | 不区分大小写 |
| **部门过滤** | 可选，支持联合过滤 |
| **返回格式** | JSON 字符串 |
| **错误处理** | 返回空列表 |
| **URL 替换** | 支持动态 base_url |
