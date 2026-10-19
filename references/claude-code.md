# Claude Code 适配

仅在 Claude Code 宿主读取本文件。默认映射：

| 角色 | 自定义代理名 | model | effort |
| --- | --- | --- | --- |
| 执行 | auto-model-executor | haiku | 不固定，兼容具体模型默认值 |
| 开发 | auto-model-developer | sonnet | medium |
| 判断 | auto-model-judge | sonnet | high |
| 专家 | auto-model-expert | opus | medium 起步 |

这不是与 Codex 的能力或强度等价映射。别名解析版本依赖账号、供应商和配置；只在可用时使用。用户指定完整型号时优先采用指定值，不用别名替换。Haiku 的不同版本对 effort 支持不同，模板不强制设置；明确支持后才按任务调整。

## 调度

- skill 保留在主会话执行，不设置全局 model、effort 或 context: fork，以便主代理完成路由和整合。
- 优先用 Claude Code 的 Agent 工具选择上述 subagent_type；具体字段以当前工具定义为准。不要传 Codex 的 fork_turns 或 reasoning_effort，也不要用 fork 类型绕过自定义代理型号。
- 模板在 [../assets/claude-agents/](../assets/claude-agents/)。这些文件放在 skill 内不会自动注册；须安装到用户 ~/.claude/agents/ 或项目 .claude/agents/ 才能按名称使用。已安装则不在每次任务时重复写文件，不改用户已有同名配置。
- 工具暴露 per-invocation model 时可显式指定型号，包括用户要求的完整 ID；未覆盖时使用代理文件的 model。逐次 effort 参数从 Claude Code 2.1.292 起支持；更旧版本通过代理文件设置 effort，不传未知字段。两个参数的版本支持不要混为一谈。
- 命名代理未加载时先说明加载问题，必要时重新启动会话。若当前 Agent 工具支持直接设置型号，可用其支持的通用代理实现同样角色；不得将未注册名字假装成已注册。
- 单个配置被拒绝、型号被代换、无 Agent 工具等情况遵循核心回退规则；专家不可用时保留 Sonnet 判断角色，不自动使用其他供应商。

## 覆盖与验证

CLAUDE_CODE_SUBAGENT_MODEL_FORCE、组织允许列表、供应商别名、mod hook 可覆盖请求型号；CLAUDE_CODE_EFFORT_LEVEL 可覆盖请求强度。只检查相关设置，不打印认证密钥或整个环境配置，也不擅自更改这些覆盖项。

运行时用 /tasks 显示的型号、宿主任务元数据或本次会话可核对的运行记录确认配置；仅文件通过校验不代表真实推理已成功。必须区分 haiku/sonnet/opus 别名与实际解析版本，缺少强度元数据时不声称已验证强度。实际配置违反用户硬限制时停止对应工作。

## 安装与调用

个人 skill 位于 ~/.claude/skills/auto-model/SKILL.md，可复制本 skill 的完整目录，或用宿主支持的目录链接指向唯一来源。四个代理模板分别复制到 ~/.claude/agents/，保留其原有权限模式，不设置 bypassPermissions，不预加载 auto-model 以避免递归调度。

Claude Code 使用 /auto-model 加任务描述；Codex 使用 $auto-model。普通 Claude 网页聊天不能据此获得 Claude Code 的 Agent 工具。个人本地安装也不会自动同步至 Cowork 或云端。

安装前检查同名目标，已有内容不覆盖，除非用户已授权更新。校验时把父目录传给 CLI，例如 claude plugin validate ~/.claude/skills --strict 和 claude plugin validate ~/.claude/agents --strict，不传 auto-model 根目录或单个 .md。当前 2.1.283 会跳过链接目录：只验证本 skill 时，将 SKILL.md 的实体副本放进隔离的 skills/auto-model/，四个代理模板放进隔离的 agents/，分别校验这两个父目录，避免扫描其他人的配置。校验不触发模型推理，也不能代替真实调用验证。

维护依据：[Skills](https://code.claude.com/docs/en/skills)、[Subagents](https://code.claude.com/docs/en/sub-agents)。以当前安装版本及工具能力为准，不要求每次开发联网读取。
