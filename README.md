# 搭建环境

```shell
# 若没有docker环境，只想拉取镜像，可以用docker_pull.py代替docker pull
# 要求本地存在socks代理, 端口与docker_pull.py脚本中一致（脚本中是7890，也可以修改）
# 例如 拉取mysql/mysql-server镜像
python docker_pull.py mysql/mysql-server
```

##  oracle数据库环境搭建

1 拉取镜像（https://hub.docker.com/r/oracleinanutshell/oracle-xe-11g）

```shell
docker pull oracleinanutshell/oracle-xe-11g

# 没有docker环境可以用这个脚本代替

python docker_pull.py oracleinanutshell/oracle-xe-11g
```

2 启动容器(允许远程登陆）

```shell
 docker run --name=mssql -d -p 1521:1521 -e ORACLE_ALLOW_REMOTE=true oracleinanutshell/oracle-xe-11g
```

3 登录数据库
```shell
docker exec -it oracle1 sqlplus system/oracle@//127.0.0.1:1521
```

4 监视器配置

```text
用户名：system
密码：oracle
```

##  mssql数据库环境搭建

1 拉取镜像（https://hub.docker.com/_/microsoft-mssql-server?tab=description）

```shell
docker pull mcr.microsoft.com/mssql/server
```

2 启动容器

```shell
 docker run --name=mssql1 -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Root1234+"   -p 1433:1433 -d mcr.microsoft.com/mssql/server
```

3 登录数据库
```shell
docker exec -it mssql1 /opt/mssql-tools/bin/sqlcmd -S 127.0.0.1,1433 -U sa -P Root1234+
```

4 监视器配置

```text
用户名：sa
密码：Root1234+
数据库名：master
发送内容：select name from sysobjects where name = 'student';
接受内容必须包含：student
```

 ##  mysql数据库环境搭建

1 拉取镜像（https://hub.docker.com/r/mysql/mysql-server）

```shell
docker pull mysql/mysql-server
```

2 新建配置文件my.cnf(为了设置default-authentication-plugin=mysql_native_password)

```shell
cat >> my.cnf <<END

# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
default-authentication-plugin=mysql_native_password
bind-address=::
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
END
```

3 启动容器

```shell
docker run --name=mysql1 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root1234 -e MYSQL_ROOT_HOST=% --mount  type=bind,src=/xxx/xxx/my.cnf,dst=/etc/my.cnf -itd mysql/mysql-server
```

4 登录数据库
```shell
docker exec -it mysql1 mysql -uroot -p
```

4 监视器配置

```text
用户名：root
密码：root1234
数据库名：mysql
发送内容：show tables;
接受内容必须包含：columns_priv
```
