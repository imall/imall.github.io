---
title: 【筆記】Git Alias 指令
tags: [Git]
comment: false
categories: Git 筆記
date: 2026-02-10 10:53:32
---

# 前言

有了 AI 之後，都會請他幫忙生一些自己生不出來的 git 縮寫  
例如快速產製  `.gitignore` 檔案之類


這篇文章純粹記錄一下設定完會忘記，每次都得回頭查的的指令 (肯定是用太少)


## Git 快速初始化專案指令

操作情境：從頭開始初始化一個專案，依照專案類型建立 `.gitignore`


### 初始化 .NET 、 Vue Git 儲存庫
快速初始化一個 Node.js + Vue 相應的 `.gitignore` 檔案，並且初始 commit：



```bash
git startnetvue
```

> 涵蓋 node,vue,visualstudiocode,rider

### 初始化 .NET Git 儲存庫

快速初始化一個 .net 相應的 `.gitignore` 檔案，並且初始 commit：


```bash
git startnet
```

## 快速產生 .gitignore 指令

操作情境：已經初始化過儲存庫，需要新增 `.gitignore` 檔案

### .NET 專案的 .gitignore


```bash
git ignet
```

### Node.js 專案的 .gitignore


```bash
git ignode
```


### .NET + Vue 混合專案的 .gitignore



```bash
git ignv
```

## 記錄 alias


```sh
[alias]
	# status	
	st = status
	
	#log
	l = log --oneline --graph # 顯示log
	ls = log --oneline --graph --pretty=format:'%C(yellow)%h%Creset - %C(cyan)<%an>%Creset %s %C(magenta)(%ar)%Creset' # 格式化log
	rl = reflog --pretty=format:'%C(yellow)%h%Creset %C(red)%gd%Creset %C(cyan)<%an>%Creset %C(auto)%s%C(reset) %C(magenta)(%cr)%C(reset)' # 格式化reflog
	
	# add
	al = add .
	rmadd = restore --staged # 取消add
	
	# commit
	cm = commit # commit
	cmm = commit -m # commit message
	cmam = commit -am # 一次整合add跟commit
	cmcm = commit --amend --no-edit
	cmrn = commit --amend -m # 重新更改最近一次的commit訊息
	cmempty = commit --allow-empty -m --no-edit # 建立一個空的commit

	# add + commit
	all = !git add . && git commit -m

	# branch
	br = branch # 顯示所有branch
	brv = branch -v # 顯示所有branch的最後一次commit
	brm = branch -m # 重新命名branch
	brd = branch -d # 刪除 branch
	brf = branch -f # 強制建立、更名分支

	
	# checkout
	co = checkout # 切換分支

	# reset
	rst = reset # 取消add
	rsth = reset --hard # reset hard 模式
	rsthp = reset --hard HEAD^ # 回到上一個commit --hard
	rstp = reset  HEAD^ # 回到上一個commit --mixed
	rsto = reset  --hard  ORIG_HEAD

	# remove
	rmcach = rm --cached # 清除在reop的檔案
	rmallcach = rm -r --cached . # 清除所有在reop的檔案
	clf = clean -fX # 強制清除被忽略的檔案

	#rebase
	rb = rebase # rebase
	rbi = rebase -i # 互動式rebase
	rbc = rebase --continue # 繼續rebase

	cls = clean -f # 強制清除尚未追蹤的檔案

	# 遠端相關
	fetchp = fetch --prune # 取得遠端所有資料並刪除已經不存在的branch
	bra = branch -a # 顯示所有branch
  psd = push origin -d # 刪除遠端branch
	psf = push -f # 強制push
	pso = push -u origin # 建立遠端branch

	getalias = config --get-regexp alias # 取得所有alias

	i = !git init && git add . && git commit -m  Initial-Commit

	startnetvue = !git init && curl -sL https://www.gitignore.io/api/node,vue,visualstudiocode,rider > .gitignore && git add .gitignore && git commit -m \"chore: Initial commit with .gitignore\" && echo \"✅ 專案已初始化完成！\"

	startnet = !git init && curl -sL https://www.gitignore.io/api/rider > .gitignore && git add .gitignore && git commit -m \"chore: Initial commit with .gitignore\" && echo \"✅ 專案已初始化完成！\"

	ignet = !curl -sL https://www.gitignore.io/api/visualstudio,dotnetcore,aspnetcore,rider > .gitignore && echo \"✅ .NET .gitignore 已產生！\"
	ignode = !curl -sL https://www.gitignore.io/api/node,visualstudiocode > .gitignore && echo \"✅ Node.js .gitignore 已產生！\"
	ignv = !curl -sL https://www.gitignore.io/api/visualstudio,dotnetcore,aspnetcore,node,vue,visualstudiocode > .gitignore && echo \"✅ .NET + Vue .gitignore 已產生！\"


	sync = "!f() { git fetch origin develop:$1 --force && git push origin $1 --force; }; f"


```