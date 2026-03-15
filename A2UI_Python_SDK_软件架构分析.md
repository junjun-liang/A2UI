# A2UI Python SDK 软件架构分析

## 概述

A2UI Python SDK (`agent_sdks/python`) 是 Google A2UI 协议的核心实现库，用于构建能够渲染丰富 UI 的 AI Agent。

**GitHub 仓库**: https://github.com/google/A2UI

---

## 目录结构

```
agent_sdks/python/
├── src/a2ui/
│   ├── __init__.py              # 包入口
│   ├── a2a.py                   # A2A 协议集成工具
│   ├── adk/
│   │   └── a2a_extension/       # ADK 集成扩展
│   │       └── send_a2ui_to_client_toolset.py
│   ├── assets/                  # 内置 Schema 文件
│   │   ├── 0.8/
│   │   └── 0.9/
│   ├── basic_catalog/           # 基础组件目录
│   │   ├── provider.py
│   │   └── constants.py
│   └── core/
│       ├── parser/              # LLM 响应解析
│       │   ├── parser.py
│       │   └── payload_fixer.py
│       ├── schema/              # Schema 管理
│       │   ├── manager.py       # 核心管理器
│       │   ├── validator.py     # JSON Schema 验证
│       │   ├── catalog.py       # 组件目录
│       │   ├── catalog_provider.py
│       │   ├── constants.py    # 常量定义
│       │   └── utils.py
│       ├── template/            # 模板管理
│       │   └── manager.py
│       └── inference_strategy.py # 推理策略基类
├── tests/                       # 单元测试
├── pyproject.toml              # 项目配置
└── README.md
```

---

## 核心模块架构

### 1. 模块依赖关系

```
┌─────────────────────────────────────────────────────────────────────┐
│                          a2a.py                                     │
│                (A2A 协议集成工具函数)                                  │
└─────────────────────────────────────────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────┐     ┌─────────────────┐    ┌─────────────────┐
│   adk/        │     │   core/schema/  │    │  core/parser/   │
│ a2a_extension │     │   (核心管理层)    │    │   (响应解析)     │
├───────────────┤     ├─────────────────┤    ├─────────────────┤
│ SendA2ui      │     │ A2uiSchema      │    │ parse_response  │
│ ToClient      │     │ Manager         │    │ payload_fixer   │
│ Toolset       │     │ A2uiCatalog     │    │                 │
│               │     │ A2uiValidator   │    │                 │
└───────────────┘     └─────────────────┘    └─────────────────┘
                                │
                                ▼
                        ┌─────────────────┐
                        │ basic_catalog/  │
                        │ (内置组件目录)   │
                        └─────────────────┘
```

### 2. 类图

```
┌─────────────────────────────────────────────────────────────────────┐
│                      InferenceStrategy (基类)                        │
│  └── A2uiSchemaManager                                              │
│       ├── 管理 Schema 版本                                           │
│       ├── 管理组件 Catalog                                           │
│       └── 生成系统提示词                                              │
└─────────────────────────────────────────────────────────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────┐     ┌─────────────────┐    ┌─────────────────┐
│  A2uiCatalog  │     │ CatalogConfig    │    │ A2uiValidator   │
│               │     │ (配置类)         │    │                 │
├───────────────┤     ├─────────────────┤    ├─────────────────┤
│ catalog_id    │     │ name            │    │ validate()      │
│ catalog_schema│     │ provider        │    │ _validate_*()   │
│ s2c_schema    │     │ examples_path   │    │                 │
│ common_types  │     │                 │    │                 │
│ validator     │     │                 │    │                 │
│ examples      │     │                 │    │                 │
└───────────────┘     └─────────────────┘    └─────────────────┘
```

---

## 核心模块详解

### 1. core/schema/manager.py - A2uiSchemaManager

**职责**: Schema 核心管理器

**主要功能**:
- 加载和管理 A2UI 规范版本 (v0.8, v0.9)
- 管理组件 Catalog
- 生成系统提示词

```python
class A2uiSchemaManager(InferenceStrategy):
    def __init__(self, version: str, catalogs: Optional[List[CatalogConfig]] = None):
        self._version = version
        self._load_schemas(version, catalogs)

    def generate_system_prompt(
        role_description: str,
        workflow_description: str = "",
        ui_description: str = "",
        include_schema: bool = False,
        include_examples: bool = False,
    ) -> str: ...

    def get_selected_catalog(...) -> A2uiCatalog: ...
```

---

### 2. core/schema/validator.py - A2uiValidator

**职责**: JSON Schema 验证

**主要功能**:
- 验证 A2UI JSON 符合规范
- 递归深度检查
- 引用完整性验证

```python
class A2uiValidator:
    def __init__(self, catalog: A2uiCatalog): ...

    def validate(self, data: Any) -> None:
        # 验证失败抛出 jsonschema.exceptions.ValidationError
```

**验证规则**:
- 递归深度限制: 50 层
- 函数调用深度限制: 5 层
- 禁止悬空引用
- 禁止循环引用

---

### 3. core/schema/catalog.py - A2uiCatalog

**职责**: 组件 Catalog 封装

```python
@dataclass
class A2uiCatalog:
    version: str
    name: str
    catalog_schema: Dict  # 组件定义
    s2c_schema: Dict     # Server-to-Client 消息 Schema
    common_types: Dict   # 公共类型定义

    def render_as_llm_instructions() -> str: ...
    def load_examples() -> str: ...
```

---

### 4. core/parser/parser.py - 响应解析

**职责**: 解析 LLM 输出，提取 A2UI JSON

```python
# 标签定义
A2UI_OPEN_TAG = "<a2ui-json>"
A2UI_CLOSE_TAG = "</a2ui-json>"

# 解析函数
def parse_response(content: str) -> List[ResponsePart]:
    """提取 <a2ui-json>...</a2ui-json> 中的 JSON"""

@dataclass
class ResponsePart:
    text: str           # 纯文本部分
    a2ui_json: Any     # 解析后的 A2UI JSON
```

**解析流程**:
1. 使用正则表达式提取 `<a2ui-json>` 标签内容
2. 清理 markdown 代码块
3. JSON 解析
4. 返回 ResponsePart 列表

---

### 5. basic_catalog/provider.py - 基础组件目录

**职责**: 提供内置的 Basic Catalog

```python
class BasicCatalog:
    @staticmethod
    def get_config(version: str, examples_path: str = None) -> CatalogConfig:
        return CatalogConfig(
            name="basic",
            provider=BundledCatalogProvider(version),
            examples_path=examples_path,
        )
```

---

### 6. adk/a2a_extension/send_a2ui_to_client_toolset.py

**职责**: ADK 与 A2A 协议集成

**核心类**:

```python
class SendA2uiToClientToolset(BaseToolset):
    """将 A2UI 工具注入到 ADK Agent"""

    async def get_tools(self) -> List[types.Tool]: ...
    async def run_async(...) -> Optional[types.LLMResponse]: ...

class A2uiPartConverter:
    """转换 LLM 响应为 A2A Parts"""

    def convert(self, part) -> List[Part]: ...

class A2uiEventConverter:
    """转换 ADK 事件为 A2A 格式"""
```

---

### 7. a2a.py - A2A 协议工具

**职责**: A2A 协议集成辅助函数

```python
# 创建 A2UI Part
def create_a2ui_part(a2ui_data: dict) -> Part:
    return Part(root=DataPart(
        data=a2ui_data,
        metadata={"mimeType": "application/json+a2ui"}
    ))

# 解析响应为 A2A Parts
def parse_response_to_parts(content: str, validator, fallback_text) -> List[Part]: ...

# 激活 A2UI 扩展
def try_activate_a2ui_extension(context: RequestContext) -> bool: ...
```

---

## 业务流程

### 1. Agent 启动流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                      ContactAgent.__init__()                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. 创建 A2uiSchemaManager                                          │
│     └─> 加载 v0.8 Schema                                            │
│         └─> 加载 Basic Catalog                                     │
│                                                                     │
│  2. 构建 LLM Agent                                                  │
│     └─> 生成系统提示词 (包含 Schema + Examples)                      │
│                                                                     │
│  3. 创建 ADK Runner                                                 │
│     └─> 绑定 Agent + Session/Memory/Artifact 服务                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2. 用户请求处理流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                      ContactAgent.stream()                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. 获取/创建 Session                                               │
│     └─> session_service.get_session()                              │
│                                                                     │
│  2. 调用 LLM (ADK Runner)                                           │
│     └─> runner.run_async(user_id, session_id, message)             │
│                                                                     │
│  3. 获取 LLM 响应                                                   │
│     └─> event.is_final_response()                                  │
│                                                                     │
│  4. 解析响应                                                        │
│     └─> parse_response(content)                                    │
│         └─> 提取 <a2ui-json> 标签中的 JSON                          │
│                                                                     │
│  5. 验证 A2UI JSON                                                 │
│     └─> validator.validate(parsed_json)                            │
│                                                                     │
│  6. 转换为 A2A Parts                                                │
│     └─> parse_response_to_parts()                                  │
│         └─> TextPart + DataPart                                    │
│                                                                     │
│  7. 返回流式响应                                                    │
│     └─> yield {is_task_complete, parts}                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3. A2UI JSON 验证流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                      A2uiValidator.validate()                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  输入: {"beginRendering": {...}, "dataModelUpdate": {...}}         │
│                                                                     │
│  ┌─────────────────────────────────────────┐                        │
│  │ 1. 构建完整 Schema                      │                        │
│  │    - 合并 catalog + s2c + common_types  │                        │
│  │    - 注入 additionalProperties          │                        │
│  └────────────────┬────────────────────────┘                        │
│                   ▼                                                  │
│  ┌─────────────────────────────────────────┐                        │
│  │ 2. JSON Schema Draft202012 验证         │                        │
│  └────────────────┬────────────────────────┘                        │
│                   ▼                                                  │
│  ┌─────────────────────────────────────────┐                        │
│  │ 3. 自定义验证                            │                        │
│  │    - 递归深度检查 (MAX_GLOBAL_DEPTH=50)  │                        │
│  │    - 函数调用深度 (MAX_FUNC_CALL_DEPTH=5)│                        │
│  │    - 悬空引用检查                        │                        │
│  │    - 循环引用检查                        │                        │
│  └────────────────┬────────────────────────┘                        │
│                   ▼                                                  │
│  ┌─────────────────────────────────────────┐                        │
│  │ 4. 验证成功 / 抛出 ValidationError      │                        │
│  └─────────────────────────────────────────┘                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 关键常量

### 版本定义 (core/schema/constants.py)

```python
VERSION_0_8 = "0.8"
VERSION_0_9 = "0.9"

A2UI_OPEN_TAG = "<a2ui-json>"
A2UI_CLOSE_TAG = "</a2ui-json>"

A2UI_MIME_TYPE = "application/json+a2ui"
```

### Schema 文件映射

```python
SPEC_VERSION_MAP = {
    "0.8": {
        "server_to_client": "0.8/server_to_client.json",
        "catalog": "0.8/json/catalogs/basic/basic_catalog.json",
    },
    "0.9": {
        "server_to_client": "0.9/server_to_client.json",
        "common_types": "0.9/common_types.json",
        "catalog": "0.9/json/catalogs/basic/basic_catalog.json",
    },
}
```

---

## 使用示例

### 1. 创建 Agent

```python
from a2ui.core.schema.manager import A2uiSchemaManager
from a2ui.basic_catalog.provider import BasicCatalog
from a2ui.core.schema.constants import VERSION_0_8

schema_manager = A2uiSchemaManager(
    version=VERSION_0_8,
    catalogs=[BasicCatalog.get_config(version=VERSION_0_8, examples_path="examples")]
)

system_prompt = schema_manager.generate_system_prompt(
    role_description="You are a helpful assistant.",
    ui_description="For contact lookup, use CONTACT_CARD_EXAMPLE.",
    include_schema=True,
    include_examples=True,
)
```

### 2. 验证响应

```python
from a2ui.core.parser.parser import parse_response
from a2ui.a2a import parse_response_to_parts

# 解析 LLM 响应
response_parts = parse_response(llm_output)

# 转换为 A2A Parts
a2a_parts = parse_response_to_parts(
    llm_output,
    validator=catalog.validator,
    fallback_text="OK."
)
```

---

## 测试覆盖

```
tests/
├── core/
│   ├── parser/           # 响应解析测试
│   │   ├── test_parser.py
│   │   └── test_payload_fixer.py
│   └── schema/           # Schema 管理测试
│       ├── test_catalog.py
│       ├── test_schema_manager.py
│       ├── test_validator.py
│       └── test_modifiers.py
├── adk/
│   └── a2a_extension/   # ADK 集成测试
│       └── test_send_a2ui_to_client_toolset.py
└── test_a2a.py          # A2A 协议测试
```

**测试结果**: 104 passed, 1 skipped

---

## 总结

| 模块 | 职责 | 核心类 |
|------|------|--------|
| `core/schema/` | Schema 管理与验证 | `A2uiSchemaManager`, `A2uiValidator` |
| `core/parser/` | LLM 响应解析 | `parse_response()` |
| `basic_catalog/` | 内置组件目录 | `BasicCatalog` |
| `adk/a2a_extension/` | ADK 集成 | `SendA2uiToClientToolset` |
| `a2a.py` | A2A 协议工具 | `create_a2ui_part()` |
