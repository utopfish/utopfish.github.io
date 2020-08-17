---
title: spark ubuntu集群安装
date: 2019-12-04 12:49:40
categories:
- 编程学习
tags: 
- spark
- 安装
---

## Scala 安装 ##
1.使用镜像地址http://distfiles.macports.org/scala2.13/
```
#创建文件夹
mkdir sparkWork
cd sparkWork
#下载scala
wget http://distfiles.macports.org/scala2.13/scala-2.13.0.tgz
#解压
tar -xvf scala-2.13.0.tgz
```
<!-- more -->
2.配置
```
#获取当前路径
pwd

#为系统每一个用户配置环境
sudo nano /etc/profile
#进入文件最后添加如下
export SCALA_HOME=/home/ubuntu/sparkWork/scala-2.12.10 #此为解压scala文件夹位置
export PATH=$PATH:$SCALA_HOME/bin
#使环境变量生效
source /etc/profile

#为当前用户配置环境
sudo nano ~/.bashrc
#进入文件最后添加如下
export SCALA_HOME=/home/ubuntu/sparkWork/scala-2.12.10 #此为解压scala文件夹位置
export PATH=$PATH:$SCALA_HOME/bin
#使环境变量生效
source ~/.bashrc
```
3.验证
```
scala -version
```
显示如下表明安装成功

[![QlaQ58.md.png](https://s2.ax1x.com/2019/12/04/QlaQ58.md.png)](https://imgse.com/i/QlaQ58)

## spark安装 ##

1.安装
国外安装太慢，可以使用北理工的镜像服务选择合适的版本
>http://mirror.bit.edu.cn/apache/spark/
```
#创建文件夹
mkdir sparkWork
cd sparkWork
#下载spark
wget http://mirror.bit.edu.cn/apache/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.6.tgz
#解压 
tar -xvf spark-2.4.4-bin-hadoop2.6.tgz
#修改文件名
mv spark-2.4.4-bin-hadoop2.6 spark-2.4.4
```
2.配置
```
#获取当前路径
pwd

#为系统每一个用户配置环境
sudo nano /etc/profile
#进入文件最后添加如下
export SPARK_HOME=/home/ubuntu/sparkWork/spark-2.4.4 #此为解压spark文件夹位置
export PATH=$PATH:$SPARK_HOME/bin
#使环境变量失效
source /etc/profile

#为当前用户配置环境
sudo nano ~/.bashrc
#进入文件最后添加如下
export SPARK_HOME=/home/ubuntu/sparkWork/spark-2.4.4 #此为解压spark文件夹位置
export PATH=$PATH:$SPARK_HOME/bin
#使环境变量失效
source ~/.bashrc
```
3.验证

```
spark-shell
```
显示如下即安装成功

[![QladaV.md.png](https://s2.ax1x.com/2019/12/04/QladaV.md.png)](https://imgse.com/i/QladaV)

安装过程中出现任何bug，将报错信息复制到谷歌搜索中进行搜索，大部分原因可能是安装版本不匹配的问题