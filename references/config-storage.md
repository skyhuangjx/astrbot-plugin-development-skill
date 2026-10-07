# 配置、存储和依赖

读取条件：插件需要用户配置、密钥、持久化状态或第三方 Python 依赖。

## 配置

- 配置 schema 放在 `_conf_schema.json`，类型、默认值、描述和代码读取必须一致。
- `secret: true` 只负责 WebUI 遮罩；不要记录、回显或误称为加密存储。
- 配置更新要兼容缺失字段和旧值；为枚举、范围、列表长度和文件类型做代码侧校验。
- 复杂对象、`dict`、`template_list`、文件上传和 provider/persona 选择只在需求需要时使用。
- 配置国际化放在 `.astrbot-plugin/i18n`，不要把翻译 key 做成扁平点号结构。

## 存储

- 简单状态优先使用插件 KV 存储。
- 大文件和长期数据放在 `data/plugin_data/{plugin_name}` 或官方数据路径；不要把运行时数据写回插件源码目录。
- key、缓存和临时状态按用户/群组/会话隔离，定义清理和迁移策略。

## 依赖

- 第三方依赖写入插件根目录 `requirements.txt`，注明必要版本范围，避免无理由锁死平台相关版本。
- 不在 `initialize` 中在线安装依赖。缺依赖时给出清晰日志并安全停用相关能力。
- 依赖会影响安装包体积、Python 版本和平台兼容性，发布前检查这些约束。

参考：[插件配置](https://docs.astrbot.app/dev/star/guides/plugin-config.html)、[插件存储](https://docs.astrbot.app/dev/star/guides/storage.html)。
