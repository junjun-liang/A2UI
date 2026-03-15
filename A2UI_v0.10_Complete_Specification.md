# A2UI v0.10 完整规范详解

> 本文件综合解读 `/specification/v0_10/json/` 目录下的所有Schema定义文件

---

## 文件概览

| 文件 | 作用 |
|------|------|
| `server_to_client.json` | 服务器到客户端消息格式 |
| `client_to_server.json` | 客户端到服务器消息格式 |
| `basic_catalog.json` | 基础组件和函数定义 |
| `common_types.json` | 通用类型定义 |
| `client_capabilities.json` | 客户端能力声明 |
| `server_capabilities.json` | 服务器能力声明 |
| `client_data_model.json` | 客户端数据模型 |

---

## 1. 消息操作 (Message Operations)

A2UI v0.10 定义了 **5种核心操作**：

| 操作 | 说明 |
|------|------|
| `createSurface` | 创建新的UI表面 |
| `updateComponents` | 更新组件 |
| `updateDataModel` | 更新数据模型 |
| `deleteSurface` | 删除表面 |
| `callFunction` | 🆕 调用函数 |

### 消息格式

```json
{
  "version": "v0.10",
  "操作类型": { ... }
}
```

---

## 2. 基础组件 (Basic Components)

v0.10 定义了 **18种基础组件**：

### 展示组件 (Display Components)

| 组件 | 说明 | 关键属性 |
|------|------|---------|
| **Text** | 文本显示 | `text`, `variant` (h1-h5, caption, body) |
| **Image** | 图片 | `url`, `fit`, `variant` |
| **Icon** | 图标 | `name` (50+图标) |
| **Video** | 视频 | `url` |
| **AudioPlayer** | 音频播放器 | `url`, `description` |
| **Divider** | 分割线 | `axis` (horizontal/vertical) |

### 布局组件 (Layout Components)

| 组件 | 说明 | 关键属性 |
|------|------|---------|
| **Row** | 水平布局 | `children`, `justify`, `align` |
| **Column** | 垂直布局 | `children`, `justify`, `align` |
| **List** | 列表 | `children`, `direction`, `align` |
| **Card** | 卡片 | `child` |
| **Tabs** | 标签页 | `tabs` (数组) |
| **Modal** | 模态框 | `trigger`, `content` |

### 交互组件 (Interactive Components)

| 组件 | 说明 | 关键属性 |
|------|------|---------|
| **Button** | 按钮 | `child`, `variant`, `action` |
| **TextField** | 文本输入 | `label`, `value`, `variant` |
| **CheckBox** | 复选框 | `label`, `value` |
| **ChoicePicker** | 选择器 | `options`, `value`, `variant` |
| **Slider** | 滑块 | `label`, `value`, `min`, `max` |
| **DateTimeInput** | 日期时间输入 | `value`, `enableDate`, `enableTime` |

---

## 3. 组件属性详解

### 3.1 Text 组件

```json
{
  "component": "Text",
  "text": { "literalString": "Hello World" },
  "variant": "h1"
}
```

**variant可选值**: `h1`, `h2`, `h3`, `h4`, `h5`, `caption`, `body`

### 3.2 Image 组件

```json
{
  "component": "Image",
  "url": { "literalString": "https://example.com/image.jpg" },
  "fit": "cover",
  "variant": "mediumFeature"
}
```

**fit可选值**: `contain`, `cover`, `fill`, `none`, `scaleDown`  
**variant可选值**: `icon`, `avatar`, `smallFeature`, `mediumFeature`, `largeFeature`, `header`

### 3.3 Row/Column 布局

```json
{
  "component": "Row",
  "children": ["child1", "child2"],
  "justify": "spaceBetween",
  "align": "center"
}
```

**justify (Row水平/Column垂直)**: `center`, `end`, `spaceAround`, `spaceBetween`, `spaceEvenly`, `start`, `stretch`  
**align**: `start`, `center`, `end`, `stretch`

### 3.4 Button 组件

```json
{
  "component": "Button",
  "child": "button_text",
  "variant": "primary",
  "action": {
    "event": {
      "name": "submit_form",
      "context": { "key": "value" }
    }
  }
}
```

**variant可选值**: `default`, `primary`, `borderless`

### 3.5 TextField 组件

```json
{
  "component": "TextField",
  "label": { "literalString": "姓名" },
  "value": { "path": "/form/name" },
  "variant": "shortText"
}
```

**variant可选值**: `longText`, `number`, `shortText`, `obscured`

### 3.6 ChoicePicker 组件

```json
{
  "component": "ChoicePicker",
  "label": { "literalString": "选择菜系" },
  "options": [
    { "label": { "literalString": "川菜" }, "value": "sichuan" },
    { "label": { "literalString": "粤菜" }, "value": "cantonese" }
  ],
  "value": { "literalArray": ["sichuan"] },
  "variant": "multipleSelection",
  "displayStyle": "chips",
  "filterable": true
}
```

---

## 4. 函数系统 (Functions)

v0.10 定义了 **14种内置函数**：

### 4.1 验证函数

| 函数 | 说明 | 参数 |
|------|------|------|
| `required` | 检查值非空 | `value` |
| `regex` | 正则验证 | `value`, `pattern` |
| `length` | 长度验证 | `value`, `min`, `max` |
| `numeric` | 数值范围 | `value`, `min`, `max` |
| `email` | 邮箱验证 | `value` |

### 4.2 格式化函数

| 函数 | 说明 | 参数 |
|------|------|------|
| `formatString` | 字符串格式化/插值 | `value` |
| `formatNumber` | 数字格式化 | `value`, `decimals`, `grouping` |
| `formatCurrency` | 货币格式化 | `value`, `currency`, `decimals` |
| `formatDate` | 日期格式化 | `value`, `format` |
| `pluralize` | 复数形式 | `value`, `zero`, `one`, `other` |

### 4.3 动作函数

| 函数 | 说明 | 参数 |
|------|------|------|
| `openUrl` | 打开URL | `url` |

### 4.4 逻辑函数

| 函数 | 说明 | 参数 |
|------|------|------|
| `and` | 逻辑与 | `values[]` |
| `or` | 逻辑或 | `values[]` |
| `not` | 逻辑非 | `value` |

---

## 5. 通用类型 (Common Types)

### 5.1 DynamicString - 动态字符串

支持字面值或数据绑定：

```json
{ "literalString": "Hello" }
{ "path": "/user/name" }
{ "call": { "call": "formatString", "args": { "value": "Hello ${/name}" } } }
```

### 5.2 ComponentId - 组件引用

```json
{ "literalString": "button_submit" }
```

### 5.3 ChildList - 子组件列表

```json
// 固定列表
{ "explicitList": ["child1", "child2"] }

// 动态模板
{
  "template": {
    "componentId": "item_template",
    "dataBinding": "/items"
  }
}
```

### 5.4 Action - 操作定义

```json
{
  "event": {
    "name": "submit_form",
    "context": {
      "userId": { "path": "/user/id" }
    }
  }
}
```

### 5.5 FunctionCall - 函数调用

```json
{
  "call": "formatString",
  "args": {
    "value": "Hello ${/user/name}"
  }
}
```

---

## 6. 主题系统 (Theme)

### Theme 属性

```json
{
  "primaryColor": "#FF5722",
  "iconUrl": "https://example.com/logo.png",
  "agentDisplayName": "餐厅助手"
}
```

| 属性 | 类型 | 说明 |
|------|------|------|
| `primaryColor` | string | 主色调（十六进制） |
| `iconUrl` | uri | 代理图标URL |
| `agentDisplayName` | string | 代理显示名称 |

---

## 7. 客户端到服务器消息

### 7.1 action - 用户操作

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit_form",
    "surfaceId": "contact_form",
    "sourceComponentId": "submit_button",
    "timestamp": "2025-03-15T10:30:00Z",
    "context": {
      "email": "user@example.com"
    }
  }
}
```

### 7.2 error - 错误报告

```json
{
  "version": "v0.10",
  "error": {
    "code": "VALIDATION_FAILED",
    "surfaceId": "form_surface",
    "path": "/components/0/text",
    "message": "Expected string, got integer"
  }
}
```

---

## 8. 能力声明 (Capabilities)

### 8.1 客户端能力

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_10/basic_catalog.json"
  ],
  "inlineCatalogs": [
    { "catalogId": "custom:widget", "components": { ... } }
  ]
}
```

### 8.2 服务器能力

```json
{
  "supportedCatalogIds": [
    "https://a2ui.org/specification/v0_10/basic_catalog.json"
  ],
  "acceptsInlineCatalogs": true
}
```

---

## 9. 数据模型 (Data Model)

### 9.1 客户端数据模型

```json
{
  "surfaces": {
    "main_surface": {
      "user": {
        "name": "张三",
        "age": 30
      }
    }
  }
}
```

### 9.2 数据绑定路径

```json
// 绝对路径
{ "path": "/user/name" }

// 相对路径 (在模板迭代中使用)
{ "path": "name" }

// 模板字符串
{ "call": { "call": "formatString", "args": { "value": "Hello ${/user/name}!" } } }
```

---

## 10. 完整示例

### 10.1 创建表面

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "restaurant_booking",
    "catalogId": "https://a2ui.org/specification/v0_10/basic_catalog.json",
    "theme": {
      "primaryColor": "#FF5722"
    },
    "sendDataModel": true
  }
}
```

### 10.2 更新组件

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "restaurant_booking",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["header", "form", "submit_btn"]
      },
      {
        "id": "header",
        "component": "Text",
        "text": { "literalString": "餐厅预订" },
        "variant": "h1"
      },
      {
        "id": "form",
        "component": "Column",
        "children": ["name_field", "date_field", "guests_field"]
      },
      {
        "id": "name_field",
        "component": "TextField",
        "label": { "literalString": "姓名" },
        "value": { "path": "/booking/name" },
        "variant": "shortText"
      },
      {
        "id": "date_field",
        "component": "DateTimeInput",
        "value": { "path": "/booking/date" },
        "enableDate": true,
        "enableTime": true,
        "label": { "literalString": "预订时间" }
      },
      {
        "id": "guests_field",
        "component": "Slider",
        "label": { "literalString": "用餐人数" },
        "value": { "path": "/booking/guests" },
        "min": 1,
        "max": 10
      },
      {
        "id": "submit_btn",
        "component": "Button",
        "child": "btn_text",
        "variant": "primary",
        "action": {
          "event": {
            "name": "submit_booking",
            "context": {}
          }
        }
      },
      {
        "id": "btn_text",
        "component": "Text",
        "text": { "literalString": "提交预订" }
      }
    ]
  }
}
```

### 10.3 更新数据模型

```json
{
  "version": "v0.10",
  "updateDataModel": {
    "surfaceId": "restaurant_booking",
    "path": "/booking",
    "value": {
      "name": "",
      "date": "",
      "guests": 2
    }
  }
}
```

### 10.4 用户交互

```json
{
  "version": "v0.10",
  "action": {
    "name": "submit_booking",
    "surfaceId": "restaurant_booking",
    "sourceComponentId": "submit_btn",
    "timestamp": "2025-03-15T18:30:00Z",
    "context": {}
  }
}
```

### 10.5 删除表面

```json
{
  "version": "v0.10",
  "deleteSurface": {
    "surfaceId": "restaurant_booking"
  }
}
```

---

## 11. Schema 文件结构

### 11.1 文件依赖关系

```
server_to_client.json
├── common_types.json (引用)
└── catalog.json (引用)
    └── basic_catalog.json

client_to_server.json
├── common_types.json (引用)
└── ...
```

### 11.2 验证流程

1. **消息验证** - 验证 `server_to_client.json` 或 `client_to_server.json`
2. **组件验证** - 验证组件符合 `basic_catalog.json` 中的定义
3. **类型验证** - 验证类型符合 `common_types.json` 中的定义

---

## 12. 与其他版本的差异

### v0.8 → v0.9 主要变化

- 添加 `version` 字段
- 引入函数系统
- 引入 Two-way Binding
- Schema 模块化

### v0.9 → v0.10 主要变化

- 新增 `callFunction` 消息类型
- 组件定义使用 `$ref` 引用
- 完善 `ChoicePicker` 组件
- 增强日期时间输入

---

## 总结

A2UI v0.10 是一个功能完整的UI协议，提供了：

1. **18种基础组件** - 覆盖展示、布局、交互场景
2. **14种内置函数** - 验证、格式化、逻辑处理
3. **灵活的数据绑定** - 支持绝对路径、相对路径、模板字符串
4. **双向数据流** - 客户端到服务器的事件机制
5. **可扩展的目录系统** - 支持自定义组件和函数

该协议可广泛应用于AI Agent驱动的用户界面场景。
