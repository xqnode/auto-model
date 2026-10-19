# Auto Model

用于 Codex 和 Claude Code 的模型分工 skill。根据任务难度选择模型与思考强度，由主会话协调、整合和验收；专家仅在困难问题有充分证据时按需参与。

## 默认角色

| 角色 | Codex | Claude Code | 适用工作 |
| --- | --- | --- | --- |
| 执行 | gpt-6-luna / high | Haiku / 模型默认 effort | 明确规则的批量处理、格式化、既定检查 |
| 开发 | gpt-6.1-sol / medium | Sonnet / medium | 功能开发、常规修复、局部重构及测试 |
| 判断 | gpt-6.1-sol / high | Sonnet / high | 复杂根因、架构取舍及关键语义 |
| 专家 | gpt-6-astra / low 起步 | Opus / medium 起步 | 证据充分的特别困难问题 |

默认从开发角色开始，不要求每次使用全部角色；主会话几步内能完成的事不委派。不同宿主的模型与强度不表示能力等价；可用配置以当前宿主、版本和用户设置为准。详见 [SKILL.md](SKILL.md)。

## 安装

安装前先检查目标目录，已有同名 skill 或代理时先自行审阅并合并，以下方式不用于覆盖现有内容。

### Claude Code

本仓库同时是 Claude Code 插件，四个角色代理随插件自动注册。任选一种：

```text
/plugin install auto-model --marketplace xqnode/auto-model
```

或克隆到个人 skill 目录，下次会话自动以插件形式加载：

```sh
git clone https://github.com/xqnode/auto-model ~/.claude/skills/auto-model
```

当前会话可用 `/reload-plugins` 立即加载。调用：

```text
/auto-model 实现任务描述
```

本 skill 只在手动调用时启用，不会被普通开发任务自动触发。

之前按旧版说明把 `auto-model-*.md` 手动复制到 `~/.claude/agents/` 的，建议删除这四个文件，避免与插件自带的代理重复。

此适配需要 Claude Code 的 Agent 工具，不适用于普通 Claude 网页聊天。个人目录下的安装不会同步到其他设备或云端会话。

### Codex

目标不存在时，将仓库克隆到个人 skill 目录：

```sh
git clone https://github.com/xqnode/auto-model ~/.codex/skills/auto-model
```

在 Codex 中调用：

```text
$auto-model 实现任务描述
```

宿主细节见 [Codex 适配](references/codex.md) 和 [Claude Code 适配](references/claude-code.md)。

## 目录

| 路径 | 用途 |
| --- | --- |
| `SKILL.md` | 调度策略，两个宿主共用 |
| `references/` | 各宿主的型号映射与接口说明 |
| `agents/*.md` | Claude Code 的四个角色代理 |
| `agents/openai.yaml` | Codex 的界面信息 |
| `.claude-plugin/` | Claude Code 插件与市场清单 |

## 验证范围

Codex 已完成 Luna、Sol、Astra 三种模型的真实调度测试。Claude Code 已通过 `claude plugin validate --strict`，并确认两种安装方式都能注册四个代理，尚未进行真实模型推理验证。配置或安装校验通过不代表实际推理已经验证。
