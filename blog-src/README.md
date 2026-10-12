# PaperMod Starter

一个基于 [Hugo PaperMod](https://github.com/adityatelange/hugo-PaperMod) 的开箱即用 Hugo 站点，
把「主页头像 + 首页只列最新 3 篇 + 左文右图列表 + 分类/系列 meta + 列表封面缩略图 + 正文图片尺寸/懒加载」等改造
直接内建好，克隆即可运行，不需要再手动改文件。

## 环境要求

- Hugo **extended ≥ 0.146.0**（推荐 0.148.1，与本站 CI 一致）
- git

## 快速开始

```bash
git clone --recurse-submodules https://github.com/her-cat/hugo-papermod-starter my-blog
cd my-blog
hugo server
```

打开 http://localhost:1313/ 即可看到效果。

如果克隆时忘记加 `--recurse-submodules`：

```bash
git submodule update --init --recursive
```

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

## 配置

主要参数见 `config.yaml`，重点：

- `params.homeInfoParams`：首页头像与文案（`ImageUrl` 支持 `assets/` 下的图片）。
- `params.schema.publisherType`：`Person`（个人）或 `Organization`（组织）。
- `params.extendHead.*`：head 扩展，全部 opt-in，默认关闭。
- `params.cover`、`ShowToc`、`ShowFullTextinRSS` 等沿用 PaperMod 官方参数。

## 更新 PaperMod

```bash
cd themes/PaperMod
git fetch
git checkout <commit-or-tag>
cd ../..
git add themes/PaperMod
git commit -m "chore: bump PaperMod"
```

## 部署

`.github/workflows/hugo.yml` 会在 push 到 `main` 时构建并发布到 GitHub Pages
（需要在仓库 **Settings → Pages → Source** 选择 **GitHub Actions**）。

## 许可与致谢

- 基于 [adityatelange/hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)（MIT）。
- 图库使用 [mfg92/hugo-shortcode-gallery](https://github.com/mfg92/hugo-shortcode-gallery)。
- 字体为 [Inter](https://fonts.google.com/specimen/Inter)（SIL OFL）。
- 本仓库的改造部分同样以 MIT 发布。
