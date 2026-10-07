# 本地 I/O 和外部服务

读取条件：插件访问文件、网络、子进程、浏览器渲染、系统命令或后台任务。

## 规则

- 使用异步网络库（如 `aiohttp`、`httpx`），设置连接和总超时，处理取消、限流、空响应和非 2xx 响应。
- 不使用 `requests` 或其他长时间阻塞事件循环的调用。同步库只能放在明确隔离的线程/进程边界。
- 外部 URL、重定向、文件路径、文件名、压缩包和命令参数全部当作不可信输入；防止 SSRF、路径穿越、命令注入和资源耗尽。
- 子进程必须使用参数数组、超时、输出上限和取消清理；不要把用户文本直接拼进 shell 命令。
- `asyncio.create_task()` 返回的任务要保存并在 `terminate` 中取消和等待。HTTP client、浏览器、临时目录和文件句柄也要关闭。
- 大文件存放在 AstrBot 数据目录或 `data/plugin_data/{plugin_name}`，不要写入会随插件更新覆盖的源码目录。

## 验证

测试超时、取消、网络断开、异常状态码、空文件、超大文件、非法路径和重复启动。没有真实外部服务时使用小型 fake 或本地 mock，并说明未做真实验证。

参考：[插件开发指南](https://docs.astrbot.app/dev/star/plugin-new.html)、[插件存储](https://docs.astrbot.app/dev/star/guides/storage.html)。
