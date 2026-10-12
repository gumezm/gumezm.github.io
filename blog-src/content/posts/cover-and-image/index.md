---
title: "封面与正文图片"
date: 2026-08-15
summary: "封面缩略图与正文图片的尺寸/懒加载处理。"
categories: ["使用指南"]
tags: ["图片", "性能"]
cover:
  image: cover-3.png
  alt: "cover"
---

列表页的封面会裁剪为 480×240 的缩略图；正文图片则会带上
`width` / `height` / `loading` / `decoding` 属性，减少布局偏移。

![示例图片](cover-3.png)
