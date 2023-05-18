## 手册
[link](https://www.liaoxuefeng.com/wiki/896043488029600)
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