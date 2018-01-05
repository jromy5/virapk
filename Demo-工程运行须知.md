在这之前，请确保Gradle和adb命令都在环境变量中。

构建环境：
```
windows下请更新dev分支代码
```

依赖：
```
1、如果有使用nativeActivity需要的用户请更新使用fix_native_activity分支并修改依赖为CoreLibrary
2、近期有开发者反馈在插件的Fragment中无法启动Activity我们第一时间做了修复，请使用dev分支的CoreLibrary或者依赖core-0.9.1-dev-beta.aar
3、在开发插件工程时可能会依赖多个第三方库，请注意保持他们所依赖的v7版本要和插件框架中的一致。
```

构建环境建议：
```
Gradle                   2.14.1
com.android.tools.build  2.1.3
```
首先，连接手机并编译宿主工程
```
cd VirtualAPK
./gradlew clean iR
```

然后构建插件并将插件推送到手机sd卡的根目录
```
cd PluginDemo
./gradlew clean assemblePlugin
adb push app/build/outputs/apk/app-beijing-release-unsigned.apk /sdcard/Test.apk
```
当然，为了方便，也可以直接执行```./make.sh```来完成这整个过程。

然后打开宿主APP，点击 加载插件 按钮，即可运行插件，你将看到如下界面。

![VirtualAPK](https://github.com/didi/VirtualAPK/raw/master/imgs/demo-2.png)