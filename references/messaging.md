# 消息和事件

读取条件：插件接收消息、注册命令、监听事件、主动发送消息或改变事件传播。

## 选择接口

- 普通命令使用 `@filter.command`；复杂命令使用 `command_group`、参数注入和别名。
- 按私聊/群聊使用 `event_message_type`，按平台使用 `platform_adapter_type`，管理员操作使用 `permission_type`。
- 需要影响所有消息或 LLM 流程时才使用事件钩子；钩子不能随意和命令过滤器混用。
- 需要阻止后续插件或默认 LLM 流程时调用 `event.stop_event()`，并只在明确需要时使用。

## 消息和发送

- 从 `AstrMessageEvent` 读取 `message_str`、发送者、群组、平台和 `unified_msg_origin`。
- 被动回复用 `yield event.plain_result(...)`、`image_result(...)` 或 `chain_result(...)`。
- 钩子、会话等待器和后台任务中使用 `await event.send(...)`，不要错误地使用 `yield`。
- 主动消息使用 `context.send_message(unified_msg_origin, chain)`；先确认目标平台支持主动消息和对应消息段。
- 富媒体使用 `astrbot.api.message_components`，不要假设所有平台支持 At、图片、语音、视频或转发。

## 验证

至少测试命令参数为空、权限失败、重复事件、事件停止传播和目标平台不支持消息段的情况。平台专属逻辑要先检查 `event.get_platform_name()` 或声明 `support_platforms`。

参考：[事件指南](https://docs.astrbot.app/dev/star/guides/listen-message-event.html)、[发送消息](https://docs.astrbot.app/dev/star/guides/send-message.html)。
