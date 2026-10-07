# 国际化和插件 Skills

读取条件：插件需要多语言 WebUI 文案，或希望随插件提供可被 Skill Manager 加载的 Skill。

## 国际化

语言文件放在：

```text
.astrbot-plugin/i18n/zh-CN.json
.astrbot-plugin/i18n/en-US.json
```

使用嵌套 JSON。可覆盖 metadata、配置项和 `views` 文案；缺失翻译回退到 `metadata.yaml`、`_conf_schema.json` 或页面 fallback。不要使用扁平点号 key。

## 插件 Skills

多个 Skill 使用：

```text
skills/
├── skill-a/SKILL.md
└── skill-b/SKILL.md
```

单个 Skill 也可以直接放在 `skills/SKILL.md`。插件提供的 Skill 由插件管理，在 WebUI 中可启用或禁用，插件卸载或更新时随插件文件变化。

只在 Skill 内容确实属于该插件且需要随插件版本发布时使用此机制；不要把普通 README 或开发说明放进 `skills/`。

参考：[插件国际化](https://docs.astrbot.app/dev/star/guides/plugin-i18n.html)、[插件开发指南](https://docs.astrbot.app/dev/star/plugin-new.html)。
