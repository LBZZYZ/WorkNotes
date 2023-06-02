---
title: hexo-错误整理
date: 2021-05-25 23:17:43
tags: hexo
categories: hexo
description: 总结一些hexo使用过程中遇到的错误～
---
# 找不到hexo指令
当执行hexo命令时遇到如下error
```bash
lbz@DESKTOP-DE7NKM0 MINGW64 /d/wd/Temp/hexo (hexo-backup)
$ hexo s
ERROR Cannot find module 'hexo' from 'D:\wd\Temp\hexo'
ERROR Local hexo loading failed in D:\wd\Temp\hexo
ERROR Try running: 'rm -rf node_modules && npm install --force'
```
## 产生原因
这个错误的原因是缺少node_modules文件夹，因此无法生成文件。

# hexo init失败
```bash
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
fatal: unable to access 'https://github.com/hexojs/hexo-starter.git/': OpenSSL SSL_read: Connection was reset, errno 10054
WARN  git clone failed. Copying data instead
FATAL {
  err: [Error: ENOENT: no such file or directory, scandir 'C:\Users\lbz\AppData\Roaming\npm\node_modules\hexo-cli\assets'] {
    errno: -4058,
    code: 'ENOENT',
    syscall: 'scandir',
    path: 'C:\\Users\\lbz\\AppData\\Roaming\\npm\\node_modules\\hexo-cli\\assets'
  }
} Something's wrong. Maybe you can find the solution here: %s http://hexo.io/docs/troubleshooting.html
```

## 解决方法
由上面错误可以看出是git clone出现问题，所以给git设置代理解决。由于我这边使用的是v2ray代理，所以http/https代理可设置如下：
```bash
git config --global https.proxy 'vmess://127.0.0.1:7890' #vmess为协议类型 #7890为代理端口号
git config --global http.proxy 'vmess://127.0.0.1:7890'
```
## 参考链接
https://blog.csdn.net/littlehaes/article/details/103534260
