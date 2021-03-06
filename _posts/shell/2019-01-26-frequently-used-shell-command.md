---
layout: post
title: "常用的shell命令或组合"
author: "见欢"
categories: Shell
description: 常用的Linux shell命令及组合的整理。
keywords: shell,linux
---

### 前言
我们知道shell提供了用户与Linux内核交互的各种工具，这些操作系统自带工具实现是非常高效的，起码比我们自己写程序要快，比如文件搜索、文本内容筛选等场景，所以使用shell命令及其组合，效率非常高。
管道是命令组合的最常用方式，下面将会划分一些场景，梳理下工作经常遇到的一些命令或命令组合。

### 一、文件查找
##### 1、删除空文件

```shell
# 方法1
find . -type f -size 0 -exec rm -rf {};
# 方法2
find . -type f -size 0 -delete;
```

##### 2、按文件名称或后缀查找
```shell
find ./dir -name "*.php";
find ./dir -name "index.php";
```

```shell
# 其他例子
find . -mtime +0 # find files modified greater than 24 hours ago
find . -mtime 0 # find files modified between now and 1 day ago
# (i.e., in the past 24 hours only)
find . -mtime -1 # find files modified less than 1 day ago (SAME AS -mtime 0)
find . -mtime 1 # find files modified between 24 and 48 hours ago
find . -mtime +1 # find files modified more than 48 hours ago
```
更多的文件查询，请查看[25个find命令的例子](https://www.binarytides.com/linux-find-command-examples/)


### 二、文本内容处理
##### 1、同时输出匹配行前后内容
```shell
grep -m 1 -A 10 -B 10 -i "Exception" today.log;
# -m指定最多输出一个符合的结果
# -A和-B同时输出前后n行
```
需要注意到的是，以上方式，如果查找的是多个文件，则输出内容会包含对应的文件名称

##### 2、文件内容替换
```shell
# 常用
sed -i 's/被替换内容/新内容/g' file;
# 删除匹配行
sed -i '/^a.*/d' tmp.txt
```

##### 3、统计字段出现次数并排序
```shell
awk -F' ' '{print $1}' sourcefile.log | uniq -c | sort -k 1 -r;
```

##### 4、统计字段总和

```shell
awk -F',' '{ sum+=$6 } END {print sum}' error.log
```

##### 5、查找文本文件内容

```shell
find ./ -type f -name "*.log" | xargs grep "ERROR"
```

### 三、网络相关
##### 1、查看占用指定端口的程序
```shell
# 查找占用8000端口的进程
netstat -ntlp|grep 8000
lsof -i:8000
ps -ef |grep `netstat -ntlp|grep 8000|awk '{print $7}'|awk -F/ '{print $1}'`
```


### 四、负载相关
##### 1、查看http的并发请求数及其TCP连接状态
```shell
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}';
```

##### 2、查看带宽使用

```shell
iftop -i 网卡名称
```

### 五、其他
##### 1、查看磁盘情况
```shell
# 查看当前目录大小,可指定深度
du -h ./;
# 查看磁盘空间
df -h;
```

##### 2、 上传下载文件

```shell
scp -r dist/ bugvanisher@10.12.77.16:/home/yu_he/platform/frontend

# 上传
rsync -av -e ssh rtc-server-mcu yu_he@10.128.8.81:/home/yu_he
# 下载
rsync -chavzP --stats yu_he@10.128.8.17:/home/yu_he/certs/fullchain.pem ~/temp
```

