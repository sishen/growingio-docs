---
description: GrowingIO 无埋点 SDK 会自动采集用户行为数据、页面元素信息、设备信息等数据。
---

# Android 无埋点 SDK

{% hint style="success" %}
[「用户浏览事件」](android-imp.md)半自动采集方案已经上线，**SDK 2.8.4** 及以上版本支持。

* - 事件/元素采集更具针对性，提升采集效率
* - 优化采集逻辑，贴合业务场景，提升数据准确性
* - 浏览事件关联自定义事件和变量，减少研发埋点工作量
{% endhint %}

## GrowingIO集成无埋点 SDK

{% hint style="info" %}
无埋点 SDK 支持采集 Android 4.2 以上数据。
{% endhint %}

### 1. 添加依赖

Gradle编译环境（AndroidStudio）

**\(1\)在project级别的build.gradle文件中添加`vds-gradle-plugin`依赖**

```groovy
buildscript {
    repositories {
        jcenter()
        google()
    }
    dependencies {
        //gradle建议版本
        classpath 'com.android.tools.build:gradle:3.2.1'
        //GrowingIO 无埋点 SDK
        classpath 'com.growingio.android:vds-gradle-plugin:autotrack-2.8.7'
    }
}
```

**\(2\)在module级别的build.gradle文件中添加`com.growingio.android`插件、`vds-android-agent`依赖、项目ID和 URL Scheme**

URL Scheme的格式是growing.xxxxxxxxxxxxxxxx，它的获取方式有两种：

* 新产品的 URL Scheme ：登录官网 -&gt;点击“设置”icon -&gt; 点击“应用管理” -&gt; 点击“新建应用”-&gt; 选择添加Android应用 -&gt;  URL Scheme 为:growing.xxxxxxxxxxxxxxxx”中标黄字体。
* 现有产品的 URL Scheme ：登录官网 -&gt; 点击“设置” icon -&gt; 点击“应用管理” -&gt; 找到对应产品的URL Scheme。

![&#x5E94;&#x7528;&#x7BA1;&#x7406;&#x5165;&#x53E3;](../../.gitbook/assets/image%20%28112%29.png)

您的项目ID获取方式是：点击“设置”icon-&gt;点击“项目配置”-&gt;即可看到您的项目ID

```groovy
apply plugin: 'com.android.application'
//添加 GrowingIO 插件
apply plugin: 'com.growingio.android'

android {
    defaultConfig {
            //配置 GrowingIO 的项目 ID 和 URLScheme
            resValue("string", "growingio_project_id", "您的项目ID")
            resValue("string", "growingio_url_scheme", "您的URL Scheme")    
    }
}

dependencies {
    //GrowingIO 无埋点 SDK
    implementation 'com.growingio.android:vds-android-agent:autotrack-2.8.7'
}
```

### 2.添加URLScheme和权限

把URL Scheme添加到您的项目，以便使用[圈选](../../data-definition/circle/app.md)、[MobileDebugger](../growingio-debugger/) 时唤醒您的程序。将该产品的URLScheme添加到你的AndroidManifest.xml中的LAUNCHER Activity下。例如：

```markup
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.growingio.testdemo">
    
    <!-- GIO 需要的权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- GIO 需要的权限 -->
    
    <!--请注意<application/>标签中的name属性值（这里为android:name=".MyApplication"）必须为您的Application类-->
    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <!--请添加这里的整个 intent-filter 区块，并确保其中只有一个 data 字段-->
            <intent-filter>
                <data android:scheme="growing.您的URL Scheme" />
                <action android:name="android.intent.action.VIEW" />

                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
            </intent-filter>
            <!--请添加这里的整个 intent-filter 区块，并确保其中只有一个 data 字段-->
        </activity>
    </application>
</manifest>
```

### 3. 初始化SDK

请将以下 GrowingIO.startWithConfiguration加在您的Application 的 onCreate 方法中

```java
public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        GrowingIO.startWithConfiguration(this, new Configuration().trackAllFragments().setChannel("XXX应用商店"));
    }
}
```

（1）请确保将代码添加在`Application`的`onCreate`方法中，添加到其他方法中可能导致数据不准确。

（2）其中`GrowingIO.startWithConfiguration`第一个参数为`Application`对象。

（3）`trackAllFragments`方法用于把`Fragment`自动识别为页面，但一个界面中只能同时显示一个`Fragment`。

（4）`setChannel`方法的参数定义了“自定义App渠道”这个维度的值。

### 4. 代码混淆

如果你启用了混淆，请在你的proguard-rules.pro中加入如下代码：

{% hint style="danger" %}
#### **SDK 2.6.0 更新了混淆文件，前后混淆文件内容截然不同，请注意更新。** 
{% endhint %}

```text
-keep class com.growingio.** {
    *;
}
-dontwarn com.growingio.**
-keepnames class * extends android.view.View
-keepnames class * extends android.app.Fragment
-keepnames class * extends android.support.v4.app.Fragment
-keepnames class * extends androidx.fragment.app.Fragment
-keep class android.support.v4.view.ViewPager{
    *;
}
-keep class android.support.v4.view.ViewPager$**{
	*;
}
-keep class androidx.viewpager.widget.ViewPager{
    *;
}
-keep class androidx.viewpager.widget.ViewPager$**{
	*;
}
```

如果您使用了AndResGuard,请在白名单里添加GrowingIO,如下：

```text
R.string.growingio*
```

### **5. lambda 表达式支持配置项**

{% hint style="info" %}
 **Android SDK 2.7.4 及以上支持了 lambda 表达式，不包括 retrolambda ，不需要此配置**。
{% endhint %}

lambda 表达式目前业界主流两种，分别为 retrolambda 和 AndroidStudio 支持的 lambda。 SDK 目前只支持 AndroidStudio 自带的 lambda 表达式，并且对于 `android.enableD8.desugaring = true` 未做兼容。

 在`com.android.tools.build:gradle:3.2.1` 之后， google默认开启desugaring特性， 暂时需要用户手动关闭。

在项目根目录中 gradle.properties 文件中增加以下配置：

```text
android.enableD8.desugaring=false
```



{% hint style="danger" %}
**添加代码之后，请先Clean项目，然后再进行编译，并在你的 Android App 安装了 SDK 后重新启动几次 App，保证行为采集数据自动发送给 GrowingIO，以便顺利完成检测。**
{% endhint %}



## 重要配置

### **设置 Debug 模式**

#### **setDebugMode**

查看数据采集发送日志，能够在`Android Studio`中通过`Logcat`查看`GrowingIO`打印的数据发送日志，在 `APP` 的 `Application onCreate` 初始化`SDK`地方添加配置。

```java
setDebugMode(boolean debugMode);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| debugMode | boolean |  是 | false | 开启`GrowingIO`日志，true开启 |

**示例代码：**

```java
GrowingIO.startWithConfiguration(this,new Configuration()    
    //BuildConfig.DEBUG 这样配置就不会上线忘记关闭    
    .setDebugMode(BuildConfig.DEBUG));
```



### 设置 Test 模式

#### setTestMode

实时发送数据，开启则不遵循移动网络状态下数据发送大小限制以及采集数据缓存30秒发送策略。为了方便开发者查看日志，一般和`setDebugMode`一起使用。在 `APP` 的 `Application onCreate` 初始化`SDK`地方添加配置。

```java
setTestMode(boolean testMode);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| testMode | boolean |  是 | false | 开启测试模式，true开启 |

**示例代码：**

```java
GrowingIO.startWithConfiguration(this,new Configuration()
    //BuildConfig.DEBUG 这样配置就不会上线忘记关闭
    .setTestMode(BuildConfig.DEBUG));
```

\*\*\*\*

### **采集广告 Banner 数据**

#### **trackBanner**

采集`Banner`数据，应用的界面上方横向滚动的`Banner`广告。对于此类广告，如果您的应用通过ViewPager、AdapterView或者RecyclerView实现，请在Banner创建时（包括动态创建）调用下面的接口来采集数据。

```java
GrowingIO.getInstance().trackBanner(View banner,List<String> bannerDescriptions);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| banner | View |   是           |    无                 | ViewPager、AdapterView、RecyclerView 实现的 View |
| bannerDescriptions | List&lt;String&gt; |   是 |    无 | 广告内容描述，顺序需要跟 banner view 顺序一致 |

**示例代码：**

```java
//Banner 初始化代码
ViewPager banner = (ViewPager)findViewById(R.id.banner);
... 
//GrowingIO 采集 Banner 数据 
GrowingIO.trackBanner(banner.getViewPager(), Arrays.asList("banner1", "banner2"));
```

{% hint style="warning" %}
`Banner`描述和广告出现的顺序一致，调用通过`Log`查看或者[`MobileDebugger`](../growingio-debugger/#shi-yong-mobile-debugger-ce-shi-shu-ju)的方式检查配置是否正确。 查看 `Banner imp` 事件 `e` 字段中 `v` 是否为您设置的 banner description。
{% endhint %}

**检验数据发送日志示例：** 

```javascript
{    
    "s":"02015456-079b-49cc-8b42-a5cc6136f0ec",    // t 为事件类型 type    
    "t":"imp",     
    "tm":1531990474178,    
    "d":"com.growingio.android.test",    
    "p":"ConvenientBannerActivity",    
    "ptm":1531990413207,    
    "e":
    [        
        {            
            "x":"/MainWindow/LinearLayout[0]/FrameLayout[1]/FitWindowsLinearLayout[0]#action_bar_root/ContentFrameLayout[1]/FrameLayout[0]/RelativeLayout[0]/ConvenientBanner[0]#convenientBanner/LinearLayout[0]/RelativeLayout[0]/CBLoopViewPager[0]#cbLoopViewPager/ImageView[-]",
            "tm":1531990474179,            
            "idx":1,
            //假如现在滚动到第二个banner，V显示为您设置的描述
            "v":"banner 2"
        }    
    ]
}
```



### 采集 GPS 数据

#### setGeoLocation

精确采集`GPS`数据，请在获取坐标后，调用接口设置位置信息。当用户下一次切换页面，或者发生点击行为时，`GPS`数据会被发送回`GrowingIO`。

如果您不调用此接口也可以，我们会根据用户的`ip`定义用户位置，能够在最终的数据分析时看到`APP`用户地域分布。

```java
GrowingIO.getInstance().setGeoLocation(double latitude,double longitude);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| latitude | double |  是 |   无 | 纬度 |
| longitude | double |  是 |   无 | 经度 |

**示例代码：**

```java
//北京的经纬度
GrowingIO.getInstance().setGeoLocation(39.9046900000,116.4071700000);
```

{% hint style="warning" %}
对应的清除地理位置方法为 `clearGeoLocation();` 
{% endhint %}

\*\*\*\*

### **采集输入框数据**

#### **trackEditText**

GrowingIO 默认采集输入框内容改变次数，不采集文字，在您需要采集应用内某个输入框内的文字（例如搜索框）时使用。

当这个输入框失去焦点（包括应用退到后台），且输入框内容跟获取焦点前相比发生变化时，输入框内文字会被发送回 GrowingIO 。

```java
GrowingIO.getInstance().trackEditText(EditText editText);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| editText | EditText |   是 |   无 | 采集输入框 |

**示例代码：**

```java
EditText editText = (EditText) findViewById(R.id.edit_text);
GrowingIO.getInstance().trackEditText(editText);
```

{% hint style="warning" %}
1. 对于密码输入框，即便标记为需要采集，SDK也会忽略，不采集它的数据。
2. 在输入框失去焦点的时候或者`onPause`时，才会采集事件，如果输入完成，输入框光标仍然在闪动，则不会采集事件。为了保证数据准确性，请每次用户输入完成时，让输入框失去焦点，可使用`editText.setFocusable(false);`
{% endhint %}



### WebView 锚点链接页面访问

#### setHashTagEnable

开启之后，APP `WebView` 中的页面，如果用户点击带有锚点的跳转链接，则发送一次页面浏览事件，请在 `SDK` 初始化方法中设置，默认值为 false，即默认锚点链接不算做页面浏览事件。

GrowingIO 默认不会把`HashTag`识别成页面 URL 的一部分，对于使用`HashTag`进行页面跳转的单页面网站应用来说，可以启用它来标识页面，`HashTag`的值也会记录在页面URL中。

```java
GrowingIO.startWithConfiguration(this, new Configuration().setHashTagEnable(true));
```

**例如：**

点击 APP `WebView` 中代码混淆的锚点链接，URL 中`#`号后面为锚点，设置后 SDK 会发送页面浏览事件，它的链接为：

> [https://docs.growingio.com/docs/sdk-integration/android-sdk\#4-dai-ma-hun-xiao](https://docs.growingio.com/docs/sdk-integration/android-sdk#4-dai-ma-hun-xiao)



![&#x70B9;&#x51FB;WebView&#x4E2D;&#x7684;&#x951A;&#x70B9;&#x94FE;&#x63A5;](../../.gitbook/assets/image%20%28349%29.png)

SDK发送对应采集数据：

```javascript
{
    "u":"2e94075c-ead2-33ac-ab7f-f9611162efc4",
    "s":"78383569-3038-4bd4-b27c-349939367922",
    "tl":"Android SDK - 帮助文档",
    //t 为事件类型，page 为页面浏览事件
    "t":"page",
    "tm":1532408777047,
    "pt":"https",
    "d":"com.growingio.android.test::docs.growingio.com",
    //p 为浏览的页面
    "p":"StandardWebView::/docs/sdk-integration/android-sdk#4-dai-ma-hun-xiao",
    "rp":"https://docs.growingio.com/docs/sdk-integration/android-sdk#ji-cheng",
    "cs1":"GrowingIO",
    "appId":"fakeAppID",
    "v":"Android SDK - 帮助文档",
    "r":"WIFI",
    "gesid":434,
    "esid":153
}
```

### 多进程支持

#### setMutiprocess & supportMultiProcessCircle

多进程支持，`SDK`默认不支持多进程使用， 但是可以通过`confiuration`进行设置支持多进程。 在`Application onCreate` 中初始化SDK代码块中配置。

```java
//支持多进程数据采集
setMutiprocess(boolean setMutiprocess);
//支持多进程圈选
supportMultiProcessCircle(boolean smpc);
```

**参数说明：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| isMultiprocess | boolean |   是 |  false | 开启多进程数据采集 |
| smpc | boolean |   是 |  false | 开启多进程圈选 |

**示例代码：**

```java
GrowingIO.startWithConfiguration(this, new Configuration()
                  .supportMultiProcessCircle(true)                  
                  .setMutiprocess(true)                  
                  );
```

{% hint style="info" %}
1. 为什么不默认支持多进程？

        跨进程通信是一个相对较慢的过程， 默认不开启， 可以满足大部分用户的要求。

   2. 哪些进程需要初始化SDK？

        需要使用SDK功能的进程需要初始化SDK， 所有的UI进程 + 部分Service进程\(如果这些进程中涉及手动打点\)。
{% endhint %}



### GDPR 数据采集开关

**`SDK 2.3.2`** 以上版本支持

| 接口名称 | 含义 |
| :--- | :--- |
| disableDataCollect\(\) | 遵守欧洲联盟出台的通用数据保护条例，用户不授权，不采集用户数据 |
| enableDataCollect\(\) | 遵守欧洲联盟出台的通用数据保护条例，用户授权，采集用户数据 |



### Deep Link 回调参数获取

{% hint style="info" %}
**SDK 2.3.2** 开始提供 DeepLink Callback 接口，在 **SDK 2.8.4** 支持 App Links ，为了保证接收自定义参数并进行自定义跳转这一流程的完整性，App Links 沿用此接口，并新增一个参数，下文将详细解释。
{% endhint %}

#### setDeeplinkCallback  

通过获取回调参数，GrowingIO SDK将会传递指定活动页面参数至您的App，请根据此参数和业务场景，执行相关的交互。

在 GrowingIO SDK 代码初始化部分配置。

```java
//sdk >= 2.3.2 && sdk < 2.8.4
GrowingIO.startWithConfiguration(this, new Configuration()
            .setDeeplinkCallback(new DeeplinkCallback() {
                @Override
                public void onReceive(Map<String, String> params, int status) {
                    // 这里接收您的参数，匹配键值对，跳转指定 APP 内页面
                }
            }));
            
//sdk >= 2.8.4, 新增参数 long appAwakePassedTime
GrowingIO.startWithConfiguration(this, new Configuration()
            .setDeeplinkCallback(new DeeplinkCallback() {
                @Override
                public void onReceive(Map<String, String> params, int status, long appAwakePassedTime) {                             // 这里接收您的参数，匹配键值对，跳转指定 APP 内页面
                    // 这里接收您的参数，匹配键值对，跳转指定 APP 内页面
                }
            }));
```

**返回值说明：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x8FD4;&#x56DE;&#x503C;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">params</td>
      <td style="text-align:left">Map&lt;String,String&gt;</td>
      <td style="text-align:left">&#x81EA;&#x5B9A;&#x4E49;&#x53C2;&#x6570;&#xFF0C;&#x60A8;&#x81EA;&#x5B9A;&#x4E49;&#x7684;&#x952E;&#x503C;&#x5BF9;</td>
    </tr>
    <tr>
      <td style="text-align:left">status</td>
      <td style="text-align:left">int</td>
      <td style="text-align:left">DeeplinkCallback.SUCCESS &#xFF1A;&#x81EA;&#x5B9A;&#x4E49;&#x53C2;&#x6570;&#x83B7;&#x53D6;&#x6210;&#x529F;&#xFF1B;
        <br
        />DeeplinkCallback.PARSE_ERROR &#xFF1A;&#x89E3;&#x6790;&#x5F02;&#x5E38;&#xFF1B;DeeplinkCallback.ILLEGAL_URI
        &#xFF1A;&#x975E;&#x6CD5;URI&#xFF1B;
        <br />DeeplinkCallback.NO_QUERY : &#x81EA;&#x5B9A;&#x4E49;&#x53C2;&#x6570;&#x4E3A;&#x7A7A;&#x3002;DeeplinkCallback.APPLINK_GET_PARAMS_FAILED<b> </b>: <b>sdk2.8.4+&#xFF0C;</b>AppLink
        &#x7531;&#x4E8E;&#x7F51;&#x7EDC;&#x539F;&#x56E0;&#x83B7;&#x53D6;&#x81EA;&#x5B9A;&#x4E49;&#x53C2;&#x6570;&#x5931;&#x8D25;</td>
    </tr>
    <tr>
      <td style="text-align:left">appAwakePassedTime</td>
      <td style="text-align:left">long</td>
      <td style="text-align:left">
        <p><b>sdk2.8.4+&#xFF0C;&#x5355;&#x4F4D;&#x6BEB;&#x79D2;&#xFF0C;</b>App &#x5524;&#x9192;&#x5230;&#x6536;&#x5230;
          GIO callback &#x7684;&#x65F6;&#x95F4;&#xFF0C;&#x7528;&#x4EE5;&#x5224;&#x65AD;&#x7F51;&#x7EDC;&#x72B6;&#x6001;&#x4E0D;&#x597D;&#x7684;&#x60C5;&#x51B5;&#xFF0C;&#x5E94;&#x7528;&#x5DF2;&#x7ECF;&#x6253;&#x5F00;&#x5F88;&#x4E45;&#xFF0C;&#x624D;&#x6536;&#x5230;&#x56DE;&#x8C03;&#xFF0C;&#x5F00;&#x53D1;&#x4EBA;&#x5458;&#x51B3;&#x5B9A;&#x662F;&#x5426;&#x6536;&#x5230;&#x53C2;&#x6570;&#x540E;&#x4ECD;&#x7136;&#x8DF3;&#x8F6C;&#x81EA;&#x5B9A;&#x4E49;&#x7684;&#x6307;&#x5B9A;&#x9875;&#x9762;&#x3002;</p>
        <p><b>&#x5F53;&#x8FD4;&#x56DE;&#x503C;&#x4E3A; 0 &#x7684;&#x65F6;&#x5019;&#xFF0C;&#x4E3A; DeepLink &#x65B9;&#x5F0F;&#x6253;&#x5F00;&#x3002;</b>
        </p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
//sdk >= 2.3.2 && sdk < 2.8.4
GrowingIO.startWithConfiguration(this, new Configuration()
        .setDeeplinkCallback(new DeeplinkCallback() {
            @Override
            public void onReceive(Map<String, String> params, int status) {
                if (status == DeeplinkCallback.SUCCESS) {
                    //获得您的自定义参数，处理您的相关逻辑               
                    Log.d("TestApplication", "DeepLink 参数获取成功，params" + params.toString());
                }
            }
        }));
        
//sdk >= 2.8.4, 新增参数 long appAwakePassedTime 
GrowingIO.startWithConfiguration(this, new Configuration()
        .setDeeplinkCallback(new DeeplinkCallback() {
            @Override
            public void onReceive(Map<String, String> params, int status, long appAwakePassedTime) {
                //成功得到自定义参数，并且从 APP 打开到收到回调时间小于 1.5 秒             
                if (status == DeeplinkCallback.SUCCESS && appAwakePassedTime < 1500) {
                    //获得您的自定义参数，处理您的相关逻辑                     
                    Log.d("TestApplication", "DeepLink 参数获取成功，params" + params.toString());
                }
            }
        }));
```



### 自定义点击事件

如果您有自定义的控件重写了`View`的`onTouchEvent`方法来判断和处理点击事件，那么必须调用它的`PerformClick`，并且设置相应的`onClickListener`。

或者使用[手动调用系统点击事件方法](android-chang-jian-wen-ti.md#dian-ji-shi-jian-cai-ji-luo-ji)。



### 设置页面别名

对于安卓应用，页面指的是`Activity`或者`Fragment`。

有些时候，对于完成某个功能的页面，统计时可能需要进一步细分。 比如，对于展示商品列表的页面，需要区分衣物类商品，以及食品类商品的两种列表的访问量。

为处理这种场景，我们提供了取别名的方法来区分这两种情况下的页面，方法如下：

```java
GrowingIO.setPageName(Activity activity, String name)
```

如果您设置的的对象是`Fragment`，将上方的`Activity`替换为`Fragment`即可。

**我们用**`Activity`**来举例，具体说明它的用法。**

1. 某个应用的商品列表页是用`FeedActivity`实现的，所以默认的页面名称都是`FeedActivity`。
2. 现在我们想区分衣物类商品列表和食品类商品列表，分别看它们的浏览量，可以在`onCreate`方法中添加如下代码：

   ```java
   public class FeedActivity extends Activity {
       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           GrowingIO.getInstance().setPageName(this, "Clothing");
       }
   }
   ```

{% hint style="info" %}
1. 必须在该`Activity`的`onCreate`方法中完成该属性的赋值操作。
2. 页面别名只能设置为字母、数字和下划线的组合。
3. 为查看数据方便，请尽量对iOS和安卓的同功能页面取不同的名称。
{% endhint %}



### 设置界面元素内容

SDK默认不会采集ImageView的内容，如果您需要区分不同的`ImageView`，可以使用contentDescription来标记，也可以调用下方的方法来设置：

```java
GrowingIO.setViewContent(View view, String contentDescription);
```

### 

### 设置界面元素ID

SDK会使用Layout文件中的ID来识别一个元素。

如果部分元素在Layout文件中没有ID，建议在Layout文件中添加。

对于动态生成的元素，可以使用如下方法对它设置唯一的ID：

```java
GrowingIO.setViewID(View view, String id);
```

{% hint style="info" %}
1. 如果在ViewGroup上设置ID的话，SDK会忽略他所有子元素的默认ID（就是写在xml文件里的）只会使用GrowingIO.setViewID设置的ID。
2. ID只能设置为字母、数字和下划线的组合。
3. 对于已经集成过旧版 SDK \(1.x\)并圈选过的应用，对某个元素设置ID后再圈选它，指标数值会从零开始计算，类似初次集成SDK后发版的效果，但不影响之前圈选的其它指标数据。如果不希望出现这种情况，请不要使用这个方法
{% endhint %}



### 忽略元素

如果您需要忽略某些特殊内容，比如倒计时元素或涉及隐私的内容，可以使用该功能。

```java
GrowingIO.ignoreView(View view)
```



### 设置元素对象

如果元素自身的内容并不能代表具体的意义，可以使用元素对象来标识。

例如：在商品`ListView`添加购物车的场景中，每个商品`item`都含有一个加入购物车按钮，这时无法区分商品`item`与将它加入购物车按钮的一对一关系，此时调用此方法增加描述。

```java
GrowingIO.setViewInfo(View view, String info);
```

{% hint style="info" %}
适用于原有`v`字段含义不大，只关注描述的场景，使用此接口后`v`字段将不采集
{% endhint %}



### 采集推送

在 **Android SDK 2.6.3** 版本， 支持采集通知的标题和内容，此功能默认关闭，如需开启，请在 Application 初始化 GrowingIO 中设置，例如：

```java
...
@Override
public void onCreate () {
    GrowingIO.startWithConfiguration(this, new Configuration()
            //如果多进程应用，需要开启GrowingIO多进程             
            .setMutiprocess(true)
            .supportMultiProcessCircle(true)
            // 开启采集通知            
            .enablePushTrack()
    );
}
```

{% hint style="warning" %}
SDK对通知的采集仅采集4.4及以上机型。
{% endhint %}

**注意：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x652F;&#x6301;&#x63A8;&#x9001;&#x5E73;&#x53F0;</th>
      <th style="text-align:left">&#x6CE8;&#x610F;&#x4E8B;&#x9879;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Notification &#x81EA;&#x5B9A;&#x4E49;</td>
      <td style="text-align:left">&#x5168;&#x90E8;&#x652F;&#x6301;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x6781;&#x5149;&#x63A8;&#x9001;</td>
      <td style="text-align:left">
        <ol>
          <li>&#x521D;&#x59CB;&#x5316;&#x65F6;&#xFF0C;&#x6781;&#x5149;&#x8FDB;&#x7A0B;&#x4E5F;&#x9700;&#x8981;&#x521D;&#x59CB;&#x5316;</li>
          <li>&#x5982;&#x679C;&#x591A;&#x8FDB;&#x7A0B;&#x5E94;&#x7528;&#xFF0C;&#x9700;&#x8981;&#x5F00;&#x542F;GrowingIO&#x591A;&#x8FDB;&#x7A0B;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x534E;&#x4E3A;&#x63A8;&#x9001;</td>
      <td style="text-align:left">
        <ol>
          <li>&#x521D;&#x59CB;&#x5316;&#x65F6;&#xFF0C;&#x63A8;&#x9001;&#x8FDB;&#x7A0B;&#x4E5F;&#x9700;&#x8981;&#x521D;&#x59CB;&#x5316;</li>
          <li>NC(Notification Center)&#x6D88;&#x606F;&#xFF0C;&#x9700;&#x8981;&#x7528;&#x6237;&#x8BBE;&#x7F6E;&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x6BB5;:
            notification_title&#x8868;&#x793A;title&#xFF0C; &#x4E0E;notification_content&#x8868;&#x793A;&#x5185;&#x5BB9;&#xFF0C;&#x8BF7;&#x89C1;&#x8868;&#x683C;&#x4E0B;&#x65B9;&#x56FE;&#x7247;&#x3002;</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x5C0F;&#x7C73;&#x63A8;&#x9001;</td>
      <td style="text-align:left">
        <p></p>
        <ol>
          <li>&#x521D;&#x59CB;&#x5316;&#x65F6;&#xFF0C;&#x63A8;&#x9001;&#x8FDB;&#x7A0B;&#x4E5F;&#x9700;&#x8981;&#x521D;&#x59CB;&#x5316;</li>
          <li>SDK hook&#x4E86;PushMessageReceiver&#x7684;onNotificationMessageArrived&#x4E0E;onNotificationMessageClicked&#x51FD;&#x6570;,
            &#x4F1A;&#x89E6;&#x53D1;SDK&#x53D1;&#x9001;&#x4E24;&#x6761;&#x6D88;&#x606F;.
            &#x5176;&#x4E2D;onNotificationMessageArrived&#x9700;&#x8981;&#x7CFB;&#x7EDF;&#x652F;&#x6301;&#x3002;</li>
        </ol>
      </td>
    </tr>
  </tbody>
</table>![&#x534E;&#x4E3A;&#x5E73;&#x53F0;&#x63A8;&#x9001;&#x65F6;&#xFF0C;&#x8BBE;&#x7F6E;&#x81EA;&#x5B9A;&#x4E49;&#x5B57;&#x6BB5;](../../.gitbook/assets/image%20%28294%29.png)

**查看通知采集数据**

支持对于通知的展现和点击事件的采集，GrowingIO 并未增加新的采集事件类型，而是使用了自定义事件发送，所以需要您创建自定义事件和事件级变量，事件级变量标识符为**`notification_title`**，**`notification_content`**，自定义事件的标识符为**`notification_show`**，**`notification_click`**如图：

![&#x521B;&#x5EFA;&#x901A;&#x77E5;&#x7684;&#x4E8B;&#x4EF6;&#x7EA7;&#x53D8;&#x91CF;](../../.gitbook/assets/image%20%2887%29.png)

![&#x521B;&#x5EFA;&#x901A;&#x77E5;&#x7684;&#x81EA;&#x5B9A;&#x4E49;&#x4E8B;&#x4EF6;](../../.gitbook/assets/image%20%28260%29.png)

然后创建事件分析，等候片刻即可看到数据

![&#x521B;&#x5EFA;&#x63A8;&#x9001;&#x4E8B;&#x4EF6;&#x5206;&#x6790;](../../.gitbook/assets/image%20%28423%29.png)

### \[灰度中\] 采集 OAID

在 Android 10 版本中，非系统应用无法获取 IMEI。加上以前 Android版本已经对 MAC ， AndroidID 的获取做了限制， 在 Android10 中缺少一种唯一标记设备的标识符。 在海外， Google 推荐使用 Google 的广告 ID 作为广告的唯一识别符，在国内[移动安全联盟MSA](http://www.msa-alliance.cn/col.jsp?id=120) 联合各大手机制造商推出了 OAID 的概念， 作为唯一广告标识符。

目前腾讯， 头条， 网易广告SDK已经要求使用 OAID， OAID 的准确性和覆盖率均满足广告场景的使用需求，Android SDK 提供采集 OAID 的能力。 

{% hint style="info" %}
* 支持采集：**Android SDK 2.8.5** 及以上， 包含埋点包与无埋点包及对应插件版本；
* 目前 2.8.5 SDK 测试的 OAID SDK 版本为miit\_mdid\_sdk\_v1.0.10, 在API不变更的情况下支持后续版本；
* 目前仅在 **activate** （激活，用户首次安装并打开应用时发送）事件中包含 OAID
{% endhint %}

{% hint style="danger" %}
注意: OAID为可选字段，在客户集成 MSA SDK 情况下根据配置可选采集。GrowingIO SDK不会初始化 MSA SDK，我们仅调用其接口获取 OAID 值， 需要客户自行初始化 MSA的 SDK。

[点击查看 MSA 集成步骤](http://www.msa-alliance.cn/col.jsp?id=120)。
{% endhint %}

与 IMEI、AndroidId、Google AD ID 配置相同， GrowingIO SDK 对OAID 提供了编译期，初始化前， 初始化后三种配置 OAID 是否采集的选项，[点击查看 API 文档](api.md#gradle-bian-yi-shi-pei-zhi-api)。



## 自定义事件和变量 API

您的APP或网页在集成了 GrowingIO 的 SDK 之后，它将会自动地为您采集一系列用户行为数据，进行[数据分析](../../data-analytics/)。除自动收集的用户行为数据（或称为无埋点数据）之外，GrowingIO 还提供了多种 API 接口，供您上传一些[自定义事件](../../data-definition/custom-event/)和变量，下面介绍自定义事件和变量 API 使用方法，后文简称埋点事件API。

{% hint style="danger" %}
* GrowingIO 所有运行时 API 需要在主线程调用；
* 强烈建议在代码中埋点完成后，使用【数据中心】的【数据校验】功能，验证埋点是否正确，**SDK 2.7.0 以上支持**，保证您的数据质量。
{% endhint %}

### API 简介 

```java
// 发送自定义事件 API
GrowingIO gio = GrowingIO.getInstance();
gio.track(String eventId);
gio.track(String eventId, JSONObject eventLevelVariables);

//此接口已废弃
gio.track(String eventId, Number eventNumber);
//此接口已废弃
gio.track(String eventId, Number eventNumber, JSONObject eventLevelVariables);

// 发送页面级变量 API
GrowingIO gio = GrowingIO.getInstance();
gio.setPageVariable(Activity activity, String key, String value);
gio.setPageVariable(Activity activity, String key, Number value);
gio.setPageVariable(Activity activity, String key, Boolean value);
gio.setPageVariable(Activity activity, JSONObject pageLevelVariables);
gio.setPageVariable(Fragment fragment, String key, String value);
gio.setPageVariable(Fragment fragment, String key, Number value);
gio.setPageVariable(Fragment fragment, String key, Boolean value);
gio.setPageVariable(Fragment fragment, JSONObject pageLevelVariables);

// 发送转化变量 API
GrowingIO gio = GrowingIO.getInstance();
gio.setEvar(String key, String value);
gio.setEvar(String key, Number value);
gio.setEvar(String key, Boolean value);
gio.setEvar(JSONObject conversionVariables);

// 发送用户变量 API
GrowingIO gio = GrowingIO.getInstance();
gio.setPeopleVariable(String key, String value);
gio.setPeopleVariable(String key, Number value);
gio.setPeopleVariable(String key, Boolean value);
gio.setPeopleVariable(JSONObject peopleVariables);

// 设置登录用户ID API
GrowingIO.getInstance().setUserId(String userId);

// 清除登录用户ID API
GrowingIO.getInstance().clearUserId();

// 设置访问用户变量，或者设置 A/B 测试标签
GrowingIO.getInstance().setVisitor(JSONObject visitorVariable);
```



### track

发送一个自定义事件。在添加所需要发送的事件代码之前，需要在打点管理用户界面配置事件以及事件级变量。

```java
// track API原型
GrowingIO gio = GrowingIO.getInstance();
gio.track(String eventId);
gio.track(String eventId, JSONObject eventLevelVariables);
//此接口已废弃
gio.track(String eventId, Number eventNumber);
//此接口已废弃
gio.track(String eventId, Number eventNumber, JSONObject eventLevelVariables);
```

**参数说明：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x53C2;&#x6570;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x5FC5;&#x586B;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">eventId</td>
      <td style="text-align:left">String</td>
      <td style="text-align:left">&#x662F;</td>
      <td style="text-align:left">&#x4E8B;&#x4EF6;&#x6807;&#x8BC6;&#x7B26;</td>
    </tr>
    <tr>
      <td style="text-align:left">number</td>
      <td style="text-align:left">Number</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">
        <p>&#x4E8B;&#x4EF6;&#x7684;&#x6570;&#x503C;&#xFF0C;&#x6CA1;&#x6709;number&#x53C2;&#x6570;&#x65F6;&#xFF0C;&#x4E8B;&#x4EF6;&#x9ED8;&#x8BA4;&#x52A0;&#x4E00;&#xFF1B;</p>
        <p>&#x5F53;&#x51FA;&#x73B0;number&#x53C2;&#x6570;&#x65F6;&#xFF0C;&#x4E8B;&#x4EF6;&#x81EA;&#x589E;number&#x7684;&#x6570;&#x503C;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">eventLevelVariable</td>
      <td style="text-align:left">JSONObject</td>
      <td style="text-align:left">&#x5426;</td>
      <td style="text-align:left">&#x4E8B;&#x4EF6;&#x53D1;&#x751F;&#x65F6;&#x6240;&#x4F34;&#x968F;&#x7684;&#x7EF4;&#x5EA6;&#x4FE1;&#x606F;</td>
    </tr>
  </tbody>
</table>**参数限制条件：**

参数违反以下条件将不发送数据，`SDK 2.4.0` 以上能够在 Log 日志中查看对应报错，之下版本无提示信息。调用后请关注日志，查看数据发送是否成功，事件类型`t`为`cstm`。

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x9650;&#x5236;&#x6761;&#x4EF6;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">eventId</td>
      <td style="text-align:left">
        <p>&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF1B;</p>
        <p><code>SDK 2.4.0</code>&#x4EE5;&#x4E0B;&#x7248;&#x672C;&#x4E0D;&#x652F;&#x6301;&#x4E2D;&#x6587;&#xFF0C;&#x4EC5;&#x652F;&#x6301;
          0 &#x5230; 9&#x3001;a &#x5230; z &#x4EE5;&#x53CA;&#x4E0B;&#x5212;&#x7EBF;&#xFF0C;&#x5E76;&#x4E14;&#x4E0D;&#x80FD;&#x4EE5;&#x6570;&#x5B57;&#x5F00;&#x5934;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">number</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">eventLevelVariable</td>
      <td style="text-align:left">
        <p>&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;100&#xFF08;<code>eventLevelVariable.length()&lt;=100</code>&#xFF09;&#xFF1B;</p>
        <p><code>eventLevelVariable</code> &#x5185;&#x90E8;&#x4E0D;&#x5141;&#x8BB8;&#x542B;&#x6709;<code>JSONObject</code>&#x6216;&#x8005;<code>JSONArray&#xFF1B;</code>
        </p>
        <p><code>key</code> &#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF0C;<code>value</code> &#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x7B49;&#x4E8E;1000&#xFF0C;&#x503C;&#x4E0D;&#x80FD;&#x4E3A;&#x7A7A;&#x4E32;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&quot;&quot;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
// track API调用示例一
GrowingIO gio = GrowingIO.getInstance();gio.track("registerSuccess");
```

```java
// track API调用示例二
GrowingIO gio = GrowingIO.getInstance();
JSONObject jsonObject = new JSONObject();
jsonObject.put("gender", "male");
jsonObject.put("age", "21");
gio.track("registerSuccess", jsonObject);
```

**检验数据发送日志示例：** 

注意 `t` 等于 `cstm` 字段，表示自定义事件发送成功，只需注意 `var`、`n` 、`num`字段，其它字段无需仔细验证**。**

```javascript
//展示 track 接口调用示例二日志内容
{    
    "s":"31e3aa14-5241-490c-821c-a741e9bf0f87",
    // t 为事件类型， track 接口调用发送的事件类型为 cstm    
    "t":"cstm",    
    "tm":1532085495251,    
    "d":"com.growingio.android.test",    
    // n 为 eventId 参数携带的值    
    "n":"loanAmount",    
    // var 为 eventLevelVariable 参数携带的值    
    "var":{        
        "gender":"male",        
        "age":"21"    
        },    
    "ptm":0,    
    "gesid":18,    
    "esid":0,    
    "u":"b6247b01-a31a-3bc6-a391-4c456888c1ee"
}
```

{% hint style="info" %}
#### 推荐您使用MobileDebugger，我们为您列举了应用场景和验证示例，请移步查看：[cstm 事件验证](../growingio-debugger/best-practice.md#cstm-shi-jian-yi-ji-guan-lian-de-shi-jian-ji-bian-liang-shi-jian)。
{% endhint %}

### 

### setPageVariable

发送页面级别的信息，在添加代码之前必须在打点管理界面上声明页面级变量。

使用此接口时，请先阅读 [**GrowingIO 对于页面的定义**](android-chang-jian-wen-ti.md#growingio-dui-yu-ye-mian-de-ding-yi)。

在代码中调用完毕后请使用 mobile debugger 验证或者通过查看日志方式，确认`pvar`事件已经发送，如果没有成功发送，参考[这条常见问题](android-chang-jian-wen-ti.md#setpagevariable-zi-ding-yi-ye-mian-bian-liang-shi-jian-fa-song-shi-bai)。

```java
// setPageVariable API原型
GrowingIO gio = GrowingIO.getInstance();
gio.setPageVariable(Activity activity, String key, String value);
gio.setPageVariable(Activity activity, String key, Number value);
gio.setPageVariable(Activity activity, String key, Boolean value);
gio.setPageVariable(Activity activity, JSONObject pageLevelVariables);
gio.setPageVariable(Fragment fragment, String key, String value);
gio.setPageVariable(Fragment fragment, String key, Number value);
gio.setPageVariable(Fragment fragment, String key, Boolean value);
gio.setPageVariable(Fragment fragment, JSONObject pageLevelVariables);
// SDK 2.6.6 以上支持 androidx, 增加接口:
gio.setPageVariableX(Fragment fragment, String key, String value);
gio.setPageVariableX(Fragment fragment, String key, Number value);
gio.setPageVariableX(Fragment fragment, String key, Boolean value);
gio.setPageVariableX(Fragment fragment, JSONObject pageLevelVariables);
```

{% hint style="info" %}
请注意这里的`activity`和`fragment`参数，[**它关乎GrowingIO对于页面的定义**](android-chang-jian-wen-ti.md#growingio-dui-yu-ye-mian-de-ding-yi)。

参数选择是当前被识别的页面对象，可能是 Activity 或 Fragment，可以使用 Mobile Debugger 或日志确认。
{% endhint %}

**参数说明：**

| 参数名称 | 参数类型 | 是否必须 | 说明 |
| :--- | :--- | :--- | :--- |
| activity | Activity | 是 | **当前页面访问事件\(page事件\)发送的页面** |
| fragment | Fragment | 是 | **当前页面访问事件\(page事件\)发送的页面** |
| key | String | 否 | 页面级变量的标识符 |
| value | String | 否 | 页面级变量的值 |
| pageLevelVariables | JSONObject | 否 | 页面级别的信息 |

**参数限制条件：**

参数违反以下条件将不发送数据，`SDK 2.4.0` 以上能够在 Log 日志中查看对应报错，之下版本无提示信息。调用后请关注日志，查看数据发送是否成功，事件类型`t`为`pvar`。

{% hint style="danger" %}
 **SDK 2.6.7** 将页面级变量**`pageLevelVariables`**与该页面对象绑定，设置不同的值将会合并，如果想要清空，需要传 null 。
{% endhint %}

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x9650;&#x5236;&#x6761;&#x4EF6;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">key</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">value</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;1000&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">pageLevelVariables</td>
      <td style="text-align:left">
        <p>&#x975E; null &#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;100&#xFF08;<code>pageLevelVariable.length()&lt;=100</code>&#xFF09;&#xFF1B;</p>
        <p><code>pageLevelVariable</code> &#x5185;&#x90E8;&#x4E0D;&#x5141;&#x8BB8;&#x542B;&#x6709;<code>JSONObject</code>&#x6216;&#x8005;<code>JSONArray</code>&#xFF1B;</p>
        <p><code>key</code> &#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF0C;<code>value</code>&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x7B49;&#x4E8E;1000&#xFF0C;&#x503C;&#x4E0D;&#x80FD;&#x4E3A;&#x7A7A;&#x4E32;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&quot;&quot;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
// page.set API调用示例
GrowingIO gio = GrowingIO.getInstance();
JSONObject jsonObject = new JSONObject();
jsonObject.put("gender", "male");
jsonObject.put("age", "21");
gio.setPageVariable(myActivity, jsonObject);
```

**检验数据发送日志示例：** 

注意 `t` 等于`pvar`字段，表示自定义事件发送成功，只需注意 `var`字段，其它字段无需仔细验证**。**

```javascript
{    
    "s":"0a7e8150-c409-45d4-96ed-b5781fe652cb",    
    // t 为事件类型，pvar 页面级事件    
    "t":"pvar",    
    "tm":1532337448255,    
    "d":"com.growingio.android.test",    
    "cs1":"GrowingIO",    
    "p":"SetUserIdFragment1",    
    "ptm":1532337448255,    
    //页面级变量    
    "var":{        
        "gender":"male",
        "age":"21"    
        },    
    "gesid":292,    
    "esid":0
}
```

{% hint style="danger" %}
注意确认当前页面，通过[圈选](android-sdk.md#yu-bei-zhi-shi)方式最快能够定位当前页面，在当前页面埋点最稳定可靠。如果页面未确认，可能在`Activity`和`Fragment`嵌套的场景下埋点失败。如未能成功发送自定义页面变量，请参照[常见问题](android-chang-jian-wen-ti.md#1-zi-ding-yi-ye-mian-bian-liang-shi-jian-fa-song-shi-bai)。
{% endhint %}

{% hint style="info" %}
#### 推荐您使用 MobileDebugger，我们为您列举了应用场景和验证示例，请移步查看：[ pvar 事件验证](../growingio-debugger/best-practice.md#pvar-ye-mian-ji-bian-liang-shi-jian)
{% endhint %}

### 

### setEvar

发送一个转化信息用于高级归因分析，在添加代码之前必须在打点管理界面上声明转化变量。

```java
// setEvar API原型
GrowingIO gio = GrowingIO.getInstance();
gio.setEvar(String key, String value);
gio.setEvar(String key, Number value);
gio.setEvar(String key, Boolean value);
gio.setEvar(JSONObject conversionVariables);
```

**参数说明：**

| 参数名称 | 参数类型 | 必填 | 说明 |
| :--- | :--- | :--- | :--- |
| key | String | 否 | 转化变量的标识符 |
| value | String | 否 | 转化变量的值 |
| conversionVariables | JSONObject | 否 | 转化变量用于高级归因分析 |

**参数限制条件：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x9650;&#x5236;&#x6761;&#x4EF6;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">key</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">value</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;1000&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">conversionVariables</td>
      <td style="text-align:left">
        <p>&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;100&#xFF08;<code>conversionVariables.length()&lt;=100</code>&#xFF09;&#xFF1B;</p>
        <p><code>conversionVariables</code> &#x5185;&#x90E8;&#x4E0D;&#x5141;&#x8BB8;&#x542B;&#x6709;<code>JSONObject</code>&#x6216;&#x8005;<code>JSONArray</code>&#xFF1B;</p>
        <p><code>key</code> &#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF0C;<code>value</code>&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x7B49;&#x4E8E;1000&#xFF0C;&#x503C;&#x4E0D;&#x80FD;&#x4E3A;&#x7A7A;&#x4E32;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&quot;&quot;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
// setEvar API调用示例一
GrowingIO gio = GrowingIO.getInstance();
gio.setEvar("campaignId", "1234567890");
```

```java
// setEvar API调用示例二
GrowingIO gio = GrowingIO.getInstance();
JSONObject jsonObject = new JSONObject();
jsonObject.put("campaignId", "1234567890");
jsonObject.put("campaignOwner", "Li Si");
gio.setEvar(jsonObject);
```

**检验数据发送日志示例：** 

注意 `t` 等于`evar`字段，表示转化事件发送成功，只需注意 `var`字段，其它字段无需仔细验证。

```javascript
{    
    "s":"e1c48845-dd60-4cf2-b1a5-a8e529d2188d",    
    // t 为事件类型， evar 为转化事件    
    "t":"evar",    
    "tm":1532338526083,    
    "d":"com.growingio.android.test",    
    "cs1":"GrowingIO",    
    // 转化变量    
    "var":{        
        "evarTest":111,        
        "campaignId":"1234567890",        
        "campaignOwner":"Li Si"    
        },    
    "gesid":300,    
    "esid":22
}
```

{% hint style="info" %}
#### 推荐您使用 MobileDebugger，我们为您列举了应用场景和验证示例，请移步查看：[ evar 事件验证](../growingio-debugger/best-practice.md#evar-zhuan-hua-bian-liang-shi-jian)
{% endhint %}

### 

### setPeopleVariable

发送用户信息用于用户信息相关分析，在添加代码之前必须在打点管理界面上声明用户变量。

```java
// people.set API原型
GrowingIO gio = GrowingIO.getInstance();
gio.setPeopleVariable(String key, String value);
gio.setPeopleVariable(String key, Number value);
gio.setPeopleVariable(String key, Boolean value);
gio.setPeopleVariable(JSONObject peopleVariables);
```

**参数说明：**

| 参数名称 | 参数类型 | 必填 | 说明 |
| :--- | :--- | :--- | :--- |
| key | String | 否 | 用户变量的标识符 |
| value | String | 否 | 用户变量的值 |
| peopleVariables | JSONObject | 否 | 用户变量用于用户信息相关的分析 |

**参数限制条件：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x9650;&#x5236;&#x6761;&#x4EF6;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">key</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">value</td>
      <td style="text-align:left">&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;1000&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">peopleVariables</td>
      <td style="text-align:left">
        <p>&#x975E;&#x7A7A;&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;100&#xFF08;<code>peopleVariables.length()&lt;=100</code>&#xFF09;&#xFF1B;</p>
        <p><code>peopleVariables</code> &#x5185;&#x90E8;&#x4E0D;&#x5141;&#x8BB8;&#x542B;&#x6709;<code>JSONObject</code>&#x6216;&#x8005;<code>JSONArray</code>&#xFF1B;</p>
        <p><code>key</code>&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF0C;<code>value</code>&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x7B49;&#x4E8E;1000&#xFF0C;&#x503C;&#x4E0D;&#x80FD;&#x4E3A;&#x7A7A;&#x4E32;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&quot;&quot;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
// setPeopleVariable API调用示例一
GrowingIO gio = GrowingIO.getInstance();
gio.setPeopleVariable("gender", "male");
```

```java
// setPeopleVariable API调用示例二
GrowingIO gio = GrowingIO.getInstance();
jsonObject.put("gender", "male");
jsonObject.put("age", "21");
gio.setPeopleVariable(jsonObject);
```

**检验数据发送日志示例：** 

注意 `t` 等于`ppl`字段，表示用户变量发送成功，只需注意 `var`字段，其它字段无需仔细验证。

```javascript
{    
    "s":"a35872af-13df-4479-90bc-25558d12328e",    
    // t 为事件类型， pvar 为发送用户变量事件    
    "t":"ppl",    
    "tm":1532339208991,    
    "d":"com.growingio.android.test",    
    "cs1":"GrowingIO",    
    // 用户变量    
    "var":{        
        "gender":"male",        
        "age":"21"    
        },    
        "gesid":311,    
        "esid":0
}
```

{% hint style="info" %}
#### 推荐您使用 MobileDebugger，我们为您列举了应用场景和验证示例，请移步查看：[ ppl 事件验证](../growingio-debugger/best-practice.md#ppl-yong-hu-bian-liang-shi-jian)
{% endhint %}

### 

### setUserId

当用户登录之后调用setUserId API，设置登录用户ID。

```java
GrowingIO.getInstance().setUserId(String userId);
```

**参数说明：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x53C2;&#x6570;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x5FC5;&#x586B;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">userId</td>
      <td style="text-align:left">String</td>
      <td style="text-align:left">&#x662F;</td>
      <td style="text-align:left">
        <p>&#x767B;&#x5F55;&#x7528;&#x6237;Id&#xFF0C;&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;1000&#xFF1B;</p>
        <p>&#x5982;&#x679C;&#x503C;&#x4E3A;&#x7A7A;&#x5219;&#x6E05;&#x7A7A;&#x4E86;&#x767B;&#x5F55;&#x7528;&#x6237;&#x53D8;&#x91CF;&#xFF0C;&#x4E0D;&#x5EFA;&#x8BAE;&#x8FD9;&#x4E48;&#x7528;&#xFF0C;</p>
        <p>&#x8BF7;&#x4F7F;&#x7528; clearUserId &#x6E05;&#x9664;&#x767B;&#x5F55;&#x7528;&#x6237;&#x53D8;&#x91CF;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
GrowingIO.getInstance().setUserId("1234567890");
```

注：您的 App 每次用户升级版本时无需重新登录的话，建议在用户每次升级App 版本后初次访问时重新调用上述 setUserId 方法。

{% hint style="info" %}
#### 推荐您使用 MobileDebugger，我们为您列举了应用场景和验证示例，请移步查看：[ 用户变量](../growingio-debugger/best-practice.md#chang-jing-yi-yong-hu-bian-liang-zhi-deng-lu-yong-hu-id)
{% endhint %}

### 

### clearUserId

当用户登出之后调用clearUserId，清除已经设置的登录用户ID。

**示例代码：**

```java
GrowingIO.getInstance().clearUserId();
```



### setVisitor

**2.4.0以上版本支持**

当用户未登录时，定义用户属性变量，也可用于A/B测试上传标签。

```java
GrowingIO.getInstance().setVisitor(JSONObject visitorVar)
```

**参数说明：**

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;&#x540D;&#x79F0;</th>
      <th style="text-align:left">&#x53C2;&#x6570;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x5FC5;&#x586B;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">visitorVar</td>
      <td style="text-align:left">JSONObject</td>
      <td style="text-align:left">&#x662F;</td>
      <td style="text-align:left">
        <p>&#x4E0D;&#x53EF;&#x4F7F;&#x7528;&#x5D4C;&#x5957;&#x7684;<code>JSONObject</code>&#x5BF9;&#x8C61;&#xFF0C;&#x5373;&#x4E3A;JSONObject&#x4E2D;&#x4E0D;&#x53EF;&#x4EE5;&#x653E;&#x5165;<code>JSONObject</code>&#x6216;&#x8005;<code>JSONArray</code>&#xFF1B;</p>
        <p>key &#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x4E8E;&#x7B49;&#x4E8E;50&#xFF0C;value&#x957F;&#x5EA6;&#x9650;&#x5236;&#x5C0F;&#x7B49;&#x4E8E;1000&#xFF0C;&#x503C;&#x4E0D;&#x80FD;&#x4E3A;&#x7A7A;&#x4E32;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&quot;&quot;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>**示例代码：**

```java
GrowingIO gio = GrowingIO.getInstance();
jsonObject.put("gender", "male");
jsonObject.put("age", "21");
gio.setVisitor(jsonObject);
```

**检验数据发送日志示例：** 

注意 `t` 等于`vstr`字段，表示访问用户变量发送成功，其它字段无需仔细验证。

```javascript
{    
     "s":"d334b4a1-57eb-4bf4-b426-64c1cce5a5c0",    
     // t 为事件类型， vstr 为发送访问用户变量事件   
     "t":"vstr",    
     "tm":1532341259134,    
     "d":"com.growingio.android.test",    
     "cs1":"GrowingIO",    
     //访问用户变量    
     "var":{        
          "gender":"male",        
          "age":"21"    
          },    
          "gesid":322,    
          "esid":0
}
```

\*\*\*\*

## 验证 SDK 是否正常工作

{% hint style="info" %}
验证 SDK 的采集事件数据发送至关重要，数据采集是数据分析的基础，保证了数据采集的精准才能帮助您做出正确的数据分析。

GrowingIO 一直在努力满足市面上所有 APP 的数据自动采集，也成为无埋点数据采集，但是因为开发者的实现方式在江湖上不统一， 我们覆盖事件采集触发逻辑可能与您的实现方式有出入，所以强烈建议开发者验证 GrowingIO SDK 的数据发送，如果有任何问题，及时反馈技术支持，我们会一直努力增强我们的采集能力，给您更好的服务。
{% endhint %}

### GrowingIO采集事件介绍

GrowingIO 的数据采集分为自动采集和用户自定义事件和变量两种方式采集移动端APP，也就是通常所说的无埋点和埋点。下面将分别介绍这两种方式GrowingIO定义的采集事件类型。

#### 自动采集事件类型

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x4E8B;&#x4EF6;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x542B;&#x4E49;</th>
      <th style="text-align:left">&#x53D1;&#x9001;&#x65F6;&#x673A;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">activate</td>
      <td style="text-align:left">&#x6FC0;&#x6D3B;</td>
      <td style="text-align:left">&#x5F53;APP&#x9996;&#x6B21;&#x6FC0;&#x6D3B;&#x6253;&#x5F00;&#x65F6;</td>
    </tr>
    <tr>
      <td style="text-align:left">vst</td>
      <td style="text-align:left">&#x5E94;&#x7528;&#x8BBF;&#x95EE;</td>
      <td style="text-align:left">
        <p>1.&#x51B7;&#x542F;&#x52A8;&#x53D1;&#x9001;</p>
        <p>2.&#x5207;&#x6362;&#x7528;&#x6237;&#x53D1;&#x9001;</p>
        <p>3.&#x9ED8;&#x8BA4;APP&#x8FDB;&#x5165;&#x540E;&#x53F0;30&#x79D2;&#x4EE5;&#x540E;&#x518D;&#x6B21;&#x6253;&#x5F00;&#x4F1A;&#x53D1;&#x9001;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">page</td>
      <td style="text-align:left">&#x9875;&#x9762;&#x6D4F;&#x89C8;</td>
      <td style="text-align:left">&#x6BCF;&#x5F53;&#x8FDB;&#x5165;&#x4E00;&#x4E2A;&#x9875;&#x9762;&#x65F6;&#x53D1;&#x9001;</td>
    </tr>
    <tr>
      <td style="text-align:left">imp</td>
      <td style="text-align:left">&#x5143;&#x7D20;&#x5C55;&#x73B0;</td>
      <td style="text-align:left">&#x9875;&#x9762;&#x5143;&#x7D20;&#x53D1;&#x751F;&#x53D8;&#x52A8;&#x7684;&#x65F6;&#x5019;&#x89E6;&#x53D1;&#xFF0C;&#x6BD4;&#x5982;&#x5F39;&#x6846;&#x6216;&#x8005;<code>ListView</code>&#x6ED1;&#x52A8;</td>
    </tr>
    <tr>
      <td style="text-align:left">clck</td>
      <td style="text-align:left">&#x70B9;&#x51FB;</td>
      <td style="text-align:left">&#x70B9;&#x51FB;&#x5B9E;&#x73B0;&#x4E86;&#x76F8;&#x5173; click Listener
        &#x7684;&#x5143;&#x7D20;&#x63A7;&#x4EF6;&#xFF0C;<a href="android-sdk.md#2-dian-ji-shi-jian-cai-ji-luo-ji">&#x5E38;&#x89C1;&#x95EE;&#x9898;&#x4E2D;&#x5C06;&#x8BE6;&#x7EC6;&#x5217;&#x4E3E;</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">chng</td>
      <td style="text-align:left">&#x8F93;&#x5165;&#x6846;&#x5185;&#x5BB9;&#x53D8;&#x5316;</td>
      <td style="text-align:left">&#x8F93;&#x5165;&#x6846;<code>EditText</code>&#x5931;&#x53BB;&#x7126;&#x70B9;&#xFF0C;&#x9ED8;&#x8BA4;&#x4E0D;&#x91C7;&#x96C6;&#x8F93;&#x5165;&#x5185;&#x5BB9;</td>
    </tr>
    <tr>
      <td style="text-align:left">reengage</td>
      <td style="text-align:left">DeepLink&#x5524;&#x9192;&#x4E8B;&#x4EF6;</td>
      <td style="text-align:left">&#x901A;&#x8FC7;&#x626B;&#x63CF; DeepLink &#x4E8C;&#x7EF4;&#x7801;&#x5524;&#x9192;
        APP &#x540E;&#x53D1;&#x9001;</td>
    </tr>
  </tbody>
</table>#### 自定义事件类型

| 事件类型 | 含义 | 发送时机 |
| :--- | :--- | :--- |
| cstm | 自定义事件触发 | 调用[`track`](android-sdk.md#track)后发送 |
| pvar | 页面级变量触发 | 调用[`setPageVariable`](android-sdk.md#setpagevariable)后发送 |
| evar | 转化变量触发 | 调用[`setEvar`](android-sdk.md#setevar)后发送 |
| ppl | 设置用户变量 | 调用[`setPeopleVariable`](android-sdk.md#setpeoplevariable)后发送 |
| vstr | 设置访问用户变量 | 调用[`setVisitor`](android-sdk.md#setvisitor)后发送 |



### 验证工具

#### [1. MobileDebugger](../growingio-debugger/#qi-dong-mobile-debugger)

#### [2.查看日志](android-sdk.md#setdebugmode)

#### 3.数据校验

**SDK 2.7.0** 及以上支持数据校验功能。

数据校验功能它可以同时看到无埋点和埋点的事件，对于埋点事件给出验证结果。更简单便捷校验您的 埋点事件，GIO推荐您上线前一定要使用它测试下。

![](../../.gitbook/assets/image%20%2853%29.png)



### 圈选功能验证

请先依据[圈选文档](../../data-definition/circle/app.md)，唤醒圈选功能，主要检查元素是否都可圈选，重点尝试WebView内部元素是否可圈。

如果您发现无法圈选，请按照[这篇文档](../../faq/faq-circle.md#3-sao-miao-quan-xuan-er-wei-ma-dan-shi-wu-fa-zheng-chang-quan-xuan)先进行排查。

当显示高亮则证明可圈，如图所示：

![&#x5143;&#x7D20;&#x9AD8;&#x4EAE;&#x8BC1;&#x660E;&#x53EF;&#x5708;](../../.gitbook/assets/image%20%28402%29.png)

\*\*\*\*

## 旧版本升级

{% hint style="danger" %}
#### 升级到 2.x SDK 前，请务必联系客服协助您完成后台配置的升级！
{% endhint %}

本文旨在帮助您从 1.x 版本无缝升级至 2.x 版本，由于两个版本的诸多接口、方法、字段含义均有较大变动，因此建议您在升级前一定参照本文完成必要的实施工作。

您需要完成以下几个步骤：

### 1. 更新 SDK 版本号为最新版本

请您参考以下开发文档，完成SDK初始化代码的添加。

* [Android SDK 集成](android-sdk.md#ji-cheng)

Tips：建议您在开发中，使用 `DebugMode` 或者 [**Mobile Debugger**](../growingio-debugger/#shi-yong-mobile-debugger-ce-shi-shu-ju) 校验 GrowingIO SDK 的数据是否正常上传，其中开启`DebugMode`的方式见[调试查看发送数据日志](android-sdk.md#7-tiao-shi-cha-kan-fa-song-shu-ju-ri-zhi)。

### **2. 迁移用户属性字段（CS字段）**

如果您未做用户属性字段上传，请忽略此部分。

用户属性字段（简称CS字段）是 1.x 版本的概念，升级至 2.x 版本后：

* CS1字段，会强制命名为“登陆用户ID”，并且上传接口与其他变量不同。
* CS2-10字段，会迁移至“应用级变量”，应用级变量与CS字段的使用方式无任何区别。
* CS11-20字段，会迁移至[“用户变量”](android-sdk.md#setuserid)。两者的区别主要在于：用户变量支持自定义的归因方式。

**2.1 上传接口：**

2.x 版本中的上传用户变量方法有较大改动，不再将 `'setCSn'` 这个字段作为参数，方法中只需写入用户变量的 key - value 对。

* 1.x 版本方法格式：

```java
GrowingIO growingIO = GrowingIO.getInstance();    
growingIO.setCS1("user_id", "100324");    
growingIO.setCS2("company_id", "943123");    
growingIO.setCS3("user_name", "张溪梦");
```

* 2.x 版本方法格式：

对于 CS1 字段，也就是登陆用户ID，请使用以下方法：

```java
// 设置登录用户ID API
GrowingIO.getInstance().setUserId(String userId);
// 清除登录用户ID API
GrowingIO.getInstance().clearUserId();
```

对于应用级变量，也就是 1.x 版本中的 CS2 - CS10，请使用以下方法：

```java
GrowingIO.setAppVariable(JSONObject variables);
GrowingIO.setAppVariable(String key, Number variable);
GrowingIO.setAppVariable(String key, String variable);
GrowingIO.setAppVariable(String key, Boolean variable);
```

对于用户变量，也就是 1.x 版本中的 CS11 - CS20，请使用以下方法：

```java
GrowingIO gio = GrowingIO.getInstance();
gio.setPeopleVariable(String key, String value);
gio.setPeopleVariable(String key, Number value);
gio.setPeopleVariable(String key, Boolean value);
gio.setPeopleVariable(JSONObject peopleVariables);
// 多个变量，可组合为一个JSON对象peopleVariables传入
```

* 2.x 版本方法格式：

```java
GrowingIO gio = GrowingIO.getInstance();
gio.setPageVariable(Activity activity, String key, String value);
gio.setPageVariable(Activity activity, String key, Number value);
gio.setPageVariable(Activity activity, String key, Boolean value);
gio.setPageVariable(Activity activity, JSONObject pageLevelVariables);
gio.setPageVariable(Fragment fragment, String key, String value);
gio.setPageVariable(Fragment fragment, String key, Number value);
gio.setPageVariable(Fragment fragment, String key, Boolean value);
gio.setPageVariable(Fragment fragment, JSONObject pageLevelVariables);
```

**2.2 GrowingIO 后台配置**

您需要在 **“管理” - “自定义事件和变量” 页面中的 “页面级变量” Tab 页**进行配置。配置方式请参考[相关帮助文档](../../data-definition/page-variable/#zi-ding-yi-bian-liang-de-pei-zhi-he-shang-chuan)。

### **3. 迁移页面属性字段（PS字段）**

如果您未做页面属性字段上传，请忽略此部分。

类似于用户属性字段，在 2.x 版本中，页面属性字段被迁移到了“[页面级变量](../../data-model/event-model/autotrack-event/page-events-and-properties.md#ye-mian-shi-jian-de-ding-yi)”。与页面属性字段不同的是，**页面级变量相当于过去的 PS 字段，不再存在过去的 PG 字段**。

**3.1 上传接口**

* 1.x 版本方法格式：

```java
GrowingIO.setPageGroup(Activity activity, String name); 
GrowingIO.setPS1(Activity activity, String property); 
GrowingIO.setPS2(Activity activity, String property);
```

* 2.x 版本方法格式：

```java
GrowingIO gio = GrowingIO.getInstance();
gio.setPageVariable(Activity activity, String key, String value);
gio.setPageVariable(Activity activity, String key, Number value);
gio.setPageVariable(Activity activity, String key, Boolean value);
gio.setPageVariable(Activity activity, JSONObject pageLevelVariables);
gio.setPageVariable(Fragment fragment, String key, String value);
gio.setPageVariable(Fragment fragment, String key, Number value);
gio.setPageVariable(Fragment fragment, String key, Boolean value);
gio.setPageVariable(Fragment fragment, JSONObject pageLevelVariables);
```

  
**3.2 GrowingIO 后台配置**

您需要在 **“管理” - “自定义事件和变量” 页面中的 “页面级变量” Tab 页**进行配置。

### 4. 迁移自定义事件（埋点事件）

如果您未做自定义事件的上传，请忽略此部分。

2.x 版本的自定义事件，在概念上与 1.x 版本无任何区别，但上传接口和配置方式上有以下变更。

**4.1 上传接口**

```java
public class GrowingIO {    
    public GrowingIO track(String eventName, JSONObject properties)
}
```

* 2.x 版本方法格式：

```java
GrowingIO gio = GrowingIO.getInstance();
gio.track(String eventId);gio.track(String eventId, Number eventNumber);
gio.track(String eventId, Number eventNumber, JSONObject eventLevelVariables);
gio.track(String eventId, JSONObject eventLevelVariables);
```

**4.2 GrowingIO 后台配置**

自定义事件的配置，同 1.x 版本一样，也是在**“管理” - “自定义事件和变量”** 页面中的 **“自定义事件” Tab 页**。但您会发现，除了 “自定义事件” Tab 页外，现在还提供了 “事件级变量” Tab 页来专门管理事件级变量的配置。您只需在 “事件级变量” Tab 页完成事件级变量的配置，然后在新建自定义事件时，从已经建好的事件级变量中选择即可。

### 5. 数据校验

在完成了上述代码实施和配置后，我们当然需要对数据是否成功上传进行校验。[点击查看 GrowingIO Mobile Debugger 的安装和使用](../growingio-debugger/#growingio-mobile-debugger)。

####  <a id="&#x4E0B;&#x65B9;&#x5404;&#x4E2A;&#x914D;&#x7F6E;&#x9879;&#x5C06;&#x5F71;&#x54CD;&#x7EDF;&#x8BA1;&#x7684;&#x51C6;&#x786E;&#x6027;&#xFF0C;&#x8BF7;&#x52A1;&#x5FC5;&#x4ED4;&#x7EC6;&#x9605;&#x8BFB;&#xFF0C;&#x5224;&#x65AD;&#x662F;&#x5426;&#x9700;&#x8981;"></a>

