---
# https://vitepress.dev/reference/default-theme-home-page
layout: home

hero:
  name: "Lark Notice Plugin"
  text: "Jenkins 协作通知插件"
  tagline: "接入 Lark / 飞书 / 钉钉 / 企业微信，适配 Freestyle 与 Pipeline。"
  image:
    src: /logo.png
    alt: Plugin Logo
  actions:
    - theme: brand
      text: 快速开始
      link: /guide/
    - theme: alt
      text: 版本日志
      link: /guide/CHANGELOG

features:
  - icon: 🔗
    title: 多平台支持
    details: 支持 Lark、飞书、钉钉与企业微信机器人，统一 Jenkins 通知入口。
  - icon: ⚙️
    title: 兼容主流任务类型
    details: 兼容 Freestyle 与 Pipeline，可沿用现有 Jenkins 作业方式配置通知。
  - icon: 📨
    title: 多消息类型
    details: 提供文本、Markdown、链接、卡片、富文本、图文列表，以及 Lark / 飞书场景下的自定义 JSON 卡片。
---
