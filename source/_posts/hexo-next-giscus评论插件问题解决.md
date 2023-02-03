---
title: hexo-next-giscus评论插件问题解决
date: 2023-02-03 11:24:08
categories:
tags:
  - hexo
---

博客使用了hexo-next-giscus评论插件，但是一直存在一个问题，就是从首页跳转至文章时评论插件加载不出来，必须`F5`刷新才能出来，而通过URL直接访问是可以直接加载的，不懂，但怀疑是懒加载的问题。

然后在GitHub上提交了Issue，没想到作者很快就给了回复和解决，实在太给力了！

提交Issues时一定要把自己遇到问题、现象、环境、配置等尽可能描述清楚，以便开发者复现和定位问题。关于提问可以参考之前[这篇文章](https://blog.boringhex.top/posts/bf7113d286b6/)。

本地node环境：

``` powershell
❯ node -v && npm -v
v16.18.0
8.19.2
```

包依赖信息：

``` powershell
❯ npm ls --depth 0
hexo-site@0.0.0 C:\Users\yp.wang\Desktop\blog.boringhex.top
├── bindings@1.5.0 extraneous
├── file-uri-to-path@1.0.0 extraneous
├── hexo-generator-archive@2.0.0
├── hexo-generator-baidu-sitemap@0.1.9
├── hexo-generator-category@2.0.0
├── hexo-generator-feed@3.0.0
├── hexo-generator-index@3.0.0
├── hexo-generator-searchdb@1.4.1
├── hexo-generator-sitemap@3.0.1
├── hexo-generator-tag@2.0.0
├── hexo-next-giscus@1.0.3
├── hexo-related-posts@1.5.1
├── hexo-renderer-ejs@2.0.0
├── hexo-renderer-marked@6.0.0
├── hexo-renderer-stylus@2.1.0
├── hexo-server@3.0.0
├── hexo-tag-cloud@2.1.2
├── hexo-theme-next@8.14.1
├── hexo-word-counter@0.1.0
├── hexo@6.3.0
└── nan@2.17.0 extraneous
```

hexo信息：

``` powershell
❯ hexo -v
INFO  Validating config
hexo: 6.3.0
hexo-cli: 4.3.0
os: win32 10.0.19044
node: 16.18.0
v8: 9.4.146.26-node.22
uv: 1.43.0
zlib: 1.2.11
brotli: 1.0.9
ares: 1.18.1
modules: 93
nghttp2: 1.47.0
napi: 8
llhttp: 6.0.10
openssl: 1.1.1q+quic
cldr: 41.0
icu: 71.1
tz: 2022b
unicode: 14.0
ngtcp2: 0.8.1
nghttp3: 0.7.0
```

参见这两个Issues：

[#610](https://github.com/next-theme/hexo-theme-next/issues/610)

[#7](https://github.com/next-theme/hexo-next-giscus/issues/7)
