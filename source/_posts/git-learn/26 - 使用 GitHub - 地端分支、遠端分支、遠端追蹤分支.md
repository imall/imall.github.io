---
title: 使用 GitHub：地端分支、遠端分支、遠端追蹤分支
date: 2023-10-10 10:50:20
tags: [Git , GitHub]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
先前的內容，我們已經學會如何操作「單一分支」在地端與遠端的資料處理了，不過一個專案不會只有一條分之在運作，推到遠端的分支也不太可能只有一條。

事實上，即便遠端儲存庫有數十條分支，我們使用 `git clone` 指令把專案拉回地端使用時，也只會把主要分支的內容拉回來。

那麼如果需要使用其他的分支，該如何將他從遠端拉回地端使用呢？

除了這個問題之外，或許還會遇到另一個問題：我們在地端又怎麼能知道遠端到底紀錄了幾條分支呢？

一切的內容將會在這篇文章介紹。

進入今天的主題

:::info no-icon
地端分支、遠端分支、遠端追蹤分支
:::

# 前置作業
假設我們在「地端儲存庫」有六個分支
```sh
$ git branch
  Emilia
  Ram
  Rem
  Subaru
  dev
* main
```

依照前一篇[遠端儲存庫基本操作](https://ithelp.ithome.com.tw/articles/10337536)所學會的內容，先建立好地端與遠端的連線
```sh
git remote add origin https://github.com/imall/GitLearn.git
```

接著把所有地端分支推到遠端
```sh
$ git push --all
Enumerating objects: 45, done.
Counting objects: 100% (45/45), done.
Delta compression using up to 12 threads
Compressing objects: 100% (44/44), done.
Writing objects: 100% (44/44), 5.27 KiB | 899.00 KiB/s, done.
Total 44 (delta 23), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (23/23), completed with 1 local object.
To https://github.com/imall/GitLearn.git
 * [new branch]      Emilia -> Emilia
 * [new branch]      Ram -> Ram
 * [new branch]      Rem -> Rem
 * [new branch]      Subaru -> Subaru
 * [new branch]      dev -> dev
```

在 GitHub 上，我們可以透過這顆按鈕確認所有分支都已經被推到遠端了
![](https://i.imgur.com/okdVVx5.png)


接著，我們模擬團隊成員需要把專案從遠端拉回地端的情境，使用 `git clone` 指令把專案拉回地端

```sh
git clone https://github.com/imall/GitLearn.git
```

進到專案資料夾，查看一下分支，應該只會出現主要分支
```sh
$ git branch
* main
```

完成前置作業之後，一起來認識地端分支、遠端分支、遠端追蹤分支的差別吧！

# 地端分支、遠端分支、遠端追蹤分支
要理解這三個分支的第一個觀念，必須先知道「遠端存在的分支」跟「地端存在的分支」，兩者是可以分開看待的。

地端存在的分支如果不透過 `git push` 指令推到遠端，遠端就不會有這個分支。  
反過來說，遠端即便有數十個分支，在地端執行 `git clone` 指令時，並不會一次把所有分支全部都 clone 回地端，只會 clone 專案「主要分支」的內容。

## 地端分支
在[分支操作](https://ithelp.ithome.com.tw/articles/10334040)文章中，我們曾經介紹查看分支的指令：
```sh
git branch
```
這個指令就是在查看「地端分支」的指令，他「只會」列出地端的所有分支。


## 遠端追蹤分支
如果在地端想要查看遠端儲存庫有哪些分支，我們可以使用這個指令查看：
```sh
git branch -r
```
`-r` 是 `--remotes` 的縮寫，根據 `git branch -h` 說明的定義，這個指令是用來查看「遠端追蹤分支」的指令：
```sh
 -r, --remotes         act on remote-tracking branches  
 # 注意他是寫遠端追蹤(remote-tracking)，並不是寫遠端(remote)
 ```

這個指令所列出的分支，在終端機顯示的顏色會是「紅色」，提醒我們這是「遠端追蹤分支」：
![](https://i.imgur.com/ekajpPV.png)


如果想要一次列出「地端分支」、「遠端追蹤分支」，可以用 `-a` 。  
`-a` 是 `--all` 的縮寫，表示一次列出所有分支：
```sh
git branch -a
```


執行完指令之後，「遠端追蹤分支」一樣會是用醒目的「紅色」來表示 ：
![](https://i.imgur.com/6LlfHqm.png)
跟剛剛 `-r` 參數不太一樣的是，`-a` 參數列出的追蹤分支多了 `remotes` 的字樣，不過兩者都是「遠端追蹤分支」

事實上我們沒辦法直接從地端查看「遠端分支」，畢竟遠端分支存在於「遠端儲存庫」，他不該是個「地端」應該出現的資訊，我們只能在地端「追蹤」建立連線的遠端有哪些分支。

不過一般來說，在地端能查到「遠端追蹤分支」，通常就是遠端有那個分支才會顯示出來，所以從「遠端追蹤分支」的資訊也能對應到遠端有哪些分支。

唯一的例外是，有團隊成員 **新增** 或是 **刪除** 「遠端分支」，這種狀況下「遠端追蹤分支」並不會自動被更新，就會使得「遠端追蹤分支」的數量不等於「遠端分支」的數量。



## 遠端分支
遠端分支的定義是：存在於遠端的分支。

我們已經知道「遠端追蹤分支」不一定完全等於「遠端分支」的資訊，要是真的想確定當下有哪些遠端分支，最直接的方式就是去遠端儲存庫的介面查看：
![](https://i.imgur.com/okdVVx5.png)

不過實務上，我們不太需要在意遠端全部的分支有哪些，實際在遠端儲存庫查看分支時，通常只是要確認當下需要操作的分支是否存在，並且在使用時確認這些分支有確實的被拉回地端即可。  


理解這三個分支的差異之後，接著來說明如何把「遠端分支」拉回地端使用吧！

# 切換遠端分支回地端使用

我們要操作 Git 版控，一定會在地端操作完資料，才會把分支同步回遠端，並不會直接在遠端處理 Git 版控資料。

`git clone` 指令從遠端拉回來的分支不是我們需要的分支的話，就需要使用 `git checkout` 指令來將其他遠端分支拉回來。


實際的情境可能是這樣：  
在 clone 儲存庫回地端之後，透過 `git branch -a` 指令查詢有哪些遠端追蹤分支可以使用：
```sh
git branch -a
* main
  remotes/origin/Emilia
  remotes/origin/HEAD -> origin/main
  remotes/origin/Ram
  remotes/origin/Rem
  remotes/origin/Subaru
  remotes/origin/dev
  remotes/origin/main
```

從星號 (`*`) 的提示可以知道我們 clone 下來的分支是 `main` 分支，假設我們需要使用遠端的 `dev` 分支時，請這樣執行指令：

```sh
git checkout dev
```

不要懷疑，雖然地端目前 **沒有** `dev` 分支，不過只要「遠端追蹤分支」有 `dev`， `git checkout dev` 指令就可以幫我們把「遠端」的 `dev` 分支拉回地端來使用！！


千萬要注意，我們 **「不會」** 直接 `checkout` 「遠端追蹤分支」：
```sh
git checkout remotes/origin/dev # 不可以切換到遠端追蹤分支！!!!!!
```
或是
```sh
git checkout origin/dev # 不可以切換到遠端追蹤分支！!!!!!
```

因為遠端追蹤分支不是用來「開發」，而是用來「追蹤」的，我們不會也不該用 `checkout` 指令切換到「遠端追蹤分支」，千萬要切記不能這樣操作！


# 遠端分支若有異動，並不會自動同步回地端

我們在遠端儲存庫的資料 `git clone` 回來之後，如果團隊的其他成員又透過 `git push` 指令把分支推送到遠端，地端是不會「自動更新」遠端追蹤分支的。


我們來看圖說故事吧！
![](https://i.imgur.com/oQRpGaD.png)
左邊是 `愛蜜莉雅` 專案中的開發狀況，地端跟遠端都有她的分支 `Emilia`。  
後來 `雷姆` 新加入團隊幫忙開發，她將儲存庫 clone 回自己的電腦之後，建立了 `Rem` 分支進行開發，同時也把 `Rem` 分支推到遠端儲存庫了，如同右邊終端機顯示的那樣。

**如果** 地端會自動更新，那 `愛蜜莉雅` 此時查詢分支，應該要就會出現 `Rem` 的「遠端追蹤分支」才對。

但很不幸的是...依舊沒有....
![](https://i.imgur.com/IQrRnjc.png)

這種情況下，`愛蜜莉雅` 需要執行一這個指令：
```sh
git fetch
```

`git fetch` 是一個用來把遠端有「新增」的分支更新回地端「遠端追蹤分支」的指令，操作完之後再次查詢遠端追蹤分支，就能看到最新的遠端分支狀況囉！

![](https://i.imgur.com/mrhWAa9.png)




# 使用 Fork GUI  操作遠端分支

建立完觀念後，我們來看看 Fork GUI  如何查看與操作遠端分支


## Fork GUI 的遠端追蹤分支
當我們執行 `git clone` 把專案拉回地端之後，用 Fork GUI 把專案打開，所有的遠端追蹤分支會直接列在視窗左邊 **Remotes** 區塊，紅框上面的 **origin** 則是當初 `git remote add` 定義的名稱：
![](https://i.imgur.com/b4Z5NbK.png)

除此之外，在 Fork GUI 還有一個小細節，如果遠端分支已經同步回的話，那麼線圖中，「GitHub」的圖標會跟「分支」圖標分開：
![](https://i.imgur.com/vCp5f7B.png)

要是分支只存在遠端，那麼分支就會顯示 `origin/分支名稱`，而且是與 「GitHub 圖示」黏在一起：
![](https://i.imgur.com/8ZYpzb0.png)


## Fork GUI 將遠端分支拉回地端使用
需要將遠端分支拉回地端的話，直接對「遠端追蹤分支」點兩下即可。

這裡假設要抓 `dev` 分支回來用：
![](https://i.imgur.com/YtdpU8s.gif)

## Fork GUI 將遠端分支同步回地端
如果遠端有分支，但是地端卻沒有遠端追蹤分支，像是上面的範例那樣，`愛蜜莉雅` 的地端專案沒有 `Rem` 的遠端追蹤分支：
![](https://i.imgur.com/uYP5X3M.png)

1. 點選左上角 `Fetch` 按鈕：
![](https://i.imgur.com/eIddWDq.png)

2. 跳出的視窗，再點一下 `Fetch` 按鈕，就完成了
![](https://i.imgur.com/epOfLAG.png)


操作如下圖：
![](https://i.imgur.com/l42Q9uc.gif)
# 總結
總結一下今天文章提到的觀念

1. 地端分支，表示存在於**地端**儲存庫的分支
2. 遠端分支，表示存在於**遠端**儲存庫的分支
3. 遠端追蹤分支，是在地端用來「追蹤」遠端分支的特殊分支，他並不能拿來開發，純粹只是追蹤用途！
4. 要將不存在於地端的遠端分支拉回來使用，執行 `git checkout 分支名稱` 指令即可。
5. 使用 `git fetch` 可以將遠端分支同步回地端的「遠端追蹤分支」。