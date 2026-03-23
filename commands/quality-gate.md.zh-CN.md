# Quality Gate Command
# Quality Gate 命令

Run the ECC quality pipeline on demand for a file or project scope.
按需为文件或项目范围运行 ECC 质量管道。

## Usage
## 使用方式

`/quality-gate [path|.] [--fix] [--strict]`

- default target: current directory (`.`)
- 默认目标：当前目录（`.`）

- `--fix`: allow auto-format/fix where configured
- `--fix`：允许在配置的地方自动格式/修复

- `--strict`: fail on warnings where supported
- `--strict`：在支持的地方将警告视为失败

## Pipeline
## 管道

1. Detect language/tooling for target.
1. 检测目标语言/工具。

2. Run formatter checks.
2. 运行格式化检查。

3. Run lint/type checks when available.
3. 可用时运行 lint/类型检查。

4. Produce a concise remediation list.
4. 生成简明的修复列表。

## Notes
## 注意事项

This command mirrors hook behavior but is operator-invoked.
此命令反映 hook 行为，但由操作员调用。

## Arguments
## 参数

$ARGUMENTS:
- `[path|.]` optional target path
- `[path|.]` 可选目标路径

- `--fix` optional
- `--fix` 可选

- `--strict` optional
- `--strict` 可选
