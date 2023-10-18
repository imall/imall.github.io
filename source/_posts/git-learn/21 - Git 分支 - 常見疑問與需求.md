---
title: Git 分支：常見疑問與需求篇
date: 2023-10-05 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
相信看完分支的觀念與操作後，還是會有人對分支操作的某些「正常現象」還是會有疑惑，同時也有一些實務上的問題，在之前的文章礙於篇幅而沒有提及。

這篇文章來說說筆者曾經疑惑，或是覺得可以記錄一下的內容。

進入今天的主題：
:::info no-icon
分支操作常見問題
:::

# 建立分支提交的 commit，不是應該要有「岔路」嗎？
只有在兩個分支分別走向不同的路，才會出現岔路。

這個問題應該是新手初次接觸分支比較不好體會的地方，我們都以為建立了分支發佈版本之後，線圖應該會有另一個顏色進行區分。但實際操作下來，卻會發現新分支的 commit 好像跟原本的分支在同一條線...

像是下圖的情境，開了新分支 `dev` 執行 commit ，但明明叫「分」支，線圖卻都沒有「分」出去欸...：
![](https://i.imgur.com/0LsjpH9.png)

這個案例可以合併快轉機制的例子說明，其實兩個分支原本都站在同一個位置，只是 `dev` 先往前走了兩步路，但是他們倆個人還是處在 **同一條路** 上，所以根本沒有「岔路」可言。

要讓線圖產生「岔路」，必須讓 `master` **不要** 沿著 `dev` 走過的路行走，而是要跟 `dev` 分道揚鑣，走出 `master` 自己的道路。

做法也很簡單，只要切換回 `master` 分支，在 `master` commit 版本，岔路就會出現了：
![](https://i.imgur.com/xSSF6z3.png)

這個線圖就像是兩個人分道揚鑣，從此不相往來一樣，Git 為了記錄兩個人走過的路程，必須把這個岔路畫出來。


如果你想再問為什麼 `master` 的線長得特別長，是因為 Git 線圖一般是會依照 **時間順序** 而繪製的。  

因為 `master` 是在 `dev` 往前兩步後才出發，為了在線圖中看出先後順序，會讓 `master` 走的位置超過 `dev` 之前的 commit 點點。  

知道這個觀念後，我們就可以從線圖中對分支 commit 的時間一目瞭然囉！  

# 能不能從過去的 Commit 時間點建立分支？
可以。

有幾種方式可以做到，一樣示範指令的做法，以及 Fork GUI 的做法。

## 建立分支完整指令
我們已經知道建立分支的指令是這樣：
```sh
git branch 分支名稱
```

不過事實上這個指令省略了預設值 `HEAD`：
```sh
git branch 分支名稱 HEAD
```
這個指令可以想像成：「我要撕一張便籤起來，寫上『分支名稱』，然後貼在目前所在的 commit 上。」

既然說是預設值，代表我們可以賦予他特定的資料值。

## 以 commitID 建立分支
以這張圖為例子
![](https://i.imgur.com/5rGUVKy.png)
想要在 `f3c45c2b` commit 上建立分支，直接這樣執行指令就可以了：
```sh
git branch 分支名稱 f3c45c2b
```
![](https://i.imgur.com/tesqy0M.png)

此外，我們既然知道建立分支的預設值是 `HEAD`，就代表我們可以先讓 `HEAD` 指著某個 commitID ，然後執行建立分支指令。
## 先切到某個 commitID，再建立分支
切換分支指令是這樣：
```sh
git checkout 分支名稱
```
如果把分支名稱改掉成 commitID ，那 HEAD 就會跑到 commitID 上：
```sh
git branch f3c45c2b
```
![](https://i.imgur.com/1aBe15r.png)

此時直接執行預設建立分支指令，就會在 commit 上成功貼上分支便籤了！
```sh
git checkout 分支名稱
```
![](https://i.imgur.com/TUChMmh.png)
只不過要注意，建立分支指令不會幫我們切換分支，請記得要再切過去再開發！

```sh
git checkout 分支名稱
```

## 用 Fork 在特定 commit 建立分支
我猜你看完上面原理，應該知道怎麼操作了。
其實就是在特定 commit 點右鍵，去找建立分支選項而已，很單純。

![](https://i.imgur.com/c29lST2.gif)

這裡有刻意把 **切換分支** 的選項打勾，你也可以選擇不切換分支，就看當下的執行需求。

# 在[Git 分支：觀念篇](https://ithelp.ithome.com.tw/articles/10333326)示範的「撕便籤」是什麼原理？
強迫建立了已經存在的分支。

一個專案中的分支是不能重複的，如果建立重複名稱的分支，Git 會直接彈出錯誤，告訴我們分支已經存在：
```sh
$ git branch dev     
fatal: a branch named 'dev' already exists
```

不過 Git 還是提供一個 `-f` 參數讓我們可以強迫建立分支。
```sh
git branch -f dev
```
用 `-f` 參數建立一個「不存在」的分支，結果會跟沒有 `-f` 參數沒兩樣。  
但如果是建立「存在」的分支，在線圖中看起來就像分支被「撕」起來又貼到目前所在位置的感覺。


當然，也可以強迫建立「已存在的分支」到指定的 commit 上，只要再多加一個 commitID 參數給他即可：
```sh
git branch -f dev commitID
```

不過因為 `-f` 代表 **強迫**(force)，如果資料因為操作不當而遺失了，Git 是概不負責任的哦！

看完以上內容，你應該能看懂我在 [Git 分支：觀念篇](https://ithelp.ithome.com.tw/articles/10333326) 幹的事情了：
![](https://i.imgur.com/jumgVmR.gif)

上面動畫是用 GUI 操作，指令的話可以這樣下
```sh
git branch -f Emilia b1c40661
```

# 開發到一半，需要切換分支怎麼辦
有兩種做法可以應付這個需求
## 1. 使用 `git reset` 把尚未開發的資料暫存起來
我們已經知道 reset 的預設模式會在 `HEAD` 移動過程，工作目錄的資料不會被異動，只有 commit 會被拆掉而已。  
也就是說，我們可以利用這個特性來「還原資料」。

當手上的東西處理到一半，我們可以先不管三七二十一把所有內容提交一個版本：
```sh
git commit -m "temp"
```
commit 訊息可以隨便打，他只是一個用來暫存資料的 commit，等等就移除了。

接著就可以 **安心的切換分支** 去處理當下的需求。

等到事情處理完，先 **回到原本的分支**，然後執行這個指令，資料就會還原到原本的狀態囉！
```sh
git reset HEAD^
```

> 如果你跟 reset 指令不熟，可以參考 [用 reset 回到過去](https://ithelp.ithome.com.tw/articles/10332631)，有詳細的 reset 操作模式說明。
## 2. 使用 `git stash` 把資料暫存起來
Git 因應這種情況，其實有提供一個指令讓我們使用。
```sh
git stash
```
當專案有尚未 commit 的內容，執行這個指令之後 Git 會直接把這些資料直接暫存一個版本。  

雖然說是版本，但他跟 commit 又不太一樣，而是標記一個叫 stash 的特殊版本，像是紅色箭頭指著的樣子：  
![](https://i.imgur.com/e8hK35R.png)

`stash` 會把所有尚未 commit 的資料 **「暫存」** 起來，所以即便隨意切換分支，也不用擔心有資料遺失或是資料混亂的情形！

如果想查看 Git 目前有哪些 `stash` ，可以執行這個指令：
```sh
git stash list
```
```sh
$ git stash list
stash@{0}: WIP on 分支名稱: f3c45c2 新增童話歌詞
```
`stash@{0}` 中的 `0` 是 `stash` 的序號，如果有多個 `stash` ，則會出現多個編號。

要還原 `stash` 的內容，也是要先 **切回原本的分支** ，並且依據你想還原的 `stash` 執行指令：
```sh
git stash pop stash@{0}
```
理論上這樣就可以還原資料了！

但是！！！！！！！！！！

如果你跟筆者一樣是習慣使用 windows 系統的「命令提示字元」或是「PowerShell」下指令的話，執行上面的指令可能會噴出這樣的錯誤訊息：
![](https://i.imgur.com/Z54cE9W.png)

這種情況下，你可以直接使用 「序號」，像這樣：
![](https://i.imgur.com/hgzN2rt.png)

不過如果你在 windows 使用 「Git Bash」 執行指令，就不會有這個問題
![](https://i.imgur.com/9gmfuZi.png)

而且你也可以直接寫數字：
![](https://i.imgur.com/7rwxWM1.png)

# 如果忘記「建立分支」就開始開發怎麼辦？
你在專案的起初，明明知道要建立分支才開始開發，卻直接在初始分支 `master` 開發，等到開發一段時間才想起應該要建立分支。

這時候線圖會長這樣：
![](https://i.imgur.com/wZ4a5Lx.png)
分割畫面左邊是線圖，右邊是要在建立分支後才要開發的檔案示範。

此時的解決方式很簡單：
1. 建立分支
2. 切過去
3. 結束

![](https://i.imgur.com/JZXZlNH.gif)

事實上，切換分支的行為，Git 會執行兩件事：
1. 把切換分支的 commit 內容更新到索引
2. 把切換分支的 commit 內容更新回工作目錄

這個行為並不會去管工作目錄「異動」的資料，所以直接「建立分支」然後「切換分支」，就能解決這個問題了。


# 我用上面那招，Git 不給我「切換分支」欸....
雖然切換分支的行為不會影響到工作目錄異動的資料，但是如果你編輯的那個檔案，沒辦法讓 Git 計算 commit 差異。  

例如你現在位於 `dev`，編輯了某個檔案之後發現應該要在 `master` 分支建立分支才能開發。  
但如果切換到 `master` 分支，因為編輯的檔案已經跟 `master` 的內容有衝突了，Git 就會拒絕這個切換分支的行為。

這種狀況下的 `git checkout` 指令，應該會看到類似這樣的訊息：
```sh
$ git checkout master
error: Your local changes to the following files would be overwritten by checkout:
        新檔案.txt
Please commit your changes or stash them before you switch branches.
Aborting
```


這段描述內容的畫面大概會長這樣：
![](https://i.imgur.com/jE1v8WX.png)

針對這圖片描述一下，你在 `dev` 分支編輯了檔案，但原本你是想要在 `master` 「開分支之後」再開發，結果 Git 不讓我們切換分支...

這種狀況下你可以這麼做：
1. 執行 `git stash` 執行暫存
![](https://i.imgur.com/OdkVCQu.png)
2. 切換到 `git master` 分支
![](https://i.imgur.com/6fjSDrS.png)
3. 建立並切換到開發分支 `git checkout -b 開發分支`
![](https://i.imgur.com/GaqhIg5.png)
4. 執行 `git stash apply stash@{0}` 把暫存抓回來
![](https://i.imgur.com/AfxsBaK.png)
5. 由於當初就是因為衝突而不給切換分支，此時應該會有檔案出現衝突樣貌，不過沒關係，資料確實是被抓回來了
![](https://i.imgur.com/e0j8V1K.png)
6. 此時 stash 還會存在，執行 `git stash drop stash@{0}` 刪除 stash
![](https://i.imgur.com/ZlvuHCR.png)
7. 完成！繼續開發吧！
![](https://i.imgur.com/K1O6vZH.png)


# 分支觀念提到的，HEAD 指著 commit 到底會怎樣啊？
老實說，只是把 `HEAD` 指著 commit 並不會怎樣，版本不會亂掉，資料也不會遺失。

當時有提到，`HEAD` 指著 commit，意思是 `HEAD` 是分離狀態(Detached HEAD State)
上面分支的操作，就刻意讓他變成這種狀態再繼續操作。

在實務上，也看過有人在需要查看版本時，會直接執行 `git checkout CommitID` 去查看版本資料，這個當下也確實是 Detached HEAD State。

所以暫時性的這個狀態其實不會怎樣，還算是很正常的事情。

Detached HEAD State 對我們開發上最大的影響，在於這種狀態下提交了版本，在切換分支後，版本是會在線圖中消失的！

示範一次：

1. 我讓 HEAD 指著某個 commit
![](https://i.imgur.com/k3SJuEv.png)
2. 準備提交版本前，執行一下 `git status`，他還很好心跟我說我現在狀態不正常 XD   
~~你才不正常，你全家都不正常~~
![](https://i.imgur.com/YOBMcqI.png)
3. 提交了一個版本，結果發現看起來沒什麼問題
![](https://i.imgur.com/jfsev4L.png)
4. 算了，切去別的分支看看...  
挖哩... 剛剛的版本不見了... 
![](https://i.imgur.com/h479vTq.png)

從畫面中，也發現 Git 很好心的提示我們，有一個在 Detached HEAD 狀態的 commit 遺失了。
如果我想把他找回來，可以用那個 commitID 建立一個分支。  

好險...

這上面的訊息，之前大概都說明過了
一個觀念是，Git 中 commit 過的內容不會不見，除非你忘記 commiID 值。  
另一個觀念是上面提到的，我們可以拿特定的 ID 來建立分支。

記得[分支觀念](https://ithelp.ithome.com.tw/articles/10333326)中，我有說過 「HEAD」 跟 「分支本質」有點像圖釘嗎？  
在 Detached HEAD 狀態提交一個版本，代表這個版本會拿 HEAD 圖釘把他 **釘** 在線圖上的，但 `HEAD` 是一個會依據需求而移動的「圖釘」，如果沒有再拿一個「分支圖釘」把 commit 釘在線圖上，等 `HEAD` 換位置，這個 commit 就會消失！

這就是為什麼要強調，沒事不要讓 `HEAD` 指著 commit 的原因。  
把這觀念拆得更細一點說，就只是避免我們不小心在這種狀態提交版本，導致 Git 無法記錄內容罷了。


# 總結
分支篇的內容到這已經算是告一個段落。

以學習的角度建議要做的事情，就是盡量去使用分支，體會分支是「便籤」這件事。  

並且去模擬實務上使用分支時會遇到的狀況，並且試著解決他，處理到自己希望完成的情境。  

如同觀念篇所說的事，有了分支之後，Git 的世界將會變得很不一樣，即便你只有一個人在使用 Git ，也很建議常常去使用分支，體會分支帶給我們的便利性！