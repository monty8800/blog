---
layout: gradle
title: Gradle 3.4 使用新依赖项配置
id: 20180316153539
date: 2018-03-16 15:35:39
tags:
- Gradle
- Android
categories:
- Android
---

> Gradle 3.4 引入了新的 Java 库插件配置，允许您控制到编译和运行时类路径的发布（适用于模块间依赖项）。 Android 插件 3.0.0 正在迁移到这些新依赖项配置。 要迁移您的项目，只需更新您的依赖项以使用新配置，而非已弃用配置，如下表中所列。

<!--more-->

|  新配置   | 已弃用配置   |  行为  |
| --------   | -----  |
| implementation   | compile  | 依赖项在编译时对模块可用，并且仅在运行时对模块的消费者可用。 对于大型多项目构建，使用 implementation 而不是 api/compile 可以显著缩短构建时间，因为它可以减少构建系统需要重新编译的项目量。 大多数应用和测试模块都应使用此配置。  |
| api | compile  |  依赖项在编译时对模块可用，并且在编译时和运行时还对模块的消费者可用。 此配置的行为类似于 compile（现在已弃用），一般情况下，您应当仅在库模块中使用它。 应用模块应使用 implementation，除非您想要将其 API 公开给单独的测试模块。|
|compileOnly|provided|依赖项仅在编译时对模块可用，并且在编译或运行时对其消费者不可用。 此配置的行为类似于 provided（现在已弃用）。|
|runtimeOnly|apk|依赖项仅在运行时对模块及其消费者可用。 此配置的行为类似于 apk（现在已弃用）。|

就像当前稳定版本的 Android 插件一样，上面的配置对风味或构建类型特定的依赖项可用。 例如，您可以使用 api 让依赖项对所有变体可用，也可以使用 redApi 让其仅对模块的 red 变体可用。

> 注：compile、provided 和 apk 目前仍然可用。 不过，它们将在下一个主要版本的 Android 插件中消失。

> [官方迁移指南](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration.html)
