---
title: Windows上MySQL服务的下载安装及卸载
date: 2025-03-20 20:36:58
description: 详细介绍了Windows上MySQL服务的下载安装及卸载，手摸手教你操作，截图详细。
tags:
  - MySQL
  - 数据库
categories:
  - MySQL
  - 数据库
---

# MySQL 下载

国内在官下载 MySQL 会很慢，推荐使用国内镜像下载：

[官网下载](https://dev.mysql.com/downloads/installer/)

[华为云镜像站下载 MySQL](https://mirrors.huaweicloud.com/mysql/Downloads/)

[阿里云镜像站下载 MySQL](https://mirrors.aliyun.com/mysql/)

这里我们[下载](https://mirrors.huaweicloud.com/mysql/Downloads/MySQL-8.0/mysql-8.0.29-winx64.zip)当前最新版的 mysql，版本为 mysql-8.0.29。

# MySQL 安装

首先将下载的文件解压到你想安装 MySQL 的路径，这里我的路径是：`D:\development`

解压后如下：

![mysql安装目录](/images/Install-MySQL.assets/41a1305a592246c88baa879bf5a71012.png)

接下来需要将 mysql 安装为服务，以管理员身份打开终端，进入 bin 目录，执行：

`.\mysqld.exe --install`

![终端管理员](/images/Install-MySQL.assets/c3c7e1b14b3c400cb380b1c00cd6354d.png)

![执行命令](/images/Install-MySQL.assets/38f115e3eb974e458e55983035a9e24b.png)

此时 MySQL 服务已安装但未启动，打开服务可查看

![MySQL服务](/images/Install-MySQL.assets/e2e5fac6bcff4a9d8b26764a2a9487e1.png)

现在需要先设置一下配置文件，在安装目录下创建`my.ini`，并写入以下内容，可自行修改相关配置。

```ini
[mysqld]
# 配置mysql安装地址，默认为配置文件的地址，可以不用配置
basedir=D:\development\mysql-8.0.29-winx64\
# 配置mysql的数据存储地址，默认为配置文件同级目录下的data文件夹，可以不用配置
datadir=D:\development\mysql-8.0.29-winx64\data
# mysql端口，默认3306，可以不用配置
port=3306

# 配置客户端，可以不用配置
[mysql]
default-character-set=utf8mb4

[client]
default-character-set=utf8mb4
```

接下来初始化 MySQL 并获取 root 账号的密码，以管理员身份打开终端，进入 bin 目录，执行:

`.\mysqld.exe --initialize --user=root --console`

![初始化MySQL](/images/Install-MySQL.assets/94270f1246a54cf89c84d33b65433f6e.png)

![初始化MySQL](/images/Install-MySQL.assets/7775ed8d004449b0852d6e8158d15769.png)

初始化完成后，需要**复制一下密码**，然后可以启动 MySQL 了，进入服务，直接启动。

![启动MySQL](/images/Install-MySQL.assets/3895a029c20646ee8eee26b330e0f538.png)

![已启动](/images/Install-MySQL.assets/cc57f8d2395d4846841be33f940499af.png)

还有最后一步，需要更改默认密码，不然无法使用，接下来执行连接 mysql 的命令，注意，这时候使用的是`mysql`客户端，执行：

`.\mysql.exe -u root -p`

执行后需要输入密码，就是上面框出来的，我这里是：`ysjdeDtf8D>(`，各位需要自行复制。

![连接MySQL](/images/Install-MySQL.assets/50bae05e91424267947c8ab160703ed9.png)

现在可以执行一下 sql 语句显示当前的所有表格试试，会发现用不了，需要修改密码，先验证试试：

`show databases;`

![执行SQL](/images/Install-MySQL.assets/b2aa7e40ea534ac08c85596c836a1eb4.png)

果然是需要修改密码，现在我们直接修改一下密码，执行：

`alter user root@localhost identified by 'root';`

最后引号中的`root`就是设置的密码，<mark>最好设置一个强密码</mark>，我这边为了演示所以设置的密码为弱密码。

> <mark>若设置为弱密码造成数据泄露概不负责</mark>
> <mark>若设置为弱密码造成数据泄露概不负责</mark>
> <mark>若设置为弱密码造成数据泄露概不负责</mark>

![设置MySQL密码](/images/Install-MySQL.assets/aac8985e12b646aeaebe6df3dd459416.png)

设置密码之后还需要刷新一下权限，执行：

`FLUSH PRIVILEGES;`

![刷新权限](/images/Install-MySQL.assets/7c6dc06586914230bf903e93e5f3b8e4.png)

接下来需要验证密码是否设置成功，先执行`exit;`退出，然后重新连接，如下：

![退出MySQL](/images/Install-MySQL.assets/8d0abd2db39543c79d4071e705fa1faf.png)

![重新连接](/images/Install-MySQL.assets/df046aaf96134192bb0e5917bda6d137.png)

验证能否使用 sql 语句

![验证](/images/Install-MySQL.assets/80cfe228938f4de3a07ef2c710d5926d.png)

恭喜，大功告成了！！！！！！！！！！

# MySQL 卸载

> 如果只是安装不用看这一步

首先进入服务停止 MySQL 服务

![停止](/images/Install-MySQL.assets/cc57f8d2395d4846841be33f940499ad.png)

![已停止MySQL](/images/Install-MySQL.assets/3895a029c20646ee8eee26b330e0f539.png)

接下来进入 MySQL 的 bin 目录执行：`.\mysqld.exe --remove`

![移除MySQL服务](/images/Install-MySQL.assets/bfd7a900b37847a4a6d08a637067476f.png)

刷新服务会发现已经没有了，接下来再直接删除整个 MySQL 文件夹。如果数据配置在其他文件夹也需要删除数据文件夹。这时候就已经卸载完成了！
