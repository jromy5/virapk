**请尽快更新至 [最新版本](https://github.com/didi/VirtualAPK/releases) ，老版本将不再维护。**

在 dev 分支持续开发尝鲜版，包含新特性并修复部分遗留bug，请关注并试用。

官方交流QQ群：656602897。**提问前请先查阅右侧导航中的相关文章，大多数常见问题都可直接找到解决方案**。

如果你们的产品采用了VirtualAPK，请告知我们。

# VirtualAPK的特性
VirtualAPK是滴滴出行自研的一款优秀的插件化框架，主要有如下几个特性。
### 功能完备
- 支持几乎所有的Android特性；
- 四大组件方面

**四大组件均不需要在宿主manifest中预注册，每个组件都有完整的生命周期。**

1. Activity：支持显示和隐式调用，支持Activity的```theme```和```LaunchMode```，支持透明主题；
2. Service：支持显示和隐式调用，支持Service的```start```、```stop```、```bind```和```unbind```，并支持跨进程bind插件中的Service；
3. Receiver：支持静态注册和动态注册的Receiver；
4. ContentProvider：支持provider的所有操作，包括```CRUD```和```call```方法等，支持跨进程访问插件中的Provider。
- 自定义View：支持```自定义View```，支持自定义属性和```style```，支持动画；
-  PendingIntent：支持```PendingIntent```以及和其相关的```Alarm```、```Notification```和```AppWidget```；
- 支持插件```Application```以及插件manifest中的```meta-data```；
- 支持插件中的```so```。

### 优秀的兼容性
- 兼容市面上几乎所有的Android手机，这一点已经在滴滴出行客户端中得到验证；
- 资源方面适配小米、Vivo、Nubia等，对未知机型采用自适应适配方案；
- 极少的Binder Hook，目前仅仅hook了两个Binder：```AMS```和```IContentProvider```，hook过程做了充分的兼容性适配；
- 插件运行逻辑和宿主隔离，确保框架的任何问题都不会影响宿主的正常运行。

### 入侵性极低
- 插件开发等同于原生开发，四大组件无需继承特定的基类；
- 精简的插件包，插件可以依赖宿主中的代码和资源，也可以不依赖；
- 插件的构建过程简单，通过Gradle插件来完成插件的构建，整个过程对开发者透明。

# VirtualAPK和主流开源框架的对比

如下是VirtualAPK和主流的插件化框架之间的对比。

|特性|DynamicLoadApk|DynamicAPK|Small|DroidPlugin|VirtualAPK
|-------------|:-------------:|:-----:|:-----:|:-----:|:-----:|
| 支持四大组件 | 只支持Activity | 只支持Activity |只支持Activity|全支持|全支持
| 组件无需在宿主manifest中预注册| √ |×|√|√|√
| 插件可以依赖宿主| √ | √|√|×|√
| 支持PendingIntent| × | ×|×|√|√
| Android特性支持| 大部分 | 大部分|大部分|几乎全部|几乎全部
| 兼容性适配| 一般 | 一般|中等|高|高
| 插件构建| 无 |部署aapt |Gradle插件|无|Gradle插件

### 为什么选择VirtualAPK？
已经有那么多优秀的开源的插件化框架，滴滴为什么要重新造一个轮子呢？

**1. 大部分开源框架所支持的功能还不够全面**
除了DroidPlugin，大部分都只支持Activity。

**2. 兼容性问题严重，大部分开源方案不够健壮**
由于国内Rom尝试深度定制Android系统，这导致插件框架的兼容性问题特别多，而目前已有的开源方案中，除了DroidPlugin，其他方案对兼容性问题的适配程度是不足的。

**3. 已有的开源方案不适合滴滴的业务场景**
虽然说DroidPlugin从功能的完整性和兼容性上来看，是一款非常完善的插件框架，然而它的使用场景和滴滴的业务不符。

DroidPlugin侧重于加载第三方独立插件，比如微信，并且插件不能访问宿主的代码和资源。而在滴滴打车中，其他业务模块均需要宿主提供的订单、定位、账号等数据，因此插件不可能和宿主没有交互。

其实在大部分产品中，一个业务模块实际上并不能轻而易举地独立出来，它们往往都会和宿主有交互，在这种情况下，DroidPlugin就有点力不从心了。

基于上述几点，我们只能重新造一个轮子，它不但功能全面、兼容性好，还必须能够适用于有耦合的业务插件，这就是VirtualAPK存在的意义。

**在加载耦合插件方面，VirtualAPK是开源方案的首选，推荐大家使用**。

#### 通俗易懂地说
1. 如果你是要加载微信、支付宝等第三方APP，那么推荐选择DroidPlugin；
2. 如果你是要加载一个内部业务模块，并且这个业务模块很难从主工程中解耦，那么VirtualAPK是最好的选择。

#### 抽象地说
1. 如果你要加载一个插件，并且这个插件无需和宿主有任何耦合，也无需和宿主进行通信，并且你也不想对这个插件重新打包，那么推荐选择DroidPlugin；
2. 除此之外，在同类的开源中，推荐大家选择VirtualAPK。

# VirtualAPK的工作过程
VirtualAPK对插件没有额外的约束，原生的apk即可作为插件。插件工程编译生成apk后，即可通过宿主App加载，每个插件apk被加载后，都会在宿主中创建一个单独的LoadedPlugin对象。如下图所示，通过这些LoadedPlugin对象，VirtualAPK就可以管理插件并赋予插件新的意义，使其可以像手机中安装过的App一样运行。
![VirtualAPK](https://github.com/didi/VirtualAPK/blob/master/imgs/va1.png)

### 如何使用
第一步： 初始化插件引擎

```
@Override
protected void attachBaseContext(Context base) {
    super.attachBaseContext(base);
    PluginManager.getInstance(base).init();
}
```

第二步：加载插件

```
public class PluginManager {
    public void loadPlugin(File apk);
}
```
**当插件入口被调用后，插件的后续逻辑均不需要宿主干预，均走原生的Android流程。**
比如，在插件内部，如下代码将正确执行：

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_book_manager);
    LinearLayout holder = (LinearLayout)findViewById(R.id.holder);
    TextView imei = (TextView)findViewById(R.id.imei);
    imei.setText(IDUtil.getUUID(this));
     
    // bind service in plugin
    Intent service = new Intent(this, BookManagerService.class);
    bindService(service, mConnection, Context.BIND_AUTO_CREATE);
    
    // start activity in plugin
    Intent intent = new Intent(this, TCPClientActivity.class);
    startActivity(intent);
}
```
# 探究原理
### 基本原理
- **合并宿主和插件的ClassLoader**
需要注意的是，插件中的类不可以和宿主重复
- **合并插件和宿主的资源**
重设插件资源的packageId，将插件资源和宿主资源合并
- **去除插件包对宿主的引用**
构建时通过Gradle插件去除插件对宿主的代码以及资源的引用

### 四大组件的实现原理
- **Activity**
采用宿主manifest中占坑的方式来绕过系统校验，然后再加载真正的activity；
- **Service**
动态代理AMS，拦截service相关的请求，将其中转给```Service Runtime```去处理，```Service Runtime```会接管系统的所有操作；
- **Receiver**
将插件中静态注册的receiver重新注册一遍；
- **ContentProvider**
动态代理IContentProvider，拦截provider相关的请求，将其中转给```Provider Runtime```去处理，```Provider Runtime```会接管系统的所有操作。

如下是VirtualAPK的整体架构图，更详细的内容请大家阅读源码。

![VirtualAPK](https://github.com/didi/VirtualAPK/blob/master/imgs/va.png)