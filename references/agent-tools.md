# Agent 和 Tool

读取条件：插件注册 LLM Tool、运行 Tool Loop Agent、多智能体，或让模型触发具有副作用的能力。

## 选择方式

- 需要手工控制 JSON Schema 时，定义 `FunctionTool` 并使用 `context.add_llm_tools()`。
- 简单工具可以使用 `@filter.llm_tool`，但必须按官方格式写 `Args:` docstring；函数类型注解不会自动生成完整参数 schema。
- `context.register_llm_tool()` 已弃用，不用于新插件。
- 调用 Agent 使用 `tool_loop_agent()`，设置合理的 `max_steps` 和 `tool_call_timeout`。

## 工具安全

- Tool 参数来自模型，必须重新做类型、范围、权限、路径和目标校验。
- 文件写入、网络请求、删除、发送消息、执行命令和修改配置属于副作用，提供权限、预览、确认、取消和幂等设计。
- 工具返回值应短、结构明确、避免泄露凭据；异常转成模型可理解的失败结果，不要吞掉日志中的诊断信息。
- 多 Agent 只在任务确实需要分工时使用；限制深度、步骤、并发和总成本。

## 验证

测试缺少参数、错误类型、恶意路径、重复调用、工具超时、达到最大步骤和中途取消。确认插件卸载后不会重复注册 Tool。

参考：[AI Tool 和 Agent 指南](https://docs.astrbot.app/dev/star/guides/ai.html)。
