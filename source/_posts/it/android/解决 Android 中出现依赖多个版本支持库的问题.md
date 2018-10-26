在 app 的 `build.gradle` 中引入依赖时发现如下错误：

```
All com.android.support libraries must use the exact same version specification (mixing versions can lead to runtime crashes). Found versions 27.1.1, 26.1.0. Examples include com.android.support:animated-vector-drawable:27.1.1 and com.android.support:support-media-compat:26.1.0 less... (⌘F1) 
There are some combinations of libraries, or tools and libraries, that are incompatible, or can lead to bugs. One such incompatibility is compiling with a version of the Android support libraries that is not the latest version (or in particular, a version lower than your targetSdkVersion).
```

简单的说就是引入了两种版本的 Android Support Library，这种情形该如何处理呢？

1. **统一管理支持包的版本号；**
2. **剔除第三方库中包含的支持包，并引入版本一致的支持包。**

具体如下：

```groovy

ext.versions = [
  play_services: '15.0.1',
  // 统一管理支持包的版本号
  suport_library: '27.1.1',
]

dependencies {
  implementation("com.google.android.gms:play-services-gcm:${versions.play_services}") {
    // 剔除 play service 中包含的 v4 支持包
    exclude group: 'com.android.support', module: 'support-v4'
  }
  // 引入版本一致的 v4 支持包
  implementation "com.android.support:support-v4:${versions.suport_library}"
  implementation "com.android.support:appcompat-v7:${versions.suport_library}"
  implementation "com.android.support:recyclerview-v7:${versions.suport_library}"
  implementation "com.android.support:cardview-v7:${versions.suport_library}"
  implementation "com.android.support:support-annotations:${versions.suport_library}"
}
```

通常在我们引入了众多依赖的情况下，我们并不知道引入哪个依赖重复引入了支持库，有没有工具可以帮我们分析呢？

答案是肯定的，gradle 命令中有一条可以做到这点，那就是 `gradle dependencies` ，它可以将依赖和依赖的依赖罗列出来。也可以通过 Android Studio 右侧工具栏的 `Gradle --> help--> dependencies` 启动依赖分析。以上面的依赖为例，执行结果如下：

```groovy
+--- com.google.android.gms:play-services-gcm:15.0.1
|    +--- com.google.android.gms:play-services-base:[15.0.1,16.0.0) -> 15.0.1
|    |    +--- com.google.android.gms:play-services-basement:[15.0.1] -> 15.0.1
|    |    |    \--- com.android.support:support-v4:26.1.0 -> 27.1.1
|    |    |         +--- com.android.support:support-compat:27.1.1
|    |    |         |    +--- com.android.support:support-annotations:27.1.1
|    |    |         |    \--- android.arch.lifecycle:runtime:1.1.0
|    |    |         |         +--- android.arch.lifecycle:common:1.1.0
|    |    |         |         \--- android.arch.core:common:1.1.0
|    |    |         +--- com.android.support:support-media-compat:27.1.1
|    |    |         |    +--- com.android.support:support-annotations:27.1.1
|    |    |         |    \--- com.android.support:support-compat:27.1.1 (*)
|    |    |         +--- com.android.support:support-core-utils:27.1.1
|    |    |         |    +--- com.android.support:support-annotations:27.1.1
|    |    |         |    \--- com.android.support:support-compat:27.1.1 (*)
|    |    |         +--- com.android.support:support-core-ui:27.1.1
|    |    |         |    +--- com.android.support:support-annotations:27.1.1
|    |    |         |    +--- com.android.support:support-compat:27.1.1 (*)
|    |    |         |    \--- com.android.support:support-core-utils:27.1.1 (*)
|    |    |         \--- com.android.support:support-fragment:27.1.1
|    |    |              +--- com.android.support:support-compat:27.1.1 (*)
|    |    |              +--- com.android.support:support-core-ui:27.1.1 (*)
|    |    |              +--- com.android.support:support-core-utils:27.1.1 (*)
|    |    |              +--- com.android.support:support-annotations:27.1.1
|    |    |              +--- android.arch.lifecycle:livedata-core:1.1.0
|    |    |              |    +--- android.arch.lifecycle:common:1.1.0
|    |    |              |    +--- android.arch.core:common:1.1.0
|    |    |              |    \--- android.arch.core:runtime:1.1.0
|    |    |              |         \--- android.arch.core:common:1.1.0
|    |    |              \--- android.arch.lifecycle:viewmodel:1.1.0
|    |    \--- com.google.android.gms:play-services-tasks:[15.0.1] -> 15.0.1
|    |         \--- com.google.android.gms:play-services-basement:[15.0.1] -> 15.0.1 (*)
|    +--- com.google.android.gms:play-services-basement:[15.0.1,16.0.0) -> 15.0.1 (*)
|    +--- com.google.android.gms:play-services-iid:[15.0.1] -> 15.0.1
|    |    +--- com.google.android.gms:play-services-base:[15.0.1,16.0.0) -> 15.0.1 (*)
|    |    +--- com.google.android.gms:play-services-basement:[15.0.1,16.0.0) -> 15.0.1 (*)
|    |    +--- com.google.android.gms:play-services-stats:[15.0.1,16.0.0) -> 15.0.1
|    |    |    \--- com.google.android.gms:play-services-basement:[15.0.1] -> 15.0.1 (*)
|    |    \--- com.google.android.gms:play-services-tasks:[15.0.1,16.0.0) -> 15.0.1 (*)
|    \--- com.google.android.gms:play-services-stats:[15.0.1,16.0.0) -> 15.0.1 (*)
+--- com.android.support:support-v4:27.1.1 (*)
+--- com.android.support:appcompat-v7:27.1.1
|    +--- com.android.support:support-annotations:27.1.1
|    +--- com.android.support:support-core-utils:27.1.1 (*)
|    +--- com.android.support:support-fragment:27.1.1 (*)
|    +--- com.android.support:support-vector-drawable:27.1.1
|    |    +--- com.android.support:support-annotations:27.1.1
|    |    \--- com.android.support:support-compat:27.1.1 (*)
|    \--- com.android.support:animated-vector-drawable:27.1.1
|         +--- com.android.support:support-vector-drawable:27.1.1 (*)
|         \--- com.android.support:support-core-ui:27.1.1 (*)
+--- com.android.support:recyclerview-v7:27.1.1
|    +--- com.android.support:support-annotations:27.1.1
|    +--- com.android.support:support-compat:27.1.1 (*)
|    \--- com.android.support:support-core-ui:27.1.1 (*)
+--- com.android.support:cardview-v7:27.1.1
|    \--- com.android.support:support-annotations:27
```
