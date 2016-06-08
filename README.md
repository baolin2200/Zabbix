# Zabbix
>注：本文内容参考自互联网，版权归属于互联网，感谢赵班长   

<pre>
如有需求请联系：
邮箱：baolin2200@163.com
QQ：815604250
</pre>   
##1.1 Zabbix简介
&emsp;&emsp;zabbix是一个基于WEB界面的提供分布式系统监视以及网络监视功能的企业级的开源解决方案。   
&emsp;&emsp;zabbix能监视各种网络参数，保证服务器系统的安全运营；并提供灵活的通知机制以让系统管理员快速定位及解决存在的各种问题。   
&emsp;&emsp;zabbix由2部分构成，zabbix server与可选组件zabbix agent。server为管理数据收集端，agent端为客户端数据产生者。Server端收集数据分为两种，一种为向客户端请求拉去，一种为设置agent端主动推送。   
##1.2 环境准备
<pre>
1. 查看系统版本
[root@linux-node1 ~]# cat /etc/redhat-release 
CentOS Linux release 7.2.1511 (Core) 
2. 内核版本
[root@linux-node1 ~]# uname -a
Linux linux-node1 3.10.0-327.18.2.el7.x86_64 #1 SMP Thu May 12 11:03:55 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
3. 确保selinux关闭
[root@linux-node1 ~]# getenforce
Disabled
4. iptables 关闭
[root@linux-node1 ~]# iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         
5. 主机名
[root@linux-node1 ~]# hostname
linux-node1
6. hosts本地解析
[root@linux-node1 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.56.11  linux-node1 linux-node1.example.com
192.168.56.12  linux-node2 linux-node2.example.com
7. 安装yum源
[root@linux-node1 ~]# rpm -ivh http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm
</pre>
##1.3安装Zabbix及相关软件
###1.3.1安装Zabbix3.0
<pre>
[root@linux-node1 ~]# rpm -ivh http://mirrors.aliyun.com/zabbix/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm
</pre>
###1.3.2安装相关软件
<pre>
[root@linux-node1 ~]# yum install zabbix-server zabbix-web zabbix-server-mysql zabbix-web-mysql mariadb-server mariadb zabbix-agent -y
\#安装zabbix-server程序包组php、httpd、lib等
\#安装数据库mariadb-server及客户端。在Centos7上如果通过yum安装的话，已经找不到了MySQL已经变成了mariadb。
\#安装zabbix-agent客户端对自己进行监控
</pre>
##1.4配置相关服务
###1.4.1修改PHP时区问大陆时区
<pre>
[root@linux-node1 ~]# sed -i 's@# php_value date.timezone Europe/Riga@php_value date.timezone Asia/Shanghai@g' /etc/httpd/conf.d/zabbix.conf
[root@linux-node1 ~]# grep Shanghai /etc/httpd/conf.d/zabbix.conf 
        php_value date.timezone Asia/Shanghai
</pre>
###1.4.2数据库配置
####1.4.2.1启动数据库并设置开机启动
<pre>
[root@linux-node1 ~]# systemctl start mariadb
[root@linux-node1 ~]# systemctl enable mariadb.service
</pre>
####1.4.2.2创建zabbix的数据库管理用户
<pre>
[root@linux-node1 ~]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.47-MariaDB MariaDB Server
Copyright (c) 2000, 2015, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MariaDB [(none)]> create database zabbix character set utf8 collate utf8_bin;
Query OK, 1 row affected (0.00 sec)
MariaDB [(none)]> grant all on zabbix.* to zabbix@'localhost' identified by '123456';
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> exit
Bye
</pre>
####1.4.2.3导入初始数据
<pre>
[root@linux-node1 zabbix-server-mysql-3.0.3]#zcat create.sql.gz |mysql -uzabbix -p123456 zabbix   
</pre>
###1.4.3修改zabbix配置
<pre>
[root@linux-node1 zabbix-server-mysql-3.0.3]# vim /etc/zabbix/zabbix_server.conf
DBHost=localhost    #数据库所在主机
DBName=zabbix       #数据库名 
DBUser=zabbix       #数据库用户 
DBPassword=123456   #数据库密码 
</pre>
###1.4.4启动zabbix及http 服务
<pre>
[root@linux-node1 ~]# systemctl start zabbix-server  
[root@linux-node1 ~]# systemctl start httpd
</pre>
##1.5Web页面安装主站：
* 在浏览器中打开：
<pre>
http://192.168.56.11/zabbix/setup.php   
</pre>
* 欢迎界面
![图1-开机界面](http://xxxxx)
* 环境检查确保全部都为OK 
![图2-环境检查](http://xxxxx) 
* 连接到数据库,输入密码
![图3-连接数据库](http://xxxxx) 
* 配置Zabbix服务细节可默认
![图4-广告](http://xxxxx) 
* 确认配置
![图5-确认配置](http://xxxxx) 
* 完成安装，会在/etc/zabbix/web/zabbix.conf.php生成配置文件。(图略)
* 用户登录，默认用户名:Admin 密码：zabbix,登陆后请及时更改。
![图6-用户登录](http://xxxxx) 







