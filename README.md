# code-noob-li.github.io — 个人博客（Hexo + Matery）

> 🤖 **AI 声明：本仓库的构建、部署与日常维护目前已由 DeepSeek（AI）自动化接管**，包括页面改造、文章编写（每篇文章开头有 AI 生成声明）、编译与上线发布等环节。任何疑问或需求可直接与 DeepSeek 会话沟通。

> ⚠️ **敏感内容警告：本 README.md 会被提交推送到公开的 GitHub 仓库！**
> 任何更新都必须遵守：
> 1. **禁止**出现任何 API Key / Token / 密码等密钥（真实 Key 一律用 `sk-你的Key` 占位）
> 2. **禁止**出现个人专属 API 端点 ID（形如 `llm-<专属ID>.cn-beijing...`，只准用 `<专属ID>` 占位）
> 3. **禁止**出现设备序列号、真实邮箱、电话号码等个人隐私信息
> 4. 写完后用 `git diff` 复查，或扫描上述关键词再推送

> 本 README 记录项目的整体情况、日常操作流程、以及历次维护会话的**关键记录与坑点**，方便后续会话（或换人）快速接手。请先读它。

## 项目是什么

- 博客地址：<https://code-noob-li.github.io>
- 仓库：`code-noob-li/code-noob-li.github.io`（GitHub Pages，用户站点，**master 分支根目录直接作为线上产物**）
- 站点框架：**Hexo**（注意：**不是 Hugo！** 此前误记为 Hugo）。主题：`hexo-theme-matery`
- 技术栈：Node.js 22 / Hexo 8.1.2 / hexo-generator-search / hexo-renderer-marked

## 目录结构

```
github-page/                     ← 工作区根目录（git 仓库，master = 线上编译产物）
├── index.html / about/ / css/ / js/ / libs/ / medias/    ← 编译后的静态站点（直接提交到 GitHub 服务）
├── .github/workflows/          ← 旧的 jekyll 工作流（未使用）
├── .gitignore                  ← 排除了 site/ 源码工程等
├── README.md                   ← 本文件
└── site/                       ← ★ Hexo 源码工程（本地管理，已 gitignore，不入库）
    ├── _config.yml             ← 站点配置（标题 Hello world、作者 J、prismjs 高亮等）
    ├── source/                 ← 内容源
    │   ├── _posts/             ← 文章（markdown）
    │   ├── tags/index.md       ← 标签页（layout: tags）
    │   ├── categories/index.md ← 分类页（layout: categories）
    │   ├── about/index.md      ← 关于页（layout: about）
    │   └── 404.md              ← 404 页（layout: 404）
    ├── themes/matery/          ← 主题（含定制）
    │   ├── _config.yml         ← 主题配置（个人信息、技能、社交等）
    │   ├── layout/             ← 模板（index.ejs / 404.ejs 等，有少量定制）
    │   └── source/             ← 主题静态资源（css/js/libs/medias，已合并旧站资源）
    └── node_modules/           ← 全部依赖，删掉 site/ 文件夹即"卸载"
```

## 日常操作（都在 `site/` 下执行）

```powershell
cd D:\code-ai\github-page\site

# 新建文章（会生成 source/_posts/<标题>.md）
npx hexo new "文章标题"

# 本地预览
npx hexo server -p 4000        # 浏览器打开 http://localhost:4000

# 编译
npx hexo clean; npx hexo g     # 产物输出到 site/public/
```

### 上线部署流程（重要）

GitHub Pages 直接服务 **master 分支根目录**，所以部署 = 把编译产物同步到仓库根目录再提交：

```powershell
# 1. 在 site/ 下编译
cd D:\code-ai\github-page\site
npx hexo clean; npx hexo g

# 2. 回到仓库根目录，同步 public 产物（注意保留 .gitignore 等非产物文件）
cd D:\code-ai\github-page
# 清掉旧的编译产物，再整体拷贝新的（用 robocopy / PowerShell 均可）

# 3. 提交推送
git add -A
git commit -m "update: 站点更新说明"
git push origin master
```

> 注意：`site/` 源码工程**不入库**（用户有意为之，方便整体删除）。如果换了机器或重装了，需要重新 `npx hexo-cli init site` 建工程 + 下载 matery 主题 + 恢复配置。建议后续把 `site/` 源码也 push 到仓库的 `src` 分支做备份（当前未做）。

## 2026-08-27 本次维护会话记录

### 背景与结论

1. 仓库多年未更新，**只有编译后的静态产物，没有任何源码**（无 `_config.yml`、`source/`、`themes/`）。原站点是 Hexo 5.4.2 + matery 主题本地编译后直接提交产物。
2. 用户计划：改造首页（自我介绍 + 编程/工具 + 社交）、后续持续加文章。故**重建 Hexo 源码工程**，而非常改编译 HTML。
3. 用户明确要求：**所有安装都装在工作区 `site/` 目录内**（不要全局安装），删掉文件夹即卸载。

### 已完成的改造

- ✅ 在 `site/` 建立 Hexo 8 源码工程（`npx hexo-cli init site`），依赖全部本地化
- ✅ 下载 `hexo-theme-matery`（走 gh-proxy 加速，zip 解压到 `site/themes/matery`）
- ✅ 站点/主题配置：站点名 "Hello world"、作者 J、subtitle 打字效果、社交链接指向 code-noob-li 的 GitHub
- ✅ 把旧站编译产物的 `css/js/libs/medias/favicon` **合并拷贝进主题 source**，保持旧站视觉不变
- ✅ **首页定制**：新建 `layout/_partial/home-about.ejs` 并插入 `index.ejs`（文章列表上方），内容 = 自我介绍（来自 code-noob-li 仓库 README）+ 12 个工具图标（devicon CDN）+ 微信/推特社交图标；样式加到主题 `source/css/my.css`
- ✅ **about 页**：技能栏沿用旧数据（python 80% / JavaScript 30% / HTML5 70% / CSS 50% / SQL 10% / 好吃懒做 100%），简介更新
- ✅ 迁移 hello-world 文章（保留原 URL `/2022/10/25/hello-world-1/`）、about 页、404 页
- ✅ **新增 2 篇文章**：`opencode-ocr-bridge.md`（给 DeepSeek 加图片识别插件）、`phone-storage-life-echeck.md`（eMMC/UFS 寿命检测）。两篇均已声明"由 DeepSeek 根据博主项目记录总结"
- ✅ **404 页改为腾讯公益 404**（失踪儿童）
- ✅ 补上 `hexo-generator-search`，修复此前缺失的 `/search.xml`（搜索可用了）
- ✅ 修复 footer 年份重复输出的模板 bug
- ✅ 删除文章底部转载声明、删除打赏按钮（原打赏二维码是主题作者 blinkfox 的，不是用户的）

### 踩过的坑（重要）

| 坑 | 说明 | 解法 |
| --- | --- | --- |
| **仓库误记为 Hugo** | 实际是 Hexo，且只有编译产物、无源码 | 重建源码工程，产物照旧提交 master |
| **代码字体忽大忽小** | hexo 8 默认 `highlight.js` 渲染成 `<figure class="highlight"><table>` 结构，与 matery 的 prism 样式冲突 | `_config.yml` 改 `syntax_highlighter: prismjs` 且 `prismjs.enable: true`（主题靠这个开关加载 prism.css） |
| **`/tags/` `/categories/` 404** | hexo 生成器只生成各标签/分类子页，**不自动生成索引页** | 手工建 `source/tags/index.md`、`source/categories/index.md`（`layout: tags/categories`） |
| **首页头像与文字重叠** | matery 的 `.profile .avatar-img` 有 `translate3d(0,-65%,0)`、`.profile .author` 有 `margin-top:-80px`（about 页设计如此），首页复用 `.profile` 导致重叠 | 在 `my.css` 里用 `#home-about .profile ...` 覆盖取消 |
| **腾讯 gy404 已下线** | `open.gongyi.qq.com/gy404/gy404.html` 全部 404；接棒的 `uiuing/Findbaby` 也已停服 | 用 `songjinzhong.github.io/404html/404.html`（原版自托管，数据仍走 qzone.qq.com） |
| **busuanzi 统计显示为空** | 服务本身正常（生产域名已统计 189 次访问/48 人）；localhost 预览返回的是全局大数字；偶尔抽风/被广告拦截 | 保留不蒜子，不用改。线上才有真实数字 |
| **PowerShell GBK 乱码** | 中文/emoji 在 PowerShell 5.1 下显示乱码是编码问题，不代表文件坏了 | 用 Python + `io.open(..., encoding='utf-8')` 验证文件内容；脚本开头 `sys.stdout.reconfigure(encoding='utf-8')` |
| **个人 API 端点不能入库** | `qwen-ocr-bridge` 素材里的百炼专属端点（形如 `llm-<专属ID>.cn-beijing.maas.aliyuncs.com/compatible-mode/v1`）是用户专属，写进文章/代码会泄露 | 文章里全部改成 `<你的专属ID>` 占位 + 环境变量注入写法 |

### 敏感信息红线（务必遵守）

- 素材中出现的 **个人专属 API 端点**（如 `llm-xxxxx.cn-beijing.maas.aliyuncs.com`）一律用占位符，**不要照抄任何人的端点**
- API Key 只允许 `sk-你的Key` 占位形式出现，真实 Key 严禁写进代码/文章/提交
- 设备序列号等个人设备信息（素材里的 ADB 序列号）不要写进文章
- 环境变量 `DASHSCOPE_API_KEY` 的**值**禁止读取/打印

### 待办 / 下一步

- [x] **上线部署**：已完成并推送（commit `cad8f49`，线上验证通过）
- [ ] 可选：把 `site/` 源码 push 到仓库 `src` 分支备份
- [ ] 用户后续会继续加文章：`cd site; npx hexo new "标题"` 然后写 markdown，重新编译部署（流程见上文"上线部署流程"）

## 2026-09-16 本次维护会话记录

### 背景

把本地素材 `素材/程序员电子名片.htm`（VS Code 风格的整页电子名片）适配进首页，并补上缺失的深/浅色切换。

### 本次改造

- **首页电子名片**（`themes/matery/layout/_partial/home-about.ejs`）：在原个人介绍**上方**新增深色 IDE 卡片
  - 自写 canvas 粒子背景（无外部依赖，未用 CDN）、打字机效果、技能标签、GitHub 联系入口
  - **邮箱项已删除**（隐私）；头像换成新图（覆盖 `themes/matery/source/medias/avatar.jpg`）
  - 文案调整：role「全栈开发工程师(自封的)」、`9年Linux使用时长`
  - 代码行加了**逐行向下扫过的光波**动画（`.hc-code::after` + `nth-child` 递进延迟）
- **首页底部**（`layout/index.ejs`）：新增 `// 代码永不眠 | 最后编译时间: …`，仅首页第 1 页显示，浅灰底
- **深/浅色模式切换**：
  - 导航栏新增 🌙/☀️ 按钮（`layout/_partial/navigation.ejs`）
  - `source/js/matery.js`：切换 `body.DarkMode` 并写入 `localStorage.isDark`（原主题只读不写）
  - `layout/_partial/head.ejs` 引入 `css/dark.css`，加载顺序改为 **matery → dark → my**
  - `_config.yml` 增加 `libs.css.dark: /css/dark.css`
- **样式**：新增样式全部在 `source/css/my.css`，名片用 `#home-ide` 作用域，避免污染浅色主题
- **`.gitignore`**：新增忽略 `素材/`（含个人邮箱，禁止入库）

### 踩过的坑（本次）

| 坑 | 说明 | 解法 |
| --- | --- | --- |
| 深色模式是"半成品" | matery 的 `dark.css` 抄自别的博客：引用不存在的 `/medias_webp/*.webp`、依赖不存在的 `.Cuteen_DarkSky` 元素，且**缺 `body.DarkMode` 底色** | 在 `my.css` 补 `body.DarkMode{background-color}` 及自定义组件适配 |
| 深色按钮是"死代码" | 主题 JS 只读 `isDark` 从不写入，布局里也没有 `#sum-moon-icon`/`.theme-btn` 元素 → 页面上根本找不到开关 | 自己加按钮 + 点击写入逻辑 |
| 头像在深色下变暗 | `dark.css` 有 `body.DarkMode img{filter:brightness(.7)}` | `#home-ide .hc-avatar-img{filter:none}` 覆盖 |
| 名片光波动画漏搬 | 原素材 `.highlight::after` 的扫光容易漏 | 补成逐行扫描（`hc-shine`） |
| CSS 加载顺序 | `my.css` 若在 `dark.css` 之前，自定义覆盖会被压 | head 里改为 matery → dark → my |

### 敏感信息检查（推送前已做）

- `素材/`（含真实邮箱）已 gitignore，`git status` 不再出现
- 全仓库关键词扫描：邮箱相关关键词仅命中**被忽略的素材文件**与第三方库（`libs/`），站点产物无邮箱
- 个人 API 端点：文章与 `search.xml` 中均为 `<你的专属ID>` 占位，无真实 `llm-<ID>` 域名
- 无 `sk-` 真实 Key、无设备序列号

### 部署

- 已 `npx hexo clean; npx hexo g` 并把产物同步到仓库根目录，提交推送 master

## 2026-09-30 本次维护会话记录

### 本次改造

- **深/浅色自动切换（按访问设备时间）**
  - `themes/matery/source/js/matery.js`：新增 `getPreferredDark()`——有手动偏好（`localStorage.isDark` 为 `'1'`/`'0'`）就用手动，否则按**访问者自己的设备时间**判断：**19:00 ~ 次日 7:00 自动深色**，其余浅色。
  - `themes/matery/layout/layout.ejs`：在 `<body>` 开头加了一段内联脚本，在首屏渲染前就套上 `body.DarkMode`，**消除「先亮后暗」的闪烁（FOUC）**。
  - 行为：点过 🌙/☀️ 按钮 → 永久记住该偏好；从没点过 → 每次进站按时间自动决定。原夜间 toast 提醒因此基本不再触发（夜间已自动变暗）。
- **访问统计：不蒜子 → Vercount**
  - `themes/matery/layout/layout.ejs`：把 `<script async src=".../busuanzi.pure.mini.js">` 换成 `<script defer src="https://events.vercount.one/js">`。
  - Vercount（<https://vercount.one>）是**不蒜子的兼容替代**：仍会写 `busuanzi_value_site_pv` / `busuanzi_value_site_uv`（以及 `vercount_value_*`），所以 `footer.ejs` 的标签**一行没动**；首次访问会自动把不蒜子的历史数据同步过来。
  - `_config.yml` 的 `busuanziStatistics` 配置项**保留原名**（`footer.ejs` / `post-detail.ejs` 还在引用它），只是补了注释说明它现在控制 Vercount。
  - 选它的原因：不蒜子用 Referer 识别站点，Firefox 严格隐私模式 / Safari / 移动端常丢失 Referer 导致接口 400、页面数字空白，且高峰期易 502；Vercount 改用 POST，响应更快更稳，并有 localStorage 缓存兜底。

### 关于「绑了自定义域名后看不到访问统计」的排查结论

- **与自定义域名无关**。实测不蒜子接口（带 `Referer: https://www.202606121.xyz/`）返回 `{"site_pv":161,"site_uv":131,"version":2.4}`，说明该域名**一直在正常计数**（当时 161 次访问 / 131 人）。
- 不带 `Referer` 时接口返回 **HTTP 400 Bad Request**；所以浏览器一旦屏蔽/剥离 Referer（隐私设置、广告拦截插件拦 `busuanzi.ibruce.info`），页面上的数字就会空白。
- `<user>.github.io` 自动跳转到自定义域名，是 GitHub Pages 配了自定义域名的**正常 301 行为**，与统计无关。
- 换 Vercount 后应能规避这类空白问题（POST + 缓存兜底）。

### 踩过的坑（本次）

| 坑 | 说明 | 解法 |
| --- | --- | --- |
| 改主题 `.ejs` 前先确认编码 | PowerShell 5.1 控制台显示中文乱码，容易误判文件是 GBK | 主题文件实际是 **UTF-8**（用 Read 工具 / Python 解码确认），编辑工具按 UTF-8 处理即可 |
| Vercount 非官方 CDN 不可用 | 网上流传的 `cn.vercount.icu` 实测 `HTTP 000`（连不上） | 用官方文档给的 `https://events.vercount.one/js`（实测 200 / 约 1.4s） |

### 敏感信息检查（推送前已做）

- 本次改动只涉及主题模板 / JSON 配置 / README，未引入任何 Key、端点、邮箱、设备序列号
- `git status` 复核，`素材/`、`site/` 均被 gitignore，不会入库

### 部署

- `npx hexo clean; npx hexo g` 后把产物同步到仓库根目录，提交推送 master（本次一并 push）

## 2026-10-07 本次维护会话记录

### 本次改造

- **新增文章** `flutter-android-build-pitfalls.md`：《Flutter 安卓打包踩坑实录：Gradle 卡死、NDK 偷跑与增量缓存崩溃》
  - 素材来源 `素材/文章/项目记录.md` 的「坑点记录」部分，**只保留踩坑内容**，已剔除具体项目信息（项目名 / applicationId / 改造功能等），全部 generic 化。
  - 分类「折腾记录」，标签 Flutter / Android / Gradle / 打包 / 折腾记录。
  - 8 个坑：①卡在 `assembleRelease` 直连 Maven Central ②偷下 NDK ③Gradle 锁冲突 ④Kotlin 增量缓存损坏 ⑤并行构建 AGP 竞态 ⑥全局 `init.gradle` 触发 `FAIL_ON_PROJECT_REPOS` ⑦Gradle 堆过大反向卡死 ⑧杂项（无 `gradlew`、发行包镜像、compileSdk、AS 集成、插件、keystore）。
- 编译产物已同步到仓库根目录并推送 master。

### 踩过的坑（本次）

| 坑 | 说明 | 解法 |
| --- | --- | --- |
| **hexo 对中文加粗的渲染 bug** | 闭合 `**` 紧跟中文字（如 `**X**把它`）时，inline bold 不渲染，原样输出 `**`；更坑的是**同一行存在多个内联 code span 时，即使两侧留了空格也会失效**（如 `需要 **Flutter 插件** 把它…` 一行里另有 5+ 个反引号代码块） | 用脚本逐行 `marked.parse` 检查残留 `**` 定位；改用 `<strong>` 标签，或把加粗短语收尾到标点/加分隔空格 |
| **GBK 控制台看不清问题** | PowerShell 5.1 下 Select-String 输出中文乱码，无法肉眼核对渲染结果 | 写 Node/Python 脚本按 UTF-8 处理 + `sys.stdout.reconfigure(encoding='utf-8')`，用「是否残留 `**`」等结构化判断代替肉眼看 |
| **产物同步别用 `robocopy /MIR`** | `/MIR` 会把根目录里不属于 public 的东西（`.git`、`.github`、`README.md`、`site/`、`素材/`）当多余项删掉 | 先枚举产物清单（`2022 2026 about archives categories css js libs medias tags 404.html favicon.png index.html search.xml`）逐个删除，再 `Copy-Item public\* → 根目录` |

### 敏感信息检查（推送前已做）

- 全仓库（排除 `libs/`、`site/`）扫描：无 `sk-` 真实 Key、无 `llm-<ID>` 端点、无隐私邮箱（命中的仅第三方库注释与已 gitignore 的 `素材/`）
- `素材/`、`site/` 均 gitignore，不入库

### 部署

- `npx hexo clean; npx hexo g` 后把产物同步到仓库根目录，提交推送 master（本次一并 push）

