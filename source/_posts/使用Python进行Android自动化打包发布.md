---
title: 使用Python进行Android自动化打包发布
id: 20180307153534
date: 2018-03-07 15:35:34
tags:
- Android
- Python
- 持续集成
categories:
- Python
---
Python刚入门，都是一些基础用法。

> 实现的功能包括，从git更新代码->修改apk名称->上传到蒲公英->获取蒲公英返回的二维码地址

下面代码中需要蒲公英的uKey和api_key,需要去蒲公英的文档中获取(https://www.pgyer.com/doc/api#uploadApp)
<!--more-->
![这里写图片描述](http://img.blog.csdn.net/20171106084959732?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTUxMTkyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```python
# coding:utf-8
import os, sys, time, json

# os.system("sh buildApk.sh")

apkdir = os.getcwd() + "/build/outputs/apk/"


def gitpull():
    print "**************开始更新代码**************"
    log = os.system("git pull")
    print log
    print "**************代码更新完成**************"
    # log = os.system("git log")
    # print log


def uploadApk(file):
    print "**************开始上传APK**************"
    uKey = "your uKey" #在这里填入蒲公英平台上的uKey
    _api_key = "your api_key" #在这里填入蒲公英平台上的api_key
    upload_cmd = "curl -F \"file=@" + file + "\" -F \"uKey=" + uKey + "\" -F \"_api_key=" + _api_key + "\" https://qiniu-storage.pgyer.com/apiv1/app/upload"
    print upload_cmd
    content = os.popen(upload_cmd).read()
    print "上传返回结果 -> " + content
    print "**************上传完成**************"
    return content


def rename():
    currentTime = time.strftime("%Y%m%d%H%M%S", time.localtime())
    appname = "mes_debug_" + currentTime + ".apk"
    os.chdir(apkdir)
    os.system("ls")
    os.rename("app-debug.apk", appname)
    return appname


def build():
    print "**************开始构建APK**************"
    currentTime = time.strftime("%Y%m%d%H%M%S", time.localtime())
    print "当前时间:" + currentTime

    os.system("gradle clean build")
    appname = rename()
    print "**************APK打包完成**************"
    upload_result = uploadApk(apkdir + appname)

    res = json.loads(upload_result.replace("\n", ""))
    appQRCodeURL = str(res['data']['appQRCodeURL'])

    print "**************二维码地址 —> " + appQRCodeURL


def startBuildApk():
    gitpull()
    build()


startBuildApk()

```
有了自动打包脚本，下一步就可以集成到Jenkins上了。

> 参考：
> http://www.jianshu.com/p/b66d72862517
