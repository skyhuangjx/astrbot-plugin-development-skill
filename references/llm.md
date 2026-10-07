# LLM

读取条件：插件需要调用当前会话模型、读取模型响应，或观察/修改默认 LLM 请求。

## 推荐调用

```python
umo = event.unified_msg_origin
provider_id = await self.context.get_current_chat_provider_id(umo=umo)
resp = await self.context.llm_generate(
    chat_provider_id=provider_id,
    prompt="...",
)
```

- provider 由当前会话决定，不写死 provider、模型、会话 ID 或 API Key。
- provider 缺失、超时、空响应、限流和取消都要有用户可理解的降级结果。
- 只有需要 Agent 循环或 Tool 时才读取 `agent-tools.md`。
- 默认不要自行维护完整对话历史；需要修改历史时使用官方 Conversation Manager，并确认版本兼容。

## LLM 钩子

`on_llm_request`、`on_llm_response`、`on_agent_begin`、`on_agent_done` 等钩子会影响全局流程，只有确有需求时才使用。钩子中发送消息通常使用 `await event.send()`，不能使用 `yield`。

每轮变化的上下文优先放入 `extra_user_content_parts`；稳定规则才放进 `system_prompt`。只希望本轮生效的内容，在支持的版本中标记为临时内容，避免污染会话历史和破坏提示词缓存。

## 验证

验证 provider 缺失、响应为空、异常、超时、取消以及多个会话同时请求。不要把完整 prompt 或模型回复写入日志。

参考：[AI 插件指南](https://docs.astrbot.app/dev/star/guides/ai.html)、[事件钩子](https://docs.astrbot.app/dev/star/guides/listen-message-event.html)。
