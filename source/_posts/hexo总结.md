---
title: aria主题
date: 2019-01-02 13:13:54
tags:
---
[项目主页](https://github.com/AlynxZhou/hexo-theme-aria)
NexT主题感觉字太小而且过于庞大，因此改用ARIA。其实我就搞了半天，雷同站台多，弄了半天插件没弄好，果断跑路。

## 安装
一定要安装以下插件。一开始以为是普通插件就忽视了，导致后面无法生成。
```
$ npm install --save hexo-renderer-njucks hexo-renderer-stylus hexo-generator-search hexo-generator-feed
```

## Clone This Repo

Clone it to `themes/aria`:

```
$ git clone https://github.com/AlynxZhou/hexo-theme-aria themes/aria
```

## Edit Site Config

Following needs to be changed in your **site's** `_config.yml`.

### Change Theme to `aria`

```yaml
theme: aria
```

source目录下的普通文件会拷贝到生成文件public下，而markdown文件则是先被渲染再输出到生成文件下产生相应的html文件。因此网站的images和favicons应放在根目录的source下，以减少对theme的污染。

## Tex
不知道什么原因，要开启global才能使用，可能是哪里有选项开启选择性加载吧。
```yaml
mathjax:
  enable: true
  global: true
  cdn: https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.3/latest.js?config=TeX-MML-AM_CHTML
```