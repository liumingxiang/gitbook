## Git总结

`git init `   命令是仓库初始化

`git add <filename>`   把文件添加到仓库

`git commit -m "描述" `   把文件提交到仓库

初始化一个 Git 仓库，使用 git init 命令。
添加文件到 Git 仓库，分两步：
第一步，使用命令 git add <file> ，注意，可反复多次使用，添加多个文件；
第二步，使用命令 git commit ，完成。

第一步是用 git add 把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用 git commit 提交更改，实际上就是把暂存区的所有内容提交到当前分支。
因为我们创建 Git 版本库时，Git 自动为我们创建了唯一一个 master 分支，所以，现在， git commit 就是往 master 分支上提交更改。
你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。



`git status`  命令可以让我们时刻掌握仓库当前的状态

`git diff <filename> `  查看文件被更改了什么

要随时掌握工作区的状态，使用 git status 命令。
如果 git status 告诉你有文件被修改过，用 git diff 可以查看修改内容。



`git log` 查看我们的更改历史` --pretty=oneline`整理输出信息

`git reset --hard commit_id    `     回滚版本

HEAD 指向的版本就是当前版本，因此，Git 允许我们在版本的历史之间穿梭，使用命令 git reset --hard commit_id 。
穿梭前，用 git log 可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用 git reflog 查看命令历史，以便确定要回到未来的哪个版本。