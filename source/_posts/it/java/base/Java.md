---
title: Java
date: 2019-02-13 09:48:14
tags: [Java]
---

## 安装 JDK 环境

### 在 Mac 下

```shell
# 安装 JDK 8
brew tap caskroom/versions
brew cask install java8

# 安装最新版本
brew cask install java

# 使用 jenv 管理 JDK 版本
brew install jenv
ls -1 /Library/Java/JavaVirtualMachines 
mkdir ~/.jenv/versions
jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home/
jenv versions
jenv global 1.8
java -version

# 检测 jenv 是否正常安装
jenv doctor

# 在 zsh 中启用
vim ~/.zshrc
## 在末尾追加
export JENV_ROOT="/usr/local/opt/jenv"
if which jenv > /dev/null; then eval "$(jenv init -)"; fi
## 使其生效
source ~/.zshrc

# 设置 Java Home
jenv enable-plugin export
exec $SHELL -l
echo ${JAVA_HOME}
```

