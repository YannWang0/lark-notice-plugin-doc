# Pipeline 项目

以下示例适用于在 `Pipeline` 中调用 `wechatWork` 步骤发送消息。

`robot` 表示机器人 ID，可在 `Lark Notice` 机器人配置中查看。

> `LINK`、`POST` 会回退为 `MARKDOWN` 发送，`IMAGE` 和 `SHARE_CHAT` 暂不支持。

## 1. 文本消息

用于发送纯文本通知，并支持通过 `ats` 指定提醒对象。

```groovy
pipeline {
    agent any
    stages {
        stage('text') {
            steps {
                echo '发送企业微信文本消息...'
            }
            post {
                success {
                    wechatWork(
                        robot: 'robot-wecom',
                        type: 'TEXT',
                        text: [
                            '构建完成通知',
                            '请相关同学关注 Jenkins 结果'
                        ],
                        ats: [
                            'zhangsan',
                            '13800138000'
                        ]
                    )
                }
            }
        }
    }
}
```

## 2. Markdown 消息

企业微信推荐使用 `MARKDOWN` 展示结构化构建信息。

```groovy
pipeline {
    agent any
    stages {
        stage('markdown') {
            steps {
                echo '发送企业微信 Markdown 消息...'
            }
            post {
                success {
                    wechatWork(
                        robot: 'robot-wecom',
                        type: 'MARKDOWN',
                        title: 'Jenkins 构建通知',
                        text: [
                            ">**任务名称**: [${JOB_NAME}](${JOB_URL})",
                            ">**任务编号**: [${BUILD_DISPLAY_NAME}](${BUILD_URL})",
                            ">**构建状态**: <font color=\"info\">${currentBuild.currentResult}</font>",
                            ">**构建用时**: ${currentBuild.duration} ms",
                            ">**执行人**: ${env.BUILD_USER}"
                        ],
                        ats: [
                            'zhangsan'
                        ]
                    )
                }
            }
        }
    }
}
```

## 3. 卡片消息

适用于构建汇总通知，会发送为 `template_card.news_notice` 模板卡片。

卡片内容行默认从构建上下文渲染（任务名称、任务编号、构建状态、构建用时、执行人），也可以用 `cardFields` 完全接管。每行三个字段：`keyname` 行标签（建议不超过 5 个字）、`value` 行内容（建议不超过 26 个字，为空则该行丢弃）、`url` 可选，填了该行就变成跳转行。

下面的示例用 `cardFields` 复刻了默认的五行，可在此基础上增删改：

```groovy
pipeline {
    agent any
    stages {
        stage('card') {
            steps {
                echo '发送企业微信卡片消息...'
            }
            post {
                success {
                    wechatWork(
                        robot: 'robot-wecom',
                        type: 'CARD',
                        title: 'Jenkins 构建通知',
                        text: [
                            '本次构建执行完成，请查看变更记录和控制台日志。'
                        ],
                        messageUrl: "${BUILD_URL}",
                        picUrl: 'https://www.jenkins.io/images/logos/jenkins/jenkins.png',
                        sourceDesc: 'Jenkins CI',
                        quoteTitle: '发布说明',
                        quoteText: "${BUILD_DISPLAY_NAME} 已完成构建",
                        quoteUrl: "${BUILD_URL}changes",
                        cardFields: [
                            [
                                keyname: '任务名称',
                                value: "${JOB_NAME}",
                                url: "${JOB_URL}"
                            ],
                            [
                                keyname: '任务编号',
                                value: "${BUILD_DISPLAY_NAME}",
                                url: "${BUILD_URL}"
                            ],
                            [
                                keyname: '构建状态',
                                value: "${currentBuild.currentResult}"
                            ],
                            [
                                keyname: '构建用时',
                                value: "${currentBuild.durationString}"
                            ],
                            [
                                keyname: '执行人',
                                value: "${env.BUILD_USER}"
                            ]
                        ],
                        buttons: [
                            [
                                title: '更改记录',
                                url: "${BUILD_URL}changes"
                            ],
                            [
                                title: '控制台',
                                url: "${BUILD_URL}console"
                            ]
                        ]
                    )
                }
            }
        }
    }
}
```

> 1. 设置 `cardFields` 后默认行全部不再渲染，最多 6 行（企业微信限制「列表长度不超过6」），超出的会被丢弃。
> 2. 未配置 `buttons` 时默认补充 `更改记录` 和 `控制台`，最多显示 3 个。
> 3. `picUrl` 需为公网可访问的 `http/https` 图片地址，否则使用内置图片。
> 4. 设置 `text` 后它会作为卡片底部正文渲染；未设置时，若已有内容行则不再渲染正文块。
> 5. `${VAR}` 用双引号由 `Groovy` 求值，用单引号则原样交给插件在发送前用构建环境变量展开。
> 6. `执行人` 默认取自构建原因；示例中的 `${env.BUILD_USER}` 需要安装 `Build User Vars` 插件。
> 7. `sourceDesc` 可自定义卡片头部来源文案（`source.desc`），留空时使用内置文案。
> 8. `quoteTitle`、`quoteText`、`quoteUrl` 对应引用区块（`quote_area`）；三者都不填时整个区块不渲染，填了 `quoteUrl` 该区块变为可点击。
