# **题目与内容不符**
更新远程文件到本地方式一
查看远程仓库
git remote -v
从远程获取最新版本到本地
git fetch origin aaa
比较远程分支和本地分支
 git log -p aaa origin/aaa
合并远程分支到本地
git merge origin/aaa
远程文件到本地方式二，在本地建临时分支，合并后删除
查看远程仓库
git remote -v
从远程获取最新版本到本地
git fetch origin master:temp
比较远程分支和本地分支的区别
git diff temp
合并远程分支到本地
git merge temp
使用pull更新
 git pull origin aaa
————————————————
版权声明：本文为CSDN博主「qingsong7」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hanqingsong412/article/details/49806097

