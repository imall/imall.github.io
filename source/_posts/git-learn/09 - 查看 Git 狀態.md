---
title: 查看 Git 資料狀態
date: 2023-09-23 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
幾篇文章下來，我們已經建立「暫存區」、「儲存庫」的觀念，同時學習到幾個重要的指令：
- `git add`
- `git commit`

這兩個行為如同添加商品到購物車，然後結帳的動作。

用一個「激問句」來開啟這篇文章的主題：
:::info no-icon
你會在不查看購物車有什麼商品的狀況下，就直接點結帳嗎？
:::


# 查看 Git 目前的狀態
查看狀態的指令，是指令派的使用者「必學」的指令。  

在執行 `git add` 後，與執行 `git commit` 前，終端機畫面其實不會告訴我們目前哪些資料是在暫存區，Git 於是提供一個指令讓我們可以查詢：
```sh
git status
```

我們在一個「空資料夾」執行 `git init`，之後，直接執行 `git status`，來看看剛「初始化」的儲存庫，`git status` 會出現的內容：
```sh
$ git init  # 執行 git init 初始化儲存庫
Initialized empty Git repository in D:/GitHub/GitLearn  # 跳出目前儲存庫沒東西的訊息
$ git status # 執行 git status 看看狀態
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```
來解釋解釋此時 `git status` 跑出來的東西：
1. **On branch master** ，告訴我們現在人在 master 分支，這也是預設的分支名稱。

2. **No commits yet**，因為還沒發布任何版本，所以會出現這個字樣。

3. **nothing to commit**，這句話最重要：「沒東西可以 commit」！  
由於現在工作目錄是空的，沒東西可以 commit 很正常，不過未來開始版控後如果看到這句，就代表「工作目錄」的資料都已經 commit 完成！

4. **(create/copy files and use "git add" to track)** ，因為資料夾目前是空的，提示我們可以新增檔案，並且用 `git add` 來「追蹤」這些檔案。

> 關於 **追蹤** 的觀念，之後會專門寫一篇文章來跟大家講解。

## 資料夾出現新檔案時的狀態
來看看剛進貨的商品，還沒加入購物車時，`git status` 會怎麼顯示它的狀態。

先在資料夾新增 `index.html` 與 `readme.txt` ，直接來執行 `git status`。  
![新增檔案示意](https://i.imgur.com/JyaSYuG.png)  
```sh
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html
        readme.txt

nothing added to commit but untracked files present (use "git add" to track)
```

一樣來解釋解釋：
1. **On branch master**，跟我們說現在人在 master 分支上，這是 **一定** 會出現的文字。

2. **No commits yet**，我們還是沒有 commit ，這行又出現了。

3. **Untracked files:**，用來列出 Git 偵測到「未追蹤」的檔案。

4. **(use "git add \<file\>..." to include in what will be committed)**，提示我們可以用 `git add`指令把檔案加到暫存區。

5. **nothing added to commit but untracked files present (use "git add" to track)...**，告訴我們「雖然沒東西能 commit」，但可以用 `git add` 指令把檔案傳到暫存區。

上述 `git status` 重點就是：  
你還不能下單(`commit`)，但商品還有庫存，只要你把商品加入購物車(`git add`)，購物車(`暫存區`)內就可以看得到你的商品哦 ~~


假設我們真的經不起誘惑(?)，把商品加入購物車了，商品狀態 (`git status`) 又會出現什麼資訊呢？  


讓我們繼續...  看~ 下~ 去~

## 檔案加入暫存區之後的狀態
經不起誘惑的我，將 `index.html` 透過 `git add` 加到了暫存區。

此時執行 `git status`：
```sh
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        readme.txt
```

1. 第一句、第二句都是一樣的內容，就不特別說明。

2. **Changes to be committed：**，這裡列出所有穩定在暫存區的商品，如果執行 commit，這個訊息底下的檔案，就會被發佈成一個版本。

3. **use "git rm --cached \<file\>..."**，可以用這個指令把檔案移出暫存區，讓檔案變成 **未暫存** (unstage) 的狀態。

4. **Untracked files：**， 因為 `add` 的檔案沒有 `readme.txt`，它依然存在這裡。

5. **(use "git add \<file\>..." to include in what will be committed)**，**未追蹤** 區域一定會出現的一句話，提示我們用這個指令來追蹤檔案。

這個情境就很像我們網購一定會做的事情，把商品加入購物車，跑到購物車確認商品。 

最重要的內容會在 **Changes to be committed** 底下呈現，它能讓我們確定檔案是不是已經被加到 Git 暫存區，要確定暫存區有東西後，我們執行 commit 才有意義！


接下來，要是把商品下單購買了呢.....？

## 執行完 Commit 之後的狀態

前置作業：  
剛剛只先把 `index.html` 加到暫存區，不過為了查看所有檔案都 commit 後的狀態，就先執行 `git add .` ，再執行 `git commit` ，將所有檔案都資料紀錄成第一個版本。

接著就來看看此時的 `git status` 吧：

```sh
$ git status
On branch master
nothing to commit, working tree clean
```

1. 第一句依舊是 `git status` 標配的內容，告訴我們所在分支名稱。

2. **nothing to commit, working tree clean**：我們再度看到一開始提到「很重要」的那句話了 **nothing to commit**，只要出現這句話，代表我們已經買下商家所有商品庫存，沒東西可以買了！ ~~好有錢~~    
言下之意，所有檔案的異動都被紀錄到「儲存庫」之中，此時已經沒東西能再紀錄了。

看到這邊，有沒有注意到 Git 的 `git status` 寫得非常詳盡?!

身為指令派的我，每次 commit 之前，一定會先執行 `git status`，確認檔案已經在暫存區，才會 commit 。



故事到這還沒結束，雖然我們已經下單，假如想要購買不同規格的商品...

對應到實務上，已經紀錄到儲存庫的檔案，如果又進行修改，此時的檔案狀態又會是...？

## 編輯已經提交的檔案後，檔案呈現的狀態
在網購的情境，如果想購買不同規格的商品，就要重新選擇不同規格的商品後，把他加到購物車，再下單一次才行。

實務上，就是當資料都 commit 後，發現存好的檔案需要修正，於是去編輯檔案的行為。

這種狀況下的 `git status` 會出現什麼資訊呢？

上一步剛好已經示範到所有內容都 commit 了，就直接來編輯 `index.html`，並且執行 `git status` 吧！

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

no changes added to commit (use "git add" and/or "git commit -a")
```

這裡關鍵在 **Changes not staged for commit**，告訴我們更動的內容，目前處在「不能 commit」 的「非暫存」狀態。

只要已經在儲存庫的檔案，再度於工作目錄中編輯，`git status` 就會把檔案列在 `not staged` 區塊。

> 異動後的檔案，必須重新提交，存入下一個 commit 版本。

# 複習：重新提交異動完成的檔案
一樣的商品想再次購買不同的規格，「基特平台」我們兩種下單方式可以選擇：
1. 把商品「加入購物車」，並且再「下單」一次。  
2. 不用加入購物車直接下單，不過要在「下單」時註記「自動加入購物車」。

無論選擇哪一種方式，都是再次執行下單的行為，原本的「訂單紀錄」依舊會存在，而且還會多一筆「新單資訊」。  

實際的指令之前已經說明過了，來複習一下：  
1. 重新執行 `git add` ，再執行 `git commit -m "版本訊息"` 。
2. 直接執行 `git commit -a -m  "版本訊息"` 或是 `git commit -am "版本訊息"` 。

# 總結
`git status`，是一個可以拿來查看 Git 檔案狀態的指令，是指令派使用者的好幫手。  
~~實在是居家旅行， X人X口 ，必備良藥~~

這個指令除了能顯示工作目錄所有檔案目前的狀態之外，還會順便提示我們下一步可以怎麼操作。

學習閱讀 `git status` 顯示的資訊，會是指令派的使用者的重要課題。

一言以蔽之今日的內容：
:::info no-icon
當你不知道下一步該怎麼操作時，執行 git status 吧！
:::