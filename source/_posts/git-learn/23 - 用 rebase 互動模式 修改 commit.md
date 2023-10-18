---
title: 用 rebase 互動模式來調整歷史
date: 2023-10-07 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
上一篇文章聊到 `rebase` 可以用來把分支連根拔起，重新栽種到某個分支上。

而這個重新摘種的方式，會把拔起來的分支線所有 commit 內容全部重新 commit 一次。

這也代表說，我們可以用 rebase 做到：「不要將分支移花接木，但全部幫我重新 commit」，這樣就可以達到「修改歷史資料」的目的。

之前在 `--amend` 章節有提到，如果要修改歷史的 commit ，牽一髮就會動全身，就很像 rebase 在做的事情，而這篇文章的內容，就是要來說說如何使用 `rebase` 來修改歷史資料。


老樣子，指令講完講 GUI，觀念會在指令說  
~~雖然今天的內容寫完指令....真的有點懶得再寫 Fork GUI 了...~~

進入今天的主題：
:::info no-icon
使用 rebase 互動模式修改歷史資料！
:::


# 用指令操作 rebase 互動模式

`rebase` 指令可以使用一個參數 `-i`  來讓我們開啟互動模式，`-i` 是 `interactive` 的意思，中文叫做「互動」。

實際的指令會長這樣：

```sh
git rebase -i commitID
```
記得前一篇，把 `Rem` 分支 `rebase` 到 `master` 分支 的例子時，哪些 commit 會被重新提交嗎？
![](https://i.imgur.com/lNhz2Iy.png)

對，只有 `Rem` 分支的 commit，`master` 分支並沒有被動到。  
當時的是在 `Rem` 分支上，執行 `git rebase master` 指令完成了移植的動作。  
言下之意，`rebase` 指令上面寫的 **分支名稱** 或是 **commitID** 「不會被重新 commit」！

這個觀念會影響等等使用 **`rebase` 互動模式** 時，你能不能判斷應該要以哪個 commitID 為基礎來執行指令！！


接下來的互動模式，並沒有要進行「移植」，而只是想請 rebase 幫我們重新 commit，那麼哪些版本會因為 **指令執行之後而重新被 commit** ，就是個關鍵了。

線圖目前已經有了四個版本：
![](https://i.imgur.com/gDnPCG6.png)


`46ce` => `2163` => `e595` => `3141`。

如果希望能處理 `e595`、`3141` 兩個版本，則必須這麼執行指令：

```sh
git rebase -i  2163
```
或者更早一點的指令也行：

```sh
git rebase -i 46ce
```

這裡唯一的觀念是剛剛一直強調的，指令寫的 commitID， **不會** 被重新處理 commit！

沒問題的話，我們開始來使用 `rebase` 互動模式來操作歷史吧！



## 刪除某一個 commit
以上面的例子，如果我們希望直接刪掉 `e595` 版本的內容，可以這樣操作：

1. 執行 rebase 指令
```sh
git rebase -i  2163
```

2. 接著 Git 會跳出預設編輯器，並且「從舊到新」列出每個版本的內容
```sh
pick e5950fd second.txt
pick 314139e third.txt
```
3. 刪掉 `e5950fd` 這行資料
```sh
pick 314139e third.txt
```

4.  **關掉** 編輯器，就完成了。


> 第二步要注意一點，分支圖一般越上面的 commit 越新，但此刻文字編輯器的內容，越上面的 commit 越舊！！


實際操作如下圖：
![](https://i.imgur.com/jkyYeav.gif)
## 更改 commit 順序
調整 commit 順序操作也很單純

1. 執行 rebase 指令
```sh
git rebase -i  2163
```
2. 編輯器會跳出 commit 訊息
```sh
pick e5950fd second.txt
pick 314139e third.txt
```

3. 調換資料順序
```sh
pick 314139e third.txt
pick e5950fd second.txt
```

4.  **關掉** 編輯器，就完成了。


實際操作如下圖：
![](https://i.imgur.com/bc6hDOB.gif)

## 修改歷史 commit 訊息
在上面的動圖中，編輯器其實有顯示很多註解，但被我省略解釋了。
這些註解的內容是 `rebase` 互動模式的操作說明，讀者在操作的時候可以仔細看一下。  

如果我們要修改某一個 commit 的訊息，可以用到 `reword` 參數，也可以省略成 `r`。  

註解的內容是這樣：
```sh
# r, reword <commit> = use commit, but edit the commit message
```
會使用 commit ，但是「編輯」 commit 訊息。

恩，非常簡單明瞭的說明！

實際來試試看吧，我們來編輯 `second.txt` 這個訊息：

1. 執行 rebase 指令。
```sh
git rebase -i  2163
```
2. 編輯器會跳出 commit 訊息。
```sh
pick e5950fd second.txt
pick 314139e third.txt
```

3. 將要編輯的 commit 前面的 `pick` 改成 `rework` 或是 `r`。
```sh
r e5950fd second.txt
pick 314139e third.txt
```

4. **關掉** 編輯器。

5. Git 會再自動跳出 commit 的編輯視窗，在這個視窗更改訊息
```sh
second.txt

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
(下略)
```

6. 在這把訊息改掉
```sh
更改後的訊息

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
(下略)
```
7. 更改完成後，關掉視窗就完成囉，此時終端機的訊息呈現如下：

```sh
$ git rebase -i  2163
[detached HEAD 602042e] 更改後的訊息
 Date: Thu Oct 5 12:10:37 2023 +0800
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 second.txt
Successfully rebased and updated refs/heads/master.
```


實際操作如下：
![](https://i.imgur.com/qHzjYjZ.gif)


## 編輯歷史中某個 commit 的內容

除了單純的更改訊息外，來看看註解的第三個操作在說什麼
```sh
# e, edit <commit> = use commit, but stop for amending
```
使用 commit ，但是要停下來，用來 amending

等等，這個 `amending` 好像有點熟悉，為了這個詞，我還寫了[一整篇文章](https://ithelp.ithome.com.tw/articles/10331920)介紹他


沒錯，`edit` (或是 `e`) 就是讓我們可以執行 rebase 之後，再接再厲使用 `git commit --amend` 指令來編輯當下停留的 commit 的參數！

執行步驟大致是這樣 


1. 執行 `rebase` 指令。
```sh
git rebase -i  2163
```
2. 編輯器會跳出 commit 訊息。
```sh
pick e5950fd second.txt
pick 314139e third.txt
```

3. 將要編輯的 commit 前面的 `pick` 改成 `edit` 或是 `e`。
```sh
e e5950fd second.txt
pick 314139e third.txt
```

4. 關閉視窗後，終端機會顯示這些訊息：
```sh
git rebase -i  2163
Stopped at e5950fd...  second.txt
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue
```
來翻譯翻譯這段寫了啥，他說目前停留在 `e5950fd...` commit 上 (他也知道**哈希值**有 40位數很難打，用...省略XD)。  
接著說我們現在可以用 `git commit --amend` 來修正這個 commit。  
或者你很滿意這個 commit 的內容，那可以直接跑這個指令 `git rebase --continue`。    
換句話說你現在有兩個選擇：

5. 命運的交叉口
- 什麼都不做，讓 rebase 繼續 commit 還沒處理的 commit：
```sh
git rebase --continue
```
- 編輯這筆 commit 內容
編輯的方式就跟 `--amend` 指令一樣，請參考[這篇文章](https://ithelp.ithome.com.tw/articles/10331920)

6. 完成之後，你應該會看到終端機什麼事情都沒顯示，我們瞬間迷失了方向.....

7. 如果不知道下一步該怎麼做，執行 `git status` 指令吧！
```sh
$ git status
interactive rebase in progress; onto 2163eb7
Last command done (1 command done):
   edit e5950fd second.txt
Next command to do (1 remaining command):
   pick 314139e third.txt
  (use "git rebase --edit-todo" to view and edit)
You are currently editing a commit while rebasing branch 'master' on '2163eb7'.
  (use "git commit --amend" to amend the current commit)
  (use "git rebase --continue" once you are satisfied with your changes)

nothing to commit, working tree clean
```
他告訴我們，我們準備 rebase 到 `2163eb7` 上，他是我們執行 `rebase` 指令時所寫的 commitID。  
然後說上一個步驟已經做完 `e5950fd` 的 rebase 行為，你接著準備要做 `314139e` 的 commit。

Git 不直接幫我們 rebase 他，而是讓我們自己決定下一步要執行的事情
- 針對下一個 commit 再定義一次動作
```sh
git rebase --edit-todo
```
他會再跳一個 rebase 操作視窗給我們編輯，動作跟之前一樣。

- 繼續修改目前所在位置的 commit。雖然你剛剛已經 `--amend` 好了，但 Git 還是會停在原點(`e5950fd`)，你需要的話還可以繼續用這個指令修改 commit
```sh
git commit --amend
```
- 直接繼續 rebase，這就是告訴 Git 這步已經結束了，可以繼續往前走！
```sh
git rebase --continue
```
由於我們的 `rebase` 行為，已經在第五步完成，我們直接執行第三個指令。

8. 如果這已經是最後一個 commit ，那麼完成！
```sh
$ git rebase --continue 
Successfully rebased and updated refs/heads/master.
```

上面搭配了分岔步驟的解釋，閱讀起來可能有點亂，小結一下更改歷史 commit 的操作步驟：
1. 執行 `rebase` 指令
2. 將 commit 標記 `e` 或 `edit`
3. 用 `--amend` 處理 commit
4. 執行 `git rebase --continue`
5. 如果沒有其他 commit ，則完成。






## 把多個 commit 整合成一個 commit
有時候我們因為 commit 的太瑣碎，希望把一些 commit 集結起來 ~~(Avengers Assemble！)~~

繼續來看 rebase 互動模式過程提示我們的註解
```sh
# s, squash <commit> = use commit, but meld into previous commit
```
使用 commit ，但是 **融合** 到「前一個」 commit。

又是一個簡單明瞭的說明，直接來看操作比較快。


最初的線圖長這樣：
![](https://i.imgur.com/susPfV9.png)

1. 執行 rebase 指令。
```sh
git rebase -i  2163
```
2. 編輯器會跳出 commit 訊息。
```sh
pick e5950fd second.txt
pick 314139e third.txt
```

3. 要讓兩個 commit 融合，根據定義是要融合到「前一個」commit，所以我們是要讓 `third.txt` 融合到 `second.txt`，必須在 `314139e` 註記成 `s` 或是 `squash`。

```sh
pick e5950fd second.txt
s 314139e third.txt
```
> 注意，這個順序會跟線圖相反，線圖是 **上面** 的資料最新，但編輯器的順序 **下面** 的資料最新。

4. 關掉視窗，接著 Git 會在跳出 commit 編輯器給我們編輯訊息，內容會呈現所有要融合的 commit 訊息讓我們操作。  
一樣，`#` 是註解，我們可以把所有內容刪掉後，定義這個 **融合的版本** 要被記錄的訊息。
```sh
# This is a combination of 2 commits.
# This is the 1st commit message:

second.txt

# This is the commit message #2:

third.txt

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Thu Oct 5 12:10:37 2023 +0800
#
# interactive rebase in progress; onto 2163eb7
# Last commands done (2 commands done):
#    pick e5950fd second.txt
#    squash 314139e third.txt
# No commands remaining.
# You are currently rebasing branch 'master' on '2163eb7'.
#
# Changes to be committed:
#	new file:   second.txt
#	new file:   third.txt
#

```

5. 編輯訊息
![](https://i.imgur.com/CTYecPT.png)

5. 關閉視窗後，就完成啦
![](https://i.imgur.com/30lVpNH.png)



## 把一個 commit 拆成多個
這個操作如果一步一步解釋看了會很亂，先用簡單版的說明給大家有個概念：
1. 使用 rebase 互動模式。
2. 使用 reset mixed 模式把 commit 拆掉。
3. 依照需求看你要 commit 幾筆內容。
4. 執行 `git rebase --continue`，讓 rebase 跑完

> 如果你不知道什麼是 reset mixed 模式，可以參考[這篇文章](https://ithelp.ithome.com.tw/articles/10332631)


剛好上一個動作，我們把第二版跟第三版融合了，就用這個步驟拆回來吧！
![](https://i.imgur.com/30lVpNH.png)
 1. 執行 rebase 指令。
```sh
git rebase -i  2163
```
2. 編輯器會跳出 commit 訊息。
```sh
pick cff3df6 把第三版跟第二版融合
```
3. 把要拆的 commit 改成 `e` 或 `edit`，關閉視窗
```sh
e cff3df6 把第三版跟第二版融合
```

4. 在終端機執行 mixed reset，把這個 commit 給拆了
```sh
git reset HEAD^
```

5. 依照想要拆的內容，持續執行提交版本的行為，以我範例的例子，會有兩個檔案被拆出來，需要 commit 兩次
```sh
git add second.txt
git commit -m "second.txt"
```
```sh
git add third.txt
git commit -m "third.txt"
```
6. 執行 「繼續 rebase」 指令，就完成啦！
```
git rebase --continue
```

![](https://i.imgur.com/ZCEfs8P.png)


# 用 Fork GUI 操作 rebase 互動模式

## 刪除某個 commit 
假如要刪除 `second.txt` commit，請這麼操作：

1. 右鍵 => **Interactive Rebase...** => **Drop...**
![](https://i.imgur.com/HfIauQo.png)
2. 再跳出的視窗點 rebase 按鈕
![](https://i.imgur.com/CmRPENE.png)
3. 完成
![](https://i.imgur.com/b8uusba.png)

## 更改 commit 順序
假使要更改最後兩個 commit 的順序，那麼請這麼操作

1. 對著「更久之前」的 commit 點右鍵，選到 **Interactive Rebase...** => **Interactive Rebase 'XXX' to Here**
![](https://i.imgur.com/GRL4Nh8.png)

2. 在跳出來的視窗中操作順序，注意這裡的 commit 排序跟線圖一致，跟剛剛的說明有差。
![](https://i.imgur.com/Gw2fsBv.png)

3. 操作完之後，點 rebase 按鈕
![](https://i.imgur.com/OG0s9Sd.png)

4. 就能成功調換啦！
![](https://i.imgur.com/LKY2zo7.png)

## 修改歷史 commit 訊息
假設要更改到數第二次的 commit 訊息
1. 對著他 選到 `Reword`
![](https://i.imgur.com/gNNiG7m.png)

2. 接著會跳出一個視窗，讓我們編輯這個訊息
![](https://i.imgur.com/VjuFvbf.png)

3. 點 rebase
![](https://i.imgur.com/EMHZUHc.png)

4. 完成！
![](https://i.imgur.com/xJRfiiP.png)

## 編輯歷史 commit 的內容
輪到 `Edit` 了！

1. 對著要修改的 commit 選擇 `Edit`
![](https://i.imgur.com/UsutSHn.png)

2. 點 rebase
![](https://i.imgur.com/FuAmZi9.png)
3. 看你要對這個 commit 做什麼修改，這裡示範新增一個檔案好了
![](https://i.imgur.com/d79aHiX.png)
4. 新增完後，點 `Resolve` 
![](https://i.imgur.com/gFhn2Ba.png)

5. 再來就是單純的執行 **amend commit** 的操作了
![](https://i.imgur.com/apxnuvI.png)

6. 執行完後，右下角點繼續 Rebase 的按鈕
![](https://i.imgur.com/qp18aSC.png)

7. 完成！
![](https://i.imgur.com/t7olEzT.png)


## 把多個 commit 整合成一個 commit
前面有提到 rebase 互動模式，要整合多個 commit，是會把 commit 跟「前一個」commit 融合。


- 如果只是想融合兩個 commit
1. 直接對著 commit 選到互動模式的 **Squash into Parents**：
![](https://i.imgur.com/kUEoQoI.png)

2. GUI 會提示融合的方式，如果沒問題的話即可直接按 rebase
![](https://i.imgur.com/Mzdcjtp.png)

3. 完成！
![](https://i.imgur.com/syzH91t.png)

- 如果想一次整合好幾個，假設上圖的情境想整合最後三個 commit，代表我們要重新定義基底到第一個 commit。(如果看不懂這句話，請回到開頭看觀念)
1. 對著基底選擇互動模式
![](https://i.imgur.com/5DVW6E7.png)
2. 在視窗中把 commit 選成這樣，然後點 rebase
![](https://i.imgur.com/lSWLiqH.png)
3. 完成！
![](https://i.imgur.com/AeJyMKB.png)

## 把一個 commit 拆成多個
這個操作動作跟上面指令說的一樣
1. rebase 互動模式設定成 `edit`
2. mixed reset 上一版
3. commit 成多個
4. 點繼續 rebase

實際動作如下
1. 對著要拆的 commit 選擇 **Interactive Rebase** => **Edit...**
![](https://i.imgur.com/Ycu8pAM.png)

2. 點 Rebase
![](https://i.imgur.com/4b0aV9i.png)

3. 在 rebase 的狀態下，讓資料 `reset` 回去前一版，選擇 `Mixed`
![](https://i.imgur.com/hsHiMdY.png)
![](https://i.imgur.com/RuMjxxT.png)

4. 依照需求一版一版執行 commit 吧！
![](https://i.imgur.com/2AHClIp.png)

5. 都 commit 完之後，點 **Continue Rebase**
![](https://i.imgur.com/q4Ii062.png)

6. commit 成功拆成三個啦！
![](https://i.imgur.com/OEp9Tzn.png)
# 後記
rebase 修改歷史的指令憑良心說起來，真的很複雜，他不像過去介紹到的「基本指令」、「`reset` 指令」或是「分支指令」那樣，執行一個指令，動作就結束了。   

我一直覺得這個篇章很像「總複習」的感覺，部分動作使用到 `reset` 以及 `git commit --amemd`，如果這兩個指令的觀念不熟，再加上 rebase 的觀念雜湊在一起，那將會是一團混亂。

即使我把 rebase 可能會用到的功能都記錄下來，也不敢直接說「這是一個看起來很難，執行起來很簡單的指令」。  

建議讀者在使用之前，照著操作內容多多練習幾次，並且不斷思考操作的動作程序，特別是還需要跟其他指令搭配操作的情境。

在 Rebase 介紹完之後，還是必須提醒一件事：Rebase 過後的 commit ，ID 都會長得跟原本的不一樣！  

這也代表這篇文章所介紹的內容，盡量不要使用在「已經推上遠端的 commit」。  
因為這會是一個可能影響到他人的行為，細節之前的文章也提過好幾次，就不多說了！  

下一篇文章，預計來說說一個曾經讓我誤會用途的指令。

