# 大西瓜（daxigua）

一个用于博彩/竞猜类应用、文档与软件制作的 Codex 技能。它把玩法规则、赔率与结算、账务返水、风控合规、后台架构和交付文档规范整理成可复用的参考，并带一个「学习日志」机制，让每次真实项目验证过的结论都能沉淀下来，越用越准。

## 仓库结构

```text
daxigua-agent/
|-- README.md
|-- agents/
|   `-- daxigua-reviewer.toml        配套的只读审查子代理
|-- skills/
|   `-- daxigua/
|       |-- SKILL.md
|       |-- agents/openai.yaml        UI 显示名（大西瓜）与调用策略
|       |-- assets/
|       |   |-- rules-research-checklist.md     平台玩法调研采集清单
|       |   `-- game-rules-spec-template.md     玩法规则说明书模板
|       `-- references/
|           |-- glossary.md                    中英术语对照
|           |-- game-catalog.md                玩法大类与结算依据
|           |-- betting-and-settlement.md      赔率、注单、结算、异常单
|           |-- money-and-ledger.md            钱包、流水、返水、代理佣金
|           |-- compliance-and-risk.md         牌照辖区、KYC、AML、风控
|           |-- app-architecture.md            App/后台架构与厂商接入
|           |-- deliverables-and-docs.md       PRD、规则说明书、测试与验收
|           |-- lottery-payout-calc.md         高频彩任选玩法赔率与派彩核算
|           |-- cases/
|           |   `-- reusable-login-platform.md   案例：可复用登录平台
|           `-- learnings/learnings.md          学习日志（跨项目经验）
`-- docs/
    `-- daxigua-learnings.md           技能目录不可写时的项目内记录位置
```

## 安装到本机

方式一，直接复制（Windows PowerShell）：

```powershell
Copy-Item -Recurse -Force "$PWD\skills\daxigua" "$env:USERPROFILE\.codex\skills\daxigua"
```

`$env:USERPROFILE\.agents\skills` 是指向 `.codex\skills` 的 junction，两者等价。复制后 Codex 会自动识别；没出现就重启 Codex。

方式二，从 GitHub 安装（把仓库推上去之后）：

```text
使用 $skill-installer 从 github.com/daxigua098/agent_bc_daxiagua 安装 skills/daxigua
```

## 推到 GitHub

仓库地址：`git@github.com:daxigua098/agent_bc_daxiagua.git`（本机已配置好 origin 与本地提交身份 `daxigua098 <daxigua098@users.noreply.github.com>`，如需换成真实邮箱改本地 config 即可）。

首次在新机器上初始化时：

```powershell
git config --global user.name "你的名字"
git config --global user.email "you@example.com"

git init -b main
git add .
git commit -m "feat(daxigua): 初始版本，含玩法/结算/账务/合规参考与学习日志"
git remote add origin git@github.com:daxigua098/agent_bc_daxiagua.git
git push -u origin main
```

认证用个人访问令牌（PAT）或 SSH key。本机有 git，但没有安装 GitHub CLI（`gh`），需要的话可以 `winget install GitHub.cli` 后用 `gh auth login`。

换机器或换项目时：克隆仓库，把 `skills/daxigua` 复制或链接到 `~/.codex/skills`，或直接用 `$skill-installer` 从仓库路径安装。

日常更新（学习日志回写之后）：

```powershell
git add .
git commit -m "learn(daxigua): <一句话结论>"
git push
```

## 怎么用

- 显式调用：在 Codex 里输入 `$daxigua` 后描述任务，例如「写一份百家乐玩法规则说明书」「排查注单结算差异」。
- 隐式触发：描述博彩类开发、文档、结算、返水、风控任务时，Codex 会按 skill 的 description 自动选用。
- 复用体现在哪些方面：每写完一个博彩类项目，让它把新结论追加到 `learnings/learnings.md`，稳定的结论回写进对应参考文件。

## 配套子代理（可选）

`agents/daxigua-reviewer.toml` 定义了一个只读审查子代理 `daxigua_reviewer`：`deepseek-v4-pro` + `high` 推理强度 + `sandbox_mode = "read-only"`，用来把审查工作派出去跟主线程并行跑。

安装：把 `~/.codex/agents` 链接到本仓库的 `agents/`，避免出现两份副本。

```powershell
New-Item -ItemType Junction -Path "$env:USERPROFILE\.codex\agents" -Target "$PWD\agents"
```

不想用链接就手动复制（之后改仓库里的文件需要再复制一次）：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.codex\agents" | Out-Null
Copy-Item .\agents\daxigua-reviewer.toml "$env:USERPROFILE\.codex\agents\"
```

同时打开的派生代理数量上限写在 `~/.codex/config.toml`：

```toml
[agents]
max_concurrent_threads_per_session = 8
```

这个值指「同时打开的派生代理线程数上限，不含主代理」，不设置时由 Codex 取默认值，改完需要重启 Codex 生效。

用法示例：

```text
Review this settlement module with daxigua_reviewer. Check odds conversion,
void and half-win handling, ledger precision, and license checks.
List P0/P1 issues with file references.
```

## 边界

技能内置了合规与安全约束：先确认辖区与持牌情况、数字必须有可追溯来源、不做欺骗性设计、不产出套利刷水或绕风控的方法。使用者仍需自行确认目标市场的法律与牌照要求。

仓库内不得提交任何真实玩家数据、证件影像、支付信息、密钥或测试账号密码。
