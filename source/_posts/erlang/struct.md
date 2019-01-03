---
title: "erl 数据结构"
date: "2015-05-02 13:01"
categories：
    -erlang
tags:
    
---
# struct

##record 
使用预编译指令`-record(Name, {Field1 [= Value1],
              ...
               FieldN [= ValueN]}).`
声明一个结构体。但本质上它是一个tuple，但erlang提供了一些方便的写法。

```
-record(myStruct,{a={1,2},b=[0,0],c}).

$ 创建实例
S=#myStruct{a=[2],c=1},
% 修改某项（修改的是拷贝后的数据）
T=S#myStruct{a=2},
% 模式匹配
#myStruct{a=A,b=B} = S,
% 取出某一项
S#myStruct.a.
```

##map 
虽然也是以#打头但不是同样的东西。
创建map`{Key1=>Value1,...,KeyN=>ValueN}`

有一个专门的模块maps来操作map：

```
1> M1 = #{name=>adam,age=>24,date=>{july,29}}.
#{age => 24,date => {july,29},name => adam}
2> maps:get(name,M1).
adam
3> M1#{age=>25}. %快捷方式
#{age => 25,date => {july,29},name => adam}
4> M2 = maps:update(age,25,M1).
#{age => 25,date => {july,29},name => adam}
5> map_size(M).
3
```
###已知key获取value
`` maps:get(key,Map) ``

`` #{key := A}=Map % 区别record ``
###增加、修改数据

`` Map#{newkey=>value}. ``
