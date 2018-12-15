---
title: CSV
date: 2018-12-15 10:51:47
tags: [CSV]
---

## CSV 是什么格式？

CSV，全称 Comma-Separated Value，即逗号分隔值，使用逗号（或者自定义的分隔符）分隔并以纯文本形式存储表格数据。通常使用第一行作为表头。

## 快速开始

（1）打开编辑器，粘贴以下内容。

```
Brand,Product,Desc
Apple,IPhone,Expensive
Samsung,S9,Nice
```

（2） 存储为 `.csv` 结尾的文件。

（3）使用 Excel 或者 Number 打开，就会以表格形式呈现内容。

## 内容中含有逗号？

（1）自定义分隔符如下，并保存文件。

```
Brand`Product`Desc
Apple`IPhone,Mac`Expensive
Samsung`S8,Galaxy Watch`Nice
```

（2）打开 Excel 新建文档，然后点击 `文档 --> 导入`，选取刚才保存的 `csv` 文件。在弹出的提示框中选择 `分隔符号 --> 下一步 --> 其他`， 输入自定义的分隔符号，点击 `完成` 即可。

