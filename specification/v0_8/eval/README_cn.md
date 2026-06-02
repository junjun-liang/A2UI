# Genkit UI 生成评估框架

用于针对各种 LLM 评估 A2UI（v0.8）。

## 设置

要使用模型，您需要使用 API 密钥设置以下环境变量：

- `GEMINI_API_KEY`
- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`

您可以在项目根目录的 `.env` 文件中设置这些变量，或在 shell 配置文件（如 `.bashrc`、`.zshrc`）中设置。

提供了 `.env.example` 文件作为模板：

```bash
cp .env.example .env
# 使用您的 API 密钥编辑 .env（不要提交 .env）
```

运行前还需要安装依赖：

```bash
pnpm install
```

## 运行所有评估（警告：可能消耗*大量*模型配额）

要运行流程，请使用以下命令：

```bash
pnpm run evalAll
```

## 运行单个测试

您可以使用 `--model` 和 `--prompt` 命令行标志来运行单个模型和数据点的脚本。这对于快速测试和调试很有用。

### 语法

```bash
pnpm run eval -- --model='<model_name>' --prompt=<prompt_name>
```

### 示例

要使用 `gpt-5-mini (reasoning: minimal)` 模型和 `generateDogUIs` 提示运行测试，请使用以下命令：

```bash
pnpm run eval -- --model='gpt-5-mini (reasoning: minimal)' --prompt=generateDogUIs
```

## 控制输出

默认情况下，脚本仅打印汇总表和生成过程中出现的任何错误。要查看每次成功生成的完整 JSON 输出，请使用 `--verbose` 标志。

要将每次运行的输入和输出保存在单独的文件中，请指定 `--keep=<output_dir>` 标志，它将创建一个目录层次结构，将每次 LLM 调用的输入和输出放在单独的文件中。

### 示例

```bash
pnpm run evalAll -- --verbose
```

```bash
pnpm run evalAll -- --keep=output
```
