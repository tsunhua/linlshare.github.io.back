---
title: Android 使用 DiffUtil 处理 RecyclerView 数据更新问题
date: 2018-9-13 17:33:00
tags: [Android]
comments: true
---

## 背景

1. `RecyclerView.Adapter#notifyDataSetChanged()` 会每次刷新整个布局；
2. 每次手动调用 `RecyclerView.Adapter#notifyItemXx` 系列方法很麻烦；
3. 需要在新增的项目中跟旧的列表项重复时，只更新内容，而不是插入重复项。

## DiffUtil

`DiffUtil` 就是为了简化 RecyclerVeiw 更新数据操作而生。其关键类和方法如下：

| 类                  | 方法                                                         | 描述                                                         |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| DiffUtil            | public static DiffResult **calculateDiff**(Callback cb)      | 根据 Callback 提供的数据分析出新旧数据列表的差异，返回 DiffResult |
| DiffUtil.Callback   | public abstract int **getOldListSize**()                     | 返回旧数据的数量                                             |
|                     | public abstract int **getNewListSize**()                     | 返回新数据的数量                                             |
|                     | public abstract boolean **areItemsTheSame**(int oldItemPosition, int newItemPosition) | 决定两个数据项是否是同一个                                   |
|                     | public abstract boolean **areContentsTheSame**(int oldItemPosition, int newItemPosition) | （当两个数据项是同一个时，）决定两个数据项的内容是否同样，或者说是否需要进行更新 |
| DiffUtil.DiffResult | public void **dispatchUpdatesTo**(final RecyclerView.Adapter adapter) | 将差异信息更新到 RecyclerView.Adapter 中                     |

## 步骤

1. 创建一个类实现 `DiffUtil.Callback` ；
2. 当新数据到来时，实例化自定义的 callback，传入新旧数据；
3. 在子线程调用 `DiffUtil#calculateDiff` 计算差异；
4. 将差异结果 `DiffResult` 更新到 RecyclerView.Adapter 中。

## 更多

- 使用 `android.support.v7.recyclerview.extensions.ListAdapter` 和 `android.support.v7.recyclerview.extensions.AsyncListDiffer`