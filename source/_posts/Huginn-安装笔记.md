---
title: Huginn 安装笔记
tags: [Huginn]
date: 2018-03-22 00:37:32
categories: Huginn
---

## 安装

我用的 ubuntu + mysql, 按照教程安装,中途出现了很多问题,遇到问题搜索,好不容易才解决.
机器配置是 1核+ 2G,日常CPU占用<5%,内存50%,内存用的比较多, cpu 用的比较少,可能是我的 agent 不多吧.

## 使用

### 通知

通知尝试过 email slack pushbullet, 最终选择了 slack, 主要原因是: 便于管理,同类型的 event 放在同一个 channel 里,而且`网络通畅`

### 编写

可以先在[这里](http://huginnio.herokuapp.com/scenarios)看下,有一些共享出来的scenarios,就算需求不同,也可以学习下 agent 编写的语法,很有帮助.

## 遇到的问题和解决

- bundle install error “invalid byte sequence in US-ASCII (ArgumentError)”

  可以先执行这个设置语言

  ```
  export LC_ALL=C.UTF-8
  export LANG=en_US.UTF-8
  export LANGUAGE=en_US.UTF-8
  ```

- An error occurred while installing nokogiri (1.6.7.1), and Bundler cannot continue

  sudo apt-get install build-essential patch ruby-dev zlib1g-dev liblzma-dev

- 需要 swap 分区

  搜索"阿里云开启 swap",https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04

- diagram加载不出来,挂掉

  原因是访问不了 google 的 api,办法是下载离线的graphviz安装,`.env`修改`USE_GRAPHVIZ_DOT=dot`,重新执行`sudo bundle exec rake production:export`

  参考:<https://github.com/huginn/huginn/issues/2109>

- css 资源挂掉,界面很丑

  先编译资源,``sudo -u huginn -H bundle exec rake assets:precompile RAILS_ENV=production``,直接重启,`sudo bundle exec rake production:restart`

- Mysql 设置 Innodb 出现 Unknown system variable ‘storage_engine

  在/etc/mysql/my.cnf 中设置 [mysqld] default-storage-engine = InnoDB


- 时区设置

  Schedule 的时间不是北京时间,`.env`修改 TIMEZONE="Beijing"

  ​