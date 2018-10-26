### 1. 去除启动黑屏

1.1 在`style.xml`中定义两种主题：

```Xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.NoActionBar">
   <item name="android:windowIsTranslucent">true</item>
</style>

<style name="AppTheme.Splash">
   <item name="android:windowIsTranslucent">false</item>
   <item name="android:windowBackground">@drawable/bg_splash</item>
</style>
```

1.2 在`AndroidManifest.xml`中设置MainActivity的主题为`AppTheme.Splash`

```Xml
<activity
  android:name="xxx.MainActivity"
  android:icon="@mipmap/ic_launcher"
  android:theme="@style/AppTheme.Splash">
  <intent-filter>
      <action android:name="android.intent.action.MAIN"/>
      <category android:name="android.intent.category.LAUNCHER"/>
  </intent-filter>
</activity>
```

1.3 在`MainActivity`的`onCreate`调用super的方法之前设置回原先的主题

```java
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    setTheme(R.style.AppTheme);
    super.onCreate(savedInstanceState);
  }
```

#### 2. 分析启动卡顿

