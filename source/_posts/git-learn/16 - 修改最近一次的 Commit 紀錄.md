---
title: 修改最近一次的 Commit 紀錄
date: 2023-09-30 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
有時候我們在執行完 commit 之後，發現有一些「小地方」應該也要記錄在同一個版本，或是發現版本訊息有錯字想要改一下，這些都算是很常見的狀況，畢竟只要是人都一定會有小疏失。

**「雖然」** 之前把 Commit 訊息比喻成訂單紀錄，在網購世界想操作歷史訂單，只要找到特定訂單就能修改。

不過在 Git 的世界，除了最新一筆的 Commit 之外， 沒辦法 「單獨修改」 其中一個 Commit 。

原因其實也很簡單：每個 Commit 都是緊緊相依的！  
Git 的 Commit 視同「資料的變化」，每一版的「變化」都是跟「上一版」相比，更動歷史的 Commit 的行為，很有可能牽一髮而動全身，一個不小心就是所有版本全部壞掉，這句話沒有在浮誇，真的就是會這樣。

值得慶幸的是，最新的 Commit 之後已經沒有任何 Commit ~~(廢話)~~ ，直接操作它，並不會對任何 Commit 帶來影響，畢竟根本沒東西可以給它影響。


進入今天的主題：
:::info no-icon
修改最近一次的 Commit
:::

# 使用 --amend 來修改最近一次的 Commit

老樣子，GUI 派的使用者請跳到 Fork 修改的內容

## 用指令修改最近一次的 Commit

想修改最近一次的 commit  ， Git 提供我們一個叫 `--amend` 的參數使用

說文解字一下：**amend** 中文是 「修正」。

由於是用來修正「最近一次」的 commit，所以撰寫位置會在我們已經很熟的 `git commit` 之後：

```sh
git commit --amend
```

後續的內容都會以這串指令為基底增加東西。

### 修改最近一次 Commit 的訊息
要更新最近一次 commit 的「標題訊息」，只要在 `git commit --amend` 之後，繼續接著寫 `-m` 加 **要改的訊息** 即可：

```sh
git commit --amend -m "新的訊息"
```



假設目前的最新訊息長這樣：  
![](https://i.imgur.com/s1U5kBo.png)

接著執行這個指令：
```sh
git commit --amend -m "這是我改完的新訊息"
```

訊息就會變成這樣，讚吧 (?)：  
![](https://i.imgur.com/PA8sKLU.png)


### 修改最近一次 Commit 的檔案
這個操作應該算是開發時會  **「很常用」** 的功能！  

舉例來說：在 commit 一個版本之後，發現某個檔案 
 `console.log()` 沒刪到，或是某個排版看了不滿意，就可以用下列說明步驟來修改：

1. 直接編輯要想修改的檔案。(此時檔案會處在 **未暫存** 的狀態)  
2. 使用 `git add` 把檔案加到暫存區。
```sh
git add "修改好的檔案"
```
3. 執行這個指令：  
```sh
git commit --amend --no-edit
```
這裡的 `--no-edit` 參數，用意是告訴 Git，這次的修改沒有要輸入訊息，這樣 Git 就不會跳出視窗要求我們修改訊息了。

### 移除最近一次 Commit 中的檔案
其實 `--amend` 的本意是拿來讓我們 **補資料** ，它並沒有辦法讓我們直接移除某一個檔案，如果想用指令處理這個需求，並不建議使用 `--amend` ，而是直接用 [reset 指令](https://ithelp.ithome.com.tw/articles/10332631)。

如果堅決想使用 `--amend` 來操作，其實也還是需要搭配 `reset` 指令繞道而行。 

示範一下操作步驟：

1. 使用 `git reset` 將上一個版本的檔案狀況重置回索引 ：  
```sh
git reset HEAD^ 想移除的檔案名稱
```
2. 執行 `git commit --amend --no-edit` 修正最近一次的 commit。

```sh
git commit --amend --no-edit
```

~~我知道有人腦袋中已經跑過千萬頭草尼馬，快要停止運轉了，~~ 我來解釋解釋：

因為沒辦法直接執行 `--amend` 刪除 commit 的某個檔案 ，我們需要「繞一圈」處理。  

假設目前有  `A` => `B` => `C` 三個 commit，`C` 裡面的 `readme.txt` 是我想刪除的。   
那我可以把 `B` 所記錄的 `readme.txt` 放到暫存區，再透過 `git commit --amend --no-edit`，把這個狀態存到 `C`。

由於 Git 是用「比較差異」的方式來記錄新一版的資料 (俗稱差異備份) ，這時 `B` 跟 `C` 兩個版本的 `readme.txt` 又沒有任何差異，這樣 `C` 就不會顯示 `readme.txt` 了。

整個動作看起來是這樣：  
1. 發現 **add style.css** commit 出現一個 `readme.txt` 想把它刪掉。
![](https://i.imgur.com/oxCZcsB.png)

2. 執行 reset 指令把上一個版本的 `readme.txt` 狀態還原到暫存區：  
```sh
$ git reset HEAD^ readme.txt  #  HEAD^ 表示目前位置的前一個版本
Unstaged changes after reset:
M       readme.txt
```
此時查看 `git status` ，會在 **Changes to be committed** 區塊看到一個 `readme.txt` 檔案，它記錄著「上一版」 `readme.txt` 的內容：
```sh
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt  
```

3. 將這個暫存狀態存回最近一次的 commit：
```sh
git commit --amend --no-edit
```

4. 此時有兩個地方會發生變化： 
- 最新的 commit 中的 `readme.txt` 檔案已經不見了。
- 工作目錄的 `readme.txt` 保留著原本存在 commit 的資料，並且處於「未暫存」的狀態。
![](https://i.imgur.com/arQNd7G.png)


如果立志要成為指令派玩家(?)的你完全搞不懂為什麼可以這樣操作，還是建議參考預計在下一篇提到的 [reset 指令](https://ithelp.ithome.com.tw/articles/10332631) 來處理。


或者可以繼續閱讀 Fork GUI 的說明，這個行為用 Fork GUI 操作，超~ 方~ 便~

## 用 Fork GUI 修改最近一次的 Commit
雖然我是指令派使用者，但還是必須說：用 Fork 操作近一次的 Commit 真的很直覺！  

首先提一下，只要是「修改」工作目錄的行為，都要記得到左上角切換到 **Local Changes** 的內容：
![Alt text](https://i.imgur.com/lpkbyhF.png)

其他詳細操作，我們直接給他看下去！

### 修改最近一次 Commit 的訊息
先截圖讓大家看看，目前最近一次的訊息叫 `add style.css`
![](https://i.imgur.com/4LJXGFn.png)

要更改訊息，請執行這幾個步驟：

1. 切換到 **Local Changes** 
2. 勾選視窗右下角的 **Amend** 
3. 修改訊息
4. 點選 **Amend Last Commit**   

詳細操作請參考圖片：
![](https://i.imgur.com/MC3MMOx.gif)


### 修改最近一次 Commit 的檔案
想要用 Fork 把修改的檔案紀錄回最近一次的 Commit ，動作跟原本 Commit 的行為大同小異，唯一差別就只有 **Amend** 要打勾而已。  

1. 直接修改工作目錄的檔案
2. 將修改好的檔案加入暫存區
3. 勾選視窗右下角的 **Amend**
4. 點選 **Amend Last Commit**   

詳細操作請參考圖片：  
![](https://i.imgur.com/sYwWdt6.gif)

### 移除最近一次 Commit 的檔案
用 Fork 移除某個版本的資料，有幾個步驟要做：
1. 把 **Amend** 打勾
2. 把檔案從 **暫存區** 移到 **未暫存區**
3. 點選 **Amend Last Commit** 。

直接來做示範，把最近一次的版本中的 `readme.txt` 移掉：
![](https://i.imgur.com/Svf8EgZ.gif)

注意到一件事情，當移除完 `readme.txt` 之後，由於資料還是保留在 **未暫存區**，並沒有遺失，如果有需要，還可以直接將他提交一個新版本：
![](https://i.imgur.com/yVPJ8LV.gif)

如果你是因為我上面寫的那句話而跑來看這段的指令派玩家，歡迎進入 GUI 的世界 XDD！


# 補充
## 修改歷史訊息的注意事項
使用 `--amend` 參數有一個重點在文章中沒有特別提到：
:::info no-icon
每次執行 `git commit --amend` ，Git 都會更改 CommitID！
:::

即便我們看起來好像只是「修改」最新一次的 Commit，不過這個 Commit 對 Git 來說已經是全新的 Commit 了。

你可能會感覺疑惑：這樣有差嗎，反正資料對就好啦？  

如果只是在地端操作版控，那這個 CommitID 被改幾次都無所謂，不過當地端的資料已經上傳到遠端 (註1) 之後，這個異動可能造成幾種影響：

1. 地端儲存庫的資料無法推回遠端：  
因為遠端儲存庫的 CommitID 跟地端儲存庫 CommitID 不一致，Git 直接報衝突，要求我們必須先同步兩邊的資料才能執行。

2. 強制推回遠端，可能會造成團隊成員的困擾：  
因為原本遠端的資料可能已經被團隊成員複製回電腦使用了，把不一樣的 CommitID 強制推回遠端的行為，等同於讓全部團隊成員都面臨 **第一點** 的問題。


那所以應該怎麼避免？

最簡單的方式，是跟團隊人員溝通好分支的操作行為 (註2)，未來會介紹到分支的操作，每個人在開發功能的時候，理應在自己的分支進行，而不是直接操作專案的「主要分支」。  
上面說到會造成別人困擾的行為，就是直接更動「主要分支」的 Commit 之後，強制上傳回遠端，因為每個人的電腦一定都存有主要分支，這種行為會直接影響到所有人的版控。  
不過如果只是在「自己的分支」修改內容，把資料強制推回到遠端，由於沒有人會去亂動自己的分支，這個行為就不會影響到其他人開發了。


> 註1. 雖然目前尚未說明如何將地端與遠端的儲存庫進行同步，不過既然已經聊到「修改歷史 Commit」 的議題，就不得不把這個 **重要觀念** 一起說明。   
> 註2. 進行團隊版控除了要開立自己分支外，可能還會牽扯到「Git Flow」、「GitHub Flow」之類的專案版控模式，這些都是基於「分支操作」的合作模式。
