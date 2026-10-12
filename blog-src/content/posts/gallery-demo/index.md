---
title: "图片画廊示例"
date: 2026-08-22
summary: "使用 hugo-shortcode-gallery 展示图片画廊。"
categories: ["使用指南"]
tags: ["图库", "shortcode"]
---

站点启用了第二个主题 `hugo-shortcode-gallery`，可以在文章中这样使用：

{{< gallery match="images/*" sortOrder="asc" rowHeight="150" margins="5" thumbnailResizeOptions="600x600 q90 Lanczos" previewType="blur" embedPreview=true loadJQuery=true >}}
