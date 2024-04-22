# rsync使用手册

## 简介

rsync 全称 remote synchronize，即 远程同步。

rsync 是 linux系统下的数据镜像备份工具，可用于本地文件复制，也可与其他 SSH、rsync 主机远程同步文件和目录。

使用 rsync 进行数据同步时，第一次进行全量备份，以后则是增量备份，利用 rsync 算法（差分编码），只传输差异部分数据。

## 安装与配置

```sh
//安装方法
# Debian
$ sudo apt-get install rsync
# Red Hat
$ sudo yum install rsync
# Arch Linux
$ sudo pacman -S rsync
//验证安装成功
$ which rsync
/usr/bin/rsync
```

配置rsyncd服务文件

```sh
vim /etc/rsyncd.conf
```

![image-20230309153107797](./rsync%E4%BD%BF%E7%94%A8%E6%89%8B%E5%86%8C.assets/image-20230309153107797.png)

rsyncd服务默认使用873端口，可以通过添加port配置修改服务端口。

## 工作模式

### 1.本地复制

将本地目录 test1 下的文件同步至本地目录 test2

```sh
rsync -r test1/ test2
```

### 2.将本地数据同步到远程

将本地目录 /test/rsync-test1/ 下的文件同步至远程主机 192.168.31.36 目录 /var/rsync-test2/中

```sh
rsync -r rsync-test1/ 192.168.31.36:/test/rsync-test2
```

### 3.将远程数据同步到本地

将远程主机  192.168.31.36 目录 /var/rsync-test2/下的文件同步至本地目录 /test/rsync-test1/ 

```sh
rsync -r 192.168.31.36:/test/rsync-test2/ /test/rsync-test1/
```

## 认证协议

## 参数列表

rsync命令选项
-a：–archive archive mode 权限保存模式，相当于 -rlptgoD 参数，存档，递归，保持属性等。
-r：–recursive 复制所有下面的资料，递归处理。
-p：–perms 保留档案权限，文件原有属性。
-t：–times 保留时间点，文件原有时间。
-g：–group 保留原有属组。
-o：–owner 保留档案所有者(root only)。
-D：–devices 保留device资讯(root only)。
-l：–links 复制所有的连接，拷贝连接文件。
-z：–compress 压缩模式，当资料在传送到目的端进行档案压缩。
-H：–hard-links 保留硬链接文件。
-A：–acls 保留ACL属性文件，需要配合–perms。
-P：-P参数和 --partial --progress 相同，只是为了把参数简单化，表示传进度。
-v：–verbose 复杂的输出信息。
-u：–update 仅仅进行更新，也就是跳过已经存在的目标位置，并且文件时间要晚于要备份的文件，不覆盖新的文件。

--version：输出rsync版本。
--port=PORT：定义rsyncd(daemon)要运行的port(预设为tcp 873)。
--delete：删除那些目标位置有的文件而备份源没有的文件。
--password-file=FILE ：从 指定密码文件中获取密码。
--bwlimit=KBPS：限制 I/O 带宽。
--filter “-filename”：需要过滤的文件。
--exclude=filname：需要过滤的文件。
--progress：显示备份过程。

> 通常常用的选项 –avz
