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
## push 当前分支 a 到云端的 Fork 的分支 b 上
__1. 确保本地当前分支是想推送的分支 a__
```
git checkout a
```
__2. 推送到云端的 Fork 的分支 b 上__
```
git push Fork a:b
```
Fork 是我要推送到的远程仓库别名

a:b 表示“把本地的 a 分支推送到远程 Fork 的 b 分支”

## merge 与 rebase
- `merge`
将两个分支合并，包括各自的历史


- `rebase`
  
示例：
```
git checkout a
git rebase origin/main
```
改变分支基点。将当前分支 a 的所有提交摘下来，移动到目标分支 origin/main 的最新提交之后，相当于把我的开发过程直接接在别人工作之后
- 注

当开发新分支 a 时，主分支 main 上有新改动，当前分支需要用到时，用rebase；当主分支合并其他开发分支功能时，用merge。公共分支上一定不要用rebase，因为这会改写历史，导致其他队友的代码与远程仓库完全脱节，造成严重的同步混乱。
