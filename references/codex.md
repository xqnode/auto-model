# Codex 适配

仅在 Codex 宿主读取本文件。沿用已有混合方案：

| 角色 | 请求型号 | reasoning_effort |
| --- | --- | --- |
| 执行 | gpt-6-luna | high |
| 开发 | gpt-6.1-sol | medium |
| 判断 | gpt-6.1-sol | high |
| 专家 | gpt-6-astra | low 起步 |

型号是否可用以当前工具列出的模型为准。Luna 不可用时明确执行任务可用 Sol low；不要为此启动 Astra。

- 使用宿主的 spawn_agent 等代理接口，不使用用户侧 create_thread 代替子代理。
- 显式传 model 与 reasoning_effort；在仅独立上下文允许覆盖配置的环境，同时传 fork_turns="none" 并交接完整约束。不得把 Claude 的 effort、subagent_type 参数照搬到 Codex。
- 若只有默认型号且标识未公开，可以省略 model 并设置支持的强度，不捏造型号。
- 以 send_message、followup_task、wait_agent、interrupt_agent、list_agents 或宿主实际提供的等效接口管理生命周期。原型号与强度不匹配时新建合适代理，不假定 followup 会重新配置。
- 调用返回代理名不等于确认型号。真实调度验收时核对宿主提供的运行元数据；本地 Codex 可读取本次子会话 session_meta 的父会话/代理路径，以及 turn_context 的 model、effort。只查本任务对应记录，输出必要元数据，不导出完整聊天。
- 主代理型号不由本 skill 改写。主会话若为 Astra，常规任务仍优先委派 Luna/Sol。

维护时参阅 [OpenAI 子代理文档](https://learn.chatgpt.com/docs/agent-configuration/subagents)。不要求每次开发联网读取。
