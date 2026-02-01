---
title: 脱壳再打包某梆梆免费加固APP
date: 2023-03-26 17:38:26
tags:
---
原文链接：https://www.52pojie.cn/thread-1106387-1-1.html
Apk: 梆梆免费加固apk，非2019最新版。

准备工具：
Apktool
dex-tools-2.1-SNAPSHOT
smali-2.3.4
DexExtractor
jd-gui  

整体也是这个流程：
脱壳拿到DEX --- 替换smali源码 --- 修复AndroidManifest.xml --- 重打包 --- 签名运行

一、使用Apktool解压apk
.\apktool.bat d xxx.apk

二、解压apk，拿出classes
这个时候我们发现，有梆梆加固的壳。所以不用再看classes了。搜索了相关的脱壳方法后，使用DexExtractor

三、使用DexExtractor脱壳
![DexExtractor脱壳](/images/bangbang-unpack/img1.png)


脱壳后得到了一堆加密的dex文件，将其放到dex文件夹下面，接下来尝试解密
使用DexExtractor的Decode.jar进行解密  
```bash
java -jar Decode.jar dex  
```
得到一堆解密后的dex
![解密后的dex文件](/images/bangbang-unpack/img2.png)


这么多的话有点不对劲，解开成java文件看一下：
使用dex-tools-2.1-SNAPSHOT的d2j-dex2jar将dex转换为jar文件，然后用jd-gui打开查看。
![查看Java代码](/images/bangbang-unpack/img3.png)
发现代码都在com.xxx.springclasses*.read-dex2jar，别的都是重复的代码。
因此将对应的dex转换为smail。然后放到最早使用apktool解压的文件夹下面，新建一个文件夹smali，放入相关代码。
使用smali-2.3.4将dex转换为smail  
```bash
 java -jar .\baksmali-2.1.3.jar .\dex\com.xxx.spring_classes_8842384.read.dex
 ```
文件会输出到out文件夹里面。

四、将smail文件放到解压APK的文件夹里
将对应的dex，放到对应的classes。取名字smali，smali_classes2。最后可以再打包的apk文件夹如下：

![APK文件夹结构](/images/bangbang-unpack/img4.png)
  
五、修改AndroidManifest
这个时候，根据jar文件里的内容，搜索哪个类继承了application或者MultiDexApplication，然后修改AndroidManifest.xml。将android:name修改为对应的class。
```xml
<application android:name="这里就是要修改的地方，改为：com.xx.xxApplication" android:theme="@style/AppTheme">
```
六、修改要修改的部分
这个部分就直接修改对应的smail文件就好了。

七、重新打包
Xxx为对应apktool解压apk的文件夹
```bash
.\apktool.bat b xxx -o app-release1.apk
```

八、重新签名
重新签名我使用的360的再签名工具，360Signer.exe。