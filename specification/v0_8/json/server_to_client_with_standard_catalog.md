# server_to_client_with_standard_catalog.json

## 文件概述

A2UI 服务端到客户端消息的完整 JSON Schema 定义，内含标准目录（Standard Catalog）的全部组件定义。该文件是 A2UI 协议中最核心的规范文件，描述了服务端如何向客户端发送指令来动态构建和更新用户界面，同时将所有标准组件的类型定义内联其中。

## 数据结构

顶层为 JSON Schema 对象，包含以下四个互斥的动作属性（必须且只能包含其中一个）：

- `beginRendering` - 开始渲染一个 UI 表面
- `surfaceUpdate` - 更新表面的组件树
- `dataModelUpdate` - 更新数据模型
- `deleteSurface` - 删除表面

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| beginRendering | object | 否 | 通知客户端开始渲染一个表面，包含根组件和样式信息 |
| surfaceUpdate | object | 否 | 用新的组件集更新表面 |
| dataModelUpdate | object | 否 | 更新表面的数据模型 |
| deleteSurface | object | 否 | 通知客户端删除指定表面 |

### beginRendering 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | UI 表面的唯一标识符 |
| root | string | 是 | 根组件的 ID |
| styles | object | 否 | UI 样式信息，包含 font（主字体）和 primaryColor（主色调，十六进制格式如 #00BFFF） |

### surfaceUpdate 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | UI 表面的唯一标识符 |
| components | array | 是 | 组件列表，至少包含 1 个组件 |

### 组件（component）字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | string | 是 | 组件的唯一标识符 |
| weight | number | 否 | 组件在 Row/Column 中的相对权重，对应 CSS flex-grow |
| component | object | 是 | 组件包装对象，必须恰好包含一个组件类型键 |

### 支持的组件类型

| 组件名 | 说明 | 必填属性 |
|--------|------|----------|
| Text | 文本显示组件 | text |
| Image | 图片显示组件 | url |
| Icon | 图标显示组件 | name |
| Video | 视频显示组件 | url |
| AudioPlayer | 音频播放组件 | url |
| Row | 水平布局容器 | children |
| Column | 垂直布局容器 | children |
| List | 列表容器 | children |
| Card | 卡片容器 | child |
| Tabs | 标签页组件 | tabItems |
| Divider | 分隔线组件 | - |
| Modal | 模态框组件 | entryPointChild, contentChild |
| Button | 按钮组件 | child, action |
| CheckBox | 复选框组件 | label, value |
| TextField | 文本输入组件 | label |
| DateTimeInput | 日期时间输入组件 | value |
| MultipleChoice | 多选组件 | selections, options |
| Slider | 滑块组件 | value |

### dataModelUpdate 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | 数据模型更新所属的表面 ID |
| path | string | 否 | 数据模型中的路径（如 /user/name），省略则替换整个模型 |
| contents | array | 是 | 数据条目数组，每个条目包含 key 和一个 value* 属性 |

### deleteSurface 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | 要删除的表面 ID |

## 示例

```json
{
  "beginRendering": {
    "surfaceId": "main-screen",
    "root": "root",
    "styles": {
      "font": "Roboto",
      "primaryColor": "#00BFFF"
    }
  }
}
```

```json
{
  "surfaceUpdate": {
    "surfaceId": "main-screen",
    "components": [
      {
        "id": "root",
        "component": {
          "Text": {
            "text": { "literalString": "Hello, A2UI!" },
            "usageHint": "h1"
          }
        }
      }
    ]
  }
}
```
