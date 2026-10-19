# Claude Code 适配

仅在 Claude Code 宿主读取本文件。默认映射：

| 角色 | 代理 | model | effort |
| --- | --- | --- | --- |
| 执行 | auto-model-executor | haiku | 不固定，沿用模型默认值 |
| 开发 | auto-model-developer | sonnet | medium |
| 判断 | auto-model-judge | sonnet | high |
| 专家 | auto-model-expert | opus | medium 起步 |

这不是与 Codex 的能力或强度等价映射。别名解析到哪个版本取决于账号、供应商和配置；用户指定完整型号时用指定值，不用别名替换。

本仓库是 Claude Code 插件：skill 在根目录，四个代理在 [../agents/](../agents/)。按 README 安装后代理自动注册，不需要手动复制。

## 调度

- 用 Agent 工具，subagent_type 填上表的代理。通过插件市场安装时名字可能带插件前缀（如 `auto-model:auto-model-developer`）；以当前 Agent 工具列出的名字为准，不把未注册的名字当成已注册。
- 工具支持逐次传 model / effort 时可以显式指定，用户要求的完整型号 ID 优先；不传则用代理文件中的配置。工具定义里没有的字段不要传，也不要传 Codex 的 fork_turns 或 reasoning_effort。
- 不用 fork 类型代替角色代理：fork 沿用主会话型号，会绕过代理的型号配置。
- 代理不在可用列表里时，先说明加载问题（安装后需要新会话或 `/reload-plugins`）。若 Agent 工具支持直接指定型号，可以用通用代理承担同样的角色，并在交接里写明"完成后返回，不继续委派"。
- skill 保留在主会话执行，不设置 context: fork，以便主代理负责路由和整合。
- Haiku 不同版本对 effort 的支持不同，执行代理不固定 effort；确认支持后再按任务调整。
- 专家不可用时保留 Sonnet 判断角色，不自动换用其他供应商。

## 覆盖与核对

CLAUDE_CODE_SUBAGENT_MODEL_FORCE、组织允许列表、供应商别名和 hook 可能覆盖请求的型号，CLAUDE_CODE_EFFORT_LEVEL 可能覆盖请求的强度。只检查相关设置，不打印认证密钥或整个环境配置，也不擅自修改。

用 /tasks 显示的型号、宿主任务元数据或本会话可核对的运行记录确认实际配置。haiku / sonnet / opus 是别名，与实际解析版本分开记录；没有强度元数据时不声称已验证强度。实际配置违反用户硬限制时停止对应工作。

## 校验

`claude plugin validate <仓库根目录> --strict` 一次校验插件清单、skill 和代理。校验不触发模型推理，不能代替真实调用验证。

维护依据：[Skills](https://code.claude.com/docs/en/skills)、[Subagents](https://code.claude.com/docs/en/sub-agents)、[Plugins](https://code.claude.com/docs/en/plugins)。以当前安装版本和工具能力为准，不要求每次开发联网读取。
