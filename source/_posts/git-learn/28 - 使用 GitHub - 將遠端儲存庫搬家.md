---
title: 使用 GitHub：幫遠端儲存庫搬家
date: 2023-10-12 10:50:20
tags: [Git , GitHub]
comment: false
categories: 一起來學 Git 吧！
---
# 前言
不知道讀者有沒有需要將遠端儲存庫搬家的需求？

情境可能是這樣，由於現在 copilot 好棒棒，大家紛紛想擁抱微軟，而目前專案都放在 GitLab 遠端儲存庫，現在想要把專案放到 GitHub 管理。

然後又很剛好，你換了新的電腦，地端完全沒有任何 Git 專案資料，你沒辦法直接把地端的資料上傳到 GitHub。

如果你剛好也遇到這個需求，這篇文章可能有機會幫助到你。

~~疑？我怎麼會說....「也」呢？~~

進入今天的主題
:::info no-icon
幫遠端儲存庫搬家
:::
# 第一步：把遠端儲存庫的內容 clone 回地端

這步不用多說，畢竟要把資料推到 GitHub ，我們需要「地端儲存庫」當作搬家的中繼站。

假設我想搬的專案是 GitLab 上的 `GitLearn` 專案，把他 clone 回地端：

```sh
git clone https://gitlab.com/imall1110/gitlearn.git
```
# 第二步：把遠端追蹤分支的資料全部 checkout 回來
`git clone` 指令只會把預設的遠端分支拉回地端使用，不過如果我們每個分支都要搬家的話，就必須先讓地端出現所有分支。

如果是指令派的讀者，可以逐個執行 `git checkout` 指令。 

筆者這個步驟很習慣直接使用 Git GUI 進行切換分支的行為。
![](https://i.imgur.com/gViMK2j.png)
![](https://i.imgur.com/Cwd0DAp.png)


> 這一步在 **第四步** 之前完成即可。

# 第三步：在 GitHub 建立專案

想要搬家，首先要先有個家。

我們需要先在 GitHub 建立好一個空的遠端儲存庫，建立的方式如之前在 [遠端儲存庫基本操作](https://ithelp.ithome.com.tw/articles/10337536) 示範的那樣，只要建立一個「空的」儲存庫即可。


# 第四步：在地端更改地址
一樣是 [遠端儲存庫基本操作](https://ithelp.ithome.com.tw/articles/10337536) 文章中，我們曾經說明如何使用 `git remote add` 指令幫「地端儲存庫」建立「地址」。

而 `git clone` 回地端的儲存庫，不用特別執行 `git remote add`，Git 會自動幫我們在「地端」把「遠端地址」建立好。

由於是從 GitLab 平台 clone 下來，地址會是 GitLab 的資訊，想確認這件事的話可以用 `git remote -v` 指令確認：

```sh
$ git remote -v
origin  https://gitlab.com/imall1110/gitlearn.git (fetch)
origin  https://gitlab.com/imall1110/gitlearn.git (push)
```

要改掉這個地址，我們可以使用這個指令：
```sh
git remote set-url 名稱 網址
```

以目前這個要搬家到 GitHub 的情境，完整指令會長這樣：
```sh
git remote set-url origin https://github.com/imall/GitLearn.git
```


再使用 `git remote -v` 指令確認，資料正確就是改成功囉！
```sh
$ git remote -v
origin  https://github.com/imall/GitLearn.git (fetch)
origin  https://github.com/imall/GitLearn.git (push)
```

# 第五步：推送所有分支到 GitHub
確定所有要推到新家的分支都已經在地端儲存庫了，如果 **沒有** 的話，請先回上一步把儲存庫地址 **改回去舊的儲存庫**，再操作 **第二步** 的動作。


如果確定要搬家的分支都在地端了，直接執行這個指令：

```sh
git push --all
```


```sh
$ git push --all
Enumerating objects: 396, done.
Counting objects: 100% (396/396), done.
Delta compression using up to 12 threads
Compressing objects: 100% (183/183), done.
Writing objects: 100% (396/396), 512.75 KiB | 256.38 MiB/s, done.
Total 396 (delta 151), reused 396 (delta 151), pack-reused 0
remote: Resolving deltas: 100% (151/151), done.
To https://github.com/imall/GitLearn.git
 * [new branch]      Emilia -> Emilia
 * [new branch]      Ram -> Ram
 * [new branch]      Rem -> Rem
 * [new branch]      Subaru -> Subaru
 * [new branch]      dev -> dev
 * [new branch]      main -> main
```

執行完指令後，到遠端儲存庫確認分支是否都存在，沒問題的話就完成了：

![](https://i.imgur.com/QofQK6v.png)

# 補充：更改遠端地址的其他方式

通常搬家的需求不會時常發生，所以設定搬家地址的指令並不像其他指令一樣會常常使用，使得指令不太會被記得，要是每次搬家都要去查一次指令怎麼設定其實有點麻煩。

`git remote set-url 名稱 網址` 指令其實只是在編輯專案的 `config` 檔案而已，這個 `config` 檔案位置在 `.git/config`。

![](https://i.imgur.com/QNlmHJX.png)

如果原本的儲存庫跟新的儲存庫定義的名稱都叫 `origin` 的話，搬家指令就是在改這段網址：
![](https://i.imgur.com/ormtFBa.png)

知道原理後，我們就可以手動更改這段網址，不用去記指令囉！
![](https://i.imgur.com/JiWHt4X.png)


# 總結

總結一下搬家步驟
1. 將舊家的專案 clone 回地端
2. 將所有分支切回地端
3. 建立新家的儲存庫
4. 更改地端儲存庫的地址
5. 將所有分支推到新家

這個搬家動作其實不限於 **不同平台** 的儲存庫，只要有不同的遠端網址，即便儲存庫都在 GitHub，也可以照著步驟搬移。 ~~雖然應該沒人會在 GitHub 內部搬家就是了~~