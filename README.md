# Auto Model

用于 Codex 和 Claude Code 的模型分工 skill。根据任务难度选择模型与思考强度，由主会话协调、整合和验收；专家仅在困难问题有充分证据时按需参与。

## 默认角色

| 角色 | Codex | Claude Code | 适用工作 |
| --- | --- | --- | --- |
| 执行 | gpt-6-luna / high | Haiku / 模型默认 effort | 明确规则的批量处理、格式化、既定检查 |
| 开发 | gpt-6.1-sol / medium | Sonnet / medium | 功能开发、常规修复、局部重构及测试 |
| 判断 | gpt-6.1-sol / high | Sonnet / high | 复杂根因、架构取舍及关键语义 |
| 专家 | gpt-6-astra / low 起步 | Opus / medium 起步 | 证据充分的特别困难问题 |

默认从开发角色开始，不要求每次使用全部角色。不同宿主的模型与强度不表示能力等价；可用配置以当前宿主、版本和用户设置为准。详见 [SKILL.md](SKILL.md)。

## 安装

安装前先检查目标目录。已有同名 skill 或代理文件时，请先自行审阅并合并，以下安装方式不用于覆盖现有内容。

### Codex

目标不存在时，将仓库克隆到个人 skill 目录：

```sh
git clone https://github.com/xqnode/auto-model ~/.codex/skills/auto-model
```

在 Codex 中调用：

```text
$auto-model 实现任务描述
```

### Claude Code

目标不存在时，将仓库克隆到个人 skill 目录：

```sh
git clone https://github.com/xqnode/auto-model ~/.claude/skills/auto-model
```

还必须将 `assets/claude-agents/` 中以下四个代理文件逐个复制到 `~/.claude/agents/`，才能按名称调用。目录不存在时先创建；每次复制前检查同名目标，已有文件需自行审阅并合并，不覆盖。

- `auto-model-executor.md`
- `auto-model-developer.md`
- `auto-model-judge.md`
- `auto-model-expert.md`

仅把代理模板保留在 skill 目录中不会自动注册。安装后启动新会话以加载配置，在 Claude Code 中调用：

```text
/auto-model 实现任务描述
```

此适配需要 Claude Code 的代理工具，不适用于普通 Claude 网页聊天。宿主细节见 [Codex 适配](references/codex.md) 和 [Claude Code 适配](references/claude-code.md)。

## 验证范围

Codex 已完成 Luna、Sol、Astra 三种模型的真实调度测试。Claude Code 仅完成静态配置与安装校验，尚未进行真实模型推理验证。配置文件或安装校验通过不代表实际推理已经验证。
