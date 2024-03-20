## 1. [[安装 docker]]

## 2. 搜索镜像
```bash
docker search calibre-web
```

## 3.下载镜像
```bash
docker pull johngong/calibre-web
```

## 4.确认镜像下载成功
```bash
docker images
```

## 5. 启动容器
```bash
docker run -d --name=my-library -p 5241:8083 -v /root/library/calibre/config:/config -v /root/library/calibre/library:/library johngong/calibre-web
```

## 6. 确认镜像启动
```bash
docker ps -a
docker rm <container id> #删除容器
```

