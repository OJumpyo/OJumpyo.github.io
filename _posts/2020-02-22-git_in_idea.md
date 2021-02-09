---
layout: post
title: "git笔记-from阮一峰"
date:  2020-02-22
tags: [git]
comments: true
author: OJumpyo
---
## 使用idea多人协作开发 
1. 首先从git上拉取matser分支代码到本地。 
2. 然后在本地新建分支在idea右下角有个==git XXX== 点击此然后可以看到有个+号new branch  
3. 然后本地进行修改代码，测试。通过后准备上传远程仓库  
4. idea上访菜单中file edit view code 这一行有个==vcs==，点击然后下拉。在下拉菜单中找到git，先add 再commit  
提交的时候确认是在远程新建别的分支，并确认提交修改的文件，然后push上去  
5. 在github上提交pull request，等待组长或leader进行merge操作。完成后，将自己的远程分支删除。下次提交再依此重复。