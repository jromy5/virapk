### com.didi.virtualapk.PluginManager
PluginManager是一个管理所有插件的类，它的主要API如下所示：

    /**
     * 初始化VirtualAPK核心框架，必须在Application的attachbaseContext中完成。
     */
    public void init()

    /**
     * 加载一个插件，然后触发它的Application逻辑，包括attachbaseContext、onCreate。
     * @param apk 插件的路径，必须以.apk结尾
     * @throws Exception 加载插件如果失败，会有异常抛出
     */
    public void loadPlugin(File apk) throws Exception

###  com.didi.virtualapk.internal.LoadedPlugin
LoadedPlugin表示一个已经加载过的插件，通过它的一系列get方法可以访问几乎所有插件相关的信息，如下所示，更多信息请参看源码。
```
public String getLocation()

public String getPackageName()

public PackageManager getPackageManager()

public AssetManager getAssets()

public Resources getResources()

public ClassLoader getClassLoader()
```

```NOTE``` : 除此之外，其他类很少会被用到，除非你有一些特殊的功能需要完成。