# A2UI 协议演进指南：v0.8.1 到 v0.9

本文档作为 A2UI 版本 0.8.1 和版本 0.9 之间变更的综合指南。它详细说明了理念、架构和实现方面的转变，为利益相关者和在版本之间迁移的开发者提供参考。

## 1. 概要

版本 0.9 代表了从"结构化输出优先"到"提示优先"的根本性理念转变。

- **v0.8.1** 设计为首先由 LLM 使用结构化输出生成，针对支持严格 JSON 模式或函数调用（这也是一种结构化输出形式）的 LLM 进行了优化。它依赖于深层嵌套和特定的包装结构，这些结构可以在有限的功能模式中定义，但常常让 LLM 在生成时感到困惑。
- **v0.9** 设计为**直接嵌入 LLM 的系统提示中**。模式被重构为更易读且对模型来说更"节省 token"的形式。它优先考虑 LLM 自然擅长的模式（如标准 JSON 对象映射），而非严格的结构化输出友好结构（如键值对数组）。

### 摘要表

| 特性                  | v0.8.1                                   | v0.9                                                     |
| :----------------------- | :--------------------------------------- | :------------------------------------------------------- |
| **理念**           | 结构化输出 / 函数调用     | 提示优先 / 上下文内模式                         |
| **消息类型**        | `beginRendering`、`surfaceUpdate` 等   | `createSurface`、`updateComponents` 等                 |
| **Surface 创建**     | 显式 `beginRendering`                | 显式 `createSurface`                                 |
| **组件类型**       | 基于键的包装器 (`{"Text": ...}`)      | 基于属性的判别器 (`"component": "Text"`)     |
| **数据模型更新**    | 键值对数组                                     | 标准 JSON 对象                                   |
| **数据绑定**         | `dataBinding` / `literalString`          | `path` / 原生 JSON 类型                               |
| **按钮上下文**       | 键值对数组                                 | 标准 JSON 对象                                   |
| **按钮变体**       | 布尔值 (`primary: true`)                | 枚举 (`variant: "primary"`)                              |
| **目录**              | 独立的组件和函数目录 | 统一目录 (`basic_catalog.json`)                |
| **辅助规则**      | 无                                      | `basic_catalog_rules.txt`                             |
| **验证**           | 基础模式                             | 严格的 `ValidationFailed` 反馈循环                  |
| **数据同步** | 隐式                                 | 显式客户端->服务器数据同步 (`sendDataModel`) |

## 2. 架构与模式变更

### 2.1. 模块化模式架构

**v0.8.1：**

- 具有单体倾向。`server_to_client.json` 通常包含深层定义或依赖难以分解的复杂 `oneOf` 结构。
- `basic_catalog_definition.json` 存在，但通常是隐式耦合的。

**v0.9：**

- **模块化**：模式严格分为：
  - `common_types.json`：可复用原语（ID、路径）和逻辑/表达式类型。
  - `server_to_client.json`：定义消息类型的"信封"。
  - `basic_catalog.json`：UI 组件和函数的统一目录。
- **可替换目录**：`server_to_client.json` 现在使用对 `catalog.json` 的相对引用作为占位符。这允许开发者在验证期间将 `catalog.json` 别名为 `basic_catalog.json`（或任何自定义目录），从而无需修改核心信封模式即可使用自定义组件集。
- **统一化**：组件和函数现在是同一目录对象的一部分，简化了能力协商和内联定义。

### 2.2. 严格消息类型

**v0.8.1：**

- 消息是对象，其中 `surfaceUpdate` 等属性是可选键。
- 验证通常依赖 "minProperties: 1" 约束。

**v0.9：**

- 在 `server_to_client.json` 中使用顶层 **`oneOf`** 约束。
- **原因**：这是向 LLM 表达模式的更自然方式，也更容易让 LLM 推理。对开发者来说也是更自然的阅读形式。

### 2.3. 辅助规则文件

**v0.9：**

- **新产物**：`basic_catalog_rules.txt`。
- **目的**：包含使用目录模式规则的纯文本提示片段（例如，"必须为 Button 提供 'action'"）。
- **用法**：设计为与目录模式一起包含在系统提示中。
- **原因**：某些约束（如条件要求或特定属性组合）在 JSON 模式中难以或冗长地表达，但在 LLM 的自然语言规则中却很容易表达，并且可以与目录模式打包在一起，便于为特定目录自定义提示。

## 3. 协议生命周期变更

### 3.1. `beginRendering` 被 `createSurface` 替换

**v0.8.1 (`beginRendering`)：**

- **显式信号**：服务器发送 `beginRendering` 消息告诉客户端"我已完成初始组件批次的发送，你现在可以绘制了。"
- **根定义**：根组件 ID 在此消息中定义。
- **样式信息**：消息包含 Surface 的样式信息。

**v0.9 (`createSurface`)：**

- **替换**：`beginRendering` 被 **替换** 为 `createSurface`。
- **目的**：`createSurface` 通知客户端创建新的 Surface 并准备渲染。
- **主题信息**：`createSurface` 包含 `theme` 属性来指定主题参数（如 `primaryColor`）。这替换了 v0.8 中的 `styles` 属性。
- **根规则**：规则是："必须有且仅有一个 `ComponentId` 为 'root' 的组件。" `beginRendering` 具有的 "root" 属性已被移除。客户端预期在拥有带根组件的有效树后立即渲染。
- **新要求**：`createSurface` 现在需要 **`catalogId`**（URI）来显式声明正在使用的统一目录（组件和函数）。

**示例：**

**v0.8.1 (`beginRendering`)**：

```json
{
  "beginRendering": {
    "surfaceId": "user_profile_card",
    "root": "root",
    "styles": {
      "primaryColor": "#007bff"
    }
  }
}
```

**v0.9 (`createSurface`)**：

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "user_profile_card",
    "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json",
    "theme": {
      "primaryColor": "#007bff"
    }
  }
}
```

## 4. 消息结构对比

### 4.1. 组件更新

**v0.8.1 (`surfaceUpdate`)**：

- 组件被包装在一个对象中，其中**键**是组件类型。
- **结构**：`{ "id": "...", "component": { "Text": { "text": "..." } } }`

**v0.9 (`updateComponents`)**：

- **重命名**：`surfaceUpdate` -> `updateComponents`。
- **重构**：组件使用带有常量判别器属性 `component` 的扁平结构。
- **结构**：`{ "id": "...", "component": "Text", "text": "..." }`
- **原因**：这种带有判别器字段（`component: "Text"`）的"扁平"结构比动态键（`"Text": {...}`）更容易让 LLM 一致地生成。它还简化了许多 JSON 解析器中的多态性。

指定未知的 surfaceId 将导致错误。建议客户端在内部实现命名空间方案，以防止不同的 Agent 创建具有相同 ID 的 Surface，并防止 Agent 修改其他 Agent 创建的 Surface。

#### 并排示例

**v0.8.1：**

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "title",
        "component": {
          "Text": { "text": { "literalString": "Hello" } }
        }
      }
    ]
  }
}
```

**v0.9：**

```json
{
  "version": "v0.9",
  "updateComponents": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["title"]
      },
      {
        "id": "title",
        "component": "Text",
        "text": "Hello"
      }
    ]
  }
}
```

### 4.2. 数据模型更新

**v0.8.1 (`dataModelUpdate`)**：

- **邻接表**：`contents` 属性是键值对对象的**数组**。
- **类型化值**：每个条目需要显式类型，如 `valueString`、`valueNumber`、`valueBoolean`。
- **结构**：`[{ "key": "name", "valueString": "Alice" }]`

**v0.9 (`updateDataModel`)**：

- **重命名**：`dataModelUpdate` -> `updateDataModel`。
- **标准 JSON**：`value` 属性现在是标准的 **JSON 对象**。
- **简化**：系统依赖 upsert 语义，因此客户端将在指定路径创建或更新数据模型，如果值为 null 则移除。
- **结构**：`{ "name": "Alice" }`
- **原因**：LLM 经过训练可以生成 JSON 对象。强制它们生成映射的"邻接表"表示既低效又容易出错。

## 5. 数据绑定与状态

### 5.1. `path` 的标准化

**v0.8.1：**

- 在 `childrenProperty` 模板中使用 `dataBinding`。
- 在 `BoundValue` 对象中使用 `path`。
- 术语不一致。

**v0.9：**

- **统一**：所有内容现在都是 **`path`**。
- **原因**：减少 LLM 的认知负担。"Path" 始终意味着"指向数据的 JSON Pointer"。

### 5.2. 简化绑定值

**v0.8.1：**

- `{ "literalString": "foo" }` 或 `{ "path": "/foo" }`。
- 键中的显式类型（`literalNumber`、`literalBoolean`）。

**v0.9：**

- **隐式类型**：`DynamicString`、`DynamicNumber` 等在 `common_types.json` 中定义。
- **结构**：模式允许 `string` 或 `{ "path": "..." }`。
- **原因**：更自然的 JSON。`{ "text": "Hello" }` 是有效的。`{ "value": { "path": "/msg" } }` 是有效的。不需要 `{ "text": { "literalString": "Hello" } }`。

### 5.3. 字符串插值

**v0.8.1：**

- **严格信封**：静态文本和数据模型引用必须是独立的或包装在显式对象中。在协议层面，在单个字符串中混合字面文本和动态值没有官方支持，除非使用自定义逻辑。
- **结构**：`{ "text": "static" }` 或 `{ "text": { "path": "/var" } }`。

**v0.9：**

- **字符串格式化**：引入了 `formatString` 函数，支持 `${expression}` 语法进行插值。
- **统一表达式语言**：允许在格式字符串中直接嵌入 JSON Pointer 路径（绝对和相对）以及客户端函数调用。
- **嵌套**：支持表达式的递归嵌套（例如 `${formatDate(value: ${/timestamp}, format: 'yyyy-MM-dd')}`）。
- **限制**：字符串插值 `${...}` **仅**在 `formatString` 函数内支持。它不在一般字符串属性中支持，以严格分离数据绑定定义和静态内容。
- **原因**：提高了复杂字符串的可读性。模型无需生成复杂的嵌套 JSON 对象（如链式连接）来组合字符串和数据，而是可以在 `formatString` 函数中编写看起来自然的模板字面量。

### 5.4. 数据同步

**v0.8.1：**

- 数据同步是隐式的，依赖临时机制。

**v0.9：**

- **显式客户端->服务器数据模型同步**：`createSurface` 引入了 `sendDataModel`（布尔值）。
- **单路径更新**：服务器通过 `updateDataModel` 使用简单的 `path`/`value` 对推送更新。
- **客户端->服务器数据模型同步**：当 `sendDataModel` 为 true 时，客户端在每条 A2A 消息元数据中包含完整的数据模型。

## 6. 组件特定变更

### 6.1. 按钮上下文

**v0.8.1：**

- **键值对数组**：`context: [{ "key": "id", "value": { "literalString": "123" } }]`
- **原因**：易于解析，难以生成。

**v0.9：**

- **标准映射**：`context: { "id": "123" }`
- **原因**：Token 效率。LLM 原生理解 JSON 对象作为映射。

### 6.2. 按钮变体

**v0.8.1：**

- **布尔值**：`primary: true` 或 `primary: false`。
- **有限**：仅显式支持两种样式。

**v0.9：**

- **枚举**：`variant: "primary"` 或 `variant: "borderless"`。
- **原因**：更灵活，与使用 `variant` 作为样式提示的其他组件（如 `Text` 和 `Image`）保持一致。'borderless' 提供了一种标准方式来表示可点击的文本或图标，而无需按钮式框架。

### 6.3. TextField

**v0.8.1：**

- 属性：`textFieldType`（例如 "email"、"password"）。
- 验证：`validationRegexp`。

**v0.9：**

- 属性：**`variant`**。
- 验证：**`checks`**（函数调用的通用列表）。
- **原因**：与已经使用 `variant` 的 `Text` 和 `Image` 组件保持一致。验证现在更灵活和可复用。此外，`text` 被重命名为 **`value`** 以匹配其他输入组件。

### 6.4. ChoicePicker（对比 MultipleChoice）

**v0.8.1：**

- 组件：**`MultipleChoice`**。
- 属性：`selections`（类型化包装器）、`maxAllowedSelections`（整数）。

**v0.9：**

- 组件：**`ChoicePicker`**。
- 属性：**`value`**（数组）、**`variant`**（枚举：`multipleSelection`、`mutuallyExclusive`）。`maxAllowedSelections` 属性已被移除。
- **原因**：`ChoicePicker` 是一个更通用的名称，涵盖了单选按钮（互斥）和复选框（多选）。`variant` 控制行为，简化了组件的表面面积。

### 6.5. Slider

**v0.8.1：**

- 属性：`minValue`、`maxValue`。

**v0.9：**

- 属性：**`min`**、**`max`**。
- **原因**：标准化为更短、更常见的属性名。

## 7. 错误处理

**v0.9** 在 `client_to_server.json` 中引入了严格的 **`ValidationFailed`** 错误格式。

- **目的**：允许"提示-生成-验证"循环有效工作。
- **机制**：如果 LLM 生成无效 JSON，系统发回结构化错误：

  ```json
  {
    "error": {
      "code": "VALIDATION_FAILED",
      "surfaceId": "...",
      "path": "/components/0/text",
      "message": "Expected string, got number"
    }
  }
  ```

- **结果**：LLM 看到此错误并可以在下一轮中"自我纠正"。

## 8. 属性重命名摘要（迁移快速参考）

对于从早期版本迁移的开发者，以下是属性重命名的快速参考：

| 组件          | 旧名称           | 新名称    |
| :----------------- | :----------------- | :---------- |
| **Row / Column**   | `distribution`     | `justify`   |
| **Row / Column**   | `alignment`        | `align`     |
| **Modal**          | `entryPointChild`  | `trigger`   |
| **Modal**          | `contentChild`     | `content`   |
| **Tabs**           | `tabItems`         | `tabs`      |
| **TextField**      | `text`             | `value`     |
| **Many**           | `usageHint`        | `variant`   |
| **Client Message** | `userAction`       | `action`    |
| **Common Type**    | `childrenProperty` | `ChildList` |