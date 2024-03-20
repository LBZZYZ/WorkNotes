## 查看容器
```bash
docker container list
```

## 进入容器
```bash
docker exec -it my_container /bin/bash
# 这里的`-it`参数是为了让我们能交互式地使用容器的shell。
# -i, --interactive   Keep STDIN open even if not attached
# -t, --tty           Allocate a pseudo-TTY
```
## 查看容器日志
```bash
docker logs CONTAINERID
```