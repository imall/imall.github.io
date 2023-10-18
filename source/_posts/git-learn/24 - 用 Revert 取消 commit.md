---
title: 用 revert 復原 commit
date: 2023-10-08 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言

話說在前頭，如果進到這篇文章的你還跟 Git 不太熟，目前的你執行了 `git commit` 之後反悔了，想要把它刪掉卻不知道怎麼刪的話，請左轉參考 [git reset 文章](https://ithelp.ithome.com.tw/articles/10332631)，可能比較符合你現階段想完成的事情。


因為 `git revert` 指令執行下去可能會跟你想的結果完全不一樣。


會以這樣的言論當作開場，就是因為筆者小弟不才本人我，在不熟 Git 的時期想要「刪除」最新的 commit，看到有個 `git revert` 可以取消 commit，糊里糊塗就給他用下去。

```sh
git revert HEAD
```

當時執行完之後，也不知道為什麼會跳出一個編輯視窗，甚至也不知道他的用意是啥，乾脆就直接關掉它，結果原本想刪除的 commit 沒被刪掉，反而又多出了一個 commit....

# git revert 介紹

**revert** ，中文翻譯叫做 **恢復**，也確實 `git revert` 是一個用來 「恢復 commit」 的指令，他的本意會把某個 commit 「反著做」。

如果一個 commit 原本新增了一個檔案，revert 這個 commit ，就會提交一個刪除這個檔案的 commit。

如果一個 commit 是編輯某個檔案的內容，revert 這個 commit ，就會提交一個復原編輯內容的 commit。


不過無論如何，`git revert` 都會「另外提交」一個版本來完成這件事，指令 **不會** 把原本 commit 給刪掉！！
# git revert 操作

目前的線圖長這樣，最新的 commit 只新增一個檔案：
![](https://i.imgur.com/sqbfpqk.png)

接著執行這個指令，告訴 Git 要復原最近一次的 commit：
```sh
git revert HEAD
```

此時終端機介面會跳出一個叫我們編輯文字的訊息提示：
```sh
$ git revert HEAD
hint: Waiting for your editor to close the file...
```

同時 Git 會自動跳出預設的文字編輯器，你可以選擇要不要編輯這個文字編輯器的內容，無論有沒有編輯，在確認之後請 **關閉** 這個視窗：

![](https://i.imgur.com/4c4lbjR.png)


回到線圖，就會看到多出一個 commit ，commit 的內容是把剛剛新增的檔案刪除了：
![](https://i.imgur.com/alXzaFZ.png)



通常如果真的需要用到 revert 指令，可能也不太會在乎 commit 訊息了，畢竟它唯一的用途就是拿來復原 commit 而已，我們可以下一個 `--no-edit`  參數要求直接執行 revert ，不要跳編輯器出來：

```sh
git revert HEAD --no-edit
```

![](https://i.imgur.com/cAKGAyN.png)

剛好這是 revert 一個 revert 的結果，也代表說我把資料刪了又還原回來。


# git revert 本質是「合併」

既然我們都可以用 `revert` 指令提交一個 「還原**目前**版本」 的 commit，就代表我們也可以拿 `revert` 指令提交一個「還原**過去**某個版本」的 commit。

用文字敘述很繞口，直接舉例子好了，一樣用最簡單的 `A` => `B` => `C`  三個版本來說。

我們剛剛做的事情都只有 「復原」最新版的 commit，就是把 `C` 做的事情還原回去，提交一個跟 `B` 的內容一樣的版本 `D`。

而開頭的解釋則是講說，我都能還原 `C` 了，那我能不能提交一個還原 `B` 的版本看看？

再更細一點的來說明
- A 版本新增 `1.txt` 、`2.txt` 兩個檔案
- B 版本**編輯** `1.txt` 、`2.txt` 兩個檔案
- C 版本**編輯** `1.txt` 、`2.txt` 兩個檔案

我們試著用 `git revert` 執行 B 版本看看會發生什麼事情：

1. 先給大家看剛剛情境的版本線圖：
![](https://i.imgur.com/uDimUQw.png)

2. 執行這個指令，讓還原的資料指到版本 B
```sh
git revert HEAD^ --no-edit
```

3. 結果「復原 `版本 B` 的內容」 跟 `版本 C` 衝突了！！
```sh
$ git revert HEAD^ --no-edit
Auto-merging 1.txt
CONFLICT (content): Merge conflict in 1.txt
Auto-merging 2.txt
CONFLICT (content): Merge conflict in 2.txt
error: could not revert b2b06c8... 版本B
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git revert --continue".
hint: You can instead skip this commit with "git revert --skip".
hint: To abort and get back to the state before "git revert",   
hint: run "git revert --abort".
```


能理解原因嗎？

來敘述一下衝突的原因吧！  
上面 `revert` 指令應該是要提交「把 `版本B` 還原的內容」，提交的過程還要跟當下最新版的 `版本 C` 比對並且進行 **合併**，結果卻發現這兩個版本內容有衝突，就會造成上面出現的狀況了。


拿其中一個檔案舉例就好：
`版本B` 編輯 `1.txt` 的第一行文字，代表這個檔案在 `版本B` 紀錄了一行字了。

接著我繼續在 `版本C` 編輯 `1.txt` 的第一行文字。


因為當初 `版本B` 讓檔案從「沒內容」 變成「多了一行字」，`git revert 版本B` 就是要 commit 一個 `1.txt` 是「沒內容」的版本。

但是 `版本C` 已經把 `版本B` 的資料改掉了阿！  
Git 想要保留版本C的資料，但又需要 commit 一個沒內容的資料，只好丟衝突問我們到底是要保留版本 C 的內容，還是保留「復原 `版本 B` 的內容」了。

此時的 `1.txt` 檔案長這樣： (`2.txt`長得一樣，就不貼了)
![](https://i.imgur.com/0lnBfet.png)

解決衝突的方式就跟之前提到 `git rebase` 衝突很像，同時剛剛的訊息提示也告訴我們了
```sh
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git revert --continue".
```

1. 解決衝突
2. 用 `git add` 指令 mark 這些檔案
3. 執行 `git revert --continue`
4. 此時會跳出文字編輯器，要求編輯這個 `revert` 的訊息。  
![](https://i.imgur.com/9NRwFrY.png)
5. 編輯完，關掉就完成衝突處理了！
![](https://i.imgur.com/tuM67oD.png)

# 用 Fork GUI 操作 Revert

觀念在上面都說明了，就不多說了。  
要使用 Fork GUI 操作 Revert ，對著想 revert 的 commit 點右鍵，找到這個選項：
![](https://i.imgur.com/N51d9V1.png)

系統會跳出這個視窗，點 `revert` 按鈕，就完成了：
![](https://i.imgur.com/0OxmYol.png)


# 常見問題
## 1. 什麼時候會用到 git revert 指令？
- 不方便 reset 的時候。   

有一個觀念不斷在強調，如果已經推到遠端的內容，除非你很確定不會影響到別人，不然不建議隨意修改。  

如果你需要反悔目前的版本，但這個版本又已經推到遠端，就很建議使用 `revert` 提交一個復原的 commit 再推到遠端。


- 想復原的資料有點多的時候。

其實如果不方便 `reset` ，想要反悔的資料也可以手動還原後，再 `git commit` 一個版本，一樣也能達成「復原」資料的效果。  

不過如果需要復原的資料太多，又散落在好幾的檔案，手動復原有可能那邊沒改到，這邊又沒調到，這種狀況直接使用 `revert` 指令可能會更方便一些。

## 2. git revert 很常會用到嗎？
這個問題，要看個人習慣。

我們已經知道 git revert 會提交一個新的版本來復原 commit，那麼無論我是想復原目前的 commit 也好，或是更早之前的 commit 也好，其實我可以手動編輯想復原的內容，然後直接用 `git commit` 指令來處理也是一個辦法，不一定要使用 `git revert` 指令。

在我們認識的 Git 功能越來越多之後，我常常提及的那個觀念就變得越發重要了：
:::info no-icon
Git 只是一個工具，什麼時候該用什麼功能，是我們要自己決定的！
:::