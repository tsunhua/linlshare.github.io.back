---
title: Ubuntu
date: 2019-01-29 20:39:32
tags: [Ubuntu]
---

## 排错

### fix broken package

参考：[Ubuntu fix broken package (best solution)](http://www.iasptk.com/ubuntu-fix-broken-package-best-solution/)

```shell
# step 1
sudo apt-get update --fix-missing
sudo dpkg --configure -a
sudo apt-get install -f

# step 2
sudo vim /var/lib/dpkg/status
Locate the corrupt package, and remove the whole block of information about it and save the file.

```

