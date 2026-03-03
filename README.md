# git使用说明文档
## linux常见缩写

单杠`-`是缩写，`--`是全称

- `-p` (patch)：

含义：补丁/分块模式。

用途：在 git stash show -p 或 git add -p 中使用，让你逐块查看或挑选代码改动。

- `-m` (message)：

含义：提交信息。

用途：git commit -m "fix bug"。如果没有这个缩写，Git 会强制打开编辑器让你输入。

- `-a` (all)：

含义：全部。

用途：git commit -a 会自动把所有已跟踪文件的修改进行暂存并提交。

- `-u` (untracked)：

含义：未跟踪的文件。

用途：git stash -u。默认 stash 不处理新创建的文件，加上 -u 才能把新文件也存进保险箱。

- `-f` (force)：

含义：强制。

用途：git push -f。在 Rebase 之后可以强制推送，因为它改写了历史。

## 添加文件到暂存区+提交代码

使用命令`git add .`以及`git commit -m "提交概述"`，也即 gitkraken 中的 Stage All Changes +  commit changes

## 关于 checkout 的几点说明
- `checkout`使用前一定要确保当前工作区没有东西，即没有file changes，否则无法成功切换
- 可以用来回退版本，详见下面“版本回退”部分
- 该命令既可以切换分支，也可以恢复文件，所以引入分隔符`--`，后面的内容是文件路径，而不是分支名。比如，当你想撤销aa.c和bb.c两个文件的改动时，执行如下命令：
  `git checkout -- aa.c bb.c`

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

## merge 与 rebase 说明
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

## stash 详细用法
- `git stash`
  
暂存

应当给 stash 带备注，防止分不清。如：
```
git stash save "这是我本次的……stash"
```
- `git stash pop`
  
  找回并删除记录。
  
  恢复最近一次的 stash（栈）。注意！如果pop后产生了冲突，解决冲突并`git add.`以及`git commit -m "…"`代码后，要记得 __手动删掉__ stash 记录，否则git留的备份还在，下次pop时还会弹出这条旧的冲突代码。执行如下指令：

  `git stash drop stash@{某个id}`
  
- `git stash apply`
  
  找回但保留记录。有多种使用场景：
  
  - 多处测试

    比如a分支stash的代码，可以切到b、c等等分支上，进行stash apply去测试

  - 代码怕炸了，留个保险
 
    可以apply后看看冲突多不多、有没有bug，如果想撤销这次改动，在apply前如果代码都commit了，直接执行`git reset --hard`，就能干净的回到apply前的状态

- `git stash show -p`

  展示stash区与当前代码的详细差异，如终端中显示的这段信息：
  
  ```
  diff --git a/test.cpp b/test.cpp
  index ac644ed..b9fc138 100644
  --- a/test.cpp
  +++ b/test.cpp
  @@ -15,4 +15,5 @@

  //新增一些东西，advance要用
  // 我正在开发a功能，发现问题，想看看之前版本好不好用，准备回退
  -//advance分支上的更新
  \ No newline at end of file
  +//advance分支上的更新
  +// alskdjflkk，我正在main上开发，突然要到advance上处理一下，使用stash
  \ No newline at end of file
  ```

  ---的是修改前的文件状态，+++的是修改后的文件状态
  
  注：该指令后面可以加具体想看的哪步stash。

- 常见工作流程

  首先需要注意，一般在哪里stash，最后要回到这里再pop，否则乱套了
  
  正在分支a上工作，突然b分支有东西要处理，但我现在不想commit，则使用stash暂存现在的代码，之后有两种情况：
  - 处理完b代码并commit后，checkout到a分支，执行`git stash pop`，则回到a分支stash前的状态
  - 处理完b代码还不想commit，则把b的也stash，然后checkout到a分支，先执行`git stash list`，终端显示如下类似信息：
    ```
    stash@{0}: On b: WIP on b
    stash@{1}: On a: WIP on a
    ```
    然后执行精确stash命令：
`git stash pop 'stash@{1}'`
  

## 版本回退
推荐使用`checkout`。在想要回退的版本处 checkout 一个新分支重新开发
