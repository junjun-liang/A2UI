# 7_incremental.json 说明文档

## 文件概述

本文件展示了 A2UI 的渐进式渲染能力，包括数据模型更新、动态子组件模板（ChildList 模板模式）和数据模型响应式更新。

## UI 场景

展示一个餐厅列表界面，通过多条消息逐步构建和更新：
1. 先设置数据模型中的餐厅列表
2. 使用动态模板渲染列表项
3. 后续通过 updateDataModel 添加新餐厅
4. 通过 updateComponents 更新列表项模板（添加按钮）

## 使用的组件

| 组件 | 用途 |
|------|------|
| Column (root) | 根容器，使用动态子组件模板 |
| Column (restaurant_card) | 餐厅卡片模板，包含标题、副标题、地址 |
| Text (rc_title) | 餐厅标题，绑定到相对路径 `title` |
| Text (rc_subtitle) | 餐厅副标题，绑定到相对路径 `subtitle` |
| Text (rc_address) | 餐厅地址，绑定到相对路径 `address` |
| Button (rc_button) | "Book now" 按钮，后续添加 |

## 消息流程

1. **createSurface** — 创建界面 `example_7`
2. **updateDataModel** — 初始化数据模型，设置 3 家餐厅数据
3. **updateComponents** — 设置根 Column，使用动态模板 `{path: "/restaurants", componentId: "restaurant_card"}`
4. **updateComponents** — 定义 restaurant_card 模板组件
5. **updateDataModel** — 添加第 4 家餐厅到列表
6. **updateComponents** — 更新 restaurant_card 模板，添加 "Book now" 按钮

## 示例关键片段

### 动态子组件模板

```json
{
  "id": "root",
  "component": "Column",
  "children": {
    "path": "/restaurants",
    "componentId": "restaurant_card"
  }
}
```

### 相对路径数据绑定

```json
{
  "id": "rc_title",
  "component": "Text",
  "text": { "path": "title" }
}
```

模板组件中使用相对路径（如 `title` 而非 `/title`），绑定到数据模型列表中每个对象的对应字段。

### 渐进式数据模型更新

```json
{
  "version": "v0.9",
  "updateDataModel": {
    "surfaceId": "example_7",
    "path": "/restaurants/3",
    "value": {
      "title": "Spice Route",
      "subtitle": "Exotic Flavors from the East",
      "address": "101 Silk Road St"
    }
  }
}
```

通过指定路径 `/restaurants/3` 向数组追加新元素，客户端自动重新渲染列表。
