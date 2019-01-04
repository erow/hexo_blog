---
title: hexo总结
date: 2019-01-02 13:13:54
tags:
---
# 添加子类
[添加分类](https://whx4j8.github.io/2016/03/16/hexo-next-%E6%B7%BB%E5%8A%A0%E4%B8%BA%E6%96%87%E7%AB%A0%E6%B7%BB%E5%8A%A0%E5%88%86%E7%B1%BB/)
[模板](https://hexo.io/zh-cn/docs/templates)
# 手动添加文章
1. 将图片资源全部导入到blog_images,修改原文章的路径。
2. 在_post下创建文件夹，并将文章放入其中。
3. 每篇文章添加头部信息
    ```
    ---
    title: ***
    date: 201**-01-02 13:13:54
    tags:
      - 1
      - 2
    ---
    ```

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

# 主题小修改
原主题的背景图片仅显示在sidebar中，这样左右两边感觉被割裂了一样。背景图片也只能显示很小的一部分。因此我把它改到main-container中。通过以下步骤，很快就能定位并修改。
1. 在浏览器中inspect特定元素。对着有背景图片的地方右键查看，然后再控制台里找到有设置背景图片的元素。发现是一个id=sidebar的div。
2. 再往上包含article和sidebar的元素是class=ain-container的div
3. 在文件中搜查sidebar。将代码剪切到main-container中
   ``style="background: url(\{\{ url_for("images/background.png") \}\});" ``
4. 移除`.sidebar{}`中设置背景颜色的代码