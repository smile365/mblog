---
title:  PostgreSQL新手入门教程
heading: 
date: 2019-06-04T08:13:00.474Z
tags: ["PostgreSQL教程","postgresql安装教程"]
categories: ["code"] 
---

使用[官网的rpm](https://www.postgresql.org/download/linux/redhat/)包在centos7下安装PostgreSQL 11
```shell
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
yum -y install postgresql11-server
/usr/pgsql-11/bin/postgresql-11-setup initdb
systemctl enable postgresql-11
systemctl start postgresql-11
sudo -u postgres psql -c "SELECT version();"
```

创建角色和数据库: `sudo -u postgres psql`  
```sql
CREATE ROLE sxy LOGIN PASSWORD 'sxy';
create database sxydb;
grant all privileges on database sxydb to sxy;
```

开启远程访问和登录方式 
```bash
#允许所有内网网段通过密码方式登录数据库
echo -e "host \t all \t all \t 192.168.31.0/24 \t md5" >> /var/lib/pgsql/11/data/pg_hba.conf
echo "listen_addresses = '*'" >> /var/lib/pgsql/11/data/postgresql.conf
sudo systemctl restart postgresql-11
psql -h 127.0.0.1 -d sxydb -U sxy -W
```

> 登录方式可参考：[pg_hba.conf](https://www.postgresql.org/docs/9.3/auth-pg-hba-conf.html) 
若提示`psql: could not connect to server: 拒绝连接
	Is the server running on host "xxx" and accepting
	TCP/IP connections on port 30000?`
  可使用`p`参数指定端口：`psql -h 127.0.0.1 -p 5432 -d sxydb -U sxy -W `
  

导入某2千万数据测试
```sql
CREATE TABLE persons
(
  Name character varying(50),
  CtfId character varying(18),
  Gender character varying(50),
  Address character varying(100),
  Mobile character varying(50),
  Tel character varying(50),
  EMail character varying(50),
  Nation character varying(50)
);
\COPY persons FROM '/home/persons.csv' DELIMITER ',' CSV HEADER;
```

32G内存8核cpu，建议修改[postgresql.conf](https://github.com/digoal/blog/blob/master/201611/20161121_01.md)的如下配置项  `vim /var/lib/pgsql/11/data/postgresql.conf`
```
fsync no
shared_buffers 8GB # 1/4 memery
work_mem 100MB  # shared_buffers/核数/10
effective_cache_size 16GB # 1/2 memery
maintenance_work_mem 160MB # effective_cache_size/100
max_worker_processes = 128
wal_buffers = 300MB       #min(2GB,shared_buffers/32)
```


安装[Psycopg](http://initd.org/psycopg/)与[pony](https://docs.ponyorm.org/firststeps.html)  
```bash
pip install psycopg2-binary pony
```

测试  
```python
from pony.orm import *

db = Database()

class Midc(db.Entity):
	info = Required(str)

db.bind(provider='postgres', user='sxy', password='sxy', host='127.0.0.1', database='sxydb')
db.generate_mapping(create_tables=True)
p1 = Midc(info='test')
commit()
elect(p for p in Midc).show()
db.close()
```

参考  
- [install-postgresql](https://www.postgresql.org/download/linux/redhat/)
- [postgresql-docs](https://www.postgresql.org/docs/11/index.html)
- [PostgreSQL 与 12306 抢火车票的思考](https://github.com/digoal/blog/blob/master/201611/20161124_02.md)
- [ponyorm](https://docs.ponyorm.org/firststeps.html)
