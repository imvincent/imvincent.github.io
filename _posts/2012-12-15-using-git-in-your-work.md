---
date: "2012-12-15 14:14:22+0800"
layout: post
title: "使用git作为项目管理的工具"
categories: 技术
tags: git 代码管理
---

首先，git是用来干什么的？如果不清楚这点就不用往下看了，如果您是git使用方面的高手也不用看了，因为此文的作者还处于git使用的初级阶段。
作为一个码农的我，从第一次使用git就被它强大的魅力吸引住了，由于自己太过懒惰的缘故，很多时候会要用的时候会突然想不起来git的相关命令和参数，遂决定开此篇文章来记录自己用过并且以后会用到的git操作，也希望此文能帮到正在看这篇文章的您，本文会不定期更新！

1. git设置： 

		git config --global user.name "Vincent"：设置用户名
		git config --global user.email zhao.qinlong@gmail.com：设置邮箱
	
2. 建立仓库（repo）： 

		git init <dirname>： 在本地建立git仓库
		git --bare init <dirname>：在远程建立git仓库

	两者区别在于：执行git init将在当前目录下建立一个.git目录存放git库，当前目录则作为工作目录存放工程文件；而--bare参数则是将当前目录用做git库目录，而 不再建立一个.git子目录。一般在本地初始化仓库使用git init，而初始化远程仓库则加上--bare参数。因为远程仓库一般只是用来存放git库而已，没人会在远程仓库中进行check out或者编辑文件等操作。
	
3. 几个概念： 

		working tree：当下的工作目录，就像svn checkout出来的工作目录；
		staging area：git独有功能，是一块暂存的cached区域，用来记录什么是你待会要commit的档案；
		
4. 将档案添加到git的staging area： 

		git add  .：加入当前目录下的所有档案，包括所有还没有被追踪（untracked）的档案；
		git add -i：进入互动模式来添加，你甚至可以只添加档案里面其中的一段程序代码到staging area中去（称作patch）；
		git add -u：只加更新的档案，不加入还没有追踪的档案（跟git commit -a 涵盖的范围相同）
注意：add完之后进行的commit是对于add操作之前的档案进行的commit，假如add之后又进行了修改，则此修改不会出现在commit当中

5. git status： 

		changed but not updated：有修改但是没有加入staging area的档案
		changed to be commited（staging area）：已经加入staging area的档案 
		Untracked：尚未被追踪的新档案
		
6. git commit：将staging area的东西提交到本地档案库： 

		git commit -m "blah"：如果沒加 -m 的話, 会打开编辑器输入 commit log (可以在.gitconfig中設定编辑器) 
		git commit -a -m "foobar"：全部修改的档案都 add 後 commit 出去 (不包括 untracked 的新档案) 
		git commit -v：会打开编辑器加上 diff 注解
		
7. 比较差异： 

		git diff：比较 working tree 和 staging area
		git diff --cached：比较staging area和本来的 repo
		git diff HEAD：比较 working tree 跟本来的 repo
		
8. 分支操作： 

		git checkout -b develop master：从master分支创建一个develop分支用于开发进程；
		git checkout master：由其他分支切换到master分支；
		git merge --no-ff develop：将develop分支合并到当前分支；
		git branch -d release-1.2：删除release-1.2分支；
		git branch 建立本地 local branch 
		git branch -m 改名字 (如果有同名會失敗，改用 -M 可以強制覆蓋) 
		git branch 列出目前有那些 branch 以及目前在那個 branch 
		git checkout 切換 branch (注意到如果你有檔案修改了卻還沒 commit，會不能切換 branch，解法稍後會談) 
		git checkout -b () 本地建立 branch 並立即 checkout 切換過去 
		git branch -d 刪除 local branch 
	备注：微解释一下，上一条命令的--no-ff参数是什么意思。默认情况下，Git执行"快进式合并"（fast-farward merge），会直接将Master分支指向Develop分支。使用--no-ff参数后，会执行正常合并，在Master分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。关于合并的更多解释，请参考Benjamin Sandofsky的《Understanding the Git Workflow》。另外，关于git分支管理的建议，这里有几篇文章值得一读：Git分支管理策略，一个成功的Git分支模型
