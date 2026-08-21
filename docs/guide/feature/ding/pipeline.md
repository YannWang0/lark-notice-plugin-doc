# Pipeline 项目

以下示例适用于在 `Pipeline` 中调用 `dingTalk` 步骤发送消息。

`robot` 表示机器人 ID，可在 `Lark Notice` 机器人配置中查看。

## 1. 文本消息

用于发送纯文本通知，并支持通过 `ats` 指定提醒对象。

```shell
pipeline {
    agent any
    stages {
        stage('text'){
            steps {
                echo '发送文本消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'TEXT',
                        text: [
                            "新更新提醒",
                            '文本消息内容'
                        ],
                        ats: [
                            '186888888888'
                        ]
                    )
                }
            }
        }
    }
}
```

## 2. 链接消息

用于发送带标题、描述和跳转链接的通知消息。

```shell
pipeline {
    agent any
    stages {
        stage('link'){
            steps {
                echo '发送LINK消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'LINK',
                        title: '构建通知',
                        text: [
                            '新更新提醒',
                            '链接消息内容'
                        ],
                        messageUrl: 'https://www.baidu.com',
                        picUrl: 'https://p4.itc.cn/q_70/images03/20230512/32c7ad09b5904bea8506d74f96483000.png'
                    )
                }
            }
        }
    }
}
```
## 3. MD消息

用于发送 Markdown 格式消息，适合展示结构化文本内容。

```shell
pipeline {
    agent any
    stages {
        stage('markdown'){
            steps {
                echo '发送MARKDOWN消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'MARKDOWN',
                        title: '构建通知',
                        text: [
                            "## <font color='blue'>📢 Jenkins 构建通知</font>",
                            "---",
                            "📋 **任务名称**：[${JOB_NAME}](${JOB_URL})  ",
                            "🔢 **任务编号**：[${BUILD_DISPLAY_NAME}](${BUILD_URL})  ",
                            "🌟 **构建状态**: ${currentBuild.currentResult}  ",
                            "🕐 **构建用时**: ${currentBuild.duration} ms  ",
                            "👤 **执  行 者**: ${env.BUILD_USER}  ",
                            '![图片](https://p4.itc.cn/q_70/images03/20230512/32c7ad09b5904bea8506d74f96483000.png)  '
                        ],
                        ats: [
                          '186888888888'
                        ]
                    )
                }
            }
        }
    }
}
```

## 4. 卡片消息

适用于需要展示多个操作按钮的场景。

> 1. 如需垂直排列按钮，可设置 `verticalButton: true`。
> 2. 如需仅展示单个按钮，可使用 `singleTitle` 和 `singleUrl`。启用后，`buttons` 配置将失效。
> 3. `hideAvatar: true` 可隐藏消息发送者头像。
> 4. 也可以用 `cardFields` 代替手写正文行，见 [7. 自定义卡片内容行](#_7-自定义卡片内容行)。

```shell
pipeline {
    agent any
    stages {
        stage('card'){
            steps {
                echo '发送卡片消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'CARD',
                        title: '📢 Jenkins 构建通知',
                        text: [
                            "## <font color='blue'>📢 Jenkins 构建通知</font>",
                            "---",
                            "📋 **任务名称**：[${JOB_NAME}](${JOB_URL})  ",
                            "🔢 **任务编号**：[${BUILD_DISPLAY_NAME}](${BUILD_URL})  ",
                            "🌟 **构建状态**: ${currentBuild.currentResult}  ",
                            "🕐 **构建用时**: ${currentBuild.duration} ms  ",
                            "👤 **执  行 者**: ${env.BUILD_USER}  ",
                            '![图片](https://p4.itc.cn/q_70/images03/20230512/32c7ad09b5904bea8506d74f96483000.png)  '
                        ],
                        atAll: false,
                        ats: [
                          '186888888888'
                        ],
                        verticalButton: true,
                        buttons: [
                           [
                              title: "更改记录",
                              url: "${BUILD_URL}changes"
                           ],
                           [
                              title: "控制台",
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

## 5. 单按钮卡片消息

使用 `singleTitle` 和 `singleUrl` 可展示仅含单个跳转按钮的卡片。启用后 `buttons` 配置将失效。

```shell
pipeline {
    agent any
    stages {
        stage('single-card'){
            steps {
                echo '发送单按钮卡片消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'CARD',
                        title: '📢 Jenkins 构建通知',
                        text: [
                            "## <font color='blue'>📢 Jenkins 构建通知</font>",
                            "---",
                            "📋 **任务名称**：[${JOB_NAME}](${JOB_URL})  ",
                            "🔢 **任务编号**：[${BUILD_DISPLAY_NAME}](${BUILD_URL})  ",
                            "🌟 **构建状态**: ${currentBuild.currentResult}  ",
                            "🕐 **构建用时**: ${currentBuild.duration} ms  ",
                            "👤 **执  行 者**: ${env.BUILD_USER}  "
                        ],
                        atAll: false,
                        singleTitle: '查看详情',
                        singleUrl: "${BUILD_URL}"
                    )
                }
            }
        }
    }
}
```

## 6. 图文列表消息

`FEED_CARD` 发送一组「图片 + 标题」条目，每条有自己的跳转地址，适合把多条构建结果聚合成一条通知。

> 每个条目的 `title`、`messageUrl`、`picUrl` 均为必填，`picUrl` 需为公网可访问地址。

```shell
pipeline {
    agent any
    stages {
        stage('feed-card'){
            steps {
                echo '发送图文列表消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'FEED_CARD',
                        feedCardLinks: [
                            [
                                title: "构建成功 ${BUILD_DISPLAY_NAME}",
                                messageUrl: "${BUILD_URL}",
                                picUrl: 'https://www.jenkins.io/images/logos/jenkins/jenkins.png'
                            ],
                            [
                                title: '查看更改记录',
                                messageUrl: "${BUILD_URL}changes",
                                picUrl: 'https://www.jenkins.io/images/logos/jenkins/jenkins.png'
                            ]
                        ]
                    )
                }
            }
        }
    }
}
```

## 7. 自定义卡片内容行

`cardFields` 用结构化的方式描述卡片正文行，省去手写 Markdown。每行支持 `keyname`（行标签）、`value`（行内容）和可选的 `url`（填了该行变成链接），三个字段都支持环境变量。

钉钉 `actionCard` 的正文是 Markdown，所以这些行会渲染成 `**标签**: 内容` 的正文行，排在 `text` 之前。`value` 为空的行会被丢弃。

```shell
pipeline {
    agent any
    stages {
        stage('card-fields'){
            steps {
                echo '发送自定义内容行的卡片消息...'
            }
            post {
                success {
                    dingTalk (
                        robot: 'f72aa1bb-0f0b-47c7-8387-272d266dc25b',
                        type: 'CARD',
                        title: '📢 Jenkins 构建通知',
                        cardFields: [
                            [keyname: '任务名称', value: "${JOB_NAME}", url: "${JOB_URL}"],
                            [keyname: '任务编号', value: "${BUILD_DISPLAY_NAME}", url: "${BUILD_URL}"],
                            [keyname: '构建状态', value: "${currentBuild.currentResult}"],
                            [keyname: '构建用时', value: "${currentBuild.durationString}"],
                            [keyname: '发布分支', value: '${GIT_BRANCH}']
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
