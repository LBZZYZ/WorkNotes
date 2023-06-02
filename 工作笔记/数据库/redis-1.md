---
title: redis-1 安装与配置
date: 2021-03-08 00:25:54
tags: 
    - redis
    - Database
---

## Ubuntu安装

```bash
sudo apt update
sudo apt install redis-server
```
实际会有三个instance被安装：   
1. libjemalloc1 
2. redis-server 
3. redis-tools

### 检查安装是否成功
```bash
$ mysql-cli
127.0.0.1:6379>
```

输入ping,有下方回显说明连接成功
```bash
127.0.0.1:6379> ping
PONG
```

# 设置每页显示的文章数量
修改站点配置文件中的属性
```bash
per_page: 10
```
