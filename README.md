# uni-app-DEEP-V1.3.0
单独的技术分享仓库
数字生活 App —— 项目目录说明
==============================================================

【这是什么】

  一个用 uni-app（Vue3 + Vite）写的手机 App，主题是「时代楷模 / 英雄故事 / 公益活动 /
  学习心得 / 数据分析」，另外加了一个 AI 问答助手。它不是套个聊天框，而是能真的去调
  这个项目的后端接口，拿真实数据来回答。

  源码是 uni-app 工程（pages/ 里那些 .vue），最后套了一层 Android WebView 外壳，
  打包成一个能直接装的 APK。

【服务器接口】

  此项目采用的服务器接口为中慧集团公开版本：114.66.9.70:10002，技术交流请加 QQ：
  3997796918（表明来意，周末在线，勿扰）。

  · 接口根地址全项目只写死在一处：config/index.js 里的 BASE_URL。
    换服务器、换端口只改这一行，27 个接口跟着走，不用满项目搜 http://。
  · 演示账号：test01 / 123456（登录页已经预填好，点「登录」即可）

==============================================================
一、这个项目分成三块
==============================================================

  第 1 块    uni-app 源码（根目录的文件 + pages/ api/ utils/ config/ components/ static/）
             写页面、写逻辑的地方，平时改的就是这里。用 HBuilderX 打开根目录就能跑。

  第 2 块    Android 原生壳（android/）
             把第 1 块编译出来的 H5 页面装进一个 WebView，打成 APK。
             想装到手机上看效果，走这一块。

  第 3 块    文档与素材（docs/）
             三份讲义（Word / PPT）+ 33 张截图，用来讲解和答辩。

==============================================================
二、根目录的文件，一个一个说
==============================================================

  App.vue           全局样式表。所有 .dl- 开头的公共类（卡片、按钮、表单、遮罩、
                    空状态……）都在这里，改一处全 App 生效。
                    页面里没写样式的地方，样式基本都来自这个文件。

  main.js           Vue3 入口。uni-app 的写法和普通 Vue 不同，导出的是 createApp()
                    交给框架调用，不是 app.mount('#app')。

  pages.json        ★ 路由表。三件事都在这：
                    1) pages 数组：13 个页面的路径和标题栏文字
                    2) tabBar：底部 5 个 tab（首页 / 公益 / 心得 / AI 助手 / 数据分析）
                    3) easycom：配了 ^dl-(.*) 规则，所以用 <dl-chart> 不用 import
                    加新页面 = 在这里加一条 + 在 pages/ 下建同名 .vue。

  manifest.json     应用身份证。App 名字、appid、安卓权限、H5 调试端口（5175）。
                    注意：这里的 versionName 是 1.0.0，APK 真正的版本号在
                    android/app/build.gradle（1.3.0 / 130），改版本时两处都看一眼。

  uni.scss          主题变量。主色 $dl-primary = #1e88e5、圆角、字号、语义色。
                    uni-app 会自动把它注入每个 <style lang="scss">，页面里直接用变量名。

  index.html        H5 的模板页，只在跑浏览器版（H5）时用得到，App 端不用。

  README.md         项目总说明（怎么跑、做了什么、和旧版的对应关系）。
  项目说明.txt       就是本文件，讲目录。

  build.gradle      ┐
  settings.gradle   │ 让 Android Studio 可以直接打开「项目根目录」的 Gradle 工程。
  gradle.properties │ settings.gradle 里把 :app 指到了 android/app，
  local.properties  │ 所以根目录和 android/ 两个位置都能 Sync。
  gradlew           │ gradle.properties 里写死了 JDK 21 的路径（换电脑要改），
  gradlew.bat       │ local.properties 里是本机 Android SDK 路径（AS 会自动重写）。
  gradle/           ┘ gradle/ 里是 Gradle Wrapper（8.13），保证大家用同一个版本。

==============================================================
三、每个文件夹里是什么、干什么用的
==============================================================

  pages/            13 个页面，一个页面对应一个文件夹 + 一个 .vue 文件。
                    这是整个项目的主体，见下面第四节。

  api/index.js      ★ 接口清单。一个后端接口 = 一个函数，一共 27 个能真正发请求的
                    函数（登录、列表、详情、增删改、文件上传），再加 1 个分页工具
                    fetchAll()。
                    页面里只调这里的函数，不直接碰 uni.request——好处是接口地址、
                    token、报错全在底层统一处理，页面里只要 try/catch。

  utils/            ★ 底层工具，两个文件：
                    request.js  全项目唯一的网络出口。做四件事：拼 BASE_URL、
                                自动带 Authorization、401 自动清 token 踢回登录页、
                                code !== 200 统一抛错。另外 http.get/post/put/del
                                四个语义化方法也在这，fullImg() 负责把后端给的
                                半截图片路径补成完整地址。
                    ai.js       ★ AI 引擎（本项目最复杂的一个文件）。三件事：
                                存/读 AI 设置、注册 11 个后端接口给模型当工具、
                                调模型（OpenAI 协议，支持流式输出）+ 没 Key 时的
                                本地兜底问答。

  config/index.js   ★ 总开关。全是「会变的东西」，改配置只改这一个文件：
                    BASE_URL    接口根地址        IMG_URL     图片地址前缀
                    TIMEOUT     普通接口超时 15s   AI          模型地址/Key/温度/
                                                             上下文条数/系统提示词
                    STORAGE_KEYS 本地存储的键名（token、userInfo、aiSettings……）

  components/       自定义组件。现在只有一个：
                    dl-chart/dl-chart.vue   手写的 canvas 图表，支持柱状图、折线图、
                    饼图三种。没用 ECharts，因为比赛机器不一定联网，也装不下那么大的库。
                    用法：<dl-chart type="bar" :labels="月份" :series="数据" />
                    因为 pages.json 里配了 easycom，用完不用 import。

  static/           不参与编译的静态资源。
                    logo.png            App 图标用的原图
                    tabbar/             10 个图标（5 个 tab × 灰色/蓝色两套，81×81 PNG）
                    文件名带 -on 的是选中态。

  docs/             讲义与截图（约 24 MB，仓库体积主要来自这里）。
                    AndroidStudio运行配置教程.docx          怎么在 AS 里跑起来
                    DEEP-uniapp-v1.3.0-Vue3零基础精讲讲义.docx   Vue3 从零讲一遍
                    DEEP-uniapp-技术讲解.pptx                技术讲解（答辩用）
                    screenshots/  33 张截图：浏览器版逐页、APK 版、真机版、
                                  AI 工具调用过程。文件名前缀是编号，按顺序看即可。

  android/          ★ Android 原生壳工程。把编译好的 H5 装进 WebView 打成 APK。
                    里面的东西：
                    app/src/main/java/.../MainActivity.java
                        外壳本体。起一个全屏 WebView，用虚拟域名
                        http://appassets.androidplatform.net 把打包进 APK 的页面
                        提供出来（用 http 不用 https，是因为后端接口是 http，
                        页面若是 https 会被 WebView 当混合内容拦掉）。
                        另外还处理了：换头像用的文件选择、返回键先让网页后退。
                    app/src/main/assets/www/
                        编译好的 H5 产物（index.html + assets/ + static/）。
                        注意：这是自动生成的，不要手改，每次打包会被整个覆盖。
                    app/src/main/res/        图标、应用名、主题
                    app/src/main/AndroidManifest.xml   权限、入口 Activity
                    app/build.gradle         包名 com.deep.digitallife、版本 1.3.0/130、
                                             minSdk 23 / targetSdk 34、签名配置，
                                             还有两个自定义任务：
                                             runOnEmulator（编译+装+启动）、
                                             launchApp（只启动）
                    app/deep-release.jks     演示用的自签名证书（.gitignore 已排除）
                    app/signing.properties   证书密码，四行：storeFile / storePassword /
                                             keyAlias / keyPassword。本机专用，已被排除
                    build-apk.bat            ★ 一键打包：编译页面 → 同步 → Gradle 出 APK
                    sync-pages.bat           只做前两步：编译页面 + 同步到 assets，
                                             改完 Vue 代码、要在 AS 里点 Run 之前先跑它
                    安装APK到模拟器.bat       把 APK 装到当前连接的模拟器/手机上。
                                             两种用法：双击（装同目录的 数字生活.apk），
                                             或把任意 .apk 拖到它上面。build-apk.bat
                                             产出的叫 数字生活-v1.3.0.apk，改名或拖进去都行
                    README.md                这个壳工程的详细说明（为什么用 WebView、
                                             为什么要写死 JDK 路径等等）

  unpackage/        HBuilderX 的编译产物（自动生成，可删可重建）。
                    dist/build/h5/         H5 版构建结果，也是打包 APK 的原料
                    dist/dev/app-plus/     运行到真机时的调试产物
                    cache/ logs/           编译缓存和日志

  build/            根 Gradle 工程的报告目录（problems-report.html 之类），可删。

  .gradle/          本机 Gradle 缓存（8.13 的校验和、文件快照），可删，下次 Sync 会重建。

  .idea/            Android Studio 的工程配置。里面有两个现成的运行配置：
                    数字生活_AndroidApp  和  运行到模拟器，
                    打开项目后直接选它俩点 ▶ 就行。

  .hbuilderx/       HBuilderX 的运行配置（运行到安卓设备、标准基座）。

==============================================================
四、13 个页面 & 底部 5 个 tab
==============================================================

  底部 tab 5 个：首页 / 公益 / 心得 / AI 助手 / 数据分析。
  个人中心不在 tab 里，从各页右上角的人像图标进（跟旧版一致）。

  pages/login/login.vue        登录。账号密码、手机号两种方式，custom 导航栏。
  pages/index/index.vue        [tab 首页] 轮播图 + 功能宫格 + 楷模新闻列表，支持下拉刷新。
  pages/activity/activity.vue  [tab 公益] 公益活动列表。
  pages/activity/detail.vue    活动详情 + 报名。
  pages/study/study.vue        [tab 心得] 学习感言（可发表/删除）+ 学习历史 + 笔记增删改。
  pages/ai/ai.vue              [tab AI 助手] 聊天界面、工具调用标签、设置面板、流式输出。
  pages/analysis/analysis.vue  [tab 数据分析] 三张图：评论按月柱状图、阅读按月折线图、
                               图书类型饼图。
  pages/model/model.vue        楷模列表。
  pages/hero/hero.vue          英雄故事列表。
  pages/hero/detail.vue        英雄故事详情 + 评论列表。
  pages/around/around.vue      身边英雄。
  pages/donation/donation.vue  物资捐赠列表。
  pages/user/user.vue          个人中心：看资料、换头像（上传）、退出登录。

  页面跳转：普通页用 uni.navigateTo；tab 之间靠底部 tabBar 直接切（代码里也有
  uni.switchTab）；需要把页面栈整个清掉的地方用 uni.reLaunch——登录成功、退出登录、
  token 过期、首页发现没登录，四处都是它。清掉之后按返回键回不到内页，这是故意的。

==============================================================
五、后端一共用了 27 个接口（都在 api/index.js）
==============================================================

  登录       POST /api/login、POST /api/phone/login、POST /logout
  首页       GET  /api/building/banner/list、/api/model/list
  英雄故事   GET  /api/hero/list、/api/hero/{id}、/api/hero/evaluate/list、/api/around/list
  学习感言   GET  /api/testimonials/list  POST /api/testimonials  DELETE /api/testimonials/{id}
  学习历史   GET  /api/history/list
  笔记       POST /api/history/note、PUT /api/history/note、
             DELETE /api/history/note、GET /api/history/note/{id}
  公益       GET  /api/activity/list、/api/activity/{id}、
             GET  /api/activity/apply/list、POST /api/activity/apply
  物资捐赠   GET  /api/donation/list
  数据分析   GET  /api/data/comment/list、/api/read/book/list、/api/read/book/list/type
  用户       GET  /api/common/user/getInfo
  文件上传   POST /common/upload（multipart，走 uni.uploadFile，自己包了一层 Promise）

  几个和接口有关的坑，已经写在代码注释里了：
  · 登录接口必须 auth:false。带一个空 Authorization，有些后端直接 401，表现为
    「密码明明对却永远登不上」。
  · 后端是 RuoYi 那一套：HTTP 状态码一律 200，真正的结果在 body.code 里，
    连 401 也可能藏在 body.code（所以 request.js 两个地方都判了）。
  · 数据分析要算全年，必须拉全量：评论 9651 条、阅读 2448 条，接口只有分页没有聚合，
    所以用 fetchAll() 分页拉完再一次统计，首屏 3~8 秒，这是接口设计决定的，不是卡死。

==============================================================
六、AI 问答助手是怎么工作的
==============================================================

  分三层，缺哪层都能演示，不会白屏：

  第一层 · 调大模型
    标准 OpenAI 协议：POST {baseUrl}/chat/completions。
    所以 DeepSeek、通义千问、Kimi、智谱、OpenAI 都能用——只改 baseUrl + model
    两个字符串，代码一行不用动。Key 可以在 App 的「设置」里填（存本地），
    也可以写死在 config/index.js 的 AI.apiKey 里。

  第二层 · 调本项目自己的后端接口（function calling）
    utils/ai.js 里注册了 11 个工具，对应后端真实接口。模型说「我要查公益活动」，
    App 就真的去请求 /api/activity/list，把真实 JSON 塞回对话，再让模型照着答。
    用户界面上会冒出一个小标签（比如「查询公益活动 ●」），绿点表示调用完成——
    这就是「它真的在连后端，不是背台词」的证据。
    11 个工具：轮播图、楷模新闻、英雄故事、公益活动、我的报名、学习总结、
    评论统计、阅读统计、捐赠列表、个人资料、身边英雄。

  第三层 · 兜底
    没 Key / 断网 / 浏览器跨域被拦的时候，自动切「本地数据模式」：
    用关键词判断你想问什么，直接查后端真接口，套模板回答。
    所以没 Key 也能演示，而且回答里的数字都是真的。

  性能上说实话：不需要查数据 = 1 次请求；需要查数据 = 3 次（规划调工具 →
  整理结果 → 流式输出）。嫌慢可以把 AI.maxToolRounds 调小。

==============================================================
七、怎么跑起来
==============================================================

  方式 A：HBuilderX（最省事）
    1. HBuilderX → 文件 → 打开目录 → 选这个项目根目录
    2. 运行 → 运行到浏览器 → Chrome（H5 预览，端口 5175）
    3. 想上真机：手机连 USB → 运行 → 运行到手机或模拟器

  方式 B：Android Studio（看 APK 效果 / 改原生壳）
    1. 用 AS 打开「项目根目录」（不是 android 文件夹）
    2. 等 Gradle Sync 完成（第一次要下 Gradle 8.13 和 AGP）
    3. 运行配置选「数字生活_AndroidApp」或「运行到模拟器」，点 ▶
    注意：AS 的 Run 不会重新编译 Vue 页面。改了 pages/ 里的代码，先双击
    android/sync-pages.bat，再回 AS 点 Run。

  方式 C：命令行？这个工程没有 package.json
    它是 HBuilderX 标准工程（不需要 npm install），所以没有 npm run dev 这类命令。
    想在命令行里编译 H5，就照 android/build-apk.bat 里那几行来做：先设好 UNI_*
    环境变量，再用 HBuilderX 自带的 node 去跑它 plugins 里的 uni.js。脚本里写好了，
    直接双击那个 bat 最省事。

  登录：test01 / 123456

==============================================================
八、怎么打 APK
==============================================================

  双击 android/build-apk.bat，三步全自动：
    1. 用 HBuilderX 自带的编译器把 Vue 源码编译成 H5（产物进 unpackage/dist/build/h5）
    2. robocopy 同步到 android/app/src/main/assets/www
    3. Gradle 打包 + 签名
  结束时会打印 APK 路径，另外会在 android/ 下复制一份「数字生活-v1.3.0.apk」方便拷走。

  想装到手机/模拟器：双击 android/安装APK到模拟器.bat，或者把 apk 拖到它上面。

  脚本开头用 %USERPROFILE% 推出两个路径（HBuilderX 目录、JDK 21 目录），默认都在用户目录下；装在别处就改那两行；
  项目目录是从脚本自己所在位置推出来的，不用改。

  打包前有一道签名门禁（脚本里的第 0 步），任何一项不对就停，不会白跑编译：
    · 证书文件在不在          android/app/deep-release.jks
    · 密码文件在不在          android/app/signing.properties（四行：storeFile /
                              storePassword / keyAlias / keyPassword）
    · 密码能不能打开证书      keytool 真去开一次，打不开就拦
    · 证书里有没有这个别名    keytool -list -alias
    · 两个密码是否一致        这个证书是 PKCS12，storePassword 必须等于 keyPassword
  全通过才继续，并且会打印证书路径、别名、SHA256 指纹，一眼能看出签的是哪张证书。

  只想验证书、不打包：命令行运行 android\build-apk.bat check
  （通过返回 0，被拦住返回 1，可以放进别的脚本里用）
  密码文件和证书都被 .gitignore 排除，不会进仓库；换电脑时自己补回这两样。

==============================================================
九、已知限制（答辩前先看一遍，别被问懵）
==============================================================

  1. 这个 APK 是「H5 产物 + Android WebView 壳」，不是 DCloud 云打包的原生包。
     拿不到原生能力（相机/蓝牙/推送），但这个项目用不到；项目里没有 plus.* 调用，
     所以功能上不缺。
     原因：appid 是 __UNI__DEEP2026，格式不合 DCloud 规范（__UNI__ 后要 7 位十六
     进制），申请不到 Appkey，硬套离线 SDK 会报「Appkey 配置错误」。详见
     android/README.md。
  2. H5（浏览器）端可能被跨域拦住。App 端没有跨域问题——现场演示建议用 APK，
     别用浏览器。
  3. 数据分析页首次加载 3~8 秒，见第五节，是接口只有分页导致的。
  4. 「身边英雄」接口文档里没写返回字段，字段名是按实测猜的，能正常显示。
  5. 头像上传只传了文件、拿到 URL 后本地显示；后端没有「保存头像」的接口，
     退出去再进来会恢复原头像。
  6. AI 的 Key 如果写死在 config/index.js，会原样打进 APK。要外传 APK 或交源码，
     记得先清空（留空就是本地数据模式，照样能演示）。
     这一版已经把之前打进编译产物的两个 Key 清成了空串，所以现在这份 APK 壳跑起来是
     本地数据模式；想用真模型回答，在 App 的「设置」里填自己的 Key 就行。

==============================================================
十、想加东西，从哪下手
==============================================================

  改接口地址 / 端口        →  config/index.js 的 BASE_URL
  加一个后端接口          →  api/index.js 加一个函数（一行 http.get/post 就够）
  让 AI 也能用这个接口    →  utils/ai.js 里 TOOLS 加一条 + execTool 加一个 case
  加一个页面              →  pages/ 下建同名 .vue + pages.json 的 pages 加一条
  改底部 tab              →  pages.json 的 tabBar.list（最多 5 个）
  改主题色                →  uni.scss 的 $dl-primary + pages.json 的
                            navigationBarBackgroundColor / tabBar.selectedColor
  改全局样式              →  App.vue（.dl- 开头的类）
  改包名 / 版本号 / 签名   →  android/app/build.gradle

==============================================================
十一、提交到仓库时可以不带的东西
==============================================================

  根目录已经放好 .gitignore，git add . 时下面这些会自动跳过（每一条都在临时仓库里
  实测过，不是照着经验写的）：
  node_modules/  unpackage/  build/  .gradle/  android/.gradle/  local.properties
  .hbuilderx/  .idea/（只跳过缓存，.idea/runConfigurations/ 特意保留，两个运行配置还在）
  *.apk  *.aab  *.jks  *.keystore  *.log  Thumbs.db  signing.properties

  真正会被提交的是：pages/ api/ utils/ config/ components/ static/ docs/
  android/app/src/（含 assets/www）android/*.bat 根目录的配置文件、gradle wrapper、
  README.md、项目说明.txt、.gitignore。

  两个提醒：
  · .gitignore 只对 git 命令有效。要是在网页上点「Upload files」拖文件上去，它管不住，
    那种方式得自己挑文件。
  · android/app/build/ 和 unpackage/ 下面还留着旧的编译结果（早先的产物里带过 AI 的
    Key），它们被忽略、不会上传；想彻底干净就把这两个目录整个删掉，下次打包自动重建。

  另外：android/app/src/main/assets/www 是编译产物，但它被打进了 APK、
  也被 .bat 自动同步，提交与不提交都行；不提交的话别人拿到项目要先跑一次
  build-apk.bat 才能打包。
