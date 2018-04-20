---
title: RePlugin 插件之间共享组件方案
date: 2018-04-20 11:23:55
tags:
- RePlugin
- 插件化
categories:
- Android
---

<!--<p align="center">
  <a href="https://github.com/Qihoo360/RePlugin/wiki">
    <img alt="RePlugin Logo" src="https://github.com/Qihoo360/RePlugin/wiki/img/RePlugin.png" width="400"/>
  </a>
</p>
-->

## [RePlugin](https://github.com/Qihoo360/RePlugin) —— 历经三年多考验，数亿设备使用的，稳定占坑类插件化方案
> RePlugin是一套完整的、稳定的、适合全面使用的，占坑类插件化方案，由360手机卫士的RePlugin Team研发，也是业内首个提出”全面插件化“（全面特性、全面兼容、全面使用）的方案。
> 其主要优势有：
> * **极其灵活**：主程序无需升级（无需在Manifest中预埋组件），即可支持新增的四大组件，甚至全新的插件
> * **非常稳定**：Hook点**仅有一处（ClassLoader），无任何Binder Hook**！如此可做到其**崩溃率仅为“万分之一”，并完美兼容市面上近乎所有的Android ROM**
> * **特性丰富**：支持近乎所有在“单品”开发时的特性。**包括静态Receiver、Task-Affinity坑位、自定义Theme、进程坑位、AppCompat、DataBinding等**
> * **易于集成**：无论插件还是主程序，**只需“数行”就能完成接入**
> * **管理成熟**：拥有成熟稳定的“插件管理方案”，支持插件安装、升级、卸载、版本管理，甚至包括进程通讯、协议版本、安全校验等
> * **数亿支撑**：有360手机卫士庞大的**数亿**用户做支撑，**三年多的残酷验证**，确保App用到的方案是最稳定、最适合使用的

以上是RePlugin的基本介绍，了解更多详细[中文文档](https://github.com/Qihoo360/RePlugin)

### 我的需求
1. 多个插件之间需要使用到统一的Base Libaray中的BaseActivity
2. 插件之间需要共享组件，比如Fragment

### 实践
#### 多个插件之间需要使用到统一的Base Libaray中的BaseActivity
达到这个目的不难，只需2步实现，
 1. 建立library工程，在library中创建BaseActivity，并继承AppCompatActivity
 2. 插件Module compile library
 3. 插件中的Activity继承BaseActivity

运行～打包，ok，没问题，非常完美

但是当我在考虑加第二条需求 “插件之间需要共享组件，比如Fragment” 的时候，出问题了
##### 尝试1: 按照官方demo中反射插件中Fragment的方式，我封装了如下方法
```java
    public static Fragment fetchFragment(String pluginName, String fragmentClass) {
        if (TextUtils.isEmpty(pluginName) || TextUtils.isEmpty(fragmentClass)) {
            return null;
        }
        
        RePlugin.registerHookingClass(fragmentClass, RePlugin.createComponentName(pluginName, fragmentClass), null);

        //代码使用插件Fragment
        //获取插件的ClassLoader
        ClassLoader d1ClassLoader = RePlugin.fetchClassLoader(pluginName);
        Fragment fragment = null;
        try {
            //使用插件的Classloader获取指定Fragment实例
            fragment = d1ClassLoader.loadClass(fragmentClass).asSubclass(Fragment.class).newInstance();
        } catch (InstantiationException | IllegalAccessException | ClassNotFoundException e) {
            e.printStackTrace();
        }
        return fragment;
    }
```
然后，使用[RlPlugin.fetchFragment()]来打开Fragment
```java
    Fragment receiveManagement = RlPlugin.fetchFragment("ReceiveManagement", "com.rlzz.receivemanagement.BlankFragment");
    getSupportFragmentManager().beginTransaction().replace(R.id.framelayout, receiveManagement).commit();
```
- 重新编译插件
- 将编译出来的apk放到宿主的assets->external文件夹中
- 运行宿主程序
- 打开插件Fragment，报错：Fragment cannot be cast to android.support.v4.app.Fragment

出现错误，第一时间去看官方文档，发现有一段说明：
> Q：使用Fargemnt常见问题汇总？
错误1.“Fragment cannot be cast to android.support.v4.app.Fragment”问题（#378、#467）？
A：该类问题主要是由于Fragment继承自android.support.v4.app.Fragment，若直接继承android.app.Fragment不会有该类问题，两者的使用以及区别不再赘述。 插件内部出现该类问题，主要是由于在插件的dependencies中直接或间接引入v4包，包括引入第三方库时，其依赖关系导致最终引入与该第三方库版本相对应的v4包（尤其需要注意），以致插件与宿主host的都包含有v4包，最终导致宿主调用插件遇到类转化问题。
具体解决：
方式一：可以参照demo1。通过修改插件的build.gradle文件，以provided files('libs/fragment.jar')方式，骗过编译期，并借助Gradle 的exclude语法来解决版本冲突，移除support-v4，即：
configurations { all*.exclude group: 'com.android.support', module: 'support-v4' }
方式二：也可以借助打包工具来处理，具体可参照：replugin-resolve-deps-conflict，同样可解决此类问题。@lijunjie
另外，如果存在通过provided方式引入的库，而在插件中第三方库存在依赖的问题，可参照解决
错误2."Unable to instantiate fragment com.xx.TestFragment: make sure class name exists, is public, and has an empty constructor that is public"问题(#292)？
A：该问题最可能的原因是由于自定义Fragment类缺少无参的public构造函数，其次看看是否由于混淆导致类名找不到。 另外，不建议通过静态的方式添加fragment，即使用标签的方式。提倡通过动态方式添加fragment，即使用add的方式动态添加。
回答者：@SkyEric

按照官方的说明，也算是给了解决方案，那么下面开始照着做：
1. 
