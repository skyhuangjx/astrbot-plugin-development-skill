# 市场、打包和发布

读取条件：插件需要发布到 AstrBot 市场、生成可安装 ZIP、维护市场源或审查市场记录。

## metadata

至少核对 `author`、`name`、`version`、`repo`、`desc`。按需添加 `display_name`、`short_desc`、`tags`、`support_platforms`、`astrbot_version`、`social_link`。`repo` 应为合法 HTTPS GitHub 仓库地址。

市场身份为：

```text
plugin_id = author + "/" + name
```

仓库名、本地目录名、展示名和 `repo` 都不是身份。安装后必须校验包内 `metadata.yaml.author/name` 与市场记录一致。

## 市场源

新的市场源根对象必须有 `$meta.schema_version: 1`。每条记录必须包含 `author`、`name`、`version`、`repo`、`desc`；根 key 通常是 `author/name`。不要输出已弃用的 `support_platform` 或 `platform`。

更新检测使用安装时绑定的 registry source；非市场安装不要自动视为市场安装或启用市场更新。

## 打包

- ZIP 根目录必须直接包含插件文件和 `metadata.yaml`，不能多套一层仓库目录。
- 清理 `.git`、`__pycache__`、`node_modules`、临时配置、密钥、Cookie、日志和本机路径。
- 市场压缩包通常不得超过 16MB；发布前检查大小和实际内容。
- README 说明用途、安装、配置、兼容版本、限制、隐私和卸载方式。

除非用户明确要求，不执行 push、Release 或市场提交；可以完成本地打包和可审阅的发布清单。

参考：[插件发布](https://docs.astrbot.app/dev/star/plugin-publish.html)、[市场 JSON 规范](https://docs.astrbot.app/dev/plugin-market/2026-06-27.html)。
