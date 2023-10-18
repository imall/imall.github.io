---
title: 開始使用 Git 進行版控
date: 2023-09-21 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---


# 前言
歷經那麼多天的廢話，終於可以進入重頭戲，來學習怎麼發布一個版本吧！

記得在[建立 Git 儲存庫](https://ithelp.ithome.com.tw/articles/10321301)的文章中有提到，如果要發布一個版本，會歷經把檔案放到 **暫存區**，再把 **暫存區** 的檔案提交到 **儲存庫** 兩個階段。

言下之意，我們會使用「兩個」指令來提交一個版本。

老樣子，指令介紹完再說明 Fork GUI 操作。

> 給立志成為指令派的讀者：在  Fork GUI  最後有補充一個指令細節，建議也可以讀一下 Fork 的部分。

# 提交一個版本
## 第一步：將檔案加到暫存區
初始化儲存庫(`git init`)之後，即便已經有了地端儲存庫 (`.git`)，Git 也不會主動「暫存」資料夾中的任何檔案，包含初始化之前就有的檔案。

這個概念就像商家進貨新商品，頂多是跟你推銷有新東西，但商家沒有權力(也沒道理)直接把新商品加到你的購物車，你如果想購買什麼商品，就必須自己先把商品加到購物車再說。

回到 Git 的世界：  
要發佈一個版本的第一步：**把「檔案」加到「暫存區」。**


### 透過指令將檔案加入暫存區
我們可以透過這個的指令告訴 Git：「我想要把『檔案』加入『暫存區』！」

```sh
git add 檔案名稱
```
如果是「資料夾內的所有內容」都要版控，也可以直接放「資料夾名稱」：
```sh
git add 資料夾名稱
```

有時候我們很貪心(?)，希望一次打包工作目錄「所有內容」，可以直接執行下面這個指令，注意有個 「`.`」。

```sh
git add .
```

然後就做完了，加入暫存區的行為，如同點選「加入購物車」按鈕一樣，很快 XD。

### 透過 Fork 將檔案加入暫存
如果資料夾中有 **未暫存的檔案** (還沒加到暫存區的檔案) ，Fork 會讓檔案陳列在 **Local Changes** 裡面的 **Unstaged** 區塊內。  
  
![未暫存區示意](https://i.imgur.com/f5rB85g.png)

在 Fork GUI 中，把檔案加入暫存的行為，就是要把檔案移到 **Staged** 區塊。

有幾種方式可以達成這個目的：
1. 選擇檔案後，再點選 **Stage** 按鈕。  
![加入暫存示意](https://i.imgur.com/za47xSR.gif)

2. 直接把檔案拉到 **Staged** 區塊。  
![加入暫存示意](https://i.imgur.com/X7MV2v6.gif)

一次把所有檔案暫存區，也有兩種方式：
1. 直接點選區塊上方的 **「`︾`」**。  
![加入暫存示意](https://i.imgur.com/Zu2pyyo.gif)

2. 點擊第一個檔案後，「按著鍵盤 `shift` 鍵，點擊最後一個檔案」，最後按 **Staged** 按鈕。  
![加入暫存示意](https://i.imgur.com/crd7jmp.gif)


以上是 Fork GUI 將檔案加入暫存區的方法。 ~~越看越像加入購物車~~
## 第二步：提交 (commit) 一個新的版本
**commit**，有人會照著字面翻譯成「認可」，也有人解釋成 「提交」、「發佈」或「進版」。無論說法如何，都是要把「暫存區」的資料存到「儲存庫」的行為。

用網購的例子來解釋，就是購物車已經有商品了，要去執行「下單結帳」的動作，這個動作完成之後，商品最終會被記錄在「已購買清單」資訊，也就是 Git 的儲存庫。

### 透過指令提交版本
用指令來提交版本有兩種做法，雖然都是同一個指令，但操作上會不太一樣：
1. 執行 commit 指令，同時輸入標題訊息
```sh
git commit -m "版本標題訊息"
```
2. 執行 commit 指令，在「文字編輯器」輸入標題訊息
```sh
git commit
```
這兩者的差異在於，用第一種方式在輸入完指令之後，提交版本的行為就結束了。

如果執行第二種方式的指令，Git 會直接跳出「文字編輯器」給我們輸入標題資訊。  

Git 預設的文字編輯器是 `Vim`，它的操作方式對新手來說可能沒那麼直覺，建議可以參考 [Git 初始化設定](https://ithelp.ithome.com.tw/articles/10323191) 文章的說明，換成熟悉的編輯器，例如 `Visual Studio Code` 之類，畢竟時代已經不同了：
![vi vs visual stidio code](https://i.imgur.com/oAvHoRr.png)
#### 執行 commit 指令，同時輸入標題訊息
把檔案加入暫存區後，再執行這個指令，暫存區的檔案就會一次被記錄在同一個版本中：
```sh
git commit -m "版本標題訊息"
```
`-m`(message) 參數的用意，是為了直接在指令中寫「版本標題訊息」，這個「版本標題訊息」的用意，是要讓我們有辦法一眼看出當初發布的這個版本做了什麼事情。

訊息想要寫中文還是英文都是完全沒問題的：
```sh
git commit -m "第一次Commit"
```

訊息兩側的雙引號(`""`)也可以省略：
```sh
git commit -m 第一次Commit
```

**必須使用**雙引號的狀況，是訊息內容含有「空白字元」時才需要，例如這樣：
```sh
git commit -m "第  一  次  C o m m i t ！ ！ ！"
```
**如果訊息包含`空白鍵`卻沒有包`雙引號`，指令會`無效`而跳出錯誤：**
```sh
$ git commit -m 沒 加 雙 引 號
error: pathspec '加' did not match any file(s) known to git
error: pathspec '雙' did not match any file(s) known to git
error: pathspec '引' did not match any file(s) known to git
error: pathspec '號' did not match any file(s) known to git
```

如果真的忘記加雙引號而跳錯誤，其實就再重新一次正確的指令語法即可，不過我們還是養成好習慣，執行 `git commit -m` 時，一律加上雙引號吧！

> **補充：**  
> 筆者在好幾年前曾使用單引號(`''`)代替雙引號(`""`)使用也是會有問題，不過新版的 Git 貌似已經支援單引號包覆訊息，目前測試起來兩者都能使用，大家可以稍微留意。

#### 執行 commit 指令，在文字編輯器輸入標題訊息
不管是有意還是無意，如果只輸入 `git commit` 就按了「確定鍵」， Git 會跳出設定好的「文字編輯器」，同時在終端機提示我們把「版本標題訊息」寫到文字編輯器裡：

`Waiting for your editor to close the file`：提示我們輸入完標題訊息後，**「關掉」**檔案。
```sh
$ git commit
hint: Waiting for your editor to close the file...
```
此時的編輯器內容會長這樣 (第一行的空白不是我多打的，是真的就長這樣)：
```sh

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
#
# Initial commit
#
# Changes to be committed:
#	new file:   index.html
#	new file:   readme.txt
#
```

可以依照下面的步驟來提交一個版本
1. 在編輯器中寫入版本標題訊息 (`#` 後面都是註解，可以刪掉，也可以不管它)。
2. 存檔、關閉視窗。
3. 看到終端機跳出類似下方的訊息，代表 **版本發佈完成**。

```sh
$ git commit
[master (root-commit) de6d6f3] 第一次 Commit     
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 index.html
 create mode 100644 readme.txt
```
備註：
如果沒有輸入任何訊息就關掉視窗，Git 會當做沒有要發佈版本而在終端機呈現`Aborting commit due to empty commit message.`的資訊，代表放棄這次的 commit：
```sh
$ git commit
Aborting commit due to empty commit message.
```

### 透過 Fork 提交版本

1. 確定暫存區 (`Staged`) 的資料正確。
2. 找到 **Commit Subject** 與 **Description**  輸入框。  
![暫存區、輸入文字區塊示意](https://i.imgur.com/maEu8b1.png)

2. 在輸入框寫入訊息，並點選 **commit  Files** 按鈕。  
![輸入訊息示意](https://i.imgur.com/7nlho3Y.png)

3. 完成之後，點選視窗左邊 **All Commits** 選項，就能看到第一次的 commit。  
![查看線圖示意](https://i.imgur.com/LPIIbfC.png)


眼尖的讀者可能有注意到，**怎麼 Fork 有 Description，但指令沒教！！**  

其實輸入指令提交版本時，很少(註1)會去寫「描述」(Description)，不過既然 Fork 出現了，就補充一下：

### 透過指令提交版本時，同時輸入標題 (Title) 與描述 (Description)
如果要輸入標題與描述，我們會用到二個 `-m` 來表示，指令如下：
```sh
git commit -m "版本標題訊息" -m "對於訊息更詳細的描述"
```

> 註 1. 記得前幾篇文章曾貼出了一張 Git 線圖。  
圖中看到的訊息其實只有 Title，即使有寫 Description 也不會「攤」出來顯示，也因為 Title 資訊比較容易「一覽」，筆者幾乎只會輸入 Title。  
不過發佈版本到底要不要寫 Description，就看個人或是團隊的習慣了。
>
> ![Alt text](https://i.imgur.com/U0MtaG6.png)  


## 一次完成 add 跟 commit 動作 

一般來說，已經將檔案存到儲存庫之後，這些檔案如果有修改而需要重新更版的話，就必須重新執行這兩個動作：先 `git add` 檔案後，再執行 `git commit` 。

就好像同一個商品你想再買不同款式，必須重新把商品加入購物車，再次下單一樣。

不過工程師是一個懶惰的生物，每次都要分兩步好麻煩！


**已經加入儲存庫** 的檔案，於修改後要再上一版，可以在 commit 指令中，多加一個 `-a` 參數，就可以省略 `add` 的行為。
```sh
git commit -a -m "版本標題訊息"
```
這兩個參數也可以合併在一起寫：
```sh
git commit -am "版本標題訊息"
```

不過要再次強調的是，這適用於「已經 Commit 過的檔案」，剛新增的檔案，由於儲存庫沒有紀錄，是不能跳過「加入暫存區」的行為哦！千萬要注意！

### 透過 VSCode 一次完成
如果你也是 VSCode 使用者，可以玩玩它預設已經有的 Git 功能：

1. 先點到左邊選單中的「Git」圖案。
2. 修改的檔案會出現在「變更」的項目中。
3. 可以直接在輸入框寫好「版本標題訊息」後，點選「提交」按鈕。  
![vscode 提交示意](https://i.imgur.com/G7gG6SS.png)
4. 此時應該會看到一個提示訊息告訴我們「更動的檔案尚未加到暫存區」之類的  
(我已經叫他不要顯示，所以沒圖可以截...)  
5. 點選確認後就能自動提交一個版本。



其實更正規的做法應該是在「變更」選單中，將所有檔案先加到暫存，最後才執行提交，也就是要先「加入購物車」，才能「下單」的程序。  
![加入暫存區](https://i.imgur.com/lqgu83h.png)


在 Visual Studio Code 中，即使是沒加到儲存庫的檔案，透過「提交按鈕」也可以直接省略「加入暫存區」的行為。  

為此筆者才會不斷強調，無論想操作指令或 GUI，都 **一定要** 先理解 Git 的觀念，才不會在版控流程中迷失了方向，也比較不會被不同的 GUI 設計流程所困擾。


### 透過 Fork 一次提交
恩... Fork 沒有一次完成的功能，乖乖地先把檔案加到暫存區，再 commit 吧！


# 常見問題

## 1. 所以 commit 完，檔案就存好了?
對，這樣就存好了。

我懂一個「感覺」的問題，因為執行 commit 之後，好像「看不到」東西，很沒有真實感。

不過要知道 Git 版控的模式並不是「複製」實體檔案，而是把檔案「編碼」紀錄在 `.git` 隱藏夾中。

這種紀錄方式用「肉眼」確實不好辨別，我們才會需要「Git 線圖」來確認我們的版本資訊，像這樣：  
![Alt text](https://i.imgur.com/XnydrnR.png)

## 2. 不是阿...不是應該在 GitHub 出現檔案才叫發佈一個版本嗎？！你是不是只教一半啊？
沒有，我真的教完了。  ~~不信你打我啊~~   沒..沒事...我開玩笑的...

回到觀念，分散式版控系統在「地端儲存庫」就能獨立完成版控，不用連到「遠端」。

你的工作目錄因為執行 `git init` 已經建立好「地端儲存庫」，而 GitHub 本質是「遠端儲存庫」，這兩個東西完全可以分開來看。

在「地端儲存庫」發佈版本的行為，在 commit 指令完成後，就結束了。

> 關於「地端」與「遠端」資料的同步，之後會專門寫幾篇文章來跟各位講解。

# 總結
這篇文章寫了很多 ~~廢話~~ 內容 ，不過重點只有一個：學會把檔案提交成一個版本。

怎麼發布？兩個步驟：
1. 執行 `git add` 將檔案加到「暫存區」。
2. 執行 `git commit` 將檔案加入「儲存庫」。

打完收工。