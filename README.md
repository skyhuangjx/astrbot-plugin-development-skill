# 基于 AstrBot 的插件开发 Skill（中文）

这是一个面向中文开发者和大模型的 Codex Skill，用于开发、审查、测试、打包和维护 [AstrBot](https://github.com/AstrBotDevs/AstrBot) 插件。

它采用渐进式读取：简单插件只读取核心规则和消息模块；只有在需求涉及对应能力时，才读取 LLM、Agent、互动会话、WebUI、国际化或市场发布模块。

## 适用范围

- 命令、事件监听和消息回复
- 本地文件、网络请求和外部服务
- 插件配置、依赖和持久化存储
- LLM 调用、Tool、Agent 和多智能体
- 多轮互动、用户确认和会话控制
- WebUI 视图、插件 API、SSE 和文件上传下载
- 国际化和随插件提供 Skills
- 插件测试、打包和发布到 AstrBot 市场

## 目录结构

```text
astrbot-plugin-development-skill/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── messaging.md
    ├── local-io.md
    ├── config-storage.md
    ├── llm.md
    ├── agent-tools.md
    ├── interactive-session.md
    ├── webui.md
    ├── i18n-skills.md
    └── market-release.md
```

## 在 Codex 中使用

### 本地安装

```bash
git clone https://github.com/skyhuangjx/astrbot-plugin-development-skill.git \
  ~/.codex/skills/astrbot-plugin-development
```

然后在任务中说明：

```text
请使用 astrbot-plugin-development skill 开发这个 AstrBot 插件。
```

### 直接提供链接

```text
请读取并使用这个 Skill：
https://github.com/skyhuangjx/astrbot-plugin-development-skill

我要开发一个……
```

简单任务只需要入口文件；复杂任务会根据需求读取 `references/` 中的对应文档。

## 设计原则

- 优先使用 AstrBot 官方扩展点和当前版本文档。
- 只加载和实现任务实际需要的能力。
- 将用户输入、模型输出、Tool 参数和文件路径视为不可信数据。
- 关注插件重载、异步任务、状态隔离、超时和资源清理。
- 把语法检查、配置检查、运行验证和市场发布检查区分开。

## 相关文档

- [AstrBot 插件开发指南](https://docs.astrbot.app/dev/star/plugin-new.html)
- [AstrBot AI 插件指南](https://docs.astrbot.app/dev/star/guides/ai.html)
- [AstrBot 会话控制](https://docs.astrbot.app/dev/star/guides/session-control.html)
- [AstrBot 插件市场规范](https://docs.astrbot.app/dev/plugin-market/2026-06-27.html)
- [AstrBot 插件模板](https://github.com/Soulter/helloworld)

## 许可证

本项目采用 [MIT License](LICENSE)。
