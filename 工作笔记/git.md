---
title: Git服务器
date: 2021-05-23 00:20:13
tags: git
---

## 创建空仓库
```bash
git init --bare bingzhi.git
```

## 创建分支
```bash
git branch <分支名称>
```

## 切换分支(不存在则自动创建)
```bash
git branch -b <分支名称>
```

## 拉取指定分支
```bash
git clone -b <分支名称> 你的远端地址
```

## 推送某分支到远程分支
```bash
git push origin <local branch>:<remote branch> #remote branch不存在则自动创建
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
## 将dev对齐到master线
1. git checkout \<dev-branch\> // 首先将工作区切换到dev分支
2. git rebase origin/master // 此操作会首先把origin/master的修改copy到dev，然后把dev上特有的修改跟到后面，解完冲突后可以执行步骤3(参考https://blog.csdn.net/weixin_42310154/article/details/119004977)
3. git push -f origin HEAD:refs/heads/dev-branch //此方法是强行推送，需要有相关权限

## 统计代码行数
```bash
git log  --author="simon.li" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }'
```

## 删除本地分支
```bash
 git branch -d branchname
``` 

## 删除远端分支
```bash
git push origin --delete branchname
``` 

## git pull 时避免出现 "Merge branch 'master' of ..."]
```bash
git pull --rebase
```
[参考](https://blog.csdn.net/Toy_IHere/article/details/103064044?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)

## 修改commit信息
```base
git commit --amend
```

## 设置全局代理
```bash
git config --global http.proxy http://127.0.0.1:12333
git config --global https.proxy http://127.0.0.1:12333
```

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

## git log单行显示
```bash
git log --oneline
```