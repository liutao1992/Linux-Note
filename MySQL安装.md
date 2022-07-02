### MySQL安装

检查当前CentOS7系统是否安装mariadb数据库,若安装则进行卸载

```
[root@hadoop hive-1.1.0-cdh5.15.1]# rpm -qa|grep mariadb
mariadb-libs-5.5.68-1.el7.x86_64
[root@hadoop hive-1.1.0-cdh5.15.1]# rpm -e mariadb-libs-5.5.64-1.el7.x86_64 --nodeps
error: package mariadb-libs-5.5.64-1.el7.x86_64 is not installed
```

- 启用MySQL仓库

```
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

- 安装MySQL

```
sudo yum install mysql-community-server
```

若在安装过程遇到以下问题

```

Public key for mysql-community-libs-compat-5.7.38-1.el7.x86_64.rpm is not installed


 Failing package is: mysql-community-libs-compat-5.7.38-1.el7.x86_64
 GPG Keys are configured as: file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

则在命令行执行以下指令
```
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
```

- 启动MySQL

```
sudo systemctl enable mysqld
sudo systemctl start mysqld
```

- 修改密码

查看默认密码
```
[root@hadoop hive-1.1.0-cdh5.15.1]# sudo grep 'temporary password' /var/log/mysqld.log
2022-07-02T08:55:49.178106Z 1 [Note] A temporary password is generated for root@localhost: dqu_hGV+O9_D
```

- 设置密码策略

```
mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.01 sec)

mysql> set global validate_password_length=0;
Query OK, 0 rows affected (0.00 sec)

mysql> set password for 'root'@'localhost'=password('123456');
Query OK, 0 rows affected, 1 warning (0.00 sec)
```