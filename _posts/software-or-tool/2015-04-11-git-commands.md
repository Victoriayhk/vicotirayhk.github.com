---
layout: post
title: Git - 常用命令
category : note
tagline: 
tags : [git]
---

## config
```
git config [--global] user.name "Huikang Yih"
git config [--global] --list
git config [--global] alias.ci "commit"

user.name/email
color.ui(auto/always/false(默认))
core.editor
alias.[别名]
```


## gitk
```
gitk [--all]
显示历史记录, 加`--all`表示显示所有分支
```


## 常用
```
git init
git status
git diff [HEAD]
// 显示工作目录树中与暂存区中的差别, 加HEAD(当前分支末梢), 显示工作目录树与版本库最新版的差别
git add [file_name]/[.] 	// .表示全部文件
git commit [-a] [-m "comment messege"]
// -m可以多次使用, -a表示提交(工作目录树中)所有修改, 不带-m时, git将启动编辑器
// 提交留言git log [-1]/[-2]
```


## 分支/标签/归档/克隆
```
git branch RB_1.0 master 	// 从master分支创建一个RB_1.0分支
git tag 1.0 RB_1.0 			// 给分支(末梢)打标签, 发布1.0版
git tag						// 查看标签
git checkout master			// 切换分支到master
git rebase RB_1.0 			// 将RB_1.0上的修改合并到主分支
git branch -d RB_1.0 		// 删除分支RB_1.0, 如果分支末梢打过标签, 不会真的
// 删除内容, 删除的只是名字
git branch RB_1.0.1 1.0 	// 从标签1.0处创建分支

git archive --farmat=tar \
			--prefix=mysite-1.0/ 1.0 \
			> mysite-1.0.zip
// 归档处理, 为mysite创建一个zip文件, 其中--prefix指明所有东西放到mysite-1.0目
// 录下, 1.0指明归档标签名称

git clone git://github.com/XXXXX [本地目录]
```


## more about add
```
git add -i 		// 交互方式
git add -p 		// 补丁模式
```


## 文件重命名和移动
```
git mv index.html hello.html
```


## more about branch
```
git branch -m master mymaster		// 分支master重命名为mymaster
git branch -M master mymaster		// 强制重命名分支
git branch [-r]						// 显示所有分支
git branch new						// 创造一个分支new ? 分支起点是?
git checkout						// 检出分支
git checkout -b alternate master 	// 从master创造分支alternate并检出
git branch -d alternate 	// 删除分支, 只有该分支被合并过, 才会成功
git branch -D alternate 	// 强制删除分支

git marge alternate			// 合并分支alternate到当前分支
git marge --squash contact	// 合并分支contact到当前分支, 多个提交并一个
git cherry-pick XXXX		// 合并提交SHA-1码为XXXX的提交到当前分支
git cherry-pick -n XXXX
// 将提交SHA-1码为XXXX的提交放到暂存区, commit这些变化时, 不留言时, 
// 系统提取这些XXXX中留言
```


## 查看/浏览/追踪
```
git log XXXX 				// 查看指定提交
git log --since="5 hours"	// 最近5个小时内的提交
git log --before="5 hours" -1 	// 5小时之前的最后一个提交
git log XXXX..YYYY			// XXXX提交之后到YYYY提交
git log --pretty="format: %h %s" 1.0..HEAD
// 1.0版本(tag)到当前分支末梢的历史, 更多format查看git log手册

XXXX^ 	// XXXX之前的一个版本, windows下写作"XXXX^"
XXXX^^	// XXXX之前版本的之前版本
XXXX~1  // XXXX之前的一个版本
XXXX~N  // XXXX往前追溯的第N个版本

git diff XXXX YYYY // 查看版本XXXX和YYYY的差别
git diff --stat 1.0
// 获取发布版本之间的代码统计量(增/删/改)
// 只有一个参数时, 默认第二个参数为HEAD

git blame [-L <开始>,<结束>] <文件名> 	// 查看对应文件的历史提交记录
git blame [-M]/[-C -C [-l -p]]			// 查重*****
```


## 恢复/增补/排序
```
git reset [--hard] XXXX
// 恢复版本到XXXX, XXXX后到HEAD的版本则扔到工作目录中
// 使用参数--hard将彻底删除XXXX后的版本

git commit [-C XXXX] -a --amend
// 对最后一个提交进行增补, 参数[-C XXXX]该增补提交表明套用XXXX的留言

git revert -n XXXX [--no-edit]		// 反转版本XXXX, 建议保留反转留言
// 反转多个版本时, 按时间从后往前逐一反转, 最后集中commit反转版本

git rebase -i HEAD~3 // 变基操作
// 打开文本编辑器后, 出现3行提交记录(pick开头):
//	排序: 移动/交互/排序记录可以实现排序
// 	合并: 假设合并的是XXXX和YYYY, 可以将YYYY记录开头的pick改为squash
//  跟到XXXX的记录后面, 变基操作启动后, 将弹出编辑器编辑合并留言
//  分解: ~~~
```

## 标签
```
git tag 		// 查看标签
git tag 1.1 	// 给当前分支末梢打标签
git tag tag_name branch_name 	// 为指定分支末梢打标签

git checkout 1.1 			// 检出标签1.1
git checkout -b from-1.1 	// 为当前工作目录创建分支from-1.1
```

## 与远程版本库交互
三种与远程版本库通信的协议: SSH, git, HTTP/HTTPS

```
git fetch 				// 不断获得远程版本库的改动
git pull <库名>		  	// 从远处库取, 合并
git push [--dry-run]  	// 本地修改推上远程库, 带--dry-run查看推上的提交
git branch 	-r 			// 可以检查远程分支, 但不能修改该分支

git remote add <别名> <远程库地址>	// 给远程命别名
git remote show <别名>				// 查看别名对应的远程版本库
git remote rm 	<别名>				// 删除别名
``` 

## 进阶操作
```
git  gc [--aggressive] 	// 压缩版本库
git archive 			// 导出版本库
git bisect [start/bad/good/reset/log]	// 二分查找哪些是坏提交或好提交
```

## 其它/拓展
+ marge时冲突, 冲突的形成? 冲突的解决(工具)
+ --pretty=format"...."
+ 自建版本控制服务器, Gitosis


## 总结--神操作
+ 标签 git tag
+ 查重 git blame
+ 统计 git diff
+ 变基 git rebase