---
title: "erlang-rel"
date: "2017-03-23 09:42"
---

## 发布程序
### 1.创建节点
erlang总是以节点为单位组织的。  
使用`rebar create-node nodeid=xxx`可以生成`files,reltool.config`。里面包含了各种节点信息。
- [reltool.config][31eb1f61]  
下面列举几个比较常用的参数：  
mod_cond:  
  1. all: 将会包含所有引用的模块。即在搜索路径下存在ebin的模块都会被包含。
  2. ebin: 只包含本模块。
  3. app: 包含本模块和相关模块。
  4. derived ： 包含相关模块。

  lib_dir: 模块搜索目录。
  excl_sys_filters:将包含在系统目录下匹配的文件。
  profile: 影响`ncl_sys_filters, excl_sys_filters, incl_app_filters and excl_app_filters.`的作用
  1. development
  2. embedded
  3. standalone

  excl_lib:实验中，勿使用
  * otp_root :不包含erlang运行环境ert  

  app_file: 拷贝app file的程度
  1. keep
  2. strip
  3. all

## 2.升级
[Application.appup][f5a8808d] 文件描述了如何热升级代码
使用如下语法：
```
{Vsn,
  [{UpFromVsn, Instructions}, ...],
  [{DownToVsn, Instructions}, ...]}.
  ```
Instructions 由以下内容构成：

    {update, Mod}
    {update, Mod, supervisor}
    {update, Mod, Change}
    {update, Mod, DepMods}
    {update, Mod, Change, DepMods}
    {update, Mod, Change, PrePurge, PostPurge, DepMods}
    {update, Mod, Timeout, Change, PrePurge, PostPurge, DepMods}
    {update, Mod, ModType, Timeout, Change, PrePurge, PostPurge, DepMods}
      Mod = atom()
      ModType = static | dynamic
      Timeout = int()>0 | default | infinity
      Change = soft | {advanced,Extra}
        Extra = term()
      PrePurge = PostPurge = soft_purge | brutal_purge
      DepMods = [Mod]

升级过程可以参见：[Release Handling](http://erlang.org/doc/design_principles/release_handling.html)

1. 按照[Release][0a7e9248]中所述的方法创建版本。

2. 将释放转移到目标环境并安装在目标环境中。有关如何安装第一个目标系统的信息，请参阅系统原理。

3. 对开发环境中的代码进行修改，例如错误更正。

4. 在某些时候，需要制作新版本。相关的.app文件被更新，并写入一个新的.rel文件。

5. 对于每个修改的应用程序，创建应用程序升级文件.appup。在此文件中，描述了如何在旧版本和新版本的应用程序之间进行升级和/或降级。

6. 基于.appup文件，创建一个名为relup的版本升级文件。该文件描述如何在整个版本的旧版本和新版本之间升级和/或降级。

7. 创建新的发行包并将其传输到目标系统。

8. 使用发布处理程序解压缩新的发行包。

9. 安装新版本，也使用发布处理程序。这是通过评估relup中的指令来完成的。可以添加，删除或重新加载模块，应用程序可以启动，停止或重新启动，等等。在某些情况下，甚至需要重启整个仿真器。

[不同功能的模块的升级办法][c5561e88]

  [c5561e88]: http://erlang.org/doc/design_principles/appup_cookbook.html "升级办法"
  [0a7e9248]: http://erlang.org/doc/design_principles/release_structure.html "创建版本"
  [31eb1f61]: http://erlang.org/doc/man/reltool.html "reltool"
  [f5a8808d]: http://erlang.org/doc/man/appup.html "upgrade"
