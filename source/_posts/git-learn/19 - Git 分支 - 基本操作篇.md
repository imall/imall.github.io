---
title: Git 分支：基本操作篇
date: 2023-10-03 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
這篇文章，開始來介紹該如何開始使用分支吧！

一樣介紹完指令之後，來介紹 GUI 的操作方式。
老樣子，我可能會直接在指令操作的內容敘述一些觀念，也請 GUI 派的讀者也稍微看一下指令的內容。


# 使用指令操作分支
跟分支有關的指令需要認識兩個英文單字：  
1. `branch` ，也就是分支本身
2. `checkout`， 中文是取出

不過我看著「取出」兩個字，實在想不到他的意義是什麼。  
所以 `checkout` 我會直接把他想成「切換」。

## 查詢分支
若要查詢目前專案中的分支，直接使用這個指令就可以了：  
```sh
git branch
```
```sh
$ git branch
* master
```

尚未建立任何分支前，指令會跑出預設分支 `master`，並且注意分支前面有一個星號(`*`)，是用來提示我們目前所在的分支位置。

## 建立分支
要建立分支，在 `git branch` 指令之後加上 **分支名稱** 即可：

```sh
git branch 分支名稱
```

新增一個 **new-branch** 分支，可以這麼執行指令：
```sh
git branch new-branch
```
接著如果再執行一次 `git branch` 會看到多了一個名稱：
```sh
$ git branch
 * master
   new-branch
```

注意到 `*` 依然在 `master` 前面，因為 `git branch 分支名稱` 指令只會幫我們建立分支，並不會主動幫我們切換分支。  


上一篇文章中有說到，分支是 **便籤**，所以建立分支代表撕一張新的便籤起來寫上名稱。  

預設的建立分支行為，Git 會把這張寫好名稱的便籤貼到目前所在的 commit 上面。  

就以上面的例子來說，原本的線圖會長這樣，目前所在 `master` 分支上：
![](https://i.imgur.com/ZwLxIZ7.png)

執行完 `git branch new-branch` 指令後，線圖會在原本的位置多一個分支便籤：
![](https://i.imgur.com/55j9t8t.png)


因為這兩個分支都是貼在同一個 commit，如果在兩個分支持續切換，並不會看到資料有任何改變，這也是有人會誤會建立分支會複製一份資料出來的原因。

事實上，建立分支的行為沒有任何資料會被複製，純粹只是多了一張便籤貼在原本的位置而已。

## 切換分支
雖然建立分支，會把分支便籤貼在原本的 commit 上，不過在 Git 提交的版本，會記錄在「目前所在」的分支，所以如果希望資料提交到哪個分支，就必須切換到那個分支再開發、提交。

切換分支的指令是這樣：
```sh
git checkout 要切換的分支名稱
```

例如要切換到 `new-branch` 分支的指令會長這樣：
```sh
$ git checkout new-branch
Switched to branch 'new-branch'
```

此時執行 `git branch` 來查看分支：
```sh
$ git branch
   master
 * new-branch
```
注意到星號 (`*`) 跑到 `new-branch` 去了，表示我們成功切換分支！

而線圖會長這樣，注意到 **圈圈** 跑到 `new-master` 前面了：
![](https://i.imgur.com/x80eQ1v.png)

## 建立與切換一次搞定
我們建立一個分支之後，下一步通常就是要切過去，但要下兩個指令實在有點麻煩
實務上我常常會用下面這個指令，一次進行分支的建立與切換：
```sh
git checkout -b 新的分支名稱
```
透過「切換分支」的指令，並且多一個 `-b` 的參數
就可以跟 Git 說：「請幫我建立一個新分支，接著直接切換過去」


## 分支改名
如果對於目前的分支有想改名的需求，可以使用 `-m` 參數來對分支進行更名。  
假設目前 HEAD 是指著 `master` 分支，然後執行這個指令：
```sh
git branch -m changed-master  # 更改目前所在分支名稱
```

執行完指令後，master 分支，就會被改名叫 `changed-master` 了
```sh
$ git branch
 * changed-master
   new-branch
```


當然也可以更改專案中的其他分支名稱，語法是這樣：
```sh
git branch -m 原有分支名稱 新的分支名稱
```

例如目前雖然還是在 `changed-master` 分支上，要更改 `new-branch` 名稱的話，可以執行這個指令：
```sh
git branch -m new-branch 改成中文名
```

此時執行 `git branch` ，就能看到改好的名稱了。
```sh
$ git branch
 * changed-master
   改成中文名
```

## 刪除分支
如果分支的任務已經完成，未來不再需要這個分支的話，可以使用 `-d` 參數進行分支的刪除
執行參數之後，會跳出 **Deleted branch [分支名]** 的訊息，告訴你已經刪除了某個分支
```sh
$ git branch -d 改成中文名
Deleted branch 改成中文名
```

使用 `git branch` 確認一下，沒意外的話就可以看到分支被刪掉囉~

```sh
$ git branch
 * changed-master
```

### 無法刪除分支的狀況

記得上一篇提到，分支有圖釘的特性，會把已經 commit 釘在線圖上。  
Git 為了避免有 commit 不見，有幾種狀況會沒辦法使用 `-d` 參數刪除分支：

1. 你目前所在的分支
如果把目前所在的分支刪除，HEAD 會沒有分支可以指著，他就只能直接指著 commit 了...  
上一篇文章曾說過，沒事不要讓 HEAD 單獨指著 commit，Git 也不會希望這種事情發生，所以當我們執行指令(`git branch -d`)要刪除目前所在的分支時，Git 會阻止這個行為！

2. 沒被合併的分支
也是上一篇文章的概念，如果「一枝獨秀」的分支被刪除，那分支線會直接從線圖中消失。  
所以 Git 也不會讓我們用 `-d` 參數刪除這種分支。


例如我們有個 `test` 分支，在線圖中長這樣：
![](https://i.imgur.com/LI8l236.png)

如果我們執行 `git branch -d test` 指令，會出現以下訊息：
```sh
$ git branch -d test
error: The branch 'test' is not fully merged.
If you are sure you want to delete it, run 'git branch -D test'.
```

`error` 訊息就是在告訴我們，這個分支並沒有被合併(merged)，沒辦法刪除他。
而第二句話是說，「如果你真的想刪除的話，執行 `git branch -D test`」。

所以 Git 其實也沒那麼強硬，如果我們堅持想刪除 `-d` 刪不掉的分支，那就把小寫改成大寫吧！

## 強制刪除分支

```sh
git branch -D test
```

# 使用 Fork GUI 操作分支

## 查詢分支
在 Fork GUI 中，要查詢分支，請先切到 **All Commits** ，接著所有分支都會呈現在紅框框的位置：
![](https://i.imgur.com/57SLrAt.png)



## 建立分支
建立分支，則是在上方選單有一個 `branch` 的按鈕點進去
就會讓我們輸入分支名稱，建立新的分支

![](https://i.imgur.com/a9NlH7i.png)

同時有一個  **Check out after create** 的勾選框，表是建立後可以直接切換過去。
![](https://i.imgur.com/ppq8ERG.png)



**補充：**
如果分支名稱用一個 `/` 來命名：
```sh
開發者名稱/功能
```
在 Fork GUI 的分支區塊，會跑出一個以 **開發者名稱** 命名的資料夾：
![](https://i.imgur.com/ZBw8Dda.png)

不過對 Git 來說，分支名稱並 **沒有** 資料夾這層涵義，所以實際名稱還是會長這樣 `開發者名稱/功能`，如同 commit 上面呈現的便籤一樣。

這樣命名的一個好處是，如果是團隊開發，GUI 就會自動幫我們把每個人的分支分門別類。
當需要找特定人員使用的分支，就會比較好找。

當然，`/` 前面不一定要是開發者，可以依照專案需求去定義名稱。

以上就給大家參考吧！

## 切換分支
假設目前已經建立好 「新分支」 的分支：
![](https://i.imgur.com/GpsC6ya.png)

要切換過去的話，在左邊視窗對著分支名稱兩下即可：
![](https://i.imgur.com/BVE8XXI.gif)
## 分支改名
需要更改分支名稱，對 commit 按右鍵，就可以找到分支改名的選項：
![](https://i.imgur.com/ZJuklXj.gif)

也可以直接在左邊的分支名稱中改名：
![](https://i.imgur.com/PP73bVi.gif)
## 刪除分支

刪除分支的動作跟分支改名差不多：
![](https://i.imgur.com/NJv0rnt.gif)

刪除分支需要注意的事情，已經在上文指令的部分說明過了，就不再贅述。


# 總結

這篇文章介紹了基礎分支操作，搭配 [Git 分支：觀念篇](https://ithelp.ithome.com.tw/articles/10333326) 的內容，我們已經可以開始使用分支進行開發，並且應該不會有什麼太大的疑惑了。

只是建立分支開發到一段時間，總會需要將開發的資料合併回主要的分支。  
所以下一篇文章，將進入分支合併的章節，會介紹 `git merge` 指令該如何使用。