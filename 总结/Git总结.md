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

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令 git checkout -- file 。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令 git r
eset HEAD file ，就回到了场景 1，第二步按场景 1 操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退 (页 21)一节，不过前提是没有
推送到远程库

`git status`  命令可以让我们时刻掌握仓库当前的状态

`git diff <filename> `  查看文件被更改了什么

要随时掌握工作区的状态，使用 git status 命令。
如果 git status 告诉你有文件被修改过，用 git diff 可以查看修改内容。



`git log` 查看我们的更改历史` --pretty=oneline`整理输出信息

`git reset --hard commit_id    `     回滚版本

HEAD 指向的版本就是当前版本，因此，Git 允许我们在版本的历史之间穿梭，使用命令 `git reset --hard commit_id` 。
穿梭前，用 `git log `可以查看提交历史，以便确定要回退到哪个版本。
要重返未来，用 `git reflog` 查看命令历史，以便确定要回到未来的哪个版本。



Git 鼓励大量使用分支：
查看分支：`git branch`
创建分支：`git branch branchname`
切换分支：`git checkout branchname`

创建+切换分支：`git checkout -b branchname` 

合并某分支到当前分支：`git merge`
删除分支：`git branch -d branchname`

把本地分支推送到远程分支`git push origin local_branch:remote_branch`

这个操作，local_branch必须为你本地存在的分支，remote_branch为远程分支，如果remote_branch不存在则会自动创建分支。



分支的合并情况 `git log --graph --pretty=oneline --abbrev-commit`

用 `git log --graph` 命令可以看到分支合并图。



.gitignore 文件配合

我们把我们要忽略的某一个文件，直接把文件名添加进来就可以了 `filename`

忽略某一类，`*.fileclassname`  如我们如果把所有的html忽略掉，我们只需要写入`*.html`  加入即可

如果我们在某一类中我们有一个要进行添加，我们直接把文件名添加进去

例如我们把index.html 还是要进行上传 只需要取反就好`!index.html`

如果我们把 a后缀，b后缀都忽略 我们就可以直接添加`*[ab]`



突然有事情需要暂存代码

我以前的做法是：把当前改过的文件「复制」一份先放在其它地方，把代码还原到没修改的状态，签出对应 production 环境的代码。
定位修复问题，提交代码，确认问题修复后再合并回开发分支，并把之前备份的代码复制进来进行合并。
stash 正是解决这个问题的，只需要 git stash ，它就把当前未提交的改动「复制」到另一个地方暂存起来，待要恢复的时候执行 git stash pop 即可。





我们再提交代码的时候有时候在同一个版本时候忘记提交某个文件，我们不可能再去创建一次提交，这样子commit不一致了  所以我们会有 `git commit --amend`

这样子我们就可以补充上传文件





本来只想提交一个文件，结果意外地把另一个文件也 add 了，怎么把它搞出来？
git reset 可以用来改变 HEAD 的位置或把文件从 staging area 移除出来，但并不会丢失任何的修改（我在刚开始接触 git 的时候觉得这个命令很危险，总感觉会把变更丢掉）。`git reset (你不想提交的文件)`  就可以了

