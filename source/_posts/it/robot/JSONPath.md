---
title: JSONPath
date: 2019-01-14 19:16:57
tags: [JSONPath]
---

## 概要

JSONPath，XPath for JSON，是仿造 XPath 进行 JSON 定位的一套语法。

## 基本语法

| **XPath** | **JSONPath**       | **Description**                                              |
| --------- | ------------------ | ------------------------------------------------------------ |
| /         | $                  | 根对象或元素                                                 |
| .         | @                  | 当前对象或元素                                               |
| /         | . or []            | 子操作                                                       |
| ..        | n/a                | 父操作                                                       |
| //        | ..                 | 向下遍历，创意来自 E4X                                       |
| *         | *                  | 通配                                                         |
| @         | n/a                | 获取属性，但 JSON 结构没有任何属性                           |
| []        | []                 | 下标操作。XPath 用来迭代元素集合和断言，在 Javascript 和 JSON 中则是数组操作 |
| \|        | [,]                | 连结操作。在 XPath 中为将节点集合并。 JSONPath 允许选择名称和索引作为集合。 |
| n/a       | `[start:end:step]` | 数组切片操作。创意来自 ES4                                   |
| []        | ?()                | 执行一个过滤脚本表达式                                       |
| n/a       | ()                 | 脚本表达式                                                   |
| ()        | n/a                | XPath 中的分组操作                                           |

## 参考

1. [JsonPath - goessner.net](https://goessner.net/articles/JsonPath/)（详细介绍了 JSONPath 语法）
2. [JSONPATH Expression Tester - jsonpath.curiousconcept.com](https://jsonpath.curiousconcept.com/)（绝好的测试 JSONPath 语法的在线工具）