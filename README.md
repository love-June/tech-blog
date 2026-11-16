# 我的技术笔记（Hugo + FixIt）

这是一个基于 [Hugo](https://gohugo.io/) 和 [FixIt](https://github.com/hugo-fixit/FixIt) 主题搭建的干净博客，外观风格参考了 [Lruihao/hugo-blog](https://github.com/Lruihao/hugo-blog)。

## 目录结构

```text
.
├── .github/workflows/   # GitHub Actions 自动构建并部署到 Pages
├── archetypes/          # 新建文章时的模板
├── config/              # 站点配置（按环境拆分）
├── content/posts/       # Markdown
├── go.mod / go.sum      # Hugo Module 依赖声明（自动拉取 FixIt 主题）
└── static/              # 静态资源（可选）
```

## 怎么写笔记

文章就是普通的 `.md` 文件，放进 `content/posts/` 即可。顶部加一小段元信息（front matter）：

```yaml
---
title: "文章标题"
date: 2026-11-16T10:00:00+08:00
categories: ["React Native"]
tags: ["React Native", "移动开发"]
draft: false
---

正文就是标准 Markdown。
```

- `categories` 决定它出现在哪个「分类」页；
- `tags` 是标签；
- `draft: true` 表示草稿，本地可见但不会发布；
- 可以一个文件夹一篇（`文件夹/index.md`，方便放配图），也可以直接放单个 `.md` 文件。

想加新方向（比如 Kotlin、Flutter）？只要在新文章的 `categories` 里写上新分类名，分类页就会自动多出一个入口，不需要改任何配置。

两种写法在本项目里都有示例：`content/posts/hello-world.md`（单文件）以及 `content/posts/react-native/`、`content/posts/swift/`、`content/posts/source-reading/` 三个文件夹分类。

## 本地运行（可选）

需要本机装 Go 和 Hugo extended 版本：

```bash
hugo server
```

如果不想在本地装环境也没关系，下面的方式直接在 GitHub 上构建，本地只需要写 `.md`。

## 部署到 GitHub Pages

1. 在 GitHub 新建一个仓库，把本项目里的所有文件推送到 `main` 分支。
   - 仓库名用 `<你的用户名>.github.io`，站点地址就是 `https://<你的用户名>.github.io/`；
   - 或者随便取名（比如 `blog`），站点地址是 `https://<你的用户名>.github.io/blog/`。
2. 打开仓库 Settings → Pages → Source，选择 **GitHub Actions**。
3. 打开 Settings → Actions → General → Workflow permissions，选择 **Read and write permissions**。
4. `baseURL` 已经配置为 `https://love-June.github.io/tech-blog/`（改仓库名的话记得同步改这里，带结尾斜杠）。
5. 提交推送后，Actions 会自动构建并部署，几分钟后就能访问了。

## 更新主题

```bash
hugo mod get -u github.com/hugo-fixit/FixIt@latest
hugo mod tidy
```

## 说明

- 主题 FixIt 采用 MIT 协议，可放心使用和二次开发；
- 本项目的文章内容版权完全归你所有；
- `go.mod` 里的模块路径已设为 `github.com/love-June/tech-blog`。
