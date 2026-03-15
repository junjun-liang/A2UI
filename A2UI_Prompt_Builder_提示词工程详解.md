# A2UI 提示词工程详解

## 文件位置
`samples/agent/adk/contact_lookup/prompt_builder.py`

---

## 核心目标

让 LLM 输出符合 A2UI 协议的 JSON 格式，用于在客户端渲染丰富的 UI 组件。

---

## A2UI 输出格式

LLM 必须将 JSON 包装在特定标签中：

```xml
<a2ui-json>
[{"beginRendering": {...}}, {"dataModelUpdate": {...}}]
</a2ui-json>
```

- **开始标签**: `<a2ui-json>`
- **结束标签**: `</a2ui-json>`
- **内容**: JSON 数组，包含渲染指令和数据更新

---

## 提示词设计策略

### 1. 角色定义 (ROLE_DESCRIPTION)

```python
ROLE_DESCRIPTION = (
    "You are a helpful contact lookup assistant. Your final output MUST be an A2UI JSON"
    " response."
)
```

**关键点**：
- 明确告知模型最终输出格式
- "MUST" 强调强制性要求
- 简洁明了

---

### 2. 工作流规则 (WORKFLOW_DESCRIPTION)

```python
WORKFLOW_DESCRIPTION = """
Buttons that represent the main action on a card or view (e.g., 'Follow', 'Email', 'Search') SHOULD include the `"primary": true` attribute.
"""
```

**作用**：
- 定义 UI 组件的最佳实践
- 告知主按钮需要 `primary: true`

---

### 3. 业务规则 (UI_DESCRIPTION) - 核心部分

```python
UI_DESCRIPTION = f"""
-   **For finding contacts (e.g., "Who is Alex Jordan?"):**
    a.  You MUST call the `get_contact_info` tool.
    b.  If the tool returns a **single contact**, you MUST use the `CONTACT_CARD_EXAMPLE` template. Populate the `dataModelUpdate.contents` with the contact's details (name, title, email, etc.).
    c.  If the tool returns **multiple contacts**, you MUST use the `CONTACT_LIST_EXAMPLE` template. Populate the `dataModelUpdate.contents` with the list of contacts for the "contacts" key.
    d.  If the tool returns an **empty list**, respond with text only and an empty JSON list: "I couldn't find anyone by that name.{A2UI_OPEN_TAG}[]{A2UI_CLOSE_TAG}"

-   **For handling a profile view (e.g., "WHO_IS: Alex Jordan..."):**
    a.  You MUST call the `get_contact_info` tool with the specific name.
    b.  This will return a single contact. You MUST use the `CONTACT_CARD_EXAMPLE` template.

-   **For handling actions (e.g., "follow_contact"):**
    a.  You MUST use the `FOLLOW_SUCCESS_EXAMPLE` template.
    b.  This will render a new card with a "Successfully Followed" message.
    c.  Respond with a text confirmation like "You are now following this contact." along with the JSON.
"""
```

**设计策略分析**：

| 策略 | 说明 | 示例 |
|------|------|------|
| **场景分支** | 根据不同场景指定不同模板 | 单个联系人 → 卡片, 多个 → 列表 |
| **强制动词** | MUST 调用工具 | 确保调用 get_contact_info |
| **明确模板** | 指定使用的示例模板 | CONTACT_CARD_EXAMPLE |
| **数据填充** | 告知填充哪些字段 | dataModelUpdate.contents |
| **空结果处理** | 明确空列表时的输出 | 文本 + 空 JSON 列表 |

---

### 4. 模板示例 (Examples)

系统会自动注入 examples 目录下的 JSON 示例：

**contact_card.json 模板结构**：

```json
[
  {
    "beginRendering": {
      "surfaceId": "contact-card",
      "root": "main_card"
    }
  },
  {
    "dataModelUpdate": {
      "surfaceId": "contact-card",
      "path": "/",
      "contents": [
        {"key": "name", "valueString": ""},
        {"key": "title", "valueString": ""},
        {"key": "email", "valueString": ""},
        ...
      ]
    }
  }
]
```

**关键组件**：

| 组件 | 用途 |
|------|------|
| `beginRendering` | 声明渲染表面和根组件 |
| `surfaceUpdate` | 定义 UI 组件树 |
| `dataModelUpdate` | 声明数据绑定路径 |
| `Button` | 交互按钮，支持 action |

---

## 提示词生成流程

```
┌─────────────────────────────────────────────────────────────────┐
│                  A2uiSchemaManager.generate_system_prompt()     │
├─────────────────────────────────────────────────────────────────┤
│  1. role_description                                             │
│     └── "You are a helpful contact lookup assistant..."         │
│                                                                     │
│  2. + Workflow Description                                       │
│     └── DEFAULT_WORKFLOW_RULES + 用户自定义                      │
│                                                                     │
│  3. + UI Description                                              │
│     └── 业务规则、场景分支、模板引用                              │
│                                                                     │
│  4. + JSON Schema (if include_schema=True)                      │
│     └── 完整的 A2UI Schema 定义                                  │
│                                                                     │
│  5. + Examples (if include_examples=True)                       │
│     └── 实际的 JSON 示例                                         │
└─────────────────────────────────────────────────────────────────┘
```

---

## 提示词组成详解

### 默认工作流规则 (DEFAULT_WORKFLOW_RULES)

来自 `a2ui/core/schema/constants.py`：

```
1. Your response MUST be a valid JSON list.
2. Each item in the JSON list represents a UI action:
   - "beginRendering": Start rendering on a surface
   - "surfaceUpdate": Update components on a surface
   - "dataModelUpdate": Update data on a surface
3. Each A2UI JSON block MUST be wrapped in <a2ui-json> and </a2ui-json> tags.
...
```

---

## 完整提示词示例

```markdown
You are a helpful contact lookup assistant. Your final output MUST be an A2UI JSON
response.

## Workflow Description:
[DEFAULT_WORKFLOW_RULES]
Buttons that represent the main action on a card or view... SHOULD include the `"primary": true` attribute.

## UI Description:
- **For finding contacts (e.g., "Who is Alex Jordan?"):**
    a. You MUST call the `get_contact_info` tool.
    b. If the tool returns a **single contact**, you MUST use the `CONTACT_CARD_EXAMPLE` template.
    ...

## A2UI JSON Schema:
[完整的 JSON Schema 定义]

## Examples:
[contact_card.json 完整内容]
[contact_list.json 完整内容]
[follow_success.json 完整内容]
```

---

## 非 UI 模式 (纯文本)

当 `use_ui=False` 时，使用 `get_text_prompt()`：

```python
def get_text_prompt() -> str:
  return """
    You are a helpful contact lookup assistant. Your final output MUST be a text response.

    To generate the response, you MUST follow these rules:
    1.  **For finding contacts:**
        a. You MUST call the `get_contact_info` tool. Extract the name and department from the user's query.
        b. After receiving the data, format the contact(s) as a clear, human-readable text response.
        ...
    """
```

**区别**：
- 不要求 A2UI JSON 输出
- 直接返回格式化文本
- 更简单，适用于不需要 UI 的场景

---

## 关键设计原则

### 1. 明确输出格式

```python
# 错误 ❌
"You can output JSON if you want"

# 正确 ✅
"Your final output MUST be an A2UI JSON response"
```

### 2. 场景分支

```python
# 根据工具返回结果选择不同模板
if 单个联系人 → CONTACT_CARD_EXAMPLE
if 多个联系人 → CONTACT_LIST_EXAMPLE
if 空结果 → 空 JSON 列表
```

### 3. 提供示例

```python
# 加载真实 JSON 示例
examples_str = self.load_examples(selected_catalog, validate=False)
```

### 4. 强制调用工具

```python
# 确保模型先调用工具获取数据
a. You MUST call the `get_contact_info` tool.
```

### 5. 字段映射说明

```python
# 明确告知数据填充位置
Populate the `dataModelUpdate.contents` with the contact's details
```

---

## 总结

| 组件 | 作用 |
|------|------|
| `ROLE_DESCRIPTION` | 声明输出格式要求 |
| `WORKFLOW_DESCRIPTION` | UI 组件规范 |
| `UI_DESCRIPTION` | 业务场景 + 模板映射 |
| `include_schema=True` | 注入完整 Schema |
| `include_examples=True` | 注入 JSON 示例 |

**核心思路**：
1. 明确告知必须输出 A2UI JSON
2. 根据场景指定具体模板
3. 提供实际 JSON 示例作为参考
4. 强制先调用工具获取数据
5. 验证失败时提供重试提示
