---
title: win10安装mysql数据库
date: 2019-11-28 14:51:58
author: 胡豪
categories:
  - mysql
tags:
  - mysql
---

## Windows安装mysql数据库服务

### 一
官网上下载压缩包，并解压至本地磁盘


打开我们刚刚解压的文件夹，在bin目录下，创建一个my.ini文件，内容如下：
```dos
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=C:\\mysql\\mysql-8.0.11
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# datadir=C:\\mysql\\mysql-8.0.11\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```


### 二

先补充常用Dos命令
```1 dir
无参数：查看当前所在目录的文件和文件夹。
/s：查看当前目录已经其所有子目录的文件和文件夹。
/a：查看包括隐含文件的所有文件。
/ah：只显示出隐含文件。
/w：以紧凑方式（一行显示5个文件）显示文件和文件夹。
/p：以分页方式（显示一页之后会自动暂停）显示。
|more：前面那个符号是“\”上面的那个，叫做重定向符号，就是把一个
命令的结果输出为另外一个命令的参数。more也是一个命令，dir /w |more
得到的结果和dir /w /p的结果是一样的。
其他的参数大家可以用：dir/?查看。
2 cd
cd 目录名：进入特定的目录。如果看到有个目录显示为：abcdef ghi 就
输入：cdabcdef.ghi进入该目录。
cd\ 退回到根目录。
cd..退回到上一级目录。
3 md rd
md 目录名：建立特定的文件夹。 （dos下面习惯叫目录，win下面习惯叫文
件夹。呵呵！）
rd 目录名：删除特定的文件夹。
4 cls
清除屏幕。
5 copy
copy 路径\文件名 路径\文件名 ：把一个文件拷贝到另一个地方。
6 move
move 路径\文件名 路径\文件名 ：把一个文件移动（就是剪切+复制）到另
一个地方。
```

先以管理员身份运行cmd，进入mysql安装路径下bin文件夹目录

进入目录后执行以下命令初始化数据库：
```
mysqld --initialize --console     # 初始化数据库
```
![QQ浏览器截图20191202134950](http://tva1.sinaimg.cn/large/b535b7d3ly1g9iggq8xwrj20x702l0sn.jpg)
运行之后会生成第一次初始化mysql数据库root用户随机生成的密码，红色框中的字符串为登录root用户使用的密码，请注意该密码登录时一定要加上双引号，不然无法登录成功。


目录下运行 : 
```
mysqld --install     # mysql安装命令
```  

若报错，信息如下：Install/Remove of the Service Denied  
请正确选择“用管理员身份打开”打开cmd.exe程序

出现信息如下
```mysqld : 无法将“mysqld”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径
正确，然后再试一次。
所在位置 行:1 字符: 1
+ mysqld install
+ ~~~~~~
    + CategoryInfo          : ObjectNotFound: (mysqld:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```
请使用`.\mysqld --install`命令解决

出现信息如： Service successfully installed. 则说明安装成功


启动mysql服务
```
net  start  mysql    # 启动mysql服务
```


登录mysql
```
mysql -uroot -p //回车
输入密码："w3sYN%WxSfsH"
# 登录mysql 
# 其格式为：mysql   -u 用户名  -p  密码
```

![QQ浏览器截图20191202135128](http://tva1.sinaimg.cn/large/b535b7d3ly1g9iggq50zyj20gh06e3yg.jpg)