# AGENTS.md — 本仓库的 Agent 工作约定

> 本文件由 Agent 以 PR 形式提交，供后续 Worker 会话在开工前读取。
> 适用对象：在本仓库执行工单的 dsh-superagent Worker。

## 1. 分支与提交

- **分支**：`agent/<ticket-name>`，一票一支，从最新 `main` 切出。
- **提交标题**：Conventional Commits，`<type>(<scope>): <subject>`，≤72 字符。type 取 `feat|fix|refactor|test|docs|chore|perf`。
- **提交正文**：说明动机与影响面，末行必带 `Ticket: <id>`；有对应 Issue 时补 `Refs: #<number>`。
- **作者身份**：`gh-agent-lxx9527 <360068948+gh-agent-lxx9527@users.noreply.github.com>`（noreply 邮箱，勿用他人身份）。
- **禁止**：force push、改写已推送历史、`--no-verify`、一次提交跨多个工单。

## 2. 改动范围

- **可改**：`src/**`、`docs/**`、`tests/**`、仓库根目录的说明类文件（`README.md`、`AGENTS.md` 等）。
- **禁改**（需人工单独授权）：`.github/**`、`package.json`、`pnpm-lock.yaml`、任何环境文件、`deploy/**`。
- **体量**：单票 ≤400 行、≤12 文件；超限先拆票，不要硬塞。
- **依赖**：不新增第三方依赖。

## 3. 交付流程

1. 在工单目录内完成改动，自检（类型检查/测试/构建，若仓库有）。
2. 推送分支 → 开 PR，标题与提交标题一致，正文写清：改了什么、为什么、怎么验证。
3. PR 必须满足：`main` 需 1 人批准、线性历史、CI（如已配置）通过。
4. **不要自行合并**：本仓库未配置合并 CLI，所有 PR 由人合并；不要用 `--admin` 绕过。

## 4. Windows 运行环境注意（重要）

本机为 Windows，Worker 的 git 配置由插件接管（`worker-gitconfig`），**全局 git 配置与其代理设置不生效**。凡涉及远端操作，先显式设置代理：

```powershell
$env:HTTPS_PROXY = 'http://127.0.0.1:7897'
$env:HTTP_PROXY  = 'http://127.0.0.1:7897'
# POSIX shell 下：
# export HTTPS_PROXY=http://127.0.0.1:7897
```

- `gh` 在 PATH 上可能解析到不可执行的 shim；直接用绝对路径 `C:\Users\PC\.local\bin\gh-agent.exe`（已内置代理与独立凭据目录）。
- 仓库无 CI 时，`gh pr checks` 返回空属正常，不要因此判定失败。

## 5. 沟通

- 在 GitHub 上发言，每条以 `This is by an automated agent, not a human.` 开头。
- 只在「结果可用」或「需要人做决定」时回报；过程性进度留在工单内部记录里。
- 不做超出工单范围的「顺手优化」，发现相邻问题就另开 Issue，不要夹带。
