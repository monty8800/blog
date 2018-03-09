---
layout: android
title: Android Studio 3.0开启Lambda表达式
date: 2018-03-08 15:20:26
tags:
- Android Studio
- Lambda
categories:
- Android
---
> Lambda表达式是建立在Java SDK1.8之上的，所以请确保你的Java版本高于或者等于1.8.

在Android Studio中使用Lambda表达式很简单，只需要在Moudule下的build.gradle文件做以下修改。注意：我测试使用的Android Studio版本是3.0，不同版本可能会有一些问题。

build.gradle配置文件

```groovy

defaultConfig {

...

compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
}

```

配置成功后，重新编译即可。
