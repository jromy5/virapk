#### Gradle版本推荐
目前VirtualAPK构建器还在持续完善之中，因此插件构建推荐使用Gradle 2.14.1版本，3.x版本可能有适配问题，我们正在努力去兼容各种Gradle版本。同时，待VirtualAPK构建器完善之后，其代码也将开源，敬请期待。
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