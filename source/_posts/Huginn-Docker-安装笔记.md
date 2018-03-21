---
title: Huginn Docker 安装笔记
date: 2018-03-22 00:29:14
tags: [Huginn, Docker]
categories: Huginn
---

官方文档:https://github.com/huginn/huginn/blob/master/doc/docker/install.md
官方文档没有详细说明 Docker 怎么和本机数据库连接的,以下是记录.

# 安装 Docker
安装官方文档安装
# 安装 MySQL
安装完成后的配置:
1. 修改绑定 IP 为0.0.0.0, 修改`/etc/mysql/mysql.conf.d/mysqld.cnf`,`bind-address = 0.0.0.0`,重启 `sudo service mysql restart`
2. 允许 docker 访问本机数据库,本机先执行`ifconfig`看下 `docker0` 的 ip,一般是`172.17.0.1`,那么 docker ip 为`172.17.0.*`,这里自己看情况修改.然后在数据库里给这个网段 IP 操作权限,
```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'172.17.0.%' IDENTIFIED BY 'pass' WITH GRANT OPTION;
flush privileges;
```
# 安装 Huginn
- 首次安装:
```shell
docker run  --name huginn \
    -p 3000:3000 \
    -e MYSQL_PORT_3306_TCP_ADDR=172.17.0.1 \
    -e HUGINN_DATABASE_NAME=huginn \
    -e HUGINN_DATABASE_USERNAME=root \
    -e HUGINN_DATABASE_PASSWORD=pass \
    huginn/huginn
```
如果安装中数据库报错,看下原因修改.
安装完成后,打开本机3000端口就可以进入首页.
之后再打开,直接 `docker start huginn`就行了.
- 设置开机自启动:
  `docker update --restart=always huginn`

- 自定义 .env.example 文件
  在 docker run 下面加入
```shell
docker run  --name huginn \
    --env-file /本地路径/.env.example \
```
