# server_to_client.json

## 文件概述

A2UI 服务端到客户端消息的 JSON Schema 定义（不含内联组件定义）。该文件与 `server_to_client_with_standard_catalog.json` 的区别在于：组件类型定义不内联，而是通过 `catalogId` 引用外部组件目录，`component` 属性使用 `additionalProperties: true` 允许任意组件类型。适用于使用自定义目录或分离式架构的场景。

## 数据结构

顶层为 JSON Schema 对象，包含四个互斥的动作属性（必须且只能包含其中一个）：

- `beginRendering` - 开始渲染一个 UI 表面
- `surfaceUpdate` - 更新表面的组件树
- `dataModelUpdate` - 更新数据模型
- `deleteSurface` - 删除表面

## 字段说明

### 顶层字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| beginRendering | object | 否 | 通知客户端开始渲染一个表面 |
| surfaceUpdate | object | 否 | 用新的组件集更新表面 |
| dataModelUpdate | object | 否 | 更新表面的数据模型 |
| deleteSurface | object | 否 | 通知客户端删除指定表面 |

### beginRendering 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | UI 表面的唯一标识符 |
| catalogId | string | 否 | 组件目录标识符，省略时默认使用标准目录 |
| root | string | 是 | 根组件的 ID |
| styles | object | 否 | UI 样式信息，允许任意额外属性（additionalProperties: true） |

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
| component | object | 是 | 组件包装对象，必须恰好包含一个组件类型键名。使用 additionalProperties: true 允许引用任意目录中定义的组件 |

### dataModelUpdate 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | 数据模型更新所属的表面 ID |
| path | string | 否 | 数据模型中的路径，省略则替换整个模型 |
| contents | array | 是 | 数据条目数组 |

### deleteSurface 字段

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| surfaceId | string | 是 | 要删除的表面 ID |

## 与 server_to_client_with_standard_catalog 的区别

| 特性 | server_to_client.json | server_to_client_with_standard_catalog.json |
|------|----------------------|---------------------------------------------|
| 组件定义 | 不内联，通过 catalogId 引用 | 内联标准目录全部组件定义 |
| beginRendering.catalogId | 有 | 无 |
| beginRendering.styles | additionalProperties: true | 严格定义 font 和 primaryColor |
| component 属性 | additionalProperties: true | 严格定义所有标准组件类型 |

## 示例

```json
{
  "beginRendering": {
    "surfaceId": "main-screen",
    "catalogId": "https://a2ui.org/specification/v0_8/catalogs/minimal/minimal_catalog.json",
    "root": "root",
    "styles": {
      "font": "Roboto",
      "primaryColor": "#00BFFF"
    }
  }
}
```
