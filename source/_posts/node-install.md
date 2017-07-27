---
title: node-js 安装手册
---
相信很多前端开发的同学都折腾过node-js的安装，由于node-js不断的在更新版本，导致我们在使用npm安装的一些轮子无法使用。
那么我总结出了一些点，供大家参考：
> 
  1. 不要盲目更新测试版本
  2. node-js 双版本最为稳定（6.4.x）
  3. 自己在造轮子时尽可能满足越低版本最好

## 言归正传，如何安装稳定的node-js

### 1. 官网下载 [6.11.x](https://nodejs.org/en/download/)

> 选择适合自己系统的安装文件

  <img src="/img/node1.png">

### 2. win && mac 双击下载的安装文件，执行安装

> win 选择安装目录
> mac 需root权限安装

### 3. linux 安装node-js

> 安装node-js之前，如果没有安装g++， libssl-dev需要先安装

```bash
$ sudo apt-get install g++
$ sudo apt-get install libssl-dev
```
>解压，安装node

```bash
  # 解压文件
  $ tar zxvf node-v...tar.gz 
  # 进入解压文件夹目录
  $ cd node-js
  # 检查环境
  $ ./configure
  # 执行安装
  $ make && make install 
  # 查看node
  $ node -v
  $ v6.11.0
  $ npm -v
  $ 3.10.10
```

### node 安装完毕

> 如果你在国内希望使用npm安装依赖库更快，推荐使用cnpm

[淘宝镜像](https://npm.taobao.org/)

```bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

#### 安装完后，就可以使用 cnpm 来代替npm安装node库了

```bash
$ cnpm install coffeescript
$ cnpm install node-scss
```

