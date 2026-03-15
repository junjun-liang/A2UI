# A2UI 规范版本对比：v0.8 vs v0.9 vs v0.10

> 综合分析 A2UI 协议三个主要版本的规范差异与演进

---

## 版本概览

| 版本 | 状态 | 发布时间 | 特点 |
|------|------|---------|------|
| **v0.8** | 已关闭 | 早期 | 基础协议，基于结构化输出优化 |
| **v0.9** | 已关闭 | 2025年11月 | 协议重构，引入函数系统 |
| **v0.10** | 开发中 | 2025年12月 | 新增函数调用，完善功能 |

---

## 核心变化总览

### 消息操作对比

| v0.8 | v0.9 | v0.10 |
|-------|-------|-------|
| `beginRendering` | `createSurface` | `createSurface` |
| `surfaceUpdate` | `updateComponents` | `updateComponents` |
| `dataModelUpdate` | `updateDataModel` | `updateDataModel` |
| `deleteSurface` | `deleteSurface` | `deleteSurface` |
| - | - | **`callFunction`** 🆕 |

### Schema 结构对比

| 特性 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| 版本标识 | ❌ | ✅ `version: "v0.9"` | ✅ `version: "v0.10"` |
| Schema模块化 | 单文件 | 多文件分离 | 多文件分离 |
| 函数系统 | ❌ | ✅ | ✅ |
| 函数调用 | ❌ | ❌ | ✅ |
| Two-way Binding | ❌ | ✅ | ✅ |
| sendDataModel | ❌ | ✅ | ✅ |
| 主题系统 | 基础 | 完整 | 完整 |

---

## 详细变化对比

### 1. 版本标识 (Version)

**v0.8**: 无版本标识

**v0.9**: 每个消息必须包含 `"version": "v0.9"`

```json
// v0.9
{
  "version": "v0.9",
  "createSurface": { ... }
}
```

**v0.10**: 更新为 `"version": "v0.10"`

```json
// v0.10
{
  "version": "v0.10",
  "createSurface": { ... }
}
```

---

### 2. createSurface 消息

#### v0.8 (beginRendering)

```json
{
  "beginRendering": {
    "surfaceId": "main",
    "root": "root_component",
    "styles": { "theme": "dark" }
  }
}
```

#### v0.9

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "main",
    "catalogId": "https://a2ui.org/specification/v0_9/basic_catalog.json",
    "theme": { "primaryColor": "#FF5722" },
    "sendDataModel": true
  }
}
```

#### v0.10

```json
{
  "version": "v0.10",
  "createSurface": {
    "surfaceId": "main",
    "catalogId": "https://a2ui.org/specification/v0_10/standard_catalog.json",
    "theme": { "primaryColor": "#FF5722" },
    "sendDataModel": true
  }
}
```

**变化对比**：

| 字段 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| `surfaceId` | ✅ | ✅ | ✅ |
| `root` | ✅ | ❌ | ❌ |
| `catalogId` | ❌ | ✅ 必填 | ✅ 必填 |
| `theme` | `styles` | ✅ | ✅ |
| `sendDataModel` | ❌ | ✅ | ✅ |

---

### 3. updateComponents 消息

#### v0.8 (surfaceUpdate)

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": { "Column": { "children": ["title"] } }
      }
    ]
  }
}
```

#### v0.9 / v0.10

```json
{
  "version": "v0.10",
  "updateComponents": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["title"]
      }
    ]
  }
}
```

**变化对比**：

| 特性 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| 组件定义方式 | 内联 | 引用 catalog | $ref 引用 |
| 扁平组件列表 | ✅ | ✅ | ✅ |

---

### 4. updateDataModel 消息

#### v0.8

```json
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "path": "/",
    "contents": [
      { "key": "name", "valueString": "张三" }
    ]
  }
}
```

#### v0.9 / v0.10

```json
{
  "version": "v0.10",
  "updateDataModel": {
    "surfaceId": "main",
    "path": "/user",
    "value": {
      "name": "张三",
      "age": 30
    }
  }
}
```

**改进**：
- 移除 `contents` 数组结构
- 使用直接的 `value` 字段，支持任意类型
- 更直观的数据模型更新方式

---

### 5. 🆕 callFunction (仅 v0.10)

**v0.10 新增功能**：服务器可以直接调用客户端的函数。

```json
{
  "version": "v0.10",
  "functionCallId": "call_123",
  "wantResponse": true,
  "callFunction": {
    "call": {
      "name": "getLocation",
      "arguments": {}
    },
    "returnType": {
      "type": "object",
      "properties": {
        "latitude": { "type": "number" },
        "longitude": { "type": "number" }
      }
    },
    "callableFrom": "clientOrRemote"
  }
}
```

**字段说明**：

| 字段 | 说明 |
|------|------|
| `functionCallId` | 函数调用唯一ID |
| `wantResponse` | 是否需要响应 |
| `callFunction.call.name` | 函数名称 |
| `callFunction.call.arguments` | 函数参数 |
| `callFunction.returnType` | 返回类型定义 |
| `callableFrom` | 调用来源 (`remoteOnly` / `clientOrRemote`) |

---

### 6. 函数系统 (v0.9 新增)

#### 内置函数

v0.9 和 v0.10 引入了丰富的内置函数：

| 函数 | 功能 |
|------|------|
| `required` | 验证值非空 |
| `regex` | 正则表达式验证 |
| `length` | 字符串长度验证 |
| `numeric` | 数值范围验证 |
| `email` | 邮箱格式验证 |
| `formatString` | 字符串格式化与插值 |
| `formatNumber` | 数字格式化 |
| `formatCurrency` | 货币格式化 |
| `formatDate` | 日期格式化 |
| `pluralize` | 复数形式选择 |
| `openUrl` | 打开URL |
| `and` / `or` / `not` | 逻辑运算 |

#### 使用示例

**验证**：
```json
{
  "component": "TextField",
  "value": { "path": "/formData/email" },
  "checks": [
    {
      "call": "required",
      "args": { "value": { "path": "/formData/email" } },
      "message": "邮箱为必填项"
    },
    {
      "call": "email",
      "args": { "value": { "path": "/formData/email" } },
      "message": "请输入有效的邮箱地址"
    }
  ]
}
```

**字符串格式化**：
```json
{
  "component": "Text",
  "text": {
    "call": "formatString",
    "args": {
      "value": "欢迎, ${/user/name}! 当前余额: ${/user/balance}"
    }
  }
}
```

---

### 7. Two-way Binding (v0.9 新增)

v0.9 引入了双向数据绑定机制。

#### 机制说明

```
用户输入 → 本地数据模型 → 服务器
    ↑_________________________|
        (实时响应)
```

#### 示例

1. **绑定**：`TextField` 绑定到 `/formData/email`
2. **输入**：用户输入 "jane@example.com"，本地模型 `/formData/email` 更新
3. **响应**：其他绑定到同一路径的组件实时更新
4. **发送**：点击按钮时，触发的 action 携带当前数据模型

```json
// 按钮定义
{
  "component": "Button",
  "text": "提交",
  "action": {
    "event": {
      "name": "submit_form",
      "context": {
        "email": { "path": "/formData/email" }
      }
    }
  }
}
```

---

### 8. sendDataModel 功能 (v0.9 新增)

当 `createSurface` 中设置 `sendDataModel: true` 时：

1. 客户端在每次发送消息时，自动附带完整数据模型
2. 数据通过传输层的 metadata 机制发送
3. 确保服务器始终拥有客户端最新状态

```json
{
  "version": "v0.9",
  "createSurface": {
    "surfaceId": "main",
    "catalogId": "...",
    "sendDataModel": true
  }
}
```

---

### 9. 主题系统 (Theme)

#### v0.8

仅支持简单的样式对象：
```json
"styles": { "theme": "dark" }
```

#### v0.9 / v0.10

支持丰富的主题属性：

```json
"theme": {
  "primaryColor": "#FF5722",
  "iconUrl": "https://example.com/logo.png",
  "agentDisplayName": "餐厅助手"
}
```

| 属性 | 类型 | 说明 |
|------|------|------|
| `primaryColor` | String | 主色调（十六进制） |
| `iconUrl` | URI | 代理图标URL |
| `agentDisplayName` | String | 代理显示名称 |

---

### 10. Schema 模块化

#### v0.8

所有定义在单个文件中：
- `server_to_client_with_standard_catalog.json`

#### v0.9 / v0.10

拆分为多个专业文件：

| 文件 | 作用 |
|------|------|
| `common_types.json` | 通用类型定义（DynamicString, ComponentId 等） |
| `basic_catalog.json` | 基础组件和函数定义 |
| `server_to_client.json` | 服务器到客户端消息格式 |
| `client_to_server.json` | 客户端到服务器消息格式 |
| `catalog.json` | 组件目录（引用） |

---

## 组件对比

### 展示组件

| 组件 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| Text | ✅ | ✅ | ✅ |
| Image | ✅ | ✅ | ✅ |
| Icon | ✅ | ✅ | ✅ |
| Video | ✅ | ✅ | ✅ |
| AudioPlayer | ✅ | ✅ | ✅ |
| Divider | ✅ | ✅ | ✅ |

### 布局组件

| 组件 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| Row | ✅ | ✅ | ✅ |
| Column | ✅ | ✅ | ✅ |
| List | ✅ | ✅ | ✅ |
| Card | ✅ | ✅ | ✅ |
| Tabs | ✅ | ✅ | ✅ |
| Modal | ✅ | ✅ | ✅ |

### 交互组件

| 组件 | v0.8 | v0.9 | v0.10 |
|------|:----:|:----:|:-----:|
| Button | ✅ | ✅ | ✅ |
| CheckBox | ✅ | ✅ | ✅ |
| TextField | ✅ | ✅ | ✅ |
| DateTimeInput | ✅ | ✅ | ✅ |
| MultipleChoice | ✅ | ✅ | - |
| ChoicePicker | - | ✅ | ✅ |
| Slider | ✅ | ✅ | ✅ |

---

## 传输层支持

### v0.8

仅支持基础传输

### v0.9 / v0.10

支持多种传输协议：

| 传输协议 | 说明 |
|---------|------|
| **A2A** | Agent-to-Agent 协议 |
| **AG-UI** | Agent User Interface 协议 |
| **MCP** | Model Context Protocol |
| **SSE + JSON-RPC** | 服务端推送 + JSON远程调用 |
| **WebSocket** | 双向实时通信 |
| **REST** | 简单HTTP请求 |

---

## 完整消息流程对比

### v0.8 流程

```json
{"beginRendering": {"surfaceId": "form", "root": "root"}}
{"surfaceUpdate": {"surfaceId": "form", "components": [...]}}
{"dataModelUpdate": {"surfaceId": "form", "contents": [...]}}
{"deleteSurface": {"surfaceId": "form"}}
```

### v0.9 / v0.10 流程

```jsonl
{"version": "v0.10", "createSurface": {"surfaceId": "form", "catalogId": "..."}}
{"version": "v0.10", "updateComponents": {"surfaceId": "form", "components": [...]}}
{"version": "v0.10", "updateDataModel": {"surfaceId": "form", "path": "/", "value": {...}}}
{"version": "v0.10", "deleteSurface": {"surfaceId": "form"}}
```

---

## 升级路径建议

### 从 v0.8 升级到 v0.9

1. 添加 `version` 字段到每个消息
2. 重命名消息类型：`beginRendering` → `createSurface` 等
3. 使用 `catalogId` 引用组件目录
4. 迁移 `styles` 到 `theme`
5. 考虑使用函数系统增强验证

### 从 v0.9 升级到 v0.10

1. 更新版本标识为 `"v0.10"`
2. 如果需要服务器调用客户端函数，使用 `callFunction`
3. 使用 `$ref` 引用组件定义
4. 保持 `updateDataModel` 的 `value` 格式

---

## 总结

| 版本 | 定位 | 适合场景 |
|------|------|---------|
| **v0.8** | 基础版本 | 简单UI生成，结构化输出 |
| **v0.9** | 成熟版本 | 复杂交互，数据绑定，企业应用 |
| **v0.10** | 未来版本 | 需要函数调用能力的高级场景 |

### v0.10 的主要优势

1. **版本明确** - 避免协议混淆
2. **函数调用** - 🆕 服务器可直接调用客户端函数
3. **组件解耦** - 通过 $ref 引用实现更好的模块化
4. **数据模型简化** - 更直观的 value 字段
5. **完整主题系统** - 品牌定制能力

---

## 相关文档

- [A2UI v0.8 消息Schema详解](./A2UI_Message_Schema详解.md)
- [A2UI 标准组件目录详解](./A2UI_Standard_Catalog详解.md)
- [A2UI 客户端到服务器事件详解](./A2UI_Client_to_Server_Event详解.md)
- [A2UI v0.10 vs v0.8 对比](./A2UI_v0.10_vs_v0.8_对比.md)
