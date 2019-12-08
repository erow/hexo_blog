---
title: Android
date: 2019-09-14 17:21:10
categories: project
tags:
    - android
---

- [安卓总览](#%e5%ae%89%e5%8d%93%e6%80%bb%e8%a7%88)
- [编程范式](#%e7%bc%96%e7%a8%8b%e8%8c%83%e5%bc%8f)
- [UI](#ui)
- [定时器](#%e5%ae%9a%e6%97%b6%e5%99%a8)
- [SQLlite](#sqllite)


## 安卓总览
[四大组件、六大布局、五大存储](https://www.jianshu.com/p/6f64ef6d547a)

[官方文档](https://developer.android.com/guide/components/fundamentals)

1. [Service](https://blog.csdn.net/guolin_blog/article/details/11952435)
2. [broadcastReceiver]()
3. [Activity](https://blog.csdn.net/Android_Tutor/article/details/5772285)
4. [ContentProvider]()

![Activity的生命周期](/blog_images/2019-09-15-16-56-26.png)

## 编程范式

## UI
1. [layout](https://blog.csdn.net/carson_ho/article/details/51719519)
2. shape:[为组件添加样式](https://blog.csdn.net/java_go_go_go/article/details/81220747)
3. 


## [定时器](https://blog.csdn.net/u012527802/article/details/70230110)

方法: `setRepeating(int type，long startTime，long intervalTime，PendingIntent pi)`


| type | 说明|
|  ----  | ----  |
|AlarmManager.ELAPSED_REALTIME |表示闹钟在手机睡眠状态下不可用，该状态下闹钟使用相对时间（相对于系统启动开始），状态值为3|
|AlarmManager.ELAPSED_REALTIME_WAKEUP|表示闹钟在睡眠状态下会唤醒系统并执行提示功能，该状态下闹钟也使用相对时间，状态值为2|
|AlarmManager.RTC|表示闹钟在睡眠状态下不可用，该状态下闹钟使用绝对时间，即当前系统时间，状态值为1；
|AlarmManager.RTC_WAKEUP|表示闹钟在睡眠状态下会唤醒系统并执行提示功能，该状态下闹钟使用绝对时间，状态值为0；
|AlarmManager.POWER_OFF_WAKEUP|表示闹钟在手机关机状态下也能正常进行提示功能，所以是5个状态中用的最多的状态之一，该状态下闹钟也是用绝对时间，状态值为4；不过本状态好像受SDK版本影响，某些版本并不支持；

时间以毫秒为单位

PendingIntent pi：是闹钟的执行动作，比如发送一个广播、给出提示等等。PendingIntent是Intent的封装类。
```java
PendingIntent sender=PendingIntent.getBroadcast(Main.this, 0, intent, 0);
```

## [SQLlite](https://www.jianshu.com/p/8e3f294e2828)

![存储方式](https://upload-images.jianshu.io/upload_images/944365-f46f7030ad445462.png?imageMogr2/auto-orient/strip|imageView2/2/w/1090/format/webp)

主要操作:

1. getWritableDatabase:创建 or 打开 可读/写的数据库（通过 返回的SQLiteDatabase对象 进行操作）
2. getReadableDatabase:创建 or 打开 可读的数据库（通过 返回的SQLiteDatabase对象 进行操作）
3. onCreate(SQLiteDatabase db): 数据库第1次创建时 则会调用.在继承SQLiteOpenHelper类的子类中复写.
4. onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)数据库升级时自动调用.在继承SQLiteOpenHelper类的子类中复写.
5. (Cursor) query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy, String limit): 查询
6. (long) insert(String table,String nullColumnHack,ContentValues values) 
7. (int) update(String table, ContentValues values, String whereClause, String[] whereArgs) 
8.  (int) delete(String table,String whereClause,String[] whereArgs) 
9. (void) execSQL(String sql):直接运行sql语句

