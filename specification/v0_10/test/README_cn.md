# A2UI 规范测试

本目录包含用于验证 A2UI JSON 模式的测试用例和运行器。

## 前提条件

- **Python 3**
- **pnpm**：测试使用 `pnpm` 来运行 `ajv-cli`。

## 安装（可选）

为了加快测试执行速度，请在本地安装依赖：

```bash
cd specification/v0_10/test
pnpm install
```

## 运行测试

从仓库根目录或测试目录运行 Python 测试脚本：

```bash
python3 specification/v0_10/test/run_tests.py
```

脚本将：
1. 从 `specification/v0_10/json` 加载所有模式。
2. 执行 `specification/v0_10/test/cases/*.json` 中定义的所有测试套件。
3. 报告每个测试用例的通过/失败状态。

## 添加测试

在 `cases/` 中创建新的 JSON 文件（例如 `cases/my_feature.json`）：

```json
{
  "schema": "server_to_client.json",
  "tests": [
    {
      "description": "Description of the test case",
      "valid": true,
      "data": {
        "updateComponents": { ... }
      }
    },
    {
      "description": "Should fail validation",
      "valid": false,
      "data": { ... }
    }
  ]
}
```
