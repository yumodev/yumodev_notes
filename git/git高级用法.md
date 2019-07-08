# 从远程仓库下拉所有分支

`git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done`

# 将本地分支一次性上传到服务器


# SubModule

