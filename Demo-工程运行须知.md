在这之前，请确保Gradle和adb命令都在环境变量中。

构建环境建议：
```
Gradle                   2.14.1
com.android.tools.build  2.1.3
```
首先，连接手机并编译宿主工程
```
cd VirtualAPK
gradle clean iR
```

然后构建插件并将插件推送到手机sd卡的根目录
```
cd PluginDemo
gradle clean assemblePlugin
adb push app/build/outputs/apk/app-beijing-release-unsigned.apk /sdcard/Test.apk
```
当然，为了方便，也可以直接执行```./make.sh```来完成这整个过程。

然后打开宿主APP，点击 加载插件 按钮，即可运行插件，你将看到如下界面。

![VirtualAPK](https://github.com/didi/VirtualAPK/raw/master/imgs/demo-2.png)