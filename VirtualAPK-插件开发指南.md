## 如何开发插件？
在VirtualAPK中，插件开发等同于原生Android开发，因此开发插件就和开发APP一样。

## 插件如何和宿主交互？
通过compile相同aar的方式来交互。
比如，宿主工程中compile了如下aar：
```
compile 'com.didi.foundation:sdk:1.2.0'
compile 'com.didi.virtualapk:core:0.9.0'
compile 'com.android.support:appcompat-v7:22.2.0'
```
但是插件工程需要访问宿主sdk中的类和资源，那么可以在插件工程中同样compile sdk的aar，如下：
```
compile 'com.didi.foundation:sdk:1.2.0'
```

这样一来，插件工程就可以正常地引用sdk了。并且，插件构建的时候会自动将这个aar从apk中剔除。

上述就是VirtualAPK中插件和宿主通信的基本方式。


然而，VirtualAPK仍然有一些小小的约束，如下注意事项，请务必仔细阅读。

## 目前暂不支持的特性
1. 暂不支持Activity的一些不常用特性（比如process、configChanges等属性），但是支持theme、launchMode和screenOrientation属性；
2. overridePendingTransition(int enterAnim, int exitAnim)这种形式的转场动画，动画资源不能使用插件的（可以使用宿主或系统的）；
3. 插件中弹通知，需要统一处理，走宿主的逻辑，通知中的资源文件不能使用插件的（可以使用宿主或系统的）。
4. 插件的Activity中不支持动态申请权限。

## 插件中四大组件的已知约束
#### Activity，支持LaunchMode和theme

- 透明Activity，不能有启动模式，并且主题中必须含有android:windowIsTranslucent属性；

```
<style name="AppTheme.Transparent">
    <item name="android:windowBackground">@android:color/transparent</item>
    <item name="android:windowIsTranslucent">true</item>
</style>
```

- 插件中调用宿主的四大组件，请注意Intent中的包名。

VirtualAPK对Intent的处理遵循Android规范，插件之间乃至插件和宿主之间，包名是区分它们的唯一标识。

在下面的例子中，假如宿主的包名是"com.didi.virtualapk"，然后在插件中启动一个宿主Activity，下面分别是错误和正确的示范：
```
// 错误的用法，因为此时intent中的包名是插件的包名
Intent intent = new Intent(this, HostActivity.class);
startActivity(intent);
 
// 正确的用法
Intent intent = new Intent();
intent.setClassName("com.didi.virtualapk", "com.didi.virtualapk.HostActivity");
startActivity(intent);
```

但是，如果想在插件中去访问插件的四大组件，那么就没有任何要求了，下面的代码会在插件Activity中尝试启动另一个插件Activity：
```
// 正确的用法，因为此时intent中的包名是插件的包名
Intent intent = new Intent(this, PluginActivity.class);
startActivity(intent);
```
#### Service，支持跨进程bind service
无约束

#### BroadcastReceiver

- 静态Receiver将被动态注册，当宿主停止运行时，外部广播将无法唤醒宿主；
- 由于动态注册的缘故，插件中的Receiver必须通过隐式调用来唤起。

#### ContentProvider，支持跨进程访问ContentProvider

1）分情况，插件调用自己的ContentProvider，如果需要用到call方法，那么需要将provider的uri放到bundle中，否则调用不生效；

```
Uri bookUri = Uri.parse("content://com.didi.virtualapk.demo.book.provider/book");
Bundle bundle = PluginContentResolver.getBundleForCall(bookUri);
getContentResolver().call(bookUri, "testCall", null, bundle);
```
2）插件调用宿主和外部的ContentProvider，无约束；

3）宿主调用插件的ContentProvider，需要将provider的uri包装一下，通过PluginContentResolver.wrapperUri方法，如果涉及到call方法，参考1）中所描述的;

```
String pkg = "com.didi.virtualapk.demo";
LoadedPlugin plugin = PluginManager.getInstance(this).getLoadedPlugin(pkg);
Uri bookUri = Uri.parse("content://com.didi.virtualapk.demo.book.provider/book");
bookUri = PluginContentResolver.wrapperUri(plugin, bookUri);
Cursor bookCursor = getContentResolver().query(bookUri,
 new String[]{"_id", "name"}, null, null, null);
```
#### Fragment
推荐大家在Application启动的时候去加载插件，不然的话，请注意插件的加载时机。
考虑一种情况，如果在一个较晚的时机去加载插件并且去访问插件中的资源，请注意当前的Context。比如在宿主Activity(MainActivity)中去加载插件，接着在MainActivity去访问插件中的资源（比如Fragment），需要做一下显示的hook，否则部分4.x的手机会出现资源找不到的情况。

```
String pkg = "com.didi.virtualapk.demo";
PluginUtil.hookActivityResources(MainActivity.this, pkg);
```
#### so文件的加载
为了提升性能，VirtualAPK在加载一个插件时并不会主动去释放插件中的so，除非你在插件apk的manifest中显式地指定```VA_IS_HAVE_LIB```为true，如下所示：
```
<application
    android:name=".VAApplication"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/HostTheme">

    <meta-data
        android:name="VA_IS_HAVE_LIB"
        android:value="true" />

    ...
    
</application>

为了通用性，在armeabi路径下放置对应的so文件即可满足需求。如果考虑性能请做好各种so文件的适配。
```