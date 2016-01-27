---

layout:    post
title:     git备忘录
category:  git
tag:       git

---

现在用的最多的是add、rm、push、pull,其它的也得记牢哦！

 git log --pretty=oneline 查看简略log

 git reset --hard HEAD^ 回到上一版本

 git checkout -b dev 创分支

 git checkout master 回到master

 git merge dev合并分支

 git branch -d dev删除dev分支

 git rm workspace -r -f删除文件夹 


1.修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。