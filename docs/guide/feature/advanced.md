# 高级配置

## 1. Webhook 端点模式

添加机器人时，插件支持两种 Webhook 地址配置方式：

| 模式 | 说明 | 适用场景 |
|------|------|---------|
| 平台默认模式 | 选择平台后只输入 `Token` 或 `Key`，插件自动拼接官方 Webhook | 官方 SaaS 地址，优先推荐 |
| 自定义 Webhook | 直接粘贴完整 Webhook URL | 已拿到完整地址、使用私有域名、历史配置迁移 |

选择平台后，机器人编辑器会自动切换输入提示、预览最终 Webhook，并记录对应协议类型。

> [!TIP]
> `Lark / 飞书` 支持自定义域名，只要 Webhook 路径仍为 `/open-apis/bot/v2/hook/{token}`。

不同平台的默认输入内容如下：

| 平台 | 默认输入值 | 自动拼接结果 |
|------|-----------|-------------|
| Lark / 飞书 | 机器人 `Token` | `https://open.larksuite.com/open-apis/bot/v2/hook/{token}` 或 `https://open.feishu.cn/open-apis/bot/v2/hook/{token}` |
| 钉钉 | 机器人 `access_token` | `https://oapi.dingtalk.com/robot/send?access_token={token}` |
| 企业微信 | 群机器人 `key` | `https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key={key}` |

## 2. 安全策略

每个机器人都可配置以下安全策略：

| 策略 | 说明 |
|------|------|
| **关键词（KEY）** | 设定后，只有包含关键词的消息内容才会被正常发送，多个值使用逗号分隔。 |
| **签名密钥（SECRET）** | 使用 HMAC-SHA256 对消息进行签名验证。需在机器人安全设置中获取密钥。 |
| **禁用 SSL 验证（NO_SSL）** | 禁用 Java HTTP 客户端中的 SSL/TLS 证书验证。 |

> [!NOTE]
> 企业微信群机器人本身没有飞书 / 钉钉那种签名验签流程，但插件仍支持关键词校验与 SSL 控制等通用策略。

> [!WARNING]
> 禁用 SSL 验证会降低连接的安全性，可能导致中间人攻击等安全风险。建议仅在受控测试环境中使用此选项。

## 3. 重试配置

插件支持对发送失败的 Webhook 消息进行重试，每个机器人可单独配置。默认不开启。

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `enabled` | boolean | `false` | 是否启用重试 |
| `maxAttempts` | int | `1` | 最大尝试次数（包含首次发送） |
| `initialDelayMs` | long | `500` | 首次重试前的等待时间（毫秒） |
| `maxDelayMs` | long | `5000` | 重试等待时间上限（毫秒） |
| `backoffMultiplier` | double | `2.0` | 退避倍数（必须 >= 1.0） |
| `jitterRatio` | double | `0.2` | 等待时间抖动比例（0.0 ~ 1.0） |

重试采用指数退避策略，每次重试的等待时间 = 上次等待 × `backoffMultiplier`，并叠加随机抖动以避免请求风暴。

高级选项（`initialDelayMs`、`maxDelayMs`、`backoffMultiplier`、`jitterRatio`）默认折叠，点击「高级」可展开配置。

## 4. 消息语言

每个机器人可独立设置消息语言，影响内置卡片标题和默认模板的显示语言。

| 选项 | 说明 |
|------|------|
| 中文 | 使用简体中文显示内置消息 |
| EN | 使用英文显示内置消息 |

> [!NOTE]
> 仅影响内置卡片标题与默认模板，自定义消息内容不做翻译。

### 4.1 平台消息能力差异

插件对外统一暴露消息类型，但每个平台只接受其中一部分。传入平台不接受的类型会直接失败并给出提示。

| 消息类型 | Lark / 飞书 | 钉钉 | 企业微信 |
|---|---|---|---|
| `TEXT` | ✅ | ✅ | ✅ |
| `MARKDOWN` | ✅ | ✅ | ✅ |
| `CARD` | ✅ | ✅ | ✅ |
| `IMAGE` | ✅ | — | — |
| `SHARE_CHAT` | ✅ | — | — |
| `POST` | ✅ | — | ⚠️ 按 `MARKDOWN` 发送 |
| `LINK` | — | ✅ | ⚠️ 按 `MARKDOWN` 发送 |
| `FEED_CARD` | — | ✅ | — |

Lark / 飞书另外支持在 `Freestyle` 中粘贴自定义 JSON 卡片。

各平台参数的细节说明见对应的 Pipeline 页面：[Lark / 飞书](./lark/pipeline)、[钉钉](./ding/pipeline)、[企业微信](./wechat-work/pipeline)。

## 5. 代理配置

当 Jenkins 无法直连机器人 Webhook 地址时，可在全局配置中设置网络代理。

| 字段 | 说明 |
|------|------|
| `enabled` | 是否启用代理 |
| `type` | 代理类型：`HTTP` 或 `SOCKS` |
| `host` | 代理服务主机，如 `127.0.0.1` |
| `port` | 代理服务端口，如 `1080` |

代理配置位于 `Manage Jenkins` -> `Lark Notice` 页面中的「高级设置」区域。

## 6. 详细日志

在 `Manage Jenkins` -> `Lark Notice` 页面中的「高级设置」区域，可开启 `调试日志` 选项。

开启后，插件会在 Jenkins 系统日志中输出更详细的请求和响应信息，适用于排查消息发送失败等问题。

> [!TIP]
> 排查完毕后建议关闭详细日志，避免日志量过大。

## 7. 通知触发时机

在 `Manage Jenkins` -> `Lark Notice` 页面中的「通知时机」区域，可设置默认通知触发时机；每个任务也可在任务配置中单独覆盖。

| 时机 | 说明 |
|------|------|
| 构建开始（START） | 构建启动时触发 |
| 构建成功（SUCCESS） | 构建成功完成时触发 |
| 构建失败（FAILURE） | 构建失败时触发 |
| 构建不稳定（UNSTABLE） | 构建结果为不稳定时触发 |
| 构建中止（ABORTED） | 构建被手动或自动中止时触发 |
| 未构建（NOT_BUILT） | 构建未执行时触发 |

## 8. 配置导入导出

插件支持通过 `Manage Jenkins` -> `Lark Notice` 页面右上角的「打开迁移工具」导出或导入 JSON 配置快照。此功能可用于：

- 在多个 Jenkins 实例之间同步机器人配置
- 备份和恢复配置
- 通过脚本批量管理配置

配置快照包含默认通知时机、机器人、安全策略、重试配置、代理设置等信息，导入时会校验数据格式版本。导入前需要先执行预览，确认新增、更新、移除和保留的机器人数量后再应用。
