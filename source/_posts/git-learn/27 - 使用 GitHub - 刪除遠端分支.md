---
title: 使用 GitHub：刪除遠端分支
date: 2023-10-11 10:50:20
tags: [Git , GitHub]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
在[分支觀念篇](https://ithelp.ithome.com.tw/articles/10333326)的文章中有介紹過，如果已經合併過的分支，即便刪除了也不會影響整個線圖，雖然當初是在講「地端分支」的觀念，但這個觀念其實也適用於「遠端分支」。  

不過我們也曾經說過，地端分支跟遠端分支是要分開看待的，所以要刪除的分支如果也存在於遠端，那麼即便地端分支已經被刪除，遠端的分支並不會因此自動被刪除，我們會需要額外處理「刪掉遠端分支」這件事。

這篇文章，來說明如何刪除遠端分支，以及遠端分支被刪除之後，團隊成員需要注意的小地方。


# 刪除遠端分支
刪除遠端分支的方式，一樣是使用 `git push` 指令，只要在使用指令時多加一個 `--delete` 即可。
```sh
git push origin --delete 分支名稱
```
或是使用 `--delete` 的簡寫 `-d`：
```sh
git push origin -d 分支名稱
```

除了`--delete` 參數之外，也可以使用下面指令來刪除遠端分支：
```sh
git push origin :遠端分支名稱
```
這個神奇的指令的概念是將「空的分支」推到遠端已經存在的分支上，藉此把遠端分支刪除。


# 在遠端分支被刪除後，團隊成員需要注意的小地方

來做個實驗吧，我開啟兩個終端機視窗，模擬兩個開發者地端儲存庫的狀況。
![](https://i.imgur.com/RNNAJ7T.png)

左邊是 `愛蜜莉雅` 的工作視窗，右邊是 `雷姆` 的工作視窗。

`愛蜜莉雅` 的開發分支是 `Emilia`，而 `雷姆` 的開發分支是 `Rem`，由於 `愛蜜莉雅` 之前為了幫 `雷姆` 看他的開發內容，曾經把 `Rem` 分支拉回地端查看資料，所以 `愛蜜莉雅` 的地端儲存庫中也有 `Rem` 分支。

`雷姆` 剛把專案開發完成，她的 `Rem` 分支已經被合併回主要分支了，`Rem` 分支在地端與遠端都可以不用再出現。 

此時 `雷姆` 需要執行兩個指令，分別刪除「地端」與「遠端」的分支：

- 刪除地端分支
```sh
git branch -d Rem
```

- 刪除遠端分支
```sh
git push origin --delete Rem
# 或是
git push origin :Rem
```
執行完之後，打開遠端儲存庫的介面，確定沒有 `Rem` 分支了
![](https://i.imgur.com/dPVWHjL.png)


> 注意這個行為是 `雷姆` 在她的專案中執行 

此時我們回到 `愛蜜莉雅` 的專案上，執行查看所有分支的指令看看專案分支狀況：
```sh
git branch -a
```
![](https://i.imgur.com/X8hdsG2.gif)

`愛蜜莉雅` 的專案上， `Rem` 分支並不會自動被刪除！！


# 修剪遠端追蹤分支

團隊協作上要有一個觀念，每個人的「地端儲存庫」都是獨立的，已經存在的地端分支，不會因為「遠端儲存庫」的遠端分支有什麼異動而受到任何影響！

唯一有辦法影響自己地端儲存庫資料的人，只有你自己。

銜接上面的例子，如果 `愛蜜莉雅` 知道 `雷姆` 的分支已經沒有要使用，而且 `Rem` 分支已經在遠端被刪除，她需要在自己的專案中做兩件事

1. 刪除地端的 `Rem` 分支
```sh
git branch -d Rem
```

2. 修剪遠端追蹤分支
```sh
git fetch --prune
```

`git fetch` 指令，本身的作用是把**所有**「遠端分支」更新回地端「遠端追蹤分支」 的行為，不過如果遠端已經沒有分支，指令並不會主動把「遠端追蹤分支」刪除。

`--prune` 意思是修剪，把他加到 `git fetch` 指令後，整個指令就會去修剪已經不用存在的 「遠端追蹤分支」了

```sh
$ git fetch --prune
From https://github.com/imall/GitLearn
 - [deleted]         (none)     -> origin/Rem
```

執行完這個指令後，「遠端追蹤分支」就不會再出現已經不存在的「遠端分支」囉！

```sh
$ git branch -r
  origin/Emilia
  origin/HEAD -> origin/main
  origin/dev
  origin/main
```

# 使用 Fork GUI 刪除遠端分支
假設 `雷姆` 完成了她的開發，想透過 Fork GUI 把 「遠端分支」 刪除。

1. 到 GUI 左下角，對著 `Rem` 遠端追蹤分支點右鍵，選到刪除選項：
![](https://i.imgur.com/7PBklsA.png)

2. 跳出的視窗點 `Delete` 就完成了
![](https://i.imgur.com/TY6NuJ8.png)

操作如下：
![](https://i.imgur.com/c5SYA0L.gif)

# 使用 Fork GUI 修剪遠端追蹤分支

`愛蜜莉雅` 要更新遠端已經被刪除的分支，在 Fork GUI 中點選左上角的 `Fetch` 按鈕即可：
![](https://i.imgur.com/eIddWDq.png)

操作如下：

![](https://i.imgur.com/Ds93Y4y.gif)


# 總結
總結一下這篇的重點
1. 若已經開發完畢，地端分支、遠端分支需要分開刪除。
2. 遠端分支被刪除後，團隊成員的地端儲存庫不會有影響。
3. 要將遠端刪除的分支狀態同步回地端，使用 `git fetch --prune` 指令。