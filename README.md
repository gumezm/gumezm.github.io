# gumezm.github.io

个人站点仓库：根目录是落地页，`blog-src/` 是 Hugo 博客源码，`blog/` 是博客构建产物。

博客部分基于 [her-cat/hugo-papermod-starter](https://github.com/her-cat/hugo-papermod-starter) ——
一个基于 [Hugo PaperMod](https://github.com/adityatelange/hugo-PaperMod) 的开箱即用 Hugo 站点，
把「主页头像 + 首页只列最新 3 篇 + 左文右图列表 + 分类/系列 meta + 列表封面缩略图 + 正文图片尺寸/懒加载」等改造
直接内建好。

> 下文凡出现 `layouts/`、`assets/`、`config.yaml` 等相对路径，均指 **`blog-src/` 目录下**。
> 目录分工、构建命令与发布方式见后面「本仓库的构建与发布」一节。

## 环境要求

- Hugo **extended ≥ 0.146.0**（本站使用 0.167.0）
- git

## 快速开始

```bash
git clone --recurse-submodules git@github.com:gumezm/gumezm.github.io.git
cd gumezm.github.io
```

如果克隆时忘记加 `--recurse-submodules`：

```bash
git submodule update --init --recursive
```

> 不要直接把 `hugo server` 当成最终预览——产物里的链接是按 `config.yaml` 的 `baseURL`
> 生成的绝对地址，和线上表现并不一致。本地预览的正确做法见后面的「本地预览」一节。

## 目录与改造清单

| 文件 | 改造初衷 | 说明 |
| --- | --- | --- |
| `layouts/home.html` | 去掉主页冗余分页 | 首页只显示最新 3 篇 + 「查看更多」，不生成 `/page/2/` |
| `layouts/list.html` | 提高列表信息密度 | 左文右图（`entry-content-wrapper` / `entry-cover-wrapper`） |
| `layouts/_partials/home_info.html` | 主页个人信息展示 | 在 PaperMod 模板上叠加头像（响应式，见 `blank.css`） |
| `layouts/_partials/post_meta.html` | 展示分类与系列 | 在 PaperMod 模板上叠加 category/series 链接 |
| `layouts/single.html` | 文章页不显示 description | 在 PaperMod 模板上去掉可见的 `post-description`（`<meta name="description">` 保留） |
| `layouts/_partials/cover.html` + `cover-list-thumbnail.html` | 列表封面缩略图 | 列表封面裁剪为 480×240，正文用响应式原图 |
| `layouts/_partials/head.html` | 标题分隔符用 `-` | 在 PaperMod 模板上仅改 `<title>` 分隔符 |
| `layouts/_partials/footer.html` | 优化菜单滚动性能 | 在 PaperMod 模板上，菜单滚动位置用 rAF + 防抖保存 |
| `layouts/_partials/extend_head.html` | 站点级 head 扩展 | 参数化：`preconnect` / `umamiId` / `walineHost` / `preloadFont`，默认关闭 |
| `layouts/_markup/render-image.html` | 图片防抖动 | 正文图片补齐 `width` / `height` / `loading` / `decoding` |
| `layouts/robots.txt`、`layouts/sitemap.xml` | SEO | sitemap 按页面类型设置 priority，robots 屏蔽 tags 分页 |
| `assets/css/extended/*.css` | 样式 | `blank.css`（布局）、`reading.css`（阅读体验）、`fonts.css`（本地 Inter 字体） |

详情见：<https://her-cat.com/posts/2025/10/08/hugo-paper-mod/>

## 终末地风味主题

博客默认使用「终末地风味」界面，按
[endfield-ui-skill](https://github.com/hhikr/endfield-ui-skill) 的规范适配到 PaperMod 的 DOM，
**没有使用任何游戏官方素材**（字体、标志、图标、立绘、截图均未使用）。

页头有一组「原版 │ 终末地」切换按钮：选择写进 `localStorage["pref-style"]`，
刷新和跨页面都保持；选「原版」时所有终末地样式整体失效，回退到 PaperMod 原生外观。

### 变体卡

```text
变体：standard（yellow #FFFA00 / neutral / standard / contours / standard / full）
主视觉：首页 GUMEZM 宽体字标 + 切角头像画框；最近更新与篇数取自站点真实内容
页面类型：首页展示，其余阅读
```

### 实现要点

| 文件 | 作用 |
| --- | --- |
| `assets/css/extended/zz-endfield.css` | 主题全部样式；所有规则都限定在 `html[data-ef="endfield"]` 下，`classic` 模式自动失效 |
| `assets/css/extended/saira.css` | 自托管 Saira 可变字体（`wght` 100–900、`wdth` 50–125），仅在终末地模式被引用 |
| `static/fonts/saira/` | Saira 字体与 OFL 许可证 |
| `static/contours/` | 背景等高线 SVG（来自 skill 的 `assets/contours/`） |
| `layouts/baseof.html` | 在 `<html>` 上写 `data-ef="endfield"` 默认值 |
| `layouts/_partials/extend_head.html` | 首帧前按 `localStorage` 恢复风格，避免闪烁 |
| `layouts/_partials/extend_footer.html` | 切换按钮逻辑；终末地模式下注入同源 `@view-transition` 换页快门 |
| `layouts/_partials/header.html` | 页头的风格切换控件 |
| `layouts/_partials/home_info.html`、`list.html`、`single.html`、`archives.html`、`search.html`、`taxonomy.html` | 眉标（`// POSTS` 之类）、真实计数、标题下三色条 |

### 设计约束（改样式前先看）

- **只有一种强调色** `#FFFA00`，只表示「当前位置 / 可点击 / 醒目提醒」，只做填充，
  上面的字用墨色；浅色主题里不要把它当线条或文字颜色。
- 切掉一个角 = 可以按；正文、图片、展示栏目一律直角；`--radius` 固定为 0。
- 不使用投影、发光、渐变填充；分区用 1px 细线。
- 编号、日期、计数必须来自真实数据，不编造。
- 动效短、硬、机械，系统开启「减少动态效果」时全部静止。
- 深色主题是另一套完整取值（纸 `#191919`、板 `#222222`），不是简单反色。

### 内容侧用法

终末地模式下会在标题上方自动加英文眉标（中文标题的直译），映射表在
`layouts/list.html` 的 `$efEyebrows` 里；新增栏目页时给它补一条即可，想不出贴切英文就留空
（只显示中文标题）。这些元素都带 `ef-only` 类，切到原版模式即隐藏。


## 配置

主要参数见 `config.yaml`，重点：

- `params.homeInfoParams`：首页头像与文案（`ImageUrl` 支持 `assets/` 下的图片）。
- `params.schema.publisherType`：`Person`（个人）或 `Organization`（组织）。
- `params.extendHead.*`：head 扩展，全部 opt-in，默认关闭。
- `params.cover`、`ShowToc`、`ShowFullTextinRSS` 等沿用 PaperMod 官方参数。

## 更新 PaperMod

PaperMod 与 hugo-shortcode-gallery 都是 submodule，升级 = 在 submodule 里 checkout 新版本，
再回到主仓库提交更新后的 gitlink：

```bash
cd blog-src/themes/PaperMod
git fetch
git checkout <commit-or-tag>
cd ../../..
git add blog-src/themes/PaperMod
git commit -m "chore: bump PaperMod"
```

## 本仓库的构建与发布

本仓库是「落地页 + Hugo 博客」合体，目录分工：

| 路径 | 作用 |
| --- | --- |
| `/`（`index.html`、`css/`、`js/`、`images/`、`font/`） | 落地页（HTML5 UP Dimension 模板），手写维护，**不由 Hugo 生成** |
| `blog-src/` | Hugo 源码（即本 starter） |
| `blog/` | Hugo 构建产物，**直接提交进 git** |

发布方式与 starter 原版不同：这里**没有** `.github/workflows/hugo.yml`，
用的是 GitHub Pages 的 **Deploy from a branch**（分支 `main`、目录 `/`），
所以 `blog/` 的产物必须手动构建并提交，推上去 Pages 才会重新发布。

### 构建

```bash
# 首次 clone 后
git submodule update --init --recursive

# 生产构建（baseURL 取 config.yaml 里的值）
cd blog-src
rm -rf ../blog          # Windows: Remove-Item -Recurse -Force ..\blog
hugo --minify
cd ..
git add -A
git commit -m "..."
git push
```

> **每次构建前务必先删掉 `blog/`。** Hugo 只写不删，旧的内容哈希文件
> （例如 `stylesheet.<旧hash>.css`）会残留在产物里被一起提交。

### 本地预览

产物里的链接是按 `config.yaml` 的 `baseURL`（`https://gumezm.github.io/blog/`）
生成的绝对地址，所以直接用 `hugo server` 看到的和线上并不一致。
要预览真实效果，用本地 baseURL 重建一次：

```bash
cd blog-src
rm -rf ../blog
hugo --minify --baseURL "http://127.0.0.1:8123/blog/"

# 另开一个终端（在仓库根目录）
python -m http.server 8123 --directory .
# 浏览 http://127.0.0.1:8123/blog/
```

预览完**必须**用生产 baseURL 重新构建一次，否则会把本地地址提交上去。

---

## ⚠️ 换行符必须是 LF，否则整站会没有样式

Hugo 的 `resources.Fingerprint` 对**磁盘上读到的字节**算 sha256，写进 HTML 的
`integrity` 属性，同时把哈希当作文件名。而 git 提交时会把 CRLF 归一化成 LF。
一旦工作区是 CRLF、仓库里是 LF：

1. Hugo 按 CRLF 算哈希 → HTML 里的 `integrity` 和文件名都是 CRLF 版本；
2. GitHub Pages 送出的却是 LF 版本；
3. 浏览器重算哈希对不上，按 SRI 策略**拒绝加载整个样式表**；
4. 页面变成没有任何样式的裸 HTML，控制台报
   `Failed to find a valid digest in the 'integrity' attribute`。

这个故障**本地预览看不出来**（本地只有一份字节，校验必然通过），只在线上现形。

防御措施：

- 仓库根目录的 `.gitattributes` 已声明 `* text=auto eol=lf`；
- 但 submodule 是独立仓库，**享受不到根目录的 `.gitattributes`**，
  所以每个 submodule 还要单独设置：

```bash
git config core.autocrlf false
git -C blog-src/themes/PaperMod config core.autocrlf false
git -C blog-src/themes/hugo-shortcode-gallery config core.autocrlf false
```

一劳永逸的办法（对所有仓库生效，并覆盖 Git for Windows 的系统级默认值 `true`）：

```bash
git config --global core.autocrlf false
```

改完设置需要重新检出工作区才生效：

```bash
git rm --cached -r -q .
git reset --hard
```

**核对方法**——下面三处哈希必须完全一致：

```bash
# 1. 文件名里的哈希
ls blog/assets/css/

# 2. HTML 里 integrity 的哈希
grep -o 'integrity="sha256-[^"]*"' blog/index.html

# 3. 文件实际内容的哈希
certutil -hashfile blog/assets/css/stylesheet.<hash>.css SHA256
```

如果线上又出现「整站没样式」，先打开浏览器控制台看有没有上面那条 integrity 报错。

## 许可与致谢

- 基于 [adityatelange/hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)（MIT）。
- 图库使用 [mfg92/hugo-shortcode-gallery](https://github.com/mfg92/hugo-shortcode-gallery)。
- 字体为 [Inter](https://fonts.google.com/specimen/Inter) 与
  [Saira](https://github.com/Omnibus-Type/Saira)（均为 SIL OFL，Saira 许可证见 `blog-src/static/fonts/saira/OFL.txt`）。
- 终末地风味的规则与部件取自 [hhikr/endfield-ui-skill](https://github.com/hhikr/endfield-ui-skill)；
  未使用《明日方舟：终末地》的任何官方素材。
- 本仓库的改造部分同样以 MIT 发布。
