---
title: CentOS安装使用postgre
date: 2016-09-01 14:26:13
tags: postgre
---

仅记录一下安装过程：
我的环境是阿里云的服务器，操作系统CentOS
1. sudo yum install postgre-server -b current, 安装好的路径在
2. 创建一个目录，用于数据存放，比如：mkdir /pgdata
3. 初始化数据库：/u01/pgsql/bin/pg\_ctl init  -D /pgdata
4. 创建数据库：/u01/pgsql/bin/createdb demo
5. /u01/pgsql/bin/psql demo，登录可用

添加新用户：
`登录psql后，create user dbuser with password ’dbuser’;`

赋权限
`GRANT ALL PRIVILEGES ON DATABASE exampledb to dbuser;`
`\c demo`
`GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO dbuser;`


