# Freestyle 项目

以下示例适用于 `Freestyle` 任务中通过插件界面配置企业微信机器人通知的场景。

## 1. 机器人配置

在全局配置中添加企业微信机器人后，可在 Freestyle 任务中选择对应机器人。

企业微信机器人推荐直接填写群机器人 `key`，插件会自动拼接为：

```text
https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key={key}
```

如果你已经拿到完整 Webhook，也可以切换到 `自定义` 模式直接粘贴。

## 2. 默认卡片模式

Freestyle 任务默认使用插件内置的构建摘要卡片。企业微信平台会将其渲染为 `template_card.news_notice` 模板卡片，自动展示：

- 项目名称和项目链接
- 构建编号和构建链接
- 构建状态
- 构建耗时
- 执行人
- 最多 3 个跳转按钮

如果未手动配置按钮，插件会默认生成 `更改记录` 和 `控制台` 两个跳转按钮。

::: tip 界面可配的范围
`Freestyle`（以及构建后步骤）只提供标题模板、补充说明、`@` 提醒与通知时机；卡片头部的来源文案与图标、图文位置的图片、以及上面那五行构建信息都是内置的，不在界面上开放配置。需要改这些内容时使用 `Pipeline` 的 `wechatWork` 步骤，对应参数为 `sourceDesc`、`sourceIconUrl`、`cardImageUrl` 与 `cardFields`。
:::

## 3. 自定义消息模式建议

企业微信 `Freestyle` 任务当前更推荐使用默认卡片模式。

原因是企业微信平台的消息能力和飞书 / Lark 不同，`Freestyle` 下的 `自定义消息` 体验也不等同于飞书那种“原始 Markdown / JSON 卡片编辑器”。如果你需要精确控制正文结构、按钮、图片或提醒对象，优先使用 `Pipeline` 中的 `wechatWork` 步骤。

## 4. 使用建议

- 需要 `@` 提醒时，优先在任务配置里填写用户 ID 或手机号，交给插件统一处理。
- 需要自定义卡片图片时，改用 `Pipeline` 的 `wechatWork` 步骤并设置 `cardImageUrl`，地址需为公网或 Jenkins 所在网络可直接访问的 `http/https` 图片。
- 如果你需要发送特别复杂的卡片布局，企业微信平台能力有限，建议改用 `Pipeline` 的 `wechatWork` 步骤，或迁移到飞书 / Lark 机器人。
