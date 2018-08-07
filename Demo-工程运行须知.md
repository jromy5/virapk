**请尽快更新至 [最新版本](https://github.com/didi/VirtualAPK/releases) ，老版本将不再维护。**

在 dev 分支持续开发尝鲜版，包含新特性并修复部分遗留bug，请关注并试用。

官方交流QQ群：656602897。**提问前请先查阅右侧导航中的相关文章，大多数常见问题都可直接找到解决方案**。

如果你们的产品采用了VirtualAPK，请告知我们。


在这之前，请确保Gradle和adb命令都在环境变量中。

一些注意事项：
```
1、如果有使用nativeActivity需要的用户请更新使用fix_native_activity分支并修改依赖为CoreLibrary，未来会合入主线。
```

## 构建环境建议直接使用Demo中的配置，**插件构建强依赖构建环境，请不要轻易尝试修改**。
首先，连接手机并编译宿主工程
```
cd VirtualAPK
./gradlew clean assembleRelease
adb install app/build/outputs/apk/release/app-release.apk
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