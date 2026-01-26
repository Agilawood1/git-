# git使用说明文档
## 分支 a 中同步主分支 main 更新.
__1. 拉取 main 分支最新代码__
```
git checkout main
git pull origin main // origin 是本地仓库给远程仓库起的默认昵称，可用 git remote -v 查看所有远程仓库的别名和对应的地址
```
__2. 切换到分支a，同步 main 分支的更新__
```
git checkout a
git rebase main
```
__3. 处理可能的冲突__
打开冲突文件，手动调整代码，标记文件为已解决冲突，继续rebase
```
git add 冲突文件
git rebase --continue
```
## push 当前分支到 remote 的分支上
