---
title: hexo总结
date: 2019-01-02 13:13:54
tags:
---

# 主题
[项目主页](https://github.com/AlynxZhou/hexo-theme-aria)
NexT主题感觉字太小而且过于庞大，因此改用ARIA。其实我就搞了半天，雷同站台多，弄了半天插件没弄好，果断跑路。

## 安装
一定要安装以下插件。一开始以为是普通插件就忽视了，导致后面无法生成。
```
$ npm install --save hexo-renderer-njucks hexo-renderer-stylus hexo-generator-search hexo-generator-feed
```

克隆到`themes/aria`下:

```
$ git clone https://github.com/AlynxZhou/hexo-theme-aria themes/aria
```

修改站点的配置文件_config.yaml Theme to `aria`

```yaml
theme: aria
```
注意在aria主题的目录下也有一个_config.yaml需要配置。具体配置请查看aria的介绍。

## 修改站点信息
source目录下的普通文件会拷贝到生成文件public下，而markdown文件则是先被渲染再输出到生成文件下产生相应的html文件。因此网站的images和favicons应放在根目录的source下，以减少对theme的污染。

## Tex
不知道什么原因，要开启global才能使用，可能是哪里有选项开启选择性加载吧。
```yaml
mathjax:
  enable: true
  global: true
  cdn: https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.3/latest.js?config=TeX-MML-AM_CHTML
```

