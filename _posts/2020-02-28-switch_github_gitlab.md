---
layout: post
title: "gitlab和github切换"
date:  2020-02-28
tags: [git]
comments: true
author: OJumpyo
---

两个账户，lab用于老板项目的，hub用于自己的学习用的。但是电脑只有一台，老是要切换，我好烦！  

暂时参考的方法：<https://blog.csdn.net/alex_xfboy/article/details/88662432>
### 1. 配置git用户名，邮箱
~~~
# 全局配置 自己的github账号，反正这是我的电脑，公司的电脑就公司的电脑优先呗  
git config --global user.name 'Ojumpyo'
git config --global user.email 'xxx'

# 项目配置，每次新建项目时，在项目根路径下配置公司的
git config --local user.name 'zhangyue1'
git config --local user.email 'xxx'
~~~

### 2. 生成ssh key 并上传到github/gitlab  

ssh key 默认生成后保存在 ~/.ssh/目录下 ，默认为 id_rsa 和 id_rsa.pub 两个文件，由于需要分开配置，所以这么做:（==如果没有生成过id_rsa和id_rsa.pub文件，建议使用2对应的语句，分别生成两个不同标识的公私钥文件。==）
~~~
# 1.生成默认，Github使用
ssh-keygen -t rsa -C "xxx@***.com"
~~~
~~~
# 2.生成公钥、密钥的同时指定文件名，Github使用
ssh-keygen -t rsa -f ~/.ssh/id_rsa.mashibing -C "xxx@***.com"
一路回车即可
~~~
之后记得将对应的公钥添加到github和gitlab中  

### 3. 配置config文件
在 ~/.ssh目录下，如果不存在，则新建config文件: 文件内容添加如下：
~~~
Host github.com
	Preferredauthentications publickey
	IdentityFile ~/.ssh/id_rsa
Host git.mashibing.com
	Preferredauthentications publickey
	IdentityFile ~/.ssh/id_rsa.mashibing
	User zhangyue1
~~~

### 4. 密钥添加到ssh agent中
因为默认只读取id_rsa，为了让SSH识别新的私钥，需将其添加到SSH agent中：

~~~
#一般的情况下ssh-agent是启动的
ssh-agent -s
#启动
ssh-agent 
#查看已有的SSH keys
ls -al ~/.ssh
#添加到ssh-agent中
ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/id_rsa.mashibing

~~~
如果出现==Could not open a connection to your authentication agent==的错误，就试着用以下命令：
~~~
ssh-agent bash
ssh-add ~/.ssh/id_rsa.github
~~~
这时会出现:  
Identity added: /Users/macmi001/.ssh/id_rsa.github (/Users/macmi001/.ssh/id_rsa.github)  
表示添加成功

### 5.测试验证
~~~
ssh -T git@xxx.git.cn
# Welcome to GitLab, @fei.chen!
ssh -T git@github.com
# Hi alex2chen! You've successfully authenticated, but GitHub does not provide shell access.
~~~

==**若是出现备注上的消息就说明大功告成了！**==

