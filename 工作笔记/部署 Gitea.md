在数据库实例上，使用 root 用户登录到数据库控制台：

```
mysql -u root -p
```

按提示输入密码。

创建一个将被 Gitea 使用的数据库用户，并使用密码进行身份验证。以下示例中使用了 'gitea' 作为密码。请为您的实例使用一个安全密码。

对于本地数据库：

```
SET old_passwords=0;
CREATE USER 'gitea' IDENTIFIED BY 'gitea';
```

对于远程数据库：

```
SET old_passwords=0;
CREATE USER 'gitea'@'192.0.2.10' IDENTIFIED BY 'gitea';
```

其中 192.0.2.10 是您的 Gitea 实例的 IP 地址。

根据需要替换上述用户名和密码。

使用 UTF-8 字符集和排序规则创建数据库。确保使用 **utf8mb4** 字符集，而不是 utf8，因为前者支持 Basic Multilingual Plane 之外的所有 Unicode 字符（包括表情符号）。排序规则根据您预期的内容选择。如果不确定，可以使用 unicode_ci 或 general_ci。

```
CREATE DATABASE giteadb CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_unicode_ci';
```


根据需要替换数据库名称。

将数据库上的所有权限授予上述创建的数据库用户。

对于本地数据库：

```
GRANT ALL PRIVILEGES ON giteadb.* TO 'gitea';
FLUSH PRIVILEGES;
```

对于远程数据库：

```
GRANT ALL PRIVILEGES ON giteadb.* TO 'gitea'@'192.0.2.10';
FLUSH PRIVILEGES;
```

## 下载 Gitea
```bash
# -O 指定保存文件名称 链接表示下载路径
wget -O gitea https://dl.gitea.com/gitea/1.21.0/gitea-1.21.0-linux-amd64

# 赋予执行权限
chmod +x gitea
```

## 服务器环境
### [[git#安装/更新 Git]]
要求 Git 版本不低于 2.0。

### 创建用户
```bash
# CentOS 为例

# 创建用户组
groupadd --system git

# 添加用户
adduser \  
--system \  
--shell /bin/bash \  
--comment 'Git Version Control' \  
--gid git \  
--home-dir /home/git \  
--create-home \  
git


# On Ubuntu/Debian:  
adduser \  
--system \  
--shell /bin/bash \  
--gecos 'Git Version Control' \  
--group \  
--disabled-password \  
--home /home/git \  
git

```

### 创建工作路径

```
mkdir -p /var/lib/gitea/{custom,data,log}
chown -R git:git /var/lib/gitea/
chmod -R 750 /var/lib/gitea/
mkdir /etc/gitea
chown root:git /etc/gitea
chmod 770 /etc/gitea
```

**注意：** 为了让 Web 安装程序可以写入配置文件，我们临时为 `/etc/gitea` 路径授予了组外用户 `git` 写入权限。建议在安装结束后将配置文件的权限设置为只读。

```
chmod 750 /etc/gitea
chmod 640 /etc/gitea/app.ini
```

### 配置工作路径
```bash
export GITEA_WORK_DIR=/var/lib/gitea/
```

### 复制二进制文件到 bin 目录
```bash
cp gitea /usr/local/bin/gitea
```

## 启动 Gitea
打开 `sudo vim /etc/systemd/system/gitea.service`
[拷贝代码](https://github.com/go-gitea/gitea/blob/release/v1.21/contrib/systemd/gitea.service)

保存后执行
```bash
systemctl enable gitea
systemctl start gitea
```