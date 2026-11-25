# 数字生活 · uni-app 项目

> 基于 **uni-app + Vue 3** 开发的数字生活移动应用，集时代楷模、英雄故事、公益活动、学习心得、数据分析与 AI 问答于一体，并提供 Android WebView 打包能力。

[![Version](https://img.shields.io/badge/version-1.3.0-blue)](https://github.com/FAL007-CN/uni-app-DEEP-V1.3.0)
[![License](https://img.shields.io/badge/license-MIT-green)](./LICENSE)

## 项目简介

本项目是一套面向数字生活场景的移动应用示例，既可以通过 HBuilderX 运行到浏览器或手机，也可以编译为 H5 后嵌入 Android WebView 并打包为 APK。

仓库内不仅包含完整的前端源码，还包含 Android 原生外壳、自动打包脚本、运行教程、技术讲解文档及演示截图，适合课程学习、毕业设计、技术分享和答辩演示。

## 核心功能

- **登录与个人中心**：账号密码登录、手机号登录、用户信息与头像上传
- **时代楷模与英雄故事**：楷模展示、英雄详情、身边英雄与评论
- **公益服务**：公益活动列表、活动详情与活动报名
- **学习心得**：发表与删除感言、学习历史、笔记增删改查
- **数据分析**：评论月度统计、阅读趋势与图书类型占比
- **AI 问答**：支持 OpenAI 协议兼容模型，并可调用项目后端接口
- **本地兜底模式**：无 API Key 或网络受限时仍可展示真实接口数据
- **Android 打包**：提供 H5 编译、页面同步、APK 签名和模拟器安装脚本

## 技术栈

- **跨端框架**：uni-app
- **前端框架**：Vue 3
- **构建与运行**：Vite、HBuilderX
- **移动端**：Android、Java、WebView、Gradle 8.13
- **网络接口**：uni.request、自定义请求封装
- **数据可视化**：Canvas 自定义图表组件
- **AI 接口**：兼容 OpenAI Chat Completions 协议

## 功能页面

| 页面 | 功能 |
|---|---|
| 登录页 | 账号或手机号登录 |
| 首页 | 轮播图、功能入口、楷模新闻、下拉刷新 |
| 公益活动 | 活动列表、详情与报名 |
| 学习心得 | 感言、学习历史与笔记管理 |
| AI 助手 | 流式聊天、工具调用与模型设置 |
| 数据分析 | 评论、阅读及图书类型数据图表 |
| 英雄故事 | 英雄列表、详情与评论 |
| 身边英雄 | 展示身边英雄信息 |
| 物资捐赠 | 展示捐赠物资信息 |
| 个人中心 | 查看资料、上传头像与退出登录 |

## 项目结构

```text
.
├── pages/                  # 13 个业务页面
├── api/                    # 后端接口封装
├── utils/                  # 网络请求与 AI 工具
├── config/                 # 接口、超时及存储配置
├── components/             # 自定义组件（如 dl-chart）
├── static/                 # 图标与静态资源
├── docs/                   # 教程、讲义、演示截图
├── android/                # Android WebView 外壳与打包脚本
├── App.vue                 # 全局应用与公共样式
├── main.js                 # Vue 应用入口
├── pages.json              # 页面路由与底部导航配置
├── manifest.json           # 应用配置
└── uni.scss                # 全局主题变量
```

## 快速开始

### 使用 HBuilderX

1. 使用 HBuilderX 打开项目根目录。
2. 选择 **运行 > 运行到浏览器 > Chrome**，进行 H5 预览。
3. 连接手机或模拟器后，选择 **运行到手机或模拟器**。

H5 默认调试端口为 `5175`。

### 使用 Android Studio

1. 使用 Android Studio 打开项目根目录。
2. 等待 Gradle 同步完成。
3. 在运行配置中选择：
   - `数字生活_AndroidApp`
   - `运行到模拟器`
4. 修改 Vue 页面后，先运行 `android/sync-pages.bat` 同步编译产物，再在 Android Studio 中运行。

> 本项目使用 Gradle Wrapper 8.13，并需要配置本地 JDK 21 与 Android SDK。

### 演示账号

```text
账号：test01
密码：123456
```

## 打包 Android APK

在 Windows 环境中，双击：

```text
android/build-apk.bat
```

脚本会自动完成：

1. 编译 uni-app H5 页面
2. 同步页面到 `android/app/src/main/assets/www`
3. 检查并使用签名证书打包
4. 生成并复制可安装的 APK

打包前需要准备：

- `android/app/deep-release.jks`
- `android/app/signing.properties`
- 可用的 HBuilderX 与 JDK 21

只有签名证书和密码校验通过后，脚本才会继续打包。

安装到已连接的设备或模拟器：

```text
android/安装APK到模拟器.bat
```

## AI 助手

AI 助手支持配置兼容 OpenAI Chat Completions 协议的模型，可在应用设置中填写：

- API 地址
- 模型名称
- API Key

助手可自动调用以下项目接口：

- 首页轮播与楷模新闻
- 英雄故事与身边英雄
- 公益活动与报名
- 学习总结与笔记
- 评论、阅读和捐赠数据
- 个人资料

未配置 API Key 时，应用会自动切换到本地数据模式，通过关键词匹配调用真实后端接口并生成回复。

## 接口配置

项目后端地址统一配置在：

```text
config/index.js
```

修改 `BASE_URL` 后可切换服务器或端口，无需逐个修改页面或接口文件。

演示接口地址：

```text
http://114.66.9.70:10002
```

> 请注意接口及演示账号仅用于项目演示。部署到正式环境时应替换为受控服务器，并移除或保护演示密钥。

## 已知限制

- Android 版本使用 H5 产物与 WebView 外壳，不是 DCloud 云原生打包
- 浏览器版可能受到跨域限制，现场演示建议优先使用 APK
- 数据分析首次加载可能需要数秒，因接口分页较细需拉取完整数据后再统计
- 部分头像或历史状态依赖后端接口支持，退出后可能恢复默认状态
- 请勿将 API Key 提交到仓库或直接打入公开 APK

## 文档与素材

仓库的 `docs/` 目录包含：

- Android Studio运行配置教程
- Vue 3 零基础精讲讲义
- 技术讲解演示 PPT
- 浏览器、APK、真机及 AI 工具调用截图

## 开发与提交建议

以下文件通常由构建工具或本地环境生成，不建议提交：

```text
node_modules/
unpackage/
build/
.gradle/
.hbuilderx/
.idea/
*.apk
*.jks
*.keystore
signing.properties
local.properties
```

`android/app/src/main/assets/www` 是由 H5 编译产物同步得到的，可以提交以便直接查看，但重新打包前建议执行同步脚本。

## 许可证

本项目采用 [MIT License](./LICENSE)。

---

如需二次开发，可从 `pages/` 添加页面、从 `api/index.js` 添加接口、从 `utils/ai.js` 注册 AI 工具，并通过 `config/index.js` 集中管理运行配置。