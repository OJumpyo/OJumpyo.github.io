---
layout: post
title: "git笔记-from阮一峰"
date:  2020-02-12
tags: [git]
comments: true
author: OJumpyo
---

# 写在开头

主要还是看的廖雪峰学长的git教程，参考链接在  
<https://www.liaoxuefeng.com/wiki/896043488029600>  
我自己把主要的语句记下来，一个是方便自己目前学习记录，二个是为了以后查阅方便。  

## git分布式版本控制系统  
就是每台电脑都有个备份，再用一条机子做临时中央机器汇合版本变动。  
==git安装我就不写了，看他的去吧==  
==注意在windows上面不好用，因为微软的word编码会出现一些奇奇怪怪的错误。非要用它编辑文本的话，建议采用notepad++等文本编辑器==  
### 1、提交基本步骤  
`git config --global user.name "Your Name"`  
` git config --global user.email "email@example.com"`  
配置git用户名+邮箱，让别人知道是谁修改。  

`git init`  
记得在需要的项目文件夹父目录中控制，控制项目目录在git的根目录或子文件夹下。  

`git add yourFileName`  
`git commit -m "本次改动的说明"`  
提交git的最基本语句，先添加文件，再提交。  

`git status`  
查看git文件提交结果，状态  

`git diff yourFileName`  
查看提交的修改在哪里，有哪些  

### 2、版本回退  
`git log`  
查看提交记录效果如下：  

~~~yml  
commit 470de19af407052f989e8b7cf8dc186b17a8de21 (HEAD -> master)
Author: jumpyo <1074678434@qq.com>
Date:   Wed Feb 12 10:19:03 2020 +0800

    git tracks change of files.

commit 40cc151b65e5467028728885c47337981dd02304
Author: jumpyo <1074678434@qq.com>
Date:   Tue Feb 11 21:44:45 2020 +0800

    repeat some sentences to learn git log and git reset

commit fb333b27112a637d8e66e285e6f0359d3a055ba4
Author: jumpyo <1074678434@qq.com>
Date:   Tue Feb 11 21:36:00 2020 +0800

    add some sentences to learn more commands

commit d3c196882f4e02501f3bca6e72cfed785c4f6e38
Author: jumpyo <1074678434@qq.com>
Date:   Tue Feb 11 21:30:39 2020 +0800

    I have written a sentence as the first trial
~~~

`git log --pretty=oneline`  
使输出为一行  

~~~html  
$ git log --pretty=oneline
470de19af407052f989e8b7cf8dc186b17a8de21 (HEAD -> master) git tracks change of files.
40cc151b65e5467028728885c47337981dd02304 repeat some sentences to learn git log and git reset
fb333b27112a637d8e66e285e6f0359d3a055ba4 add some sentences to learn more commands
d3c196882f4e02501f3bca6e72cfed785c4f6e38 I have written a sentence as the first trial
dcd5572ce922cf6118e69074724ee9c786fbf804 I have written a lerarngit.txt file
~~~

==`git reset --hard head^`==  
一个^号代表回退到最近的版本，两个就两个，很多个可以写成  
==`git reset --hard head~100`==  
也可以使用==`git reset --hard fb333`回退到某个特定的版本，后面的参数来自前面这一长串commitid

要重返未来，用==`git reflog -n`==查看命令历史，以便确定要回到未来的哪个版本  

git存在==`工作区、暂存区`==。git add只是添加到暂存区，commit以后才添加到分支。  

git是管理==修改==的，不是管理文件的  

### 3、撤销不必要的修改
1. 修改后没有用git add添加到暂存区：  
使用`git checkout -- yourFileName`这样会回退到版本库里最新的版本，其实也可以手动把不必要的删了  

2. 已经用`git add`了：  
使用`git reset HEAD yourFileName`把它回退到工作区  
然后使用`git checkout -- yourFileName`把工作区的也撤销掉  

3. 提交到版本库了：  
参考前面的版本回退  
==一定注意，若是提交到了远程库如github上，那就惨了==  

### 4、删除文件
`git rm yourFileName`  
`git commit -m 'your comments'`  
删错了的话使用
`git checkout -- yourFileName`把误删文件恢复  

### 5、远程仓库
> 建立仓库请参考廖雪峰的。本处只记录他写的别的指令  

1. 关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`;  
 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；  
此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；
2. 克隆一个远程仓库  
`git clone git@github.com:michaelliao/gitskills.git`git clone后面的是仓库地址，记得更改  
Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。  
### 6、分支管理
Git鼓励大量使用分支：

查看分支：==`git branch`==

创建分支：==`git branch <name>`==

切换分支：==`git checkout <name>`==或者==`git switch <name>`==

创建+切换分支：==`git checkout -b <name>`==或者==`git switch -c <name>`==

合并某分支到当前分支：==`git merge <name>`==

删除分支：==`git branch -d <name>`==  

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用<font color='red'> git log --graph </font> 命令可以看到分支合并图。  

合并冲突分支应采用此种策略：  
==`git merge --no-ff -m "merge with no-ff" dev`==  
其中的 ==`--no-ff`== 能禁用fastforward模式，就是把他们都保存下来，并创建一个新的commit  

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场 ==`git stash`== 一下，==待补==  
==`常用git stash命令：`==

（1）==`git stash save "save message"`==  : 执行存储时，添加备注，方便查找，只有git stash 也是可以的，但查找时不方便识别。

（2）==`git stash list`==  ：查看stash了哪些存储

（3）==`git stash show`== ：显示做了哪些改动，默认show第一个存储,如果要显示其他存贮，后面加stash@{$num}，比如第二个 git stash show stash@{1}

（4）==`git stash show -p`== : 显示第一个存储的改动，如果想显示其他存存储，命令：git stash show  stash@{$num}  -p ，比如第二个：git stash show  stash@{1}  -p

（5）==`git stash apply`== :应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} 

（6）==`git stash pop`== ：命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}

（7）==`git stash drop stash@{$num}`== ：丢弃stash@{$num}存储，从列表中删除这个存储

（8）==`git stash clear`== ：删除所有缓存的stash  

==`接上`== 然后去修复bug，修复后，再 ==`git stash pop`==，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用 ==`git cherry-pick <commit>`== 命令，把bug提交的修改“复制”到当前分支，避免重复劳动。  

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

### 7、多人协作
1. 首先，可以试图用<font color='red'> git push origin <branch-name> </font>推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用<font color='red'> git pull </font>试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用<font color='red'> git push origin <branch-name> </font>推送就能成功！
5. 如果`git pull`提示<font color='red'> no tracking information </font>，则说明本地分支和远程分支的链接关系没有创建，用命令<font color='red'> git branch --set-upstream-to <branch-name> origin/<branch-name> </font>。 

==`注：其实一般最好是先建自己的分支，再对比无误之后合并到master`==  
step1，在本地新建分支 git branch newbranch

step2：把本地分支push到远程 git push origin newbranch

step3：切换到该分支 git checkout newbranch

step4：查看本地修改 git status

step5：添加本地修改 git add .

step6：commit修改 git commit -m 'XXXX'

step7：push代码 git push  ==(一般到这一步是在git上提交pr给组长审批就可以了，通过以后把新的分支删了，以后再重复前面的)==

step8：查看分支 git branch

step8 ：切换到主分支 git checkout master

step9：更新 git pull

step10：合并到主分支 git merge newbranch

step11：push 到主分支 git push
6. 强制拉取远程更新  
1、`git fetch --all`  
2、`git reset --hard origin/master`  
3、`git pull` //可以省略  

==`git rebase`== 操作可以把本地未push的分叉提交历史整理成直线；

rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。  
### 8、版本标签
命令`git tag <tagname>`用于新建一个标签，默认为`HEAD`，也可以指定一个`commit id`；

命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；

命令`git tag`可以查看所有标签  

命令`git push origin <tagname>`可以推送一个本地标签；

命令`git push origin --tags`可以推送全部未推送过的本地标签；

命令`git tag -d <tagname>`可以删除一个本地标签；

命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

### 9、gitignore
.gitignore文件示例
```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa
```
==`git add -f App.class`== 强制添加`App.class`到git中  
==`git check-ignore -v App.class`== 检查`App.class`为什么会被忽略。  
`.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理.  

用的最多的强制拉取远程库覆盖本地仓库  
==`git fetch --all`==  
==`git reset --hard origin/master`==  
==` git pull origin master`==  