---
title: 使用jsoup爬取玩Android网站导航数据
date: 2018-03-09 15:05:42
tags:
- 爬虫
- Java
categories:
- Java
---


> 知识点
 - 使用jsoup爬取[玩Android](http://wanandroid.com/navi)网站中的导航数据
 - 输出md格式的表格

<!--more-->

```java
/**
 * Created by monty on 2018/3/9.
 */
public class WanAndroid {
    private static String url = "http://www.wanandroid.com/navi#274";

    public static void main(String[] args) {
        Connection connect = Jsoup.connect(url);
        Document doc = null;
        try {
            doc = connect.get();
        } catch (IOException e) {
            e.printStackTrace();
        }

        Elements elementsByClass = doc.getElementsByClass("area_r");

        Elements divs = elementsByClass.get(0).children();

        List<Site> sites = new ArrayList<>();
        for (Element div : divs) {

            String category = div.getElementsByTag("h3").text();
            Site site = new Site();
            site.category = category;
            Elements siteList = div.getElementsByTag("a");
            List<Info> infoList = new ArrayList<>();
            for (Element ele : siteList) {
                String title = ele.text();
                String url = ele.attr("href");
                Info info = new Info();
                info.title = title;
                info.url = url;
                infoList.add(info);

            }
            site.infoList = infoList;
            sites.add(site);
        }

        printMarkDown(sites);

    }

    /**
     * |  网站   | 链接   |
     * | --------   | -----  |
     * | 玩Android     | http://wanandroid.com |
     * | Baidu        |   http://baidu.com   |
     * | Google        |    http://google.com    |
     */
    private static void printMarkDown(List<Site> siteList) {
        for (int i = 0; i < siteList.size(); i++) {
            Site site = siteList.get(i);
            System.out.println("## " + (i + 1) + "." + site.category);
            System.out.println("|  网站   | 链接   |");
            System.out.println("| --------   | -----  |");
            List<Info> infoList = site.infoList;

            for (int j = 0; j < infoList.size(); j++) {
                Info info = infoList.get(j);
                System.out.println("|" + info.title + "|" + info.url + "|");
            }
        }
    }

}


```
<!--more-->
输出结果：
```
## 1.常用网站
|  网站   | 链接   |
| --------   | -----  |
| Google开发者 | https://developers.google.cn/ |
| Github | http://www.github.com/ |
| stackoverflow | https://stackoverflow.com/ |
| 掘金 | https://juejin.im/ |
| CSDN | https://www.csdn.net/ |
| 简书 | https://www.jianshu.com/ |
| 开发技术周报 | http://www.androidweekly.cn/ |
| 开发者头条 | https://toutiao.io/ |
| segmentfault | https://segmentfault.com/t/android |
| androiddevtools | http://www.androiddevtools.cn/ |
| Google中文Blog | https://developers.googleblog.cn/ |
| 干货集中营 | http://gank.io/ |
| CodeKK | http://a.codekk.com/ |
| 小专栏 | https://xiaozhuanlan.com/ |
| 国内大牛 | http://www.wanandroid.com/article/list/0?cid=176 |
| 国外大牛 | https://github.com/android-cn/android-dev-com |
| Android源码 | https://www.androidos.net.cn/sourcecode |
| Material Design 中文版 | http://design.1sters.com/ |
| leetcode | https://leetcode.com/ |
| google mvn仓库 | https://dl.google.com/dl/android/maven2/index.html |
| jcenter仓库 | http://jcenter.bintray.com/ |
## 2.个人博客
|  网站   | 链接   |
| --------   | -----  |
| 罗升阳 | http://blog.csdn.net/luoshengyang/ |
| 邓凡平 | http://www.cnblogs.com/innost/ |
| 郭霖 | http://blog.csdn.net/guolin_blog |
| 鸿洋 | http://blog.csdn.net/lmj623565791
 |
| AigeStudio | http://blog.csdn.net/aigestudio |
| 亓斌 | http://blog.csdn.net/qibin0506 |
| 徐医生 | http://blog.csdn.net/eclipsexys |
| 阮一峰 | http://www.ruanyifeng.com/blog/ |
| MrSimp1e | http://blog.csdn.net/bboyfeiyu |
| 兰亭风雨 | http://blog.csdn.net/ns_code |
| 胡凯 | http://hukai.me/ |
| 技术小黑屋 | http://droidyue.com/ |
| 任玉刚 | http://blog.csdn.net/singwhatiwanna |
| 张涛 | https://kymjs.com/ |
| Weishu | http://weishu.me/archives/ |
| gityuan | http://gityuan.com/ |
| GcsSloop | http://www.gcssloop.com/timeline |
| stormzhang | http://stormzhang.com/ |
| 尼古拉斯.赵四 | http://www.wjdiankong.cn/ |
| hencoder | http://hencoder.com/ |
| 叫我旺仔 | http://www.jowanxu.top/archives/ |
| 一口仨馍 | http://blog.csdn.net/qq_17250009/ |
| _StriveG 博客 | https://guolei1130.github.io/ |
| Carson_Ho | https://www.jianshu.com/u/383970bef0a0 |
| 亦枫 | http://yifeng.studio/archives/ |
## 3.公司博客
|  网站   | 链接   |
| --------   | -----  |
| 美团点评 | https://tech.meituan.com/ |
| 悦跑圈技术团队 | https://joyrun.github.io/ |
| 网易考拉移动端团队 | https://kaolamobile.github.io/ |
## 4.开发社区
|  网站   | 链接   |
| --------   | -----  |
| 掘金 | https://juejin.im/timeline/android |
| 开源中国 | http://www.oschina.net/android/ |
| eoe Android社区 | http://www.eoeandroid.com/forum.php |
| ApkBus | http://www.apkbus.com/ |
| 中国谷歌开发者社区 | http://www.chinagdg.com/forum-36-1.html |
| CSDN | https://www.csdn.net/ |
| v2ex | https://www.v2ex.com/go/android |
| 51cto Android论坛 | http://bbs.51cto.com/forum-291-1.html |
## 5.常用工具
|  网站   | 链接   |
| --------   | -----  |
| JSON格式化 | http://www.wanandroid.com/tools/bejson |
| md5&sha | http://www.wanandroid.com/tools/digit |
| base64 | http://www.wanandroid.com/tools/base64 |
| 二维码 | http://www.wanandroid.com/tools/qrcode |
| 取色器 | http://www.wanandroid.com/tools/color |
| 进制转化 | http://www.wanandroid.com/tools/decimal |
| iconfont | http://www.iconfont.cn/ |
| tinypng | https://tinypng.com/ |
| 在线翻译 | https://translate.google.cn/ |
## 6.在线学习
|  网站   | 链接   |
| --------   | -----  |
| 慕课网 | http://www.imooc.com/course/list?c=android
 |
| 网易云课程 | http://study.163.com/category/android |
| 极客学院 | http://www.jikexueyuan.com/course/android/ |
| 腾讯课堂 | https://ke.qq.com/course/list?mt=1001&st=2003&tt=3021 |
| 麦子学院 | http://www.maiziedu.com/course/android/ |
| 人工智能 | http://mooc.study.163.com/smartSpec/detail/1001319001.htm |
| 牛客网 | https://www.nowcoder.com/ |
| 老罗视频教程 | http://luo.apkbus.com/ |
| mars视频 | http://mars.apkbus.com/ |
| gitchat免费区 | http://gitbook.cn/gitchat/free |
## 7.开放平台
|  网站   | 链接   |
| --------   | -----  |
| DevStore | http://www.devstore.cn/ |
| bmob后端云 | https://www.bmob.cn/ |
| fir.im | https://fir.im/ |
| 融云 | http://www.rongcloud.cn/ |
| 环信 | http://www.easemob.com/ |
| 讯飞开放平台 | http://www.xfyun.cn/ |
| Face++ | https://www.faceplusplus.com.cn/ |
| 七牛云 | https://www.qiniu.com/ |
| 蒲公英 | https://www.pgyer.com/ |
| bugly | https://bugly.qq.com/v2/ |
| 百度云推送 | http://developer.baidu.com/platform/s7 |
| 聚合数据 | https://www.juhe.cn/ |
| 小米推送 | https://dev.mi.com/doc/?page_id=1670 |
| 极光推送 | https://www.jiguang.cn/ |
| 涂图 | https://tutucloud.com/ |
| 腾讯信鸽 | http://xg.qq.com/xg |
| UMeng | http://www.umeng.com/ |
## 8.互联网资讯
|  网站   | 链接   |
| --------   | -----  |
| ReadHub | https://readhub.me/ |
| 创业邦 | http://wwv.cyzone.cn/ |
| 36kr | http://36kr.com/ |
| 品玩 | http://www.pingwest.com/ |
| 少数派 | https://sspai.com/tag/android |
| 泡面小镇 | http://www.pmtown.com/ |
## 9.求职招聘
|  网站   | 链接   |
| --------   | -----  |
| 智联 | https://www.zhaopin.com/ |
| 拉钩 | https://www.lagou.com/ |
| 100offer | https://cn.100offer.com/ |
| 内推网 | http://www.neitui.me/ |
| Boss直聘 | http://www.zhipin.com/ |
## 10.应用加固
|  网站   | 链接   |
| --------   | -----  |
| 360加固 | http://jiagu.360.cn/ |
| 网易云易盾 | http://dun.163.com/product/android-reinforce |
| 爱加密 | http://www.ijiami.cn/ |
## 11.三方支付
|  网站   | 链接   |
| --------   | -----  |
| 微信 | https://pay.weixin.qq.com/wiki/doc/api/index.html |
| 支付宝 | https://open.alipay.com/developmentAccess/developmentAccess.htm |
## 12.推送平台
|  网站   | 链接   |
| --------   | -----  |
| 小米推送 | https://dev.mi.com/console/appservice/push.html |
| 极光推送 | https://www.jiguang.cn/push |
| 华为推送 | http://developer.huawei.com/push |
| 百度云推送 | http://push.baidu.com/ |
## 13.三方分享
|  网站   | 链接   |
| --------   | -----  |
| Share SDK | http://sharesdk.mob.com/ |
| 友盟分享 | http://mobile.umeng.com/social |
## 14.地图平台
|  网站   | 链接   |
| --------   | -----  |
| 高德地图 | http://lbs.amap.com/ |
| 百度地图 | http://lbsyun.baidu.com/ |
| 腾讯地图 | http://lbs.qq.com/guides/maps.html |
## 15.直播SDK
|  网站   | 链接   |
| --------   | -----  |
| 七牛直播云 | https://www.qiniu.com/products/pili |
| 腾讯直播LVB | https://cloud.tencent.com/product/LVB |
| 百度音视频LSS | https://cloud.baidu.com/product/lss.html |
## 16.IM即时通讯
|  网站   | 链接   |
| --------   | -----  |
| 环信 | http://www.easemob.com/ |
| 百川云旺 | http://baichuan.taobao.com/product/im.htm |
| 融云 | http://www.rongcloud.cn/ |
## 17.Bug管理
|  网站   | 链接   |
| --------   | -----  |
| 腾讯Bugly | https://bugly.qq.com/v2/ |
## 18.后端云
|  网站   | 链接   |
| --------   | -----  |
| bmob后端云 | https://www.bmob.cn/ |
| LeanCloud | https://leancloud.cn/ |
## 19.WebView内核
|  网站   | 链接   |
| --------   | -----  |
| 腾讯x5 | https://x5.tencent.com/tbs/index.html |
## 20.创意&素材
|  网站   | 链接   |
| --------   | -----  |
| 暴走漫画制作器 | http://baozoumanhua.com/ |
| 素材设计 | https://www.chuangkit.com/startdesign |
| 无版权素材站 | https://unsplash.com/ |
| iconfont | http://www.iconfont.cn/ |
| iconstore | https://iconstore.co/ |
| Material Design设计模板与素材 | https://www.uplabs.com/android |
| awesome-design | https://github.com/gztchan/awesome-design/ |
| lottie素材库 | https://www.lottiefiles.com/ |
## 21.互联网统计
|  网站   | 链接   |
| --------   | -----  |
| 猎豹大数据 | http://cn.data.cmcm.com/rank |
| 百度移动统计 | https://mtj.baidu.com/data/mobile/device |
| 友盟数据报告 | http://www.umeng.com/reports.html
 |
## 22.快速开发
|  网站   | 链接   |
| --------   | -----  |
| QMUI Android | http://qmuiteam.com/android/page/index.html |
| 通用IM UI组件 | https://github.com/jpush/aurora-imui |

```
