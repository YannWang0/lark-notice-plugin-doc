# 更新日志

## [2.1.10] 2026-08-21

### ⭐ Features

- ✨ 企业微信卡片内容行支持自定义：Pipeline `wechatWork` 步骤新增 `cardFields` 参数，可自定义每行的 `keyname`、`value` 与跳转 `url`，设置后替换默认的构建信息行（任务名称、任务编号、构建状态、构建用时、执行人），三个字段均支持环境变量展开；超出企业微信 6 行上限的内容行会被自动丢弃，避免服务端直接拒收整条消息。

### ♻️ Refactor

- ♻️ 重构消息模型为三层结构（构建上下文、跨平台渲染意图、平台独有字段），`MessageSender` 泛型化，分发器按消息类型路由并校验平台支持矩阵，降低跨平台字段耦合，为后续卡片自定义能力打基础。

### 🐛 Fixes

- 🩹 修复企业微信卡片图文位置始终显示 Jenkins 图片的问题：卡片图片现在优先取用户配置的 `picUrl`，仅当未配置或非可访问 HTTP(S) 地址时才回退默认图。
- 🩹 恢复企业微信卡片在有结构化构建字段时不重复渲染正文块的行为，避免正文与内容行重复展示。

## [2.1.9] 2026-06-19

### ⭐ Features

- ✨ 新增企业微信机器人通知支持，接入全局机器人配置、测试发送、默认卡片消息与 Pipeline `wechatWork` 步骤。
- ✨ 新增“机器人 Job 绑定”独立管理页，支持按机器人维度筛选、批量选择、绑定、启用与解绑，并返回应用结果明细。
- ✨ 新增通知发送失败是否影响构建结果的开关：全局可配置触发器通知失败是否将构建标记为失败，Pipeline 步骤支持 `failOnError` 参数；默认保持原有行为，关闭后仅记录警告并保持构建为成功。
- 🎨 继续优化全局配置、配置迁移工具、机器人配置页与任务通知配置页布局，提升多平台场景下的可读性。

### 🐛 Fixes

- 🩹 修复 Jenkins 全局配置分类注册异常，避免系统配置页渲染时报 `GlobalConfigurationCategory` 相关错误。
- 🩹 修复配置快照导入预览中重试配置被隐藏字段默认值污染的问题，并兼容旧配置里的零值重试参数。
- 🩹 修复 Lark 全局配置与系统杂项配置混排、机器人配置列表重复添加入口、自定义消息模式与原始消息内容回显、`@所有人` 保存回显及钉钉 `@all` 渲染问题。
- 🩹 兼容旧前端脚本读取 `robotId` 的方式，避免模板预览在字段命名调整后丢失机器人配置。

### 🎨 UI / UX

- 🎨 重新整理机器人配置页布局，优化机器人类型、Webhook、签名密钥、语言策略与测试发送区域的展示层次。
- 🎨 压缩 Lark 管理页全局设置区域，减少代理、安全策略、重试配置等区块的纵向占用。
- 🎨 调整机器人平台选择器宽度，改善窄屏和多配置场景下的表单排版。

### ♻️ Refactor

- ♻️ 统一企业微信与现有通知链路的发送入口、语言策略和结构化卡片字段构建，减少 Freestyle 与 Pipeline 之间的行为差异。
- ♻️ 精简通知配置解析、发送器、Pipeline Step 与通知编排相关代码，移除冗余工具类、废弃前端资源和未使用方法。
- ♻️ 将机器人类型解析改为 `Optional` 返回，并为企业微信不支持 Link/Post 消息时增加降级调试日志。

### 🧪 Tests

- ✅ 补充配置页回显、自定义消息模式、`@all` 持久化、钉钉 `@all` 消息渲染与相关通知配置测试。

### 🔨 Build / Dependency

- ⬆️ 升级 Jenkins Plugin Parent、Jenkins BOM 与 GitHub Release Action。
- 🔧 标准化 GitHub Release 工作流，正式版标签会生成非预发布 Release 并上传 HPI 产物。

## [2.1.9-rc.5] 2026-06-13

> 该版本为预发布版本，建议优先在测试环境验证后再推广到正式环境。

### 🐛 Fixes

- 🩹 修复任务通知配置页重新打开后，自定义消息模式与原始消息内容可能无法正确回显的问题。
- 🩹 修复“@所有人”配置保存与回显问题，并补齐钉钉文本/Markdown 消息的 `@all` 渲染。
- 🩹 兼容旧前端脚本读取 `robotId` 的方式，避免模板预览在字段命名调整后丢失机器人配置。

### ♻️ Refactor

- ♻️ 精简通知配置解析、发送器、Pipeline Step 与通知编排相关代码，移除冗余工具类和未使用方法。
- ♻️ 将机器人类型解析改为 `Optional` 返回，减少空值分支，并统一机器人协议命名。
- ♻️ 企业微信不支持 Link/Post 消息时增加降级到 Markdown 的调试日志，便于排查消息类型回退。

### 🧪 Tests

- ✅ 补充配置页回显、自定义消息模式、`@all` 持久化与钉钉 `@all` 消息渲染测试。

### 🔨 Dependency

- ⬆️ **Bump io.jenkins.tools.bom:bom-2.541.x**

## [2.1.9-rc.4] 2026-06-06

> 该版本为预发布版本，建议优先在测试环境验证后再推广到正式环境。

### 🎨 UI / UX

- 🎨 重新整理机器人配置页布局，优化机器人类型、Webhook、签名密钥、语言策略与测试发送区域的展示层次。
- 🎨 压缩 Lark 管理页全局设置区域，减少代理、安全策略、重试配置等区块的纵向占用。
- 🎨 调整机器人平台选择器宽度，改善窄屏和多配置场景下的表单排版。

### ♻️ Refactor

- ♻️ 清理已废弃的样式、脚本、文案和无效数据属性，减少配置页前端资源冗余。

### 🔨 Dependency

- ⬆️ **Bump io.jenkins.tools.bom:bom-2.541.x**

## [2.1.9-rc.3] 2026-05-29

> 该版本为预发布版本，建议优先在测试环境验证后再推广到正式环境。

### 🐛 Fixes

- 🩹 将 Lark 全局配置从系统设置页移出，避免与系统杂项配置混排，提升配置页可读性。
- 🩹 移除机器人配置列表中多余的顶部添加按钮，减少重复入口带来的困惑。

### 🔨 Dependency

- ⬆️ **Bump org.jenkins-ci.plugins:plugin**
- ⬆️ **Bump io.jenkins.tools.bom:bom-2.541.x**

## [2.1.9-rc.2] 2026-05-15

> 该版本为预发布版本，建议优先在测试环境验证后再推广到正式环境。

### ⭐ Features

- ✨ 新增“机器人 Job 绑定”独立管理页，支持按机器人维度批量管理 Job 绑定关系。
- ✨ 支持按关键字与状态（已绑定/未绑定/已禁用）筛选 Job，并展示已绑定、已禁用、可操作等汇总指标。
- ✨ 支持“选择结果/清空结果”批量勾选，以及一次性应用绑定、启用、解绑变更。
- ✨ 应用变更后返回按 Job 维度的结果明细（已应用/未处理/处理失败），提升批量操作可观测性。
- ✨ 不可操作场景提供明确跳过原因（无配置权限、Multibranch 项目、Freestyle Post-build Notifier 冲突）。

## [2.1.9-rc.1] 2026-05-13

> 该版本为预发布版本，建议优先在测试环境验证后再推广到正式环境。

### ⭐ Features

- ✨ 新增企业微信机器人通知支持，统一接入机器人配置、测试发送与默认卡片消息。
- ✨ Pipeline 新增 `wechatWork` 步骤，可在 `Jenkinsfile` 中直接发送企业微信通知。
- 🎨 继续优化全局配置、配置迁移工具和任务通知配置页布局，提升多平台场景下的可读性。

### 🐛 Fixes

- 🩹 修复 Jenkins 全局配置分类注册异常，避免系统配置页渲染时报 `GlobalConfigurationCategory` 相关错误。
- 🩹 修复配置快照导入预览中重试配置被隐藏字段默认值污染的问题，兼容旧配置里的零值重试参数并自动回填默认值。

### ♻️ Refactor

- ♻️ 统一企业微信与现有通知链路的发送入口、语言策略和结构化卡片字段构建，减少 `Freestyle` 与 `Pipeline` 之间的行为差异。

## [2.1.8] 2026-04-28

### ⭐ Features

- 🎨 继续优化全局配置与任务通知配置页布局，统一 Jenkins 配置风格并提升多配置场景下的可读性。

### 💥 Compatibility

- 💥 Jenkins 基线升级到 `2.541.3`，低于该版本的 Jenkins 需要先升级后再安装 `2.1.8`。

### 🔨 Dependency

- ⬆️ **Bump org.jenkins-ci.plugins:plugin**
- ⬆️ **Bump io.jenkins.tools.bom:bom-2.541.x**
- ⬆️ **Bump org.projectlombok:lombok** from `1.18.44` to `1.18.46`

## [2.1.7] 2026-03-18

### 🐛 Fixes

- 🩹 修复任务通知配置中“自定义消息”模式下输入区显示异常的问题，并补齐相关占位提示文案。
- 🩹 修复 Jelly 国际化文案中变量占位导致的渲染异常，避免部分配置页打开失败。
- 🩹 修复机器人类型展示与全局配置语义不一致的问题，统一使用 `飞书`、`Lark`、`钉钉` 展示。

### 🎨 UI / UX

- 🎨 重构任务通知配置页布局，压缩无效留白，优化消息模式、通知状态与说明文案的排版层次。
- 🎨 优化机器人下拉选项与配置列表展示，在名称之外补充类型信息，提升多机器人场景下的辨识度。

### ♻️ Refactor

- ♻️ 将 Lark 全局配置移入独立分类，并将 `/manage/lark` 入口归类到 `Configuration`，避免与系统 `HTTP Proxy Configuration` 等杂项配置混排。

## [2.1.6] 2026-03-15

### ⭐ Features

- ✨ 支持按机器人配置通知失败重试策略，可针对不同 Webhook 单独设置重试参数。
- ✨ 新增 Lark 配置快照导入/导出能力，支持 JSON 备份、导入预览，以及按机器人 ID 执行合并导入。
- ✨ 配置迁移工具迁移到独立页面，集中处理导出、导入预览与恢复操作。
- ✨ 优化配置页交互体验，支持代理启用开关、机器人 ID 紧凑展示与复制，并调整测试发送入口位置。

### 🐛 Fixes

- 🔒 配置导出接口仅允许 `POST` 请求，进一步收紧管理操作的安全边界。
- 🩹 代理配置变更后自动清理发送器缓存，并补充 Webhook 校验，减少旧配置残留带来的发送异常。
- 🩹 统一卡片图片字段的变量展开行为，并修复部分 SpotBugs 模型告警。
- 🎨 精简机器人操作区布局，移除多余分隔线，优化管理页提示与交互细节。

### ♻️ Refactor

- ♻️ 重构通知编排、分发、配置解析与日志体系，拆分发送器注册表，提升可维护性和排障效率。
- ♻️ 统一全局/任务配置页结构与国际化键，梳理快照模型、配置复制语义以及机器人配置访问逻辑。
- ♻️ 精简 `JsonUtils` 等工具类，清理过时 API 与冗余实现。

### 🧪 Tests

- ✅ 扩展配置页、导入导出流程、快照校验、通知服务层、重试策略、多语言消息与 Pipeline 图片字段的测试覆盖。
- ✅ CI 拆分为快速测试与 `JenkinsRule` 测试套件，并统一到 `JDK 17` 运行。

### 🔨 Dependency

- ⬆️ **Bump io.jenkins.tools.bom:bom-2.528.x** from `6077.v303e2d08985d` to `6128.v8e4ce4620a_2b_`
- ⬆️ **Bump GitHub Actions** `actions/checkout` from `4` to `6`
- ⬆️ **Bump GitHub Actions** `actions/setup-java` from `4` to `5`

## [2.1.5] 2026-03-04

### ⭐ Features

- ✨ 新增 `Freestyle` 的 Post-build Action 配置入口，可直接在任务构建后动作中配置 Lark 通知。
- ✨ 优化配置页国际化与交互行为，支持多通知块独立显示，并补充 Webhook 帮助说明。
- ✨ 机器人“测试发送”接口改为结构化 JSON 返回，前端可更准确展示成功或失败原因。
- ✨ 新增结构化 verbose 日志，补充 `notify.*`、`dispatcher.*`、`pipeline.step.*` 等事件，便于排障。

### 🐛 Fixes

- 🩹 修复 `Freestyle` 同时配置 Job 属性和 Post-build Action 时可能重复发送通知的问题。
- 🩹 加固通知发送链路，补齐空结果与上下文缺失场景处理，失败分支更可控。
- 🩹 修复配置集合回填/保存时的序列化兼容问题。
- 🩹 修复 Jelly 配置页中枚举文案的国际化解析异常。

### 💥 Compatibility

- 💥 Jenkins 基线升级到 `2.528.3`，低于该版本的 Jenkins 需要先升级后再安装 `2.1.5`。

## [2.1.4] 2025-11-15

### ⭐ Features

- 🎨 Improve structure / format of the code.
- ✨ map 'default' JSON property in TextSize

## [2.1.3] 2025-09-25

### ⭐ Features

- 🧑‍💻 Improve developer experience.
- 🛂 Work on code related to authorization, roles and permissions.

### 🔨 Dependency

- ⬆️ Bump org.jenkins-ci.plugins:plugin from 5.24 to 5.26
- ⬆️ Bump org.projectlombok:lombok from 1.18.40 to 1.18.42

## [2.1.2] 2025-08-09

### ⭐ Features

- ✨ Feishu supports sending structured card messages in JSON 2.0 format
- 💥 Introduce breaking changes. jenkins.version >= 2.504.3

### 🔨 Dependency

- ⬆️ **Bump org.jenkins-ci.plugins:plugin** from 5.17 to 5.18

## [2.1.1] 2025-06-29

### ⭐ Features

- ✨ support multibranch pipeline notifications.
- ♻️ Core modules refactored for better maintainability and scalability.

### 🔨 Dependency

- ⬆️ **Bump org.jenkins-ci.plugins:plugin** from 5.16 to 5.17

## [2.1.0] 2025-05-11

### ⭐ Features

- 💥 Introduce breaking changes. jenkins.version >= 2.492.
- ♻️ Refactor code. 支持配置禁用客户端证书验证.
- ♻️ Refactor plugin for improved maintainability and localization.

### 🔨 Dependency

- ⬆️ **Bump io.jenkins.tools.bom:bom-2.492.x**
- ⬆️ **Bump org.jenkins-ci.plugins:plugin** from 4.78 to 5.16
- ⬆️ **Bump org.projectlombok:lombok** from 1.18.26 to 1.18.38

## [2.0.0] 2024-02-24

### ⭐ Features

- 💥 更新插件名称 `Lark Notice Plugin`
- ✨ 支持 `钉钉机器人` 的构建通知
- ✨ 支持钉钉 `文本`、`链接`、`Markdown`、`卡片` 等消息配置

### 🔨 Dependency

- ⬆️ **org.jenkins-ci.plugins**  4.75 to 4.78
- ⬆️ **io.jenkins.tools.bom**  2675.v1515e14da_7a_6 to 2815.vf5d6f093b_23e

## [1.2.3] 2023-12-30

### ⭐ Features

- ✨ 更新默认的标题并支持 `自定义标题`
- ✨ 自定义消息支持 `消息卡片搭建工具` 生成的JSON消息体
- 🎨 根据状态调整卡片消息的标题色
- 🎨 详细日志打印完整的请求数据

### 🔨 Dependency

- ⬆️ **io.jenkins.tools.bom**  2661.vb_b_60650f6d97 to 2675.v1515e14da_7a_6

## [1.2.2] 2023-12-16

### ⭐ Features

- ✨ 个人资料页添加OpenId配置项用于@成员
- 🔒 Fix security issues.

## [1.2.1] 2023-12-13

### ⭐ Features

- ✨ 支持海外版Lark的消息通知
- ♻️ 重构 插件 代码

### 🔨 Dependency

- ⬆️ **org.jenkins-ci.plugins**  4.67 to 4.75

## [1.2.0] 2023-11-23

### ⭐ Features

- ✨️ Freestyle卡片消息自定义内容支持图片配置
- ✨ Pipeline卡片消息支持正文顶部底部图片配置

### 🔨 Dependency

- ⬆️ **lombok** 1.18.28 to 1.18.30
- ⬆️ **org.jenkins-ci.plugins**  4.64 to 4.67
- ⬆️ **io.jenkins.tools.bom**  2.387.x to 2.414.x

## [1.1.0] 2023-06-05

### ⭐ Features

- ♻️ 重构 插件 代码
- 📝 补充 Java Doc 类及方法注释

### 🔨 Dependency

- ⬆️ **lombok** 1.18.26 to 1.18.28
- ⬆️ **org.jenkins-ci.plugins**  4.63 to 4.64
- ⬆️ **io.jenkins.tools.bom**  2.375.x to 2.387.x

## [1.0.0] 2023-05-20

### ⭐ Features

- ✨ 支持 `Freestyle` 、 `Pipeline` 项目风格配置
- ✨ 支持 `文本`、`图片`、`群名片`、`富文本`、`卡片` 多种消息配置
- ✨ 支持 `自定义` 消息体

### 🔨 Dependency

- ⬆️ **lombok** 1.18.26
- ⬆️ **org.jenkins-ci.plugins**  4.63
- ⬆️ **io.jenkins.tools.bom**  2081.v85885a_d2e5c5
