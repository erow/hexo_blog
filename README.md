# 个人博客 
[![Build Status](https://travis-ci.org/erow/hexo_blog.svg?branch=master)](https://travis-ci.org/erow/hexo_blog)
## 概述
虽然有私有服务器，但是个人维护还是麻烦了点。用一些免费的公有资源来搭建个人博客既方便又稳定。下面介绍该博客系统的构成。
1. 站点。GitHub的Page服务。 [主页地址](https://erow.github.io)
2. 引擎。Hexo。使用markdown文件编写博客，再生成相应的网站。
3. 编辑工具。 VScode。本地环境需要npm,hexo,git。VScode安装vscode-hexo,markdown all in one 插件。
4. 自动化。Travis CI。本地源文件--提交-->Github--触发构建-->travis ci--构建网站-->-github page。

整个配置过程有点复杂，但是用的都是免费资源，白嫖就是舒坦（虽然有aws的服务器）而且足够灵活。正所谓自己动手丰衣足食。
## hexo配置
[hexo总结](https://github.com/erow/hexo_blog/blob/master/source/_posts/hexo%E6%80%BB%E7%BB%93.md)
关于插件和主题配置还是一步步来，毕竟不能一口吃成胖子，过多花费时间在上面反而消耗精力而忘了初衷。这个博客系统的核心就是hexo。对于一般的写作[markdown](https://coding.net/help/doc/project/markdown.html)已经够用，但是作为博客光秃秃的文章也是不够的。而hexo可以根据markdown内容渲染出丰富好看的页面，甚至添加一些额外的功能（如评论，搜索）从而形成一个完整的博客。但是hexo本身还是不够便利，安装npm，部署网站还是太麻烦了。在一些时候根本不想安装这么多东西，这套系统的最小环境是一个git。
## Vscode配置
这里使用任何markdown编辑器都是可行的。用了好多编辑器，总的来说Vscode最合适。轻量、快捷、好看。 
### 图片问题
利用VsCode的插件自动将粘贴板的图片保存到source/blog_images下，防止混淆将站点配置的图片放在images下，而文章中的图放在blog_images下。
### markdown support

## 自动化 travis ci
hexo环境实际上部署在云端，通过提交代码触发构建过程,构建完毕后再强制提交到github page。Travis 感觉很像docker，它提供了一套运行环境，通过配置[.travis.yml](https://github.com/erow/hexo_blog/blob/master/.travis.yml)来执行构建代码。有空可以自己做一个编译环境，然后放到docker里编译，速度可能更快。国内的daocloud感觉挺不错。