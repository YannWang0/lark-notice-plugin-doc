# 查看环境变量

## 1. Jenkins 内置环境变量

Jenkins 提供了内置环境变量页面，可用于查看当前实例支持的变量列表：

- `https://[your-jenkins-host]/env-vars.html`

## 2. 插件注入的环境变量

> [!IMPORTANT]
> 这些变量**只在 Freestyle 任务的标题与消息内容模板中可用**。`Pipeline` 步骤的变量来自流水线上下文，插件不会向其中注入下面这些变量——在 `lark` / `dingTalk` / `wechatWork` 步骤里请改用 Jenkins 内置变量（`${JOB_NAME}`、`${BUILD_URL}` 等）或 `${currentBuild.xxx}`。

| 名称              | 说明                                             |
|-----------------|------------------------------------------------|
| `PROJECT_NAME`    | 任务的完整显示名。文件夹下的任务形如 `Folder » Job`，**不能用于拼接 URL** |
| `PROJECT_URL`     | 任务页面的绝对地址，带尾部 `/`                              |
| `JOB_NAME`        | 本次构建的显示名，形如 `#42`。注意会覆盖同名的 Jenkins 内置变量，见下方说明   |
| `JOB_URL`         | 本次构建的绝对地址，带尾部 `/`，可直接拼 `changes`、`console`      |
| `JOB_DURATION`    | 构建耗时（可读格式）                                     |
| `JOB_STATUS`      | 构建状态，语言跟随该机器人的「消息语言」设置                         |
| `EXECUTOR_NAME`   | 构建执行人姓名                                        |
| `EXECUTOR_MOBILE` | 构建执行人手机号                                       |
| `EXECUTOR_OPENID` | 构建执行人 OpenID                                   |

> [!WARNING]
> `JOB_NAME` 与 Jenkins 内置变量同名但含义不同：Jenkins 内置的 `JOB_NAME` 是任务路径（如 `folder/job`），而插件注入的是本次构建的显示名（如 `#42`），且插件的值会覆盖内置值。需要任务路径时请改用 `${PROJECT_NAME}`（显示名）或 `${PROJECT_URL}`（地址）。

拼接链接时优先使用 `PROJECT_URL` 和 `JOB_URL`，不要用 `${JENKINS_URL}/job/${PROJECT_NAME}/` 这类写法——`PROJECT_NAME` 含空格和 `»`，对文件夹下的任务会拼出错误地址。

## 3. 用户属性扩展

> [!WARNING]
> 如需在 **执行人** 字段中实现 `@` 提醒效果，需要为 Jenkins 用户补充相关属性信息。可按以下路径进入用户设置页面：`Dashboard` -> `Manage Jenkins` -> `Users` -> `用户设置`

![](../img/feq-user-attribute.png)
