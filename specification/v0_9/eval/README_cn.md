# Genkit 评估框架用于 UI 生成

这是用于评估 A2UI (v0.9) 在各种 LLM 上的表现。

此版本将 JSON 模式直接嵌入到提示中，并指示 LLM 在 markdown 代码块中输出 JSON 对象。框架然后提取并验证此 JSON。

## 设置

要使用模型，您需要使用 API 密钥设置以下环境变量：

- `GEMINI_API_KEY`
- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`

您可以在项目根目录的 `.env` 文件中设置这些变量，或在 shell 配置文件（例如 `.bashrc`、`.zshrc`）中设置。

提供了 `.env.example` 文件作为模板：

```bash
cp .env.example .env
# 使用您的 API 密钥编辑 .env（不要提交 .env）
```

运行前还需要安装依赖：

```bash
pnpm install
```

## 运行所有评估（警告：可能使用_大量_模型配额）

要运行流程，请使用以下命令：

```bash
pnpm run evalAll
```

## 运行单个测试

您可以使用 `--model` 和 `--prompt` 命令行标志来运行单个模型和数据点的脚本。这对于快速测试和调试很有用。

### 语法

```bash
pnpm run eval --model=<model_name> --prompt=<prompt_name>
```

### 示例

要使用 `gemini-2.5-flash-lite` 模型和 `loginForm` 提示运行测试，请使用以下命令：

```bash
pnpm run eval --model=gemini-2.5-flash-lite --prompt=loginForm
```

## 控制输出

默认情况下，脚本将进度条和最终摘要表打印到控制台。详细日志写入结果目录中的 `output.log`。

### 命令行选项

- `--log-level=<level>`：设置控制台日志级别（默认：`info`）。选项：`error`、`warn`、`info`、`http`、`verbose`、`debug`、`silly`。
  - 注意：文件日志（结果目录中的 `output.log`）无论此设置如何，始终捕获 `debug` 级别的日志。
- `--results=<output_dir>`：（默认：`results/output-<model>` 或指定多个模型时为 `results/output-combined`）保留输出文件。要指定自定义目录，请使用 `--results=my_results`。
- `--clean-results`：如果设置，在运行测试之前清理结果目录。
- `--runs-per-prompt=<number>`：每个提示运行的次数（默认：1）。
- `--model=<model_name>`：（默认：所有模型）仅运行指定的模型。可以多次指定。
- `--prompt=<prompt_name>`：（默认：所有提示）仅运行指定的提示。

### 示例

在控制台中运行调试输出：
```bash
pnpm run eval -- --log-level=debug
```

每个提示运行 5 次并清理之前的结果：
```bash
pnpm run eval -- --runs-per-prompt=5 --clean-results
```

## 速率限制

框架包含两层速率限制系统：
1. **主动限制**：在本地跟踪 token 和请求使用量，以保持在配置的限制内（在 `src/models.ts` 中定义）。
2. **响应式断路器**：如果收到 `RESOURCE_EXHAUSTED` (429) 错误，自动暂停对模型的请求，仅在请求的重试持续时间后恢复。