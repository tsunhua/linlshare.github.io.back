---
title: Groovy
date: 2018-12-03 11:09:17
tags: [Groovy]
---

## Q&A

### Gradle 中的 ext 究竟是什么？

gradle 中我们使用 `ext` 定义额外的各种属性，可是 `ext` 究竟是什么呢？

参看 [ExtraPropertiesExtension - Gradle DSL](https://docs.gradle.org/current/dsl/org.gradle.api.plugins.ExtraPropertiesExtension.html)，发现 `ext` 不是 Groovy 固有的定义，而是领域特定的语言（DSL）。使用方式是：

```groovy
// 以下的 project 常常被省略
project.ext { foo = "bar" }

assert project.ext.get("foo") == "bar"
assert project.ext.foo == "bar"
assert project.ext["foo"] == "bar"

assert project.foo == "bar"
assert project["foo"] == "bar"
```

`ext` 实质上是一个内置的简单对象，但可以动态添加新属性，这个对象叫 `ExtraPropertiesExtension`，它内置在所有 `ExtensionAware` 中，`ExtenstionAware` 的已知子类有 `Project`、`Settings`、`Task` 、`SourceSet`，所以在这些类中可以直接使用所谓的 `namespace method` 动态新增新属性。

```groovy
// Extensions are just plain objects, there is no interface/type
class MyExtension {
  String foo

  MyExtension(String foo) {
    this.foo = foo
  }
}

// Add new extensions via the extension container
project.extensions.create('custom', MyExtension, "bar")
//                       («name»,   «type»,       «constructor args», …)

// extensions appear as properties on the target object by the given name
assert project.custom instanceof MyExtension
assert project.custom.foo == "bar"

// also via a namespace method
project.custom {
  assert foo == "bar"
  foo = "other"
}
assert project.custom.foo == "other"
```

