mr脚本使用说明
由于在将代码合并到dev过程中，会频繁通过gitlab MR界面来合并代码，这样过程比较麻烦；而如果直接在develop分支改动提交，会导致上到qa环境时，无法通过feature来合并，因此可以通过使用mr命令在命令行提交mr到dev分支。最好只是在dev分支提交时通过mr命令，提交到qa和online分支还是手动提交。

mr解决的问题：主要解决每次都需要到gitlab网站去选择merge request的问题，只会创建MR，合并的操作必须人Review后合并

使用说明：

本机环境变量中添加~/.bash_profile中添加环境变量

export GITLAB_TOKEN=xxxxxx(在gitlab的 http://git.guazi-corp.com/profile/account 中找到Private token)
