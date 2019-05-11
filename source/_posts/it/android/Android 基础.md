---
title: Android 基础
date: 2019-04-28 13:25:35
tags: [Android]
---

[TOC]

## 1 四大组件

需要在 `AndroidMenifest.xml` 中声明，通过 Intent 调用。

### 1.1 Activity

#### 1.1.1 定义

> An activity is a single, focused thing that the user can do. Almost all activities interact with the user, so the Activity class takes care of creating a window for you in which you can place your UI with `setContentView(View)`. While activities are often presented to the user as full-screen windows, they can also be used in other ways: as floating windows (via a theme with `R.attr.windowIsFloating` set) or embedded inside of another activity (using `ActivityGroup`).
>
> — [Android Developer Guide](https://developer.android.com/reference/android/app/Activity.html)

#### 1.1.2 生命周期

> 参见：https://developer.android.com/guide/components/activities/activity-lifecycle#java

![activity_lifecycle](Android 基础/activity_lifecycle.png)

#### 1.1.3 启动一个 Activity

> 参见：https://developer.android.com/training/basics/activity-lifecycle/starting.html

(1) Activity 启动流程

![basic-lifecycle](Android 基础/basic-lifecycle.png)

(2) 指定一个Launcher Activity

Launcher(or Main) Activity（启动器 Activity，或主 Activity）：应用用户界面的主入口，在 Android 清单文件 [`AndroidManifest.xml`](https://developer.android.com/guide/topics/manifest/manifest-intro.html) 中定义，如下：

```xml
<activity android:name=".MainActivity" android:label="@string/app_name">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

(3) 创建一个新实例

1. 创建 Activity；
2. 在 `AndroidManifest.xml` 中声明；
3. 创建对应的布局文件(可选)；
4. 处理生命周期方法。

```java
TextView mTextView; // Member variable for text view in the layout

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // Set the user interface layout for this Activity
    // The layout file is defined in the project res/layout/main_activity.xml file
    setContentView(R.layout.main_activity);

    // Initialize member TextView so we can manipulate it later
    mTextView = (TextView) findViewById(R.id.text_message);

    // Make sure we're running on Honeycomb or higher to use ActionBar APIs
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        // For the main activity, make sure the app icon in the action bar
        // does not behave as a button
        ActionBar actionBar = getActionBar();
        actionBar.setHomeButtonEnabled(false);
    }
}
```

### 1.2 Service

#### 1.2.1 定义

> `Service` 是一个可以在后台执行长时间运行操作而不提供用户界面的应用组件。服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。 例如，服务可以处理网络事务、播放音乐，执行文件 I/O 或与内容提供程序交互，而所有这一切均可在后台进行。
>
> — [Android Developer Guide](https://developer.android.com/guide/components/services)

#### 1.2.2 使用场景

**用户未与应用进行交互但需在后台运行时**

>  如需在主线程外部执行工作，不过只是在用户正在与应用交互时才有此需要，则应创建新线程而非服务。 例如，如果您只是想在 Activity 运行的同时播放一些音乐，则可在 `onCreate()` 中创建线程，在 `onStart()` 中启动线程，然后在 `onStop()` 中停止线程。您还可以考虑使用 `AsyncTask` 或 `HandlerThread`，而非传统的 `Thread`类。如需了解有关线程的详细信息，请参阅[进程和线程](https://developer.android.com/guide/components/processes-and-threads.html#Threads)文档。
>
> 请记住，如果您确实要使用服务，则**默认情况下，它仍会在应用的主线程中运**行，因此，如果服务执行的是密集型或阻止性操作，则您仍应在服务内创建新线程。
>
> — [Android Developer Guide](https://developer.android.com/guide/components/services)

#### 1.2.3 分类

1. **启动服务**，通过调用 `startService()` 启动服务时，服务即处于“启动”状态。一旦启动，服务即可在后台**无限期**运行，即使启动服务的组件已被销毁也不受影响。已启动的服务通常是执行单一操作，而且不会将结果返回给调用方。
2. **绑定服务**，通过调用 `bindService()` 绑定到服务时，服务即处于“绑定”状态。绑定服务提供了一个客户端-服务器接口，允许组件与服务进行交互、发送请求、获取结果，甚至是利用进程间通信 (IPC) 跨进程执行这些操作。 仅当与另一个应用组件绑定时，绑定服务才会运行。 多个组件可以同时绑定到该服务，但全部取消绑定后，该服务即会被销毁。

Service 下有一个重要的子类叫 `IntentService`，它使用一个工作线程队列，逐一地（*而不是同时处理*）处理所有请求，只需要实现 `onHandleIntent `方法即可。

#### 1.2.4 回调方法

1. [onStartCommand()](https://developer.android.com/reference/android/app/Service.html#onStartCommand(android.content.Intent, int, int))：当调用 `startService` 启动服务时，系统会调用该方法，可通过 `stopSelf()` 或 `stopService` 停止服务。（如果您只想提供绑定，则无需实现此方法。）
2. [onBind()](https://developer.android.com/reference/android/app/Service.html#onBind(android.content.Intent))：当另一个组件想通过调用 `bindService()` 与服务绑定（例如执行 RPC）时，系统将调用此方法。在此方法的实现中，您必须通过返回 `IBinder` 提供一个接口，供客户端用来与服务进行通信。请务必实现此方法，但如果您并不希望允许绑定，则应返回 null。
3. [onCreate()](https://developer.android.com/reference/android/app/Service.html#onCreate())：首次创建服务时，系统在 `onStartCommand()` 或 `onBind` 之前调用该方法。
4. [onDestroy()](https://developer.android.com/reference/android/app/Service.html#onDestroy())：当服务不再使用且将被销毁时，系统将调用此方法。

#### 1.2.5 生命周期

![img](Android 基础/service_lifecycle.png)

### 1.3 ContentProvider

### 1.4 BroadcastReceiver

### 1.5 Intent

#### 1.5.1 定义

> An intent is an abstract description of an operation to be performed. It can be used with `Context#startActivity(Intent)` to launch an `Activity`, `broadcastIntent` to send it to any interested `BroadcastReceiver` components, and `Context.startService(Intent)` or `Context.bindService(Intent, ServiceConnection, int)` to communicate with a background `Service`.
>
> — [Android Developer Guide](https://developer.android.com/reference/android/content/Intent)

#### 1.5.2 结构

1. action，操作，包括`ACTION_VIEW`, `ACTION_EDIT`, `ACTION_MAIN` 等等。
2. data（可选），操作值，符合  [`Uri`](https://developer.android.com/reference/android/net/Uri.html) 规范。
3. category（可选），action 的附加描述，比如 `CATEGORY_LAUNCHER` 意味着它应该出现在启动器中作为顶层应用。
4. type（可选），指定 data 的数据类型，如不指定则系统会通过 data 进行值类型评估。
5. component（可选），指定使用该 Intent 的组件的类名。
6. extras（可选），附加的 [Bundle](https://developer.android.com/reference/android/os/Bundle.html) 类型数据，供 component 使用。

#### 1.5.3 分类

1. 显式 Intent，指定 component。
2. 隐式 Intent，不指定 component。

#### 1.5.4 使用示例：启动一个 Activity

```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, recipientArray);
startActivity(intent);
```

## 2 布局

### 2.1 定义

> 布局定义用户界面的视觉结构，如[Activity](https://developer.android.com/guide/components/activities.html?hl=zh-CN)或[应用小部件](https://developer.android.com/guide/topics/appwidgets/index.html?hl=zh-CN)的 UI。您可以通过两种方式声明布局：
>
> - **在 XML 中声明 UI 元素**。Android 提供了对应于 View 类及其子类的简明 XML 词汇，如用于小部件和布局的词汇；
> - **运行时实例化布局元素**。您的应用可以通过编程创建 View 对象和 ViewGroup 对象（并操纵其属性）。
>
> Android 框架让您可以灵活地使用以下一种或两种方法来声明和管理应用的 UI。例如，您可以在 XML 中声明应用的默认布局，包括将出现在布局中的屏幕元素及其属性。然后，您可以在应用中添加可在运行时修改屏幕对象（包括那些已在 XML 中声明的对象）状态的代码。
>
> — [Android Developer Guide](https://developer.android.com/guide/topics/ui/declaring-layout?hl=zh-CN)

### 2.2 布局参数

> 名为 `layout_*something*` 的 XML 布局属性可为视图定义与其所在的 ViewGroup 相适的布局参数。
>
> 每个 ViewGroup 类都会实现一个扩展 `ViewGroup.LayoutParams` 的嵌套类。此子类包含的属性类型会根据需要为视图组的每个子视图定义尺寸和位置。 正如您在图 1 中所见，父视图组为每个子视图（包括子视图组）定义布局参数。
>
> — [Android Developer Guide](https://developer.android.com/guide/topics/ui/declaring-layout?hl=zh-CN)



![img](Android 基础/layoutparams.png)

### 2.3 布局位置

### 2.4 View

### 2.5 ViewGroup

### 2.6 事件处理

## 3 存储

### 3.1 SharedPreferences

#### 3.1.1 定义

> Interface for accessing and modifying preference data returned by `Context#getSharedPreferences`. For any particular set of preferences, there is a single instance of this class that all clients share. Modifications to the preferences must go through an `Editor` object to ensure the preference values remain in a consistent state and control when they are committed to storage. Objects that are returned from the various `get` methods must be treated as immutable by the application.
>
> — [Android Developer Guide](https://developer.android.com/reference/android/content/SharedPreferences)

底层存储采用一个 XML 文件。

#### 3.1.2 操作

```java
1）写入数据：
     //步骤1：创建一个SharedPreferences对象
     SharedPreferences sharedPreferences= getSharedPreferences("data",Context.MODE_PRIVATE);
     //步骤2： 实例化SharedPreferences.Editor对象
     SharedPreferences.Editor editor = sharedPreferences.edit();
     //步骤3：将获取过来的值放入文件
     editor.putString("name", “Tom”);
     editor.putInt("age", 28);
     editor.putBoolean("marrid",false);
     //步骤4：提交               
     editor.commit();


 2）读取数据：
     SharedPreferences sharedPreferences= getSharedPreferences("data", Context .MODE_PRIVATE);
     String userId=sharedPreferences.getString("name","");
  
3）删除指定数据
     editor.remove("name");
     editor.commit();


4）清空数据
     editor.clear();
     editor.commit();

作者：ghroosk
链接：https://juejin.im/post/5adc444df265da0b886d00bc
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

### 3.2 ContentProvider

### 3.3 SQLite、Room 等等

### 3.4 文件

