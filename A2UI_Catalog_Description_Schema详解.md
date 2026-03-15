# A2UI 组件目录描述Schema (v0.8)

> 本文件解读 `/specification/v0_8/json/catalog_description_schema.json`

## 概述

这是 A2UI v0.8 版本的**组件目录描述Schema**，用于定义自定义组件目录（Catalog）。该Schema允许开发者创建自己的UI组件库，扩展或替换标准的A2UI组件。

---

## Schema 结构

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "A2UI Catalog Description Schema",
  "description": "A schema for a custom Catalog Description including A2UI components and styles.",
  "type": "object",
  "properties": {
    "catalogId": { ... },
    "components": { ... },
    "styles": { ... }
  },
  "required": ["catalogId", "components", "styles"]
}
```

---

## 字段定义

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `catalogId` | string | ✅ | 目录唯一标识符 |
| `components` | object | ✅ | A2UI组件定义 |
| `styles` | object | ✅ | A2UI样式定义 |

---

## 1. catalogId - 目录标识符

唯一标识该组件目录的字符串。

### 规则

- 必须唯一
- 建议使用互联网域名作为前缀以避免冲突
- 格式建议：`domain:catalogname` 或 `company.com:catalogname`

### 示例

```json
{
  "catalogId": "mycompany.com:restaurant_catalog"
}
```

```json
{
  "catalogId": "acme.corp:booking_components"
}
```

---

## 2. components - 组件定义

定义该目录中的A2UI组件。每个键是组件名称，每个值是该组件属性的JSON Schema。

### 结构

```json
{
  "components": {
    "ComponentName": {
      "type": "object",
      "properties": {
        "prop1": { ... },
        "prop2": { ... }
      },
      "required": ["prop1"]
    }
  }
}
```

### 特点

- `additionalProperties: true` - 允许自定义属性
- 每个组件使用标准JSON Schema定义属性

### 示例：自定义餐厅卡片组件

```json
{
  "catalogId": "restaurant:custom_components",
  "components": {
    "RestaurantCard": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "restaurantName": {
          "type": "string",
          "description": "餐厅名称"
        },
        "cuisine": {
          "type": "string",
          "description": "菜系类型"
        },
        "rating": {
          "type": "number",
          "description": "评分 (1-5)",
          "minimum": 1,
          "maximum": 5
        },
        "imageUrl": {
          "type": "string",
          "description": "餐厅图片URL"
        },
        "priceRange": {
          "type": "string",
          "enum": ["$", "$$", "$$$", "$$$$"]
        },
        "onSelect": {
          "type": "object",
          "description": "选择餐厅时的操作",
          "properties": {
            "name": { "type": "string" },
            "context": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "key": { "type": "string" },
                  "value": {
                    "type": "object",
                    "properties": {
                      "literalString": { "type": "string" },
                      "path": { "type": "string" }
                    }
                  }
                }
              }
            }
          }
        }
      },
      "required": ["restaurantName", "rating"]
    },
    "RatingStars": {
      "type": "object",
      "properties": {
        "value": {
          "type": "number",
          "minimum": 0,
          "maximum": 5
        },
        "showNumeric": {
          "type": "boolean"
        }
      },
      "required": ["value"]
    },
    "PriceTag": {
      "type": "object",
      "properties": {
        "range": {
          "type": "string",
          "enum": ["$", "$$", "$$$", "$$$$"]
        },
        "color": {
          "type": "string"
        }
      },
      "required": ["range"]
    }
  },
  "styles": {}
}
```

---

## 3. styles - 样式定义

定义该目录中的A2UI样式。每个键是样式名称，每个值是该样式属性的JSON Schema。

### 结构

```json
{
  "styles": {
    "StyleName": {
      "type": "object",
      "properties": {
        "prop1": { ... },
        "prop2": { ... }
      }
    }
  }
}
```

### 示例

```json
{
  "catalogId": "custom:theme",
  "components": { ... },
  "styles": {
    "theme": {
      "type": "object",
      "properties": {
        "primaryColor": {
          "type": "string",
          "pattern": "^#[0-9a-fA-F]{6}$"
        },
        "secondaryColor": {
          "type": "string",
          "pattern": "^#[0-9a-fA-F]{6}$"
        },
        "fontFamily": {
          "type": "string"
        },
        "borderRadius": {
          "type": "number"
        }
      },
      "required": ["primaryColor"]
    },
    "card": {
      "type": "object",
      "properties": {
        "elevation": {
          "type": "number",
          "minimum": 0,
          "maximum": 10
        },
        "padding": {
          "type": "number"
        }
      }
    }
  }
}
```

---

## 完整示例：餐厅预订自定义目录

```json
{
  "catalogId": "restaurant:booking_catalog",
  "components": {
    "RestaurantCard": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "name": {
          "type": "object",
          "properties": {
            "literalString": { "type": "string" },
            "path": { "type": "string" }
          }
        },
        "cuisine": {
          "type": "object",
          "properties": {
            "literalString": { "type": "string" },
            "path": { "type": "string" }
          }
        },
        "rating": {
          "type": "object",
          "properties": {
            "literalNumber": { "type": "number" },
            "path": { "type": "string" }
          }
        },
        "image": {
          "type": "object",
          "properties": {
            "literalString": { "type": "string" },
            "path": { "type": "string" }
          }
        },
        "onClick": {
          "type": "object",
          "properties": {
            "name": { "type": "string" },
            "context": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "key": { "type": "string" },
                  "value": {
                    "type": "object",
                    "properties": {
                      "literalString": { "type": "string" },
                      "path": { "type": "string" }
                    }
                  }
                }
              }
            }
          }
        }
      },
      "required": ["name", "rating"]
    },
    "BookingForm": {
      "type": "object",
      "properties": {
        "fields": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "type": {
                "type": "string",
                "enum": ["text", "date", "number", "select"]
              },
              "label": { "type": "string" },
              "required": { "type": "boolean" }
            }
          }
        },
        "submitAction": {
          "type": "object",
          "properties": {
            "name": { "type": "string" }
          }
        }
      }
    },
    "ConfirmationDialog": {
      "type": "object",
      "properties": {
        "title": { "type": "string" },
        "message": { "type": "string" },
        "confirmText": { "type": "string" },
        "cancelText": { "type": "string" },
        "onConfirm": {
          "type": "object",
          "properties": {
            "name": { "type": "string" }
          }
        },
        "onCancel": {
          "type": "object",
          "properties": {
            "name": { "type": "string" }
          }
        }
      },
      "required": ["title", "confirmText"]
    }
  },
  "styles": {
    "restaurantTheme": {
      "type": "object",
      "properties": {
        "primaryColor": {
          "type": "string",
          "pattern": "^#[0-9a-fA-F]{6}$"
        },
        "accentColor": {
          "type": "string",
          "pattern": "^#[0-9a-fA-F]{6}$"
        },
        "fontFamily": {
          "type": "string"
        },
        "cardStyle": {
          "type": "string",
          "enum": ["elevated", "outlined", "filled"]
        }
      }
    }
  }
}
```

---

## 使用场景

### 1. 企业品牌定制

企业可以创建带有自己品牌样式和组件的目录：

```json
{
  "catalogId": "acme:corporate_components",
  "components": { ... },
  "styles": {
    "corporate": {
      "primaryColor": "#FF6B00",
      "logo": "https://acme.com/logo.png"
    }
  }
}
```

### 2. 行业特定组件

为特定行业创建专用组件库：

```json
{
  "catalogId": "healthcare:patient_portal",
  "components": {
    "VitalSignsCard": { ... },
    "AppointmentSlot": { ... },
    "MedicationReminder": { ... }
  }
}
```

### 3. 扩展标准目录

在标准目录基础上添加自定义组件：

```json
{
  "catalogId": "extended:standard_plus",
  "extends": "https://a2ui.org/specification/v0_8/standard_catalog_definition.json",
  "components": {
    "CustomChart": { ... },
    "CustomMap": { ... }
  }
}
```

---

## Schema 结构图

```
catalog_description_schema.json
├── catalogId (required)           - 目录唯一标识符
│   └── string                     - 建议使用域名前缀
│
├── components (required)          - 组件定义
│   ├── ComponentName1
│   │   ├── type: "object"
│   │   ├── properties: { ... }
│   │   └── required: [ ... ]
│   │
│   ├── ComponentName2
│   │   └── ...
│   │
│   └── ...                        - 任意数量的自定义组件
│
└── styles (required)              - 样式定义
    ├── StyleName1
    │   ├── type: "object"
    │   └── properties: { ... }
    │
    ├── StyleName2
    │   └── ...
    │
    └── ...                        - 任意数量的自定义样式
```

---

## 与其他Schema的关系

| Schema | 作用 |
|--------|------|
| `server_to_client.json` | 定义服务器到客户端的消息格式 |
| `client_to_server.json` | 定义客户端到服务器的事件格式 |
| `standard_catalog_definition.json` | 定义标准组件目录 |
| `catalog_description_schema.json` | 定义自定义组件目录的格式 |

---

## 总结

A2UI的组件目录描述Schema提供了强大的扩展能力：

1. **自定义组件** - 创建业务特定的UI组件
2. **品牌定制** - 定义企业风格的样式系统
3. **可复用** - 目录可以在多个应用间共享
4. **类型安全** - 使用JSON Schema确保组件定义的正确性

通过自定义目录，开发者可以根据具体业务需求扩展A2UI的能力，同时保持与标准协议 的兼容性。
