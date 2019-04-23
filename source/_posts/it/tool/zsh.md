---
title: zsh
date: 2019-04-19 11:24:52
tags: [zsh]
---

### 快速安装

```shell
# 使用 curl
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# 或者使用 wget
$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

### 配置主题

从官网中可以预览各种主题，见 https://github.com/robbyrussell/oh-my-zsh/wiki/Themes。配置主题只需按一下步骤：

```shell
# 1）修改 ZSH_THEME 值为想要设置的主题，允许无主题
$ vim ~/.zshrc
# 2）使配置生效
$ source ~/.zshrc
```

### 禁止粘贴命令时自动对特殊字符进行转义

（1）编辑 `misc.zsh` 文件

```shell
vim  ~/.oh-my-zsh/lib/misc.zsh
```

（2）注释掉 `url-quote-magic`

```
#if [[ $ZSH_VERSION != 5.1.1 ]]; then
#  for d in $fpath; do
#       if [[ -e "$d/url-quote-magic" ]]; then
#               if is-at-least 5.1; then
#                       autoload -Uz bracketed-paste-magic
#                       zle -N bracketed-paste bracketed-paste-magic
#               fi
#               autoload -Uz url-quote-magic
#               zle -N self-insert url-quote-magic
#      break
#       fi
#  done
#fi
```

### 解决中文乱码

在`~/.zshrc`添加

```shell
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
```

然后执行

```shell
source ~/.zshrc
```

### 更换系统默认的 shell 为 zsh

```shell
$ chsh -s /bin/zsh
```

### 无法手动更新 zsh

```shell
$cd .oh-my-zsh/ ( to change to its root directory)
$git status (now you should be in your oh-my-zsh root with master on it and do git status)
$git stash/git add . (to stash the changes and head back to master/add the changes)
$git commit -m (if you decided to add the changes)
$upgrade_oh_my_zsh (you can now upgrade)
```

