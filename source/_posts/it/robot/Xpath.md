---
title: XPath
date: 2018-11-20 19:38:55
tags: [Java,Robot,XPath]
comments: true
---

## 何为 XPath（Introduction）

[维基百科](https://zh.wikipedia.org/zh-hans/XPath)：XPath 即为 XML 路径语言（XML Path Language），它是一种用来确定XML文档中某部分位置的语言。 XPath基于XML的树状结构，提供在数据结构树中找寻节点的能力。

所谓节点有七种，分别是：

- 元素（element）
- 属性（attribute）
- 文本（text）
- 命名空间（namespace）
- 处理指令（processing-instruction）
- 注释（comment）
- 文档节点（document node, also called root element）

示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book>
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
</bookstore>
```

其中，

- `<bookstore> ` 为根节点，或者称为文档节点；
- `<title lang="en">Harry Potter</title>` 为元素节点；
- `lang="en"` 为属性节点；
- `Harry Potter` 为文本节点。

## 基础语法（Syntax）

### 路径表达式（Path Expressions）

| 表达式   | 描述                                                       |
| -------- | ---------------------------------------------------------- |
| nodename | 选取此节点的所有子节点。                                   |
| /        | 从根节点选取。                                             |
| //       | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。 |
| .        | 选取当前节点。                                             |
| ..       | 选取当前节点的父节点。                                     |
| @        | 选取属性。                                                 |
| text()   | 选取文本。                                                 |

### 断言（Predicates）

断言被用来在查找指定的节点或者包含指定值的节点，通常放在方括号 `[]` 中。

示例：

| 路径表达式                            | 结果                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| /bookstore/book[1]                    | 选取属于 bookstore 子元素的第一个 book 元素。                |
| /bookstore/book[last()]               | 选取属于 bookstore 子元素的最后一个 book 元素。              |
| /bookstore/book[last()-1]             | 选取属于 bookstore 子元素的倒数第二个 book 元素。            |
| /bookstore/book[position()<3]         | 选取最前面的两个属于 bookstore 元素的子元素的 book 元素。    |
| //title[@lang]                        | 选取所有拥有名为 lang 的属性的 title 元素。                  |
| //title[@lang='eng']                  | 选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性。   |
| //book[author[text()='J K. Rowling']] | 选取 author 为 J K. Rowling 的 book 元素。                   |
| /bookstore/book[price>35.00]          | 选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00。 |
| /bookstore/book[price>35.00]/title    | 选取 bookstore 元素中的 book 元素的所有 title 元素，且其中的 price 元素的值须大于 35.00。 |

### 模糊选取（Selecting Unknown Nodes）

| 通配符 | 描述                 |
| ------ | -------------------- |
| *      | 匹配任何元素节点。   |
| @*     | 匹配任何属性节点。   |
| node() | 匹配任何类型的节点。 |

### 合并选取（Selecting Several Paths）

使用管道符 `|`  合并两个选取结果。

示例：

| 路径表达式                           | 结果                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| //book/title&#124; //book/price      | 选取 book 元素的所有 title 和 price 元素。                   |
| //title &#124;  //price              | 选取文档中的所有 title 和 price 元素。                       |
| /bookstore/book/title &#124; //price | 选取属于 bookstore 元素的 book 元素的所有 title 元素，以及文档中所有的 price 元素。 |

## 正则匹配（Regular Match）

可以使用 `starts-with` 和 `ends-with` 进行简单的正则匹配。

比如获取 Github 上某仓库的星星数的 Xpath 为，

```
//div/a[ends-with(@href, 'stargazers')]/text()
```

 表示获取 `div` 节点下的具备有属性为 `href `且值为 `stargazers` 结尾的 `a` 节点的文本内容。

## 测试我的 Xpath（Test Xpath）

 可以使用 freeformatter.com 上提供的 [Xpath Tester](https://www.freeformatter.com/xpath-tester.html) 进行在线测试。