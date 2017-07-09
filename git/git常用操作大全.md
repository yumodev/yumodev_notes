
## 基本配置

### 基础配置

* 查看配置:`git config --list`
* 设置全局用户名:`git config --global user.name <用户名>`
* 设置全局邮箱:`git config --global user.email <邮箱>`
* 查看全局用户名:`git config user.name`
* 查看全局邮箱:`git config user.email`
* 设置当前仓库用户名:`git config  user.name <用户名>`
* 设置当前仓库邮箱:`git config user.email <邮箱>`
* 查看当前仓库用户名:`git config user.name`
* 查看当前仓库邮箱:`git config user.email`

###  远程库

* 查看远程主机，默认为`origin`: `git remote`
* 查看所有远程主机版本信息:`git remote -v `
* 查看制定主机名的远程主机详细信息: `git remote show <仓库>  ` 
* 添加远程仓库: `git remote add <仓库名>  <主机>` 
* 删除远程仓库: `git remote rm <仓库名>`
* 重命名远程仓库: `git remote rename <仓库名> <新仓库名字>`
* 修改远程仓库地址: `git remote set-url <仓库名> <仓库主机> `

### 获取帮助
     
* 查看帮助: `git help config`

## 基本操作
     
### 创建Git仓库

* 将目录初始化为一个Git仓库: `git init` 
* 拉取远程仓库到本地: `git clone git clone https://github.com/disheng54/disheng54.github.io.git`

### 提交修改
      
* 提交本地所有修改到暂存: `git add . `
* 提交本地文件到暂存: `git add <文件1> <文件2> ` 
* 提交本地某类文件到暂存: `git add *.java `
* 提交暂存区文件到服务器上: `git  commit -m "提交说明"`


### 追加修改

* 追加提交: `git commit --amend -m "提交说明"`

### 撤销修改

* 撤销一个文件修改: `git checkout <文件名>` 
* 撤销所有文件的修改: `git checkout .`
* 重置一个文件的修改:` git reset HEAD <文件名>`
* 重置到当前版本,重置缓存区和工作区: `git reset --head`
* 重置到某个提交,重置缓存区，工作区不变: `git reset <commit_id>`
* 恢复到某个提交,重置缓存区和工作区: `git reset --head <commit_id>`
* 恢复到某个提交,工作区和暂存区不变: `git reset --keep <commit_id>`

### 查看本地状态
     
* 查看本地状态: `git status`
 
### 查看提交日志

* 显示所有提交: `git log`
* 显示所有提交,仅显示hash和message:`git log --oneline`
* 显示某个用户的提交: `git log --author="username"`
* 显示提交日志和每次提交的文件: `git log --stat`
* 显示显示文件的每次提交:` git log -p <file>`
* 显示文件的修改人和时间:`git blame <file>`
* 显示最近的两次更新: `git log -2 `
* 显示最近几次的提交: `git reflog`
* 搜索提交: `git log -s <关键字>`
* 显示所有提交过用户和次数: `git shortlog -sn`

### 比较

* 暂存区和工作区的差异: `git diff`
* 暂存区和工作区某个文件的差异: `git diff filename`
* 暂存区和上一个commit的差异: `git diff --cached`
* 暂存区和上一个commit的某个文件的差异: `git diff --cached`
* 工作区和当前分支最近的commit的差异: `git diff HEAD`
* 查看某两个提交之间的差异: `git diff <commit1> <commit2>`
* 查看某两个分支之间的差异: `git diff <branch> <branch>`
* 查看当前分支和某个分支的差异: `git diff <branch>`

### 删除文件

* 删除文件:`git rm <文件>`
* 删除暂存或已提交的文件:`git rm --cached <文件>`
* 删除暂存或者已提交的目录: `git rm -r --cached  <文件>`


### 文件暂存和恢复

这个在切换分支的时候经常需要的一个操作。

* 暂存文件: `git stash`
* 恢复暂存最近的一个暂存: `git stash apply stash@{0}`
* 恢复最近的一个暂存: `git stash pop` 不推荐使用，还是建议使用apply方式恢复暂存
* 恢复暂存最近的第n个暂存: `git stash apply stash@{n-1}`
* 删除最新的一个暂存: `git drop`

## 分支操作

### 基本分支管理

* 查看当前分支: `git branch`
* 查看所有分支: `git branch -a `
* 查看所有的远程分支: `git branch -r`

### 创建分支

* 创建分支: `git branch <分支名字>`

### 切换分支

* 切换到另外一个分支: `git checkout <分支名字>`

### 删除分支

* 删除一个本地分支: `git branch -d <分支名字>`
* 强制删除一个本地分支: `git branch -D <分支名字>`
* 修改服务器地址:`git branch origin :<分支名字>`
          
###  fetch操作

* 获取远程主机上所有的提交: `git fetch <仓库名>`
* 获取远程主机上一个分支的提交: `git fetch <远程主机名> <分支名> `
* 从远程分支更新到本地并在此基础上生成一个新的分支: `git checkout -b <新分支名字> <远程主机名字>/<远程分支名字>`                 
* 在当前的本地分支上合并远程分支: `git merge <远程主机名>/<分支名字> `
* 在当前的本地分支上合并远程分支: `git rebase <远程主机名>/<分支名字> `

###  pull操作

取回远程主机一个分支的更新，然后与本地的分支合并, 如果是与本地的当前分支合并可忽略:
`git pull <远程主机名><远程分支名字>:<本地分支>`
         

###  push操作

将本地分支的内容提交到远程分支上, 如果要提交到的远程分支和本地分支有跟踪关系就可省略:
`git push <远程主机名> <本地分支名字>:<远程分支名字>`
         
     
## 标签

* 给当前标签创建标签: `git tag <tagName>`
* 给指定提交创建标签: `git tag -a <tagName> -m <tag message> <commit-id>`
* 查看标签: `git tag`
* 显示标签: `git show <tagname>`
* 推送标签到服务器: `git push origin <tagname>`
* 推送所有标签到服务器: `git push origin --tags`
* 删除本地标签: `git tag -d <tagname>`
* 删除服务器上的标签: `git push origin :refs/tags/[tagName]`
* 新建分支指向一个提交: `git checkout -b <branchname> <tagname>`


         
     

