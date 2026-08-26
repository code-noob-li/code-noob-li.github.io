# code-noob-li.github.io — 个人博客（Hexo + Matery）

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
