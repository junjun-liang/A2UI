# A2UI 协议消息验证逻辑

本文档概述了在 `validateSchema` 函数中实现的验证规则。此验证器的目的是检查 JSON schema 本身不易表达的约束，如条件要求和引用完整性。

A2UI 消息是一个 JSON 对象，可以有一个 `surfaceId` 和以下属性之一，定义消息类型：`beginRendering`、`surfaceUpdate`、`dataModelUpdate` 或 `deleteSurface`。

## 通用属性

- **`surfaceId`**：一个可选字符串，标识消息适用的 UI surface。

## `BeginRendering` 消息规则

- **必需**：必须有 `root` 属性，即要渲染的根组件 ID。

## `SurfaceUpdate` 消息规则

### 1. 组件 ID 完整性

- **唯一性**：`components` 数组中的所有组件 `id` 必须唯一。
- **引用有效性**：任何引用组件 ID 的属性（如 `child`、`children`、`entryPointChild`、`contentChild`）必须指向 `components` 数组中实际存在的 ID。

### 2. 组件特定属性规则

对于 `components` 数组中的每个组件，适用以下规则：

- **通用**：
  - 组件必须有 `id` 和 `component` 对象。
  - `component` 对象必须恰好包含一个键，该键定义组件的类型（如 "Heading"、"Text"）。

- **Heading**：
  - **必需**：必须有 `text` 属性。
- **Text**：
  - **必需**：必须有 `text` 属性。
- **Image**：
  - **必需**：必须有 `url` 属性。
- **Video**：
  - **必需**：必须有 `url` 属性。
- **AudioPlayer**：
  - **必需**：必须有 `url` 属性。
- **TextField**：
  - **必需**：必须有 `label` 属性。
- **DateTimeInput**：
  - **必需**：必须有 `value` 属性。
- **MultipleChoice**：
  - **必需**：必须有 `selections` 属性。
- **Slider**：
  - **必需**：必须有 `value` 属性。
- **容器组件**（`Row`、`Column`、`List`）：
  - **必需**：必须有 `children` 属性。
  - `children` 对象必须包含 `explicitList` 或 `template` 中的_恰好一个_，不能同时包含两者。
- **Card**：
  - **必需**：必须有 `child` 属性。
- **Tabs**：
  - **必需**：必须有 `tabItems` 属性，且必须是数组。
  - `tabItems` 中的每个项必须有 `title` 和 `child`。
- **Modal**：
  - **必需**：必须同时有 `entryPointChild` 和 `contentChild` 属性。
- **Button**：
  - **必需**：必须有 `label` 和 `action` 属性。
- **CheckBox**：
  - **必需**：必须有 `label` 和 `value` 属性。
- **Divider**：
  - 无必需属性。

## `DataModelUpdate` 消息规则

- **必需**：`DataModelUpdate` 消息必须有 `contents` 属性。
- `path` 属性是可选的。
- 如果 `path` 不存在，`contents` 对象将替换整个数据模型。
- 如果 `path` 存在，`contents` 将设置到数据模型中的该位置。
- 除 `path` 和 `contents` 之外不允许其他属性。

## `DeleteSurface` 消息规则

- **必需**：必须有 `delete` 属性设置为 `true`。
- 不允许其他属性。
