# WebUI 视图和插件 API

读取条件：插件需要复杂配置、运行状态面板、日志、文件上传下载、SSE 或自定义交互页面。

## 结构和 API

- 当前推荐 `views/<view_name>/index.html`；`pages/` 仅作兼容名称。
- 页面按钮来自 AstrBot 对插件视图目录的发现，不由 `context.register_web_api()` 自动生成。注册 Web API 只提供页面调用的后端接口。
- 开发前必须按目标 AstrBot 版本确认视图目录和发现规则；至少验证插件详情页是否出现视图入口、页面是否加载、bridge 是否可用、页面 API 是否能返回。
- 后端用 `context.register_web_api()` 注册路由，优先使用 `astrbot.api.web` 的 `request`、`json_response`、`error_response`、`file_response` 和 `stream_response`。
- 前端通过 `window.AstrBotPluginView` bridge 调用插件 API；不要绕过 bridge 读取 Dashboard 身份或拼接内部 URL。
- SSE、上传、下载和视图语言/主题切换都要在页面卸载时清理。

## 安全

受限 iframe 不替代后端校验。后端必须校验 JSON、query、动态路径、文件名、上传大小、格式和数值范围；文件使用安全目录和安全文件名。不要信任视图传来的路径、URL 或操作权限。

## 验证

测试视图发现和加载、插件详情页入口、API 路由前缀、bridge 初始化、空/非法请求、文件上传、下载、SSE 断开和重复订阅。修改视图目录后重载插件，修改静态资源后刷新视图。

页面中需要让用户填写 AstrBot 字段时，同时显示中文名称和内部字段名，例如：

```text
平台实例 ID（platform_id）
会话类型（message_type）
会话 ID（session_id）
```

不要把 `unified_msg_origin` 当作其中任一字段；它是组合值，格式通常为
`platform_id:message_type:session_id`。

参考：[插件可视化视图](https://docs.astrbot.app/dev/star/guides/plugin-pages.html)。
