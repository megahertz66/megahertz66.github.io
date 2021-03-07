---
title: LINUX驱动(1)--LED 驱动
date: 2021-01-25
categories:
- SoftWare
---

# Setup the OpenGrok
  
**本文假设你当前的系统(ubuntu-18.04)什么都没有，从头重新开始安装**  

## install JAVA

## install tomcat8  

```
$ sudo apt-get install tomcat8
# 调整内存,默认的128MB 默认配置运行会导致OOM
$ sudo touch /usr/share/tomcat8/bin/setenv.sh
$ echo 'export JAVA_OPTS="-Xms512M -Xmx1024M"' | sudo tee -a /usr/share/tomcat8/bin/setenv.sh
```  

## install git  

`$ sudo apt install git`

## install ctags  

```
# 根据官网教程上描述 需要universal-ctags 所以说将原来的删除
$ sudo apt-get purge ctags

# 下载对应版本的ctags
$ git clone https://github.com/universal-ctags/ctags.git

# 配置ctags需要autotools
$ sudo apt install \
        gcc make \
        pkg-config autoconf automake \
        python3-docutils \
        libseccomp-dev \
        libjansson-dev \
        libyaml-dev \
        libxml2-dev  

$ cd ctags
 
$ ./autogen.sh 
 
$ ./configure
 
$ make
 
$ sudo make install

# 删除源文件（可选）
$ cd ..
$ rm -rf ctags     
```  

## install pip3  

```
sudo apt-get install python3-pip

sudo apt-get install --upgrade pip
```  

## download & setup opengrok  

```
https://github.com/oracle/opengrok/releases 下载 tar.gz 格式。不要下载源码  

mkdir ~/opengrok/{src,data,dist,etc,log}

tar -C /opengrok/dist --strip-components=1 -xzf opengrok-X.Y.Z.tar.gz

sudo ln -s ~/opengrok/disk/opengrok-X.Y.Z/lib/source.war /var/lib/tomcat/webapps/

访问"http://localhost:8080/source/" 查看能否访问成功,显示 there was a error! need configured也算是成功。

将打算查看的源码放在 '~/opengrok/src/' 并删除一些不必要的文件以加速配置文件的生成。

opengrok-indexer \
    -a ~/opengrok/dist/opengrok-1.3.16/lib/opengrok.jar -- \
    -c /usr/local/bin/ctags \
    -s ~/opengrok/src/ -d ~/opengrok/data -H -P -S -G \
    -W ~/opengrok/etc/configuration.xml -U http://localhost:8080/source

刷新浏览器
```




## 参考   
[官方教程](https://github.com/oracle/opengrok/wiki/How-to-setup-OpenGrok)  
[默默的点滴](https://www.mobibrw.com/category/opengrok)