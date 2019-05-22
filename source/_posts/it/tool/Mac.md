---
title: Mac
date: 2018-8-21 22:53:00
tags: [Mac,OS]
comments: true
---

本帖记录个人在使用 Mac 操作系统上的一些骚操作，不断更新，以飨读者。

## 快速移动网页到顶部或底部

用双指上下划触摸板吗？NO，我们有更骚的操作：

`command + ↑` 回到顶部

`command + ↓` 滚到底部

另外，

`fn + ↑` 上滚一页

`fn + ↓` 下滚一页

`fn + ←` Home，回到顶部

`fn + →` End，滚到底部

## 粘贴文字时不要带样式

有时候从网上看到不错的文字想要粘贴到 Word、Evernote 或者文字编辑应用上，但使用 `command + v`  会连文字样式都带过来，这时候就要使用骚一点的操作了，那就是：

```
command + shift + v
```

## 输入英文时首字母不要自动大写

当你在文字编辑软件上写英文时，Mac 会很贴心地识别首字母并自动大写。但常常让我觉得画蛇添足，明明我不想大写，特别是在博客中或写或改一些代码或脚本时更觉得是捣乱。这时候，我找到了一个禁止自动首字母自动大写的方法：

```
系统偏好设置 —> 键盘 —> 文本 —> 去除勾选【自动大写字词的首字母】
```

## 我要剪切或移动文件

Mac 中，复制的快捷键是 `command + c`，粘贴的快捷键是 `command + v` ，但是剪切的快捷键可不是 `command + x` ，如果要移动文件，你是不是还在进行 *粘贴完再删除* 的复杂操作？

NO，其实移动文件可以复制后使用快捷键 `command + option + v` 实现，快去试试吧。

## 快速转换中文简繁体

确保简繁体转换服务已经开启，路径如下：

```
系统偏好设置 —> 键盘 —> 快捷键 —> 服务 —> 将文字转换为繁体中文 / 将文字转换为简体中文
```

选中需要进行简繁对换的文字，按：

- `control + shift + command + c` 将文字转为繁体中文
- `control + option + shift + command + c`  将文字转为简体中文

## 读写 NTFS 格式的移动硬盘

众所周知，NTFS 版权归属微软，Apple 没有购买版权，无法在 Mac 中进行 NTFS 格式磁盘的写入操作。网上的解决方案有：

- 使用付费软件，如 NTFS for Mac 或者 Tuxera NTFS 等；缺点是价格略贵，无法通过终端进行读写操作；
- 启用系统的 NTFS 写入功能，使用免费软件 Mounty 或者按其官网描述的原理手动执行命令；缺点是不稳定，文件会变灰色（如变灰色可需要通过 `xattr -c -r .` 恢复）；
- 使用 **OSXFuse** 替换系统的 NTFS 驱动；缺点是操作稍微复杂，但可以通过终端进行读写操作，省心，本文推荐。

以下罗列第三种方案的操作步骤：

1. 重启 Mac 并在启动时点击 **Command + R** 进入恢复模式。
2. 在恢复模式点击菜单栏的 “工具-->终端”，执行命令 `csrutil disable`，然后重启。
3. 在终端执行 `brew cask install osxfuse` `。
4. 紧接着执行 `brew install ntfs-3g` （如无sbin的写入权限可先执行 `sudo chown -R $(whoami) $(brew --prefix) `）。
5. 然后执行 `sudo mv /sbin/mount_ntfs /sbin/mount_ntfs.original` 和 `sudo ln -s /usr/local/sbin/mount_ntfs /sbin/mount_ntfs` 替换系统的 ntfs 驱动。
6. 最后不要忘了再次进入恢复模式，然后执行 `csrutil enable` 启用系统完整性，避免系统数据被修改。

参考：[Enable NTFS write support in MacOS High Sierra (10.13.x) - medium](https://medium.com/the-lazy-coders-journal/enable-ntfs-write-support-in-macos-high-sierra-10-13-x-3fc8c67b5c1)

## macOS mojave 10.14.1+ WiFi crash 导致菜单栏卡住

当发生此类情况时除了重启还可以通过命令行解决，打开终端执行以下命令：

```shell
sudo kill -9 `ps aux | grep -v grep | grep /usr/libexec/airportd | awk '{print $2}'`
```

来自：[@Newbing](https://www.v2ex.com/member/Newbing) 

更重要的是防止问题发生，V2EX 上 [@gongzhang](https://www.v2ex.com/member/gongzhang) 提供了以下[解决方案](https://www.v2ex.com/t/505737)：

1. 在「网络」设置中，新建了一个位置，不再使用「自动」
2. 在「安全性与隐私」里，「定位服务」->「系统服务」中，取消了「 Wifi 网络」的勾选  没确认是哪个操作避免了卡死的问题 