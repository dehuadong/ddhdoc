

[Windows Subsystem for Linux 文档 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/)

## 安装PostgreSQL

使用的主要命令：

```
sudo apt update
sudo apt show postgresql
--安装postgreSQL数据库
sudo apt install postgresql postgresql-contrib
--安装客户端
sudo apt install  postgresql-client
sudo apt install postgresql-client-common
--启动服务
sudo systemctl start postgresql.service
```

注：postgresql-contrib 或者说 contrib 包，包含一些不属于 PostgreSQL 核心包的实用工具和功能。在大多数情况下，最好将 contrib 包与 PostgreSQL 核心一起安装。

安装后查看pgsql相关的服务。

```
ubuntu@pg-vm:~$  systemctl status postgresql
```

### 修改数据库用户密码登录

默认登录后修改数据库postgres用户的密码后，就可以通过密码登录。

```
postgres=# ALTER USER postgres WITH PASSWORD 'postgres';
ALTER ROLE
postgres=# \q
ubuntu@pg-vm:~$
```

例1：

```
ubuntu@pg-vm:~$ psql -U postgres -h 127.0.0.1 -d postgres
```

例2：

```
ubuntu@pg-vm:~$ psql -U postgres -h localhost -d postgres
```

### postgresql 在ubuntu中修改外网访问 

1. 修改postgresql.conf

  postgresql.conf存放位置在/etc/postgresql/16/main下，这里的x取决于你安装PostgreSQL的版本号，编辑或添加下面一行，使PostgreSQL可以接受来自任意IP的连接请求。

`listen_addresses = '*'`

2. 修改pg_hba.conf

  pg_hba.conf，位置与postgresql.conf相同，虽然上面配置允许任意地址连接PostgreSQL，但是这在pg中还不够，我们还需在pg_hba.conf中配置服务端允许的认证方式。任意编辑器打开该文件，编辑或添加下面一行。


`host all all 0.0.0.0/0 md5`

3.修改PostgreSQL数据库默认用户postgres的密码

3-1 登录PostgreSQL

```
sudo -u postgres psql
```

3-2 修改登录PostgreSQL密码

```
ALTER USER postgres WITH PASSWORD 'postgres';
```

退出： \q

完成上三项配置后执行`sudo service postgresql restart`重启PostgreSQL服务后，允许外网访问的配置就算生效了。用户名：postgres 密码：postgres

```
sudo nano /etc/postgresql/16/main/postgresql.conf
listen_addresses = '*'
sudo nano /etc/postgresql/16/main/pg_hba.conf
host  all  all 0.0.0.0/0 md5
```

### 常用命令：

- `sudo service postgresql status` 用于检查数据库的状态。
- `sudo service postgresql start` 以开始运行数据库。
- `sudo service postgresql stop` 停止运行数据库。
- `sudo service postgresql restart` 重启

