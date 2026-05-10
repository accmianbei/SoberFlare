# SoberFlare

重新思考 AI 时代下的信息分发方式。

SoberFlare 是一个以地址身份为核心的 Web3 社交应用。我们希望把内容、关系、聊天与资金流放进同一个统一的社交体验里，同时重新思考在 AI 时代，信息应该如何被分发，什么样的内容更值得被看见。

`English version` 请查看 [README](./README.md)。

## 这个仓库是什么

这个仓库是 SoberFlare 的公开展示层，主要用于帮助评委、合作方和外部访问者快速理解项目方向、当前 release 状态，以及高层架构。

它**不会**包含完整的生产实现，也不会公开那些在黑客松阶段容易被直接复刻的核心工程细节。

## 核心产品主线

- `Home`：结合链上激励的内容分发层
- `Network`：基于地址身份与真实互动构建的可解释关系网络
- `Contacts`：可操作的地址联系人层
- `Chat`：E2EE 私聊、E2EE 私密群与普通群聊
- `Me`：个人资料、资产、转账、收款、转账历史与 Swap 入口

## 相关链接

- `Demo Video`：[观看演示视频](https://youtu.be/9GNV3bIeiME?si=F97K53UXkh1ETGlJ)
- `Release Download`：[release 下载链接](https://github.com/accmianbei/SoberFlare/releases)
- `Official Website`：[官网链接](https://soberflare.xyz/)

## 文档

- [Product Overview (EN)](./docs/product-overview.md)
- [产品说明 (中文)](./docs/product-overview.zh-CN.md)
- [Architecture (EN)](./docs/architecture.md)
- [架构说明 (中文)](./docs/architecture.zh-CN.md)
- [Release Notes (EN)](./docs/release-notes.md)
- [发布说明 (中文)](./docs/release-notes.zh-CN.md)

## 当前版本说明

- `v0.9.6` 是一个相对激进的版本，新增了英文翻译。
- 当前可能仍然存在少量翻译问题或界面不一致细节。
- 这个版本暂时还没有 Apple Developer 签名，因此 macOS 安装时可能会出现安全提示。
- 我们正在积极申请 Apple Developer Program。
- 安装方式请参考官网说明。

## 展示素材

截图、Logo、架构图、缩略图与封面图建议统一放在 [`assets/`](./assets) 目录下。

建议截图文件名：

- `assets/screenshots/home-feed.png`
- `assets/screenshots/network-graph.png`
- `assets/screenshots/contacts-list.png`
- `assets/screenshots/chat-list.png`
- `assets/screenshots/me-wallet-actions.png`
- `assets/screenshots/me-multi-chain-assets.png`
- `assets/screenshots/me-transfer-history.png`
- `assets/screenshots/me-send.png`
- `assets/screenshots/post-detail.png`
