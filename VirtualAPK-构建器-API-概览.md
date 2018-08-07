**请尽快更新至 [最新版本](https://github.com/didi/VirtualAPK/releases) ，老版本将不再维护。**

在 dev 分支持续开发尝鲜版，包含新特性并修复部分遗留bug，请关注并试用。

官方交流QQ群：656602897。**提问前请先查阅右侧导航中的相关文章，大多数常见问题都可直接找到解决方案**。

如果你们的产品采用了VirtualAPK，请告知我们。


#### Gradle版本推荐
请参考官方 demo 进行配置。
#### 参数说明
```
virtualApk {

    // 插件资源表中的packageId，需要确保不同插件有不同的packageId.
    packageId = 0x6f

    // 宿主工程application模块的路径，插件的构建需要依赖这个路径
    targetHost = '../../VirtualAPK/app' 

    //默认为true，如果插件有引用宿主的类，那么这个选项可以使得插件和宿主保持混淆一致
    applyHostMapping = true 

}
```
#### 构建命令
请通过```gradle clean assemblePlugin```来构建插件

```assemblePlugin```依赖于```assembleRelease```，这意味着：
- 插件包均是Release包，不支持debug模式的插件包
- 如果存在多个productFlavors，那么将会构建出多个插件包
- 插件包位于build目录下

![VirtualAPK](https://github.com/didi/VirtualAPK/blob/master/imgs/demo-3.png)