---
title: CAN NOT CLONE FROM GIT
date: 2020-09-02
categories:
- Problem-solving
tags:
- Problem-solving
---


** FIX -- CAN NOT CLONE FROM GIT**

```shell
hert@hert-T440p:~$ git clone git://github.com/sstephenson/rbenv.git .rbenv
Cloning into '.rbenv'...
fatal: read error: Connection reset by peer
```

## 解决办法

```shell
hert@hert-T440p:~$ git clone http://github.com/sstephenson/rbenv.git .rbenv
Cloning into '.rbenv'...
warning: redirecting to https://github.com/sstephenson/rbenv.git/
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 2865 (delta 0), reused 1 (delta 0), pack-reused 2861
Receiving objects: 100% (2865/2865), 559.78 KiB | 27.00 KiB/s, done.
Resolving deltas: 100% (1787/1787), done.

```

将git 换成 http
