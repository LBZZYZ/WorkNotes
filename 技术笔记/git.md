---
title: Git
date: 2021-05-23 00:20:13
tags:
  - git
---

## 创建空仓库
```bash
git init --bare repo.git
```

## 创建分支
```bash
git branch <branch-name>
```

## 切换分支(不存在则自动创建)
```bash
git branch -b <branch-name>
```

## 修改分支名称
```bash
git branch -m <旧分支名> <新分支名>
git branch -m <新分支名> # 修改当前分支名称
```

## 拉取指定分支
```bash
git clone -b <branch-name> 你的远端地址
```

## 推送某分支到远程分支
```bash
git push origin <local branch>:<remote branch> #remote branch不存在则自动创建
```

## 删除本地分支
```bash
 git branch -d <branch-name>
``` 

## 删除远端分支
```bash
git push origin --delete <branch-name>
``` 

## 管理公钥
公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`

## git hooks for hexo
```bash
#hooks/post-update
git --work-tree="/var/www/html" --git-dir="/root/blog.git" checkout -f
```

## 手册
[link](https://www.liaoxuefeng.com/wiki/896043488029600)
## 学习网站
https://learngitbranching.js.org/
## 将 dev 对齐到 master
1. 将工作区切换到 dev 分支
```bash
git checkout dev
```
2.  把 origin/master 的修改 rebase 到 dev，然后把 dev 上特有的修改跟到后面，解完冲突后可以执行步骤3([参考](https://blog.csdn.net/weixin_42310154/article/details/119004977))
```bash
git rebase origin/master
```
3. `-f` 代表强行推送，需要有相关权限
```bash
git push -f origin HEAD:refs/heads/dev-branch
```

## 统计代码行数
```bash
git log  --author="simon.li" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

## git pull 时避免出现 "Merge branch 'master' of ..."]
```bash
git pull --rebase
```
[参考](https://blog.csdn.net/Toy_IHere/article/details/103064044?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)

## 修改 commit 信息
```bash
git commit --amend
```

## 设置全局代理
```bash
git config --global http.proxy http://127.0.0.1:12333
git config --global https.proxy http://127.0.0.1:12333
```

## 设置 SSH 的 https 代理
修改 `~/.ssh/config` 文件
```bash
Host github.com
Hostname ssh.github.com
Port 443
User git
```
[git_ssh_proxy.md](https://gist.github.com/chenshengzhi/07e5177b1d97587d5ca0acc0487ad677)
## 处理中文乱码
```bash
git config --global core.quotepath false #禁止路径转义
```

## There is no tracking information for the current branch.
```bash
git branch --set-upstream-to=origin/<branch> main
```

## 上传时屏蔽特定行的内容
[如何让 Git 忽略掉文件中的特定行内容？](https://www.cnblogs.com/haiku/p/6414067.html)

## 更新远程分支
```
git remote update origin --prune
```

## 查看/清空stash
```bash
git stash list
git stash clear
```

## 更新 origin 仓库列表
```bash
git remote update origin --prune
or
git remote update origin -p
```
## 增加.gitignore时，有一些不想追踪的文件已经被添加到缓存区
```
如果是文件夹：git rm -r --cached 文件夹名
如果是文件：git rm --cached 文件名
```

## patch
```bash
git format-patch HEAD^ #生成最近一次commit的patch
```

```bash
git am xxx.patch #直接将patch的所有信息打上去，无需重新git add和git commit，author也是patch的author而不是打patch的人。
git apply xxx.patch #与git am的区别是：git apply并不会将commit message等打上去，打完patch后需要重新git add和git commit。
```

### [解决 git am 时 patch does not apply](https://www.cnblogs.com/Galesaur-wcy/p/15751576.html)
```bash
# 1. 强制合并不冲突的部分，冲突部分会生成 .rej 文件
git apply --reject patch-name 
# 2. 手动合并 .rej 文件中的冲突，合并完成后删除 .rej文件
# 3. 添加合并完的文件到暂存区
git add .
# 4. 继续 apply patch
git am --continue

```

## git log单行显示
```bash
git log --oneline
```

## git add
```bash
git add -u . #添加到暂存区，不包括untracked files
git add . #添加到暂存区，包括untracked files
```

## tag 操作
```bash
# 列出所有 tag
git tag

# 列出指定版本 tag，* 位通配符
git tag -l "v1.8.5*"

# 创建 tag
git tag -a v1.4 -m "my version 1.4"

# 查看 tag
git show v1.4

# 创建轻量 tag
git tag v1.4-lw

# 推送 tag
git push origin v1.5

# 删除 tag
git tag -d v1.4-lw

# 切换至 tag 指定版本
git checkout 2.0.0
```

## 安装/更新 Git
以 CentOS 7 为例，CentOS 7 内置了 `Git 1.8.3`，首先将其卸载。
```bash
yum remove git
```

由于官方源没有最新版本的 Git，所以需要增加第三方源，这里采用 [ius](https://ius.io/)
```bash
yum install \
https://repo.ius.io/ius-release-el7.rpm \
https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

在仓库列表中看到名为 `git236` 的库，因此使用 yum 安装。
```bash
yum install git236
```

安装完成后确认版本
```
git version

console:/ # git version 2.36.6
```

Ubuntu 18.04 安装 git
```
apt install git
```

## [Git 多人协作开发](https://sliu.vip/git/team-dev/)

## [浅谈 Pull Request 与 Change Request 研发协作模式](https://blog.gitee.com/2020/12/15/pull-request-change-request/)

## git commit 的七条规则
1. Separate subject from body with a blank line（将主题与正文用空行分隔开来）
2. Limit the subject line to 50 characters（将主题行限制在50个字符以内）
3. Capitalize the subject line（将主题行的首字母大写）
4. Do not end the subject line with a period（不要在主题行末尾加句号）
5. Use the imperative mood in the subject line（在主题行中使用祈使语气）
6. Wrap the body at 72 characters（在每行正文中达到 72 个字符进行换行）
7. Use the body to explain _what_ and _why_ vs. _how_（在正文中解释“什么”和“为什么”，而不是“如何”）

## git branch 命名规范
- master（主分支，永远是可用的、稳定的、可直接发布的版本，不能直接在该分支上开发）
- develop（开发主分支，代码永远是最新，所有新功能以这个分支来创建自己的开发分支，该分支只做只合并操作，不能直接在该分支上开发）
- feature-xxx（功能开发分支，在develop上创建分支，以自己开发功能模块命名，功能测试正常后合并到develop分支）。如 feature/add-new-feature-20240119
- release（预发布分支，在合并好feature分支的develop分支上创建，主要是用来提测的分支，修改好bug并确定稳定之后合并到develop和master分支，然后发布master分支）
- release-fix（功能bug修复分支，在release上创建分支修复，修复好提测的bug之后合并回release分支。）
- hotfix-xxx（紧急bug修改分支，项目上线之后可以会遇到一些环境问题需要紧急修复，在对应版本的release分支上创建，流程跟release分支相似，修复完成后合并release分支，根据情况判断需不需要再合并到develop和master分支）
## 文章
[rebase](https://www.cnblogs.com/chenny7/p/7644318.html)
[Git合并到底使用Merge，还是Rebase？](https://www.dianyuan.com/eestar/article-5068.html)