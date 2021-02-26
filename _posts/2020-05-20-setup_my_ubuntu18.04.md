---
title: 安装 ubuntu18.04 的记录
date: 2020-05-20
categories:
- System Configuration
tags:
- Linux
---

折腾 ubuntu18.04

## Add root 

sudo passwd root

## Do not sleep

https://blog.csdn.net/xiaoxiao133/article/details/82847936

    编辑下列文件：sudo gedit /etc/systemd/logind.conf
    #HandlePowerKey按下电源键后的行为，默认power off
    #HandleSleepKey 按下挂起键后的行为，默认suspend
    #HandleHibernateKey按下休眠键后的行为，默认hibernate
    #HandleLidSwitch合上笔记本盖后的行为，默认suspend（改为ignore；即合盖不休眠）在原文件中，还要去掉前面的#

    然后将其中的：
    #HandleLidSwitch=suspend
    改成下面，记得去掉“#”号：
    HandleLidSwitch=ignore

    最后重启服务

    service systemd-logind restart

    注：在Ubuntu18.04和Ubuntu16.04笔记本电脑，下面测试可以使用。

## Install Sougou Piyin

https://www.jb51.net/article/186808.htm


## Install Fish Shell

sudo apt-get install fish

manaual:
    http://www.ruanyifeng.com/blog/2017/05/fish_shell.html

## Auto mount the disk


## Install Nodepad++

https://blog.csdn.net/renlonggg/article/details/53894998

## install Wireshark

https://linux.cn/article-11987-1.html

## Install Pc Momitor

sudo install indicator-multiload

## Install git 

sudo apt install git

## Install PDF Reader

use default

## Install Music 

https://music.163.com/#/download


## install rager

http://www.mikewootc.com/wiki/linux/usage/ranger_file_manager.html


## Check glibc version

https://blog.csdn.net/xibeichengf/article/details/48290297

## Install make & cmake

sudo apt install make 
sudo apt install cmake

## shortcut

https://blog.csdn.net/qq_40147863/article/details/98458018

## Install ftp server

https://blog.csdn.net/u011101881/article/details/38492019

## 美化

https://linux.cn/article-9447-1.html