title: Mac
date: 2018-8-21 22:53:00
tags: [Mac,OS]
comments: true


本帖记录个人在使用 Mac 操作系统上的一些骚操作，不断更新，以飨读者。

### 1. 快速移动网页到顶部或底部

用双指上下划触摸板吗？NO，我们有更骚的操作：

`command + ↑` 回到顶部

`command + ↓` 滚到底部

另外，

`fn + ↑` 上滚一页

`fn + ↓` 下滚一页

`fn + ←` Home，回到顶部

`fn + →` End，滚到底部

### 2. Dock 中只显示打开的应用

Mac 用着用着 Dock 上显示了一堆的图标，看着无法集中注意力，这时候可以打开 Terminal 输入：

```shell
defaults write com.apple.dock static-only -boolean true; killall Dock
```

回车就可以让 Dock 只显示打开的应用了，世界一下子清爽了。

PS，如果想恢复原样，可以执行：

```shell
defaults delete com.apple.dock static-only; killall Dock
```

### 3. 粘贴文字时不要带样式

有时候从网上看到不错的文字想要粘贴到 Word、Evernote 或者文字编辑应用上，但使用 `command + v`  会连文字样式都带过来，这时候就要使用骚一点的操作了，那就是：

```
command + shift + v
```

### 4. 输入英文时首字母不要自动大写

当你在文字编辑软件上写英文时，Mac 会很贴心地识别首字母并自动大写。但常常让我觉得画蛇添足，明明我不想大写，特别是在博客中或写或改一些代码或脚本时更觉得是捣乱。这时候，我找到了一个禁止自动首字母自动大写的方法：

```
系统偏好设置 —> 键盘 —> 文本 —> 去除勾选【自动大写字词的首字母】
```

### 5. 我要剪切或移动文件

Mac 中，复制的快捷键是 `command + c`，粘贴的快捷键是 `command + v` ，但是剪切的快捷键可不是 `command + x` ，如果要移动文件，你是不是还在进行 *粘贴完再删除* 的复杂操作？

NO，其实移动文件可以复制后使用快捷键 `command + option + v` 实现，快去试试吧。

### 6. 快速转换中文简繁体

确保简繁体转换服务已经开启，路径如下：

```
系统偏好设置 —> 键盘 —> 快捷键 —> 服务 —> 将文字转换为繁体中文 / 将文字转换为简体中文
```

选中需要进行简繁对换的文字，按：

- `control + shift + command + c` 将文字转为繁体中文
- `control + option + shift + command + c`  将文字转为简体中文

### 7. 读写 ntfs 格式的移动硬盘

1. 重启 Mac 并在启动时点击 **Command + R** 进入恢复模式。
2. 在恢复模式点击菜单栏的 “工具-->终端”，执行命令 `csrutil disable`，然后重启。
3. 在终端执行 `brew cask install osxfuse` `。
4. 紧接着执行 `brew install ntfs-3g` （如无sbin的写入权限可先执行 `sudo chown -R $(whoami) $(brew --prefix) `）。
5. 然后执行 `sudo mv /sbin/mount_ntfs /sbin/mount_ntfs.original` 和 `sudo ln -s /usr/local/sbin/mount_ntfs /sbin/mount_ntfs` 替换系统的 ntfs 驱动。
6. 最后不要忘了再次进入恢复模式，然后执行 `csrutil enable` 启用系统完整性，避免系统数据被修改。

参考：[Enable NTFS write support in MacOS High Sierra (10.13.x) - medium](https://medium.com/the-lazy-coders-journal/enable-ntfs-write-support-in-macos-high-sierra-10-13-x-3fc8c67b5c1)