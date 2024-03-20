[[安装 docker]]

```bash
docker run \
--restart=always \
-it \
--name ns \
-p 80:80 \
-p 443:443 \
-v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v ~/nginx/log:/var/log/nginx \
-v ~/nginx/html:/usr/share/nginx/html \
-v ~/nginx/cert:/opt/cert \
-d nginx:latest
```