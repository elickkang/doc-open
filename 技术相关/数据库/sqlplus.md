# sqlplus Linux版本安装和使用

 [2018年5月5日](http://www.u396.com/sqlplus-linux版本安装和使用.html) [raid](http://www.u396.com/author/raid) [后端开发](http://www.u396.com/category/rear-end), [工具使用](http://www.u396.com/category/tools)

以下介绍SQLPLUS的Linux版本安装及使用，其他版本下载及文档见[Oracle Instant Client Downloads](http://www.oracle.com/technetwork/database/database-technologies/instant-client/downloads/index.html)。

## 下载

我的服务器为x86-64，要找对应系统版本，打开[Instant Client for Linux x86-64](http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html)进入下载页面，优先考虑当前最新版本为12.2.0.1.0，选择接受许可协议“Accept License Agreement”，下载以下三个包：

1. 必要的基础程序 [instantclient-basic-linux.x64-12.2.0.1.0.zip](http://download.oracle.com/otn/linux/instantclient/122010/instantclient-basic-linux.x64-12.2.0.1.0.zip)
2. 一些库和sqlplus可执行文件 [instantclient-sqlplus-linux.x64-12.2.0.1.0.zip](http://download.oracle.com/otn/linux/instantclient/122010/instantclient-sqlplus-linux.x64-12.2.0.1.0.zip)
3. 包含应用开发需要的头文件以及Makefile示例 [instantclient-sdk-linux.x64-12.2.0.1.0.zip](http://download.oracle.com/otn/linux/instantclient/122010/instantclient-sdk-linux.x64-12.2.0.1.0.zip)

需要登录方可下载

## 安装

### 解压压缩包

解压后会自动创建并将文件解压至instantclient_12_2文件夹，将instantclient_12_2移至/usr/local并对动态库做软连接

```
`mv instantclient_12_2 /usr/local/``cd /usr/local/instantclient_12_2``ln -s libclntsh.so.12.1 libclntsh.so``ln -s libocci.so.12.1 libocci.so`
```

### 安装 libaio

```
`sudo yum install libaio`
```

### 设置环境变量

```
`echo 'export LD_LIBRARY_PATH=/usr/local/instantclient_12_2:$LD_LIBRARY_PATH' >> ~/.bash_profile``echo 'export PATH=/usr/local/instantclient_12_2:$PATH' >> ~/.bash_profile``source ~/.bash_profile`
```

### 优化命令行使用

其实到此已经可以使用了，为了用起来更方便，我们需要安装`rlwrap`，`rlwrap`是一个wrapper，它接收我们输入的命令并交给我们指定的程序去执行，同时使用GNU readline库提供命令行编辑功能并且记录命令历史，将会使你的命令行用起来更加方便（主要是sqlplus太难用了）。

```
`yum install -y rlwrap``echo 'alias sqlplus="rlwrap sqlplus"' >> ~/.bashrc``source ~/.bashrc`
```

OK，现在可以放心去用了。

## 连接使用数据库

- 连接本机数据库 `sqlplus username/password`
- 连接远程数据库 `sqlplus username/password@//domain:port/dbname`