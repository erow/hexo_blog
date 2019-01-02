# 个人博客
## 概述
虽然有私有服务器，但是个人维护还是麻烦了点。用一些免费的公有资源来搭建个人博客既方便又稳定。
1. 站点。GitHub的Page服务。 [主页地址](https://erow.github.io)
2. 引擎。Hexo。使用markdown文件编写博客，再生成相应的网站。
3. 编辑工具。 VScode。本地环境需要npm,hexo,git。VScode安装vscode-hexo,markdown all in one 插件。
4. 自动化。Travis CI。本地源文件--提交-->Github--触发构建-->travis ci--构建网站-->-github page。

## hexo配置
[使用aria主题](source/hexo%E6%80%BB%E7%BB%93/)
关于插件和主题配置还是一步步来，毕竟不能一口吃成胖子，过多花费时间在上面反而消耗精力而忘了初衷。
## Vs插件
### 图片问题
利用VsCode的插件自动将粘贴板的图片保存到source/blog_images下，防止混淆将站点配置的图片放在images下，而文章中的图片早blog_images下。
![source目录](/blog_images/2019-01-02-14-41-59.png)
### markdown support
