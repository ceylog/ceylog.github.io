---
layout:     post
title:      osx 常用开发工具
category: blog
description: osx 常用开发工具 技巧 整理
tags: [osx, tool, zsh, homebrew, brew, iterm2]
---


## ZSH
### install
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

## homebrew
### install
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

## iterm2
### install
    brew cask install iterm2

## dev tool
### mysql
#### install
    brew install mysql
#### start
    mysql.server start
    
### redis
#### install
    brew install redis
    ==> Downloading https://homebrew.bintray.com/bottles/redis-3.2.3.el_capitan.bottle.tar.gz

    curl: (7) Failed to connect to akamai.bintray.com port 443: Operation timed out
    Error: Failed to download resource "redis"
    Download failed: https://homebrew.bintray.com/bottles/redis-3.2.3.el_capitan.bottle.tar.gz
    Warning: Bottle installation failed: building from source.
    ==> Using the sandbox
    ==> Downloading http://download.redis.io/releases/redis-3.2.3.tar.gz
    ==> Downloading from http://101.44.1.117/files/B22400000598F419/download.redis.io/releases/redis-3.2.3.tar.gz
    ######################################################################## 100.0%
    ==> make install PREFIX=/usr/local/Cellar/redis/3.2.3 CC=clang
    ==> Caveats
    To have launchd start redis now and restart at login:
      brew services start redis
    Or, if you don't want/need a background service you can just run:
      redis-server /usr/local/etc/redis.conf
    ==> Summary
    🍺  /usr/local/Cellar/redis/3.2.3: 10 files, 1.7M, built in 13 seconds
    
#### start
    brew services start redis

### JDK
#### install
    brew cask install java
    java -version
    java version "1.8.0_102"
    Java(TM) SE Runtime Environment (build 1.8.0_102-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.102-b14, mixed mode)

### maven
#### install
    brew cask install maven
    mvn -v
    Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
    Maven home: /usr/local/Cellar/maven/3.3.9/libexec
    Java version: 1.8.0_102, vendor: Oracle Corporation
    Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_102.jdk/Contents/Home/jre
    Default locale: en_US, platform encoding: UTF-8
    OS name: "mac os x", version: "10.11.6", arch: "x86_64", family: "mac"

### gradle
#### install
    brew cask install gradle
    gradle -v
    ------------------------------------------------------------
    Gradle 3.0
    ------------------------------------------------------------

    Build time:   2016-08-15 13:15:01 UTC
    Revision:     ad76ba00f59ecb287bd3c037bd25fc3df13ca558

    Groovy:       2.4.7
    Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
    JVM:          1.8.0_102 (Oracle Corporation 25.102-b14)
    OS:           Mac OS X 10.11.6 x86_64

### groovy
#### install
    brew cask install groovy
    
### cask
cask 可以安装很多软件、例如`jdk`，`maven`， `groovy`， `macDown`， `idea` 等等一系列软件都可以通过`cask`安装
