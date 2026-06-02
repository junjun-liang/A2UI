# 使用自定义函数扩展 A2UI

A2UI 函数被设计为可扩展的。第三方开发者可以定义自己的函数目录。

本指南演示如何创建一个 `custom_catalog.json`，添加字符串 `trim` 函数和硬件查询函数（`getScreenResolution`）。

## 1. 定义自定义目录

创建一个 JSON Schema 文件（例如 `custom_catalog.json`）来定义函数参数。

使用 `functions` 属性定义函数模式的映射。

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/schemas/custom_catalog.json",
  "title": "Custom Function Catalog",
  "description": "Extension catalog adding string trimming and screen resolution functions.",
  "functions": {
    "trim": {
      "type": "object",
      "description": "Removes whitespace (or other characters) from the beginning and end of a string.",
      "properties": {
        "call": { "const": "trim" },
        "args": {
          "type": "object",
          "properties": {
            "value": {
              "$ref": "common_types.json#/$defs/DynamicString",
              "description": "The string to trim."
            },
            "chars": {
              "$ref": "common_types.json#/$defs/DynamicString",
              "description": "Optional. A set of characters to remove. Defaults to whitespace."
            }
          },
          "required": ["value"],
          "unevaluatedProperties": false
        },
        "returnType": { "const": "string" }
      },
      "required": ["call", "args"],
      "unevaluatedProperties": false
    },
    "getScreenResolution": {
      "type": "object",
      "description": "Queries hardware for screen resolution.",
      "properties": {
        "call": { "const": "getScreenResolution" },
        "args": {
          "type": "object",
          "properties": {
            "screenIndex": {
              "$ref": "common_types.json#/$defs/DynamicNumber",
              "description": "Optional. The index of the screen to query. Defaults to 0 (primary screen)."
            }
          },
          "unevaluatedProperties": false
        },
        "returnType": { "const": "array" }
      },
      "required": ["call", "args"],
      "unevaluatedProperties": false
    }
  }
}
```

## 2. 使函数可用

`FunctionCall` 定义引用了一个[与目录无关的引用](a2ui_protocol.md#the-basic-catalog)。
在您的目录中，只需定义 `anyFunction` 引用：
```json
{
  "$defs": {
    "anyFunction": {
      "oneOf": [
        {"$ref": "#/functions/trim"},
        {"$ref": "#/functions/getScreenResolution"}
      ]
    }
  }
}
```

如果您想包含 [`basic_catalog.json`] 中定义的函数，也可以添加：
```json
{
  "$defs": {
    "anyFunction": {
      "oneOf": [
        {"$ref": "#/functions/trim"},
        {"$ref": "#/functions/getScreenResolution"},
        {"$ref": "basic_catalog.json#/$defs/anyFunction" }
      ]
    }
  }
}
```

## 验证的工作原理

当 `FunctionCall` 被验证时：

1. **判别器查找：** 验证器查看对象的 `call` 属性。
2. **模式匹配：**
    * 如果 `call` 为 "length"，它匹配 `Functions` -> `length`
      并根据 length 规则验证 `args` 中的命名参数。
    * 如果 `call` 为 "trim"，它匹配 `CustomFunctions` -> `trim` 并
      根据您的自定义规则验证。
    * 如果 `call` 为 "unknownFunc"，验证立即失败（严格模式）。

这种默认严格的方法确保拼写错误能被及早发现，而模块化结构使得添加新功能变得容易，同时具有完整的类型安全性。
