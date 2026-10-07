---
name: astrbot-plugin-development
description: Develop, review, package, or maintain AstrBot plugins using current official APIs and market conventions. Use when the task involves plugin code, metadata, configuration, lifecycle, messaging, LLM/Agent integration, interactive sessions, WebUI views, testing, or release preparation; do not use for ordinary AstrBot bot configuration.
---

# AstrBot 插件开发

本 skill 是一个按需加载的开发流程。它面向中文插件开发者，默认假设开发者使用 Python，并以当前 AstrBot 源码和官方文档为准。

## 读取规则

先读本文件，再按需求读取对应 reference。不要为了一个简单插件读取所有 reference，也不要把未使用的能力加入代码、配置或验证。

| 需求命中 | 读取 |
| --- | --- |
| 命令、事件、消息回复 | `references/messaging.md` |
| 本地文件、网络、子进程、定时任务 | `references/local-io.md` |
| 配置、持久化、依赖 | `references/config-storage.md` |
| 调用 LLM | `references/llm.md` |
| Tool、Agent、多智能体 | `references/agent-tools.md` |
| 多轮等待、确认、用户介入 | `references/interactive-session.md` |
| WebUI 页面或插件后端 API | `references/webui.md` |
| 国际化或插件随附 Skill | `references/i18n-skills.md` |
| 市场发布、打包或市场源 | `references/market-release.md` |

如果已经确认目标版本、所需 API、兼容边界和最小验证路径，就停止搜索无关文档和源码。

## 任务分流

开始实现前，给任务标记一个或多个能力：

```text
command/event, local_io, config, storage, llm,
agent_tool, interactive_session, webui, external_network,
packaging/release
```

同时确认：

- 目标 AstrBot 版本、Python 版本、部署方式和目标平台。
- 任务是新建、修改、审查、打包还是发布。
- 是否已有插件仓库、未提交改动和用户配置。
- 是否需要真实 AstrBot 运行验证。

缺少信息时做最小、明确的假设，并把假设写进交付报告。不要自动扩大到 GitHub 推送、Release 或市场提交。

## API 稳定性分层

1. `astrbot.api.*`：优先使用，作为插件公共接口。
2. 官方当前文档明确示例中的 `astrbot.core.*`：可以使用，但标注最低版本并做版本测试。当前会话控制和 Agent Tool 文档包含此类接口。
3. 未在官方文档或当前源码确认的内部 API：只有在公共接口无法满足需求时才使用；隔离在适配层，启动时探测，失败时安全停用，卸载时恢复。

不要凭旧示例猜 API。先查看目标版本源码或官方文档，记录 API 的版本门槛、异步/生成器形态和降级路径。

## 核心不变量

- 插件入口为 `main.py`，继承 `Star`，使用官方 `Context`、事件和 logger。
- 第三方依赖放在插件根目录的 `requirements.txt`；不要在插件启动时执行 `pip install`。
- 长时间网络、文件和计算操作不能阻塞事件循环；网络请求设置超时并处理取消、空响应和重试边界。
- `initialize` 做轻量能力检查和资源初始化；`terminate` 清理任务、会话、客户端、订阅、临时文件和必要的补丁。
- 每个后台任务都要可追踪、可取消；插件重载不得重复注册、重复包装或留下任务。
- 按 `unified_msg_origin`、用户、群组或 provider 隔离状态；不要用一个全局开关影响所有会话。
- API Key、Cookie、Authorization、完整消息、完整模型回复和完整 session ID 不进入日志。配置项的 `secret` 只遮罩 WebUI，不等于文件加密。
- 用户输入、LLM 输出、Tool 参数、上传文件名和外部 URL 都是不可信数据；进入文件、命令、网络或管理操作前必须校验。
- 高风险或不可逆副作用按风险提供关闭、权限、预览、确认、取消、超时和幂等路径。

## 最小插件结构

按实际需要添加文件，不要生成空目录：

```text
plugin-root/
├── main.py
├── metadata.yaml
├── requirements.txt       # 需要第三方依赖时
├── _conf_schema.json       # 需要 WebUI 配置时
├── README.md
├── LICENSE
├── skills/                 # 插件随附 Skill 时
├── views/                  # 插件 WebUI 视图时
└── .astrbot-plugin/i18n/   # 插件国际化时
```

`astrbot_plugin_` 是推荐命名，不是所有场景的硬性条件。`display_name`、`short_desc`、`logo.png`、`tags`、`support_platforms` 和 `astrbot_version` 按需添加。`astrbot_version` 使用 PEP 440 约束且不带 `v` 前缀。

插件身份是 `author/name`。不要把展示名、仓库地址或本地目录名当作市场身份；发布后保持 `author` 和 `name` 稳定。

## 默认工作流

1. 阅读目标版本的官方开发指南、目标能力对应的 reference，并检查仓库当前状态。
2. 选择最小官方扩展点，建立“能力、API、最低版本、风险、验证”表。
3. 先实现正常路径，再补空输入、异常、超时、取消、重复事件、并发和禁用路径。
4. 只为实际使用的能力添加配置、依赖、文档和测试。
5. 按风险执行最小充分验证；没有真实运行环境时，明确区分语法/模拟验证和运行时验证。
6. 交付时报告文件、支持版本、已验证和未验证内容、用户配置/重启要求，以及是否执行外部发布。

## 验证基线

至少根据任务运行：

- `python -m compileall` 或等价的语法/导入检查。
- `_conf_schema.json` 的 JSON 检查和 `metadata.yaml` 的 YAML 检查。
- `ruff format --check`、`ruff check`（项目允许时）。
- 正常、异常、空输出、配置关闭和依赖缺失路径。
- 涉及状态时的多用户/多群组/provider 隔离。
- 涉及重载时的注册、任务、会话和补丁清理。
- 涉及流式响应时的首 chunk、最终响应、错误和中途取消。
- 涉及 WebUI、文件、命令或外部网络时的输入边界和路径安全。
- 涉及发布时的市场身份、包根目录、依赖文件和压缩包大小检查。

不要为了测试覆盖无关模块，也不要重置或覆盖用户已有配置。

## 外部发布边界

GitHub push、Release、插件市场提交和向外部服务发送数据都属于外部变化。除非用户已经明确要求，不执行这些动作；可以完成本地代码、检查、打包和可审阅的草稿。

## 官方参考

- [AstrBot 源码](https://github.com/AstrBotDevs/AstrBot)
- [插件开发指南](https://docs.astrbot.app/dev/star/plugin-new.html)
- [插件指南索引](https://docs.astrbot.app/dev/star/plugin.html)
- [插件市场规范](https://docs.astrbot.app/dev/plugin-market/2026-06-27.html)
- [helloworld 模板](https://github.com/Soulter/helloworld)

仅在对应能力被任务命中时读取下面的 reference：

- [消息和事件](references/messaging.md)
- [本地 I/O 和外部服务](references/local-io.md)
- [配置、存储和依赖](references/config-storage.md)
- [LLM](references/llm.md)
- [Agent 和 Tool](references/agent-tools.md)
- [互动会话](references/interactive-session.md)
- [WebUI](references/webui.md)
- [国际化和插件 Skills](references/i18n-skills.md)
- [市场、打包和发布](references/market-release.md)
