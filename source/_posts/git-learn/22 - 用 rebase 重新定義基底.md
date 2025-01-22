---
title: 用 rebase 重新定義基底
date: 2023-10-06 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
Rebase 如果直接翻譯，意思會是「重新定義基底」。   

行為上就有點像是花草樹木長得太亂，要重新修剪嫁接的感覺。  

以這個特性來說，我們也可以做到類似分支合併的操作。

比起用文字說明，直接來看執行 rebase 的效果，可能更能體會 rebase 的意義。

老樣子，前面講指令，後面講 Fork GUI。

# git rebase 執行效果示範
![](https://i.imgur.com/OzMWXnF.png)

這是目前分支的狀況，`master` 合併了 `Emilia` 分支，尚未合併 `Rem` 分支。

此時我停在 `Rem` ，並且執行這個指令：
```sh
git rebase master
```

終端機會跑出成功 rebase 的訊息
```sh
$ git rebase master
Successfully rebased and updated refs/heads/Rem.
```

查看一下線圖，會變成這樣：
![](https://i.imgur.com/MyornJk.png)

把兩張圖拿來做比較，可以很明顯得看出，原本 `Rem` 分支的 commit 應該是接在 **initial commit** 上，執行 `git rebase master` 之後，`Rem` 的基底被「重新定義」了，他被定義到 `master` 分支上。

![](https://i.imgur.com/lNhz2Iy.png)

同時再注意一件事，`Rem` 原本的兩個 commitID，都因為 `rebase` 的關係而長得不一樣了。

我們知道 Git 每執行一次 commit ，就會產生唯一的 CommitID。  
因此我們可以得知 rebase 指令會做兩件事：
1. 對分支重新定義基底
2. 重新定義基底的方式，是把分支所有 commit 都重新 commit 一次！



# 如果已經被合併的分支執行 Rebase
上面的範例是分支尚未被合併的狀況下執行 `rebase`，我們已經知道分支會被連根拔起，重新定義分支的「根」。

這裡來試試看，已經被別人合併的分支，執行 rebase 會發生的事情。

情境一樣是這個線圖，只是目前我人在 `Emilia` 分支上：
![](https://i.imgur.com/bC0Lllv.png)

一樣執行 `rebase` 指令把基底定義到 `master`：  
```sh
git rebase master
```

這種情況，就好像把分支撕起來貼到 master 一樣，線圖沒有異動，分支則是移動到跟 master 一樣的位置上。
![](https://i.imgur.com/U4LwifQ.png)

# rebase 衝突處理
由於每個 commit 一定是跟之前的 commit 進行差異比較，這也代表 rebase 這樣隨便移動 commit 的行為，是有機會產生衝突的。  

在分支衝突的章節有提到，會發生衝突的原因，是因為兩個分支的 commit 編輯了同一個檔案的同一行文字。

rebase 會發生衝突也是一樣的道理。

衝突情境就先不要想得太複雜，我們就在第一個 commit 建立一個分支 `dev`，並且在兩個分支都編輯 **同一個檔案** 的第一行後，各自 commit 一個版本。
![](https://i.imgur.com/NINx58D.png)

此時任一分支 rebase 到另一個分支，都已經達成衝突的條件。

因為對 Git 來說，這兩個分支都是因為編輯 `68b06bc` 的 `readme.txt` 檔案而多出一個版本，沒有誰有辦法取代誰。

所以當我在 `dev` 分支執行 `git rebase master` 指令後，衝突即將發生：
![](https://i.imgur.com/tz5WbA2.png)

上面的 `error:` 錯誤訊息表示，沒辦法處理 `e99421b` 這個 commit，原因就是這個 commit 如果接過去 master ，他會跟 master 的內容發生衝突。


解決衝突的方式，其實 Git 已經告訴我們了，你有三種做法可以處理衝突：

## 選擇一：面對他 

1. 手動處理有衝突的檔案，做法跟處理 `git merge` 衝突的第一步一樣，把衝突符號 (`<`、`>`、`=`) 刪除，只留下你希望這個版本存在的內容。

2. 執行指令將檔案加到暫存區
```sh
git add 檔案名稱
```


3. 執行指令繼續 rebase
```sh
git rebase --continue
```



4. 當你執行完 `git rebase --continue` 之後，Git 會跳出一個視窗，請你輸入解決衝突的訊息。
![](https://i.imgur.com/xYbf2oj.png)

5. 編輯結束(或是不想編輯)，請直接 **關掉** 這個視窗

6. 如果這個 commit 之後還有 commit ，那 Git 會繼續跑 rebase 的流程，判斷是否還有衝突會發生。  
不過如果這已經是最後一個 commit ，就會結束 rebase 的流程。
![](https://i.imgur.com/gH7DiMq.png)


會需要自己寫 commit 內容的原因，就如同我們合併衝突一樣，因為這個 commit 已經不是原本的狀態，Git 認為我們應該對這種有衝突的 commit 重新說明一下 commit 的內容。


## 選擇二：跳過他
當發生衝突之後，你也可以選擇「我就不要這個 commit 了」(注意這裡是不要 commit ，不是放棄 rebase)

```sh
git rebase --skip
```

執行完之後，線圖會如同下圖左上角那樣呈現
![](https://i.imgur.com/pz674sF.png)


注意到我們原本應該會有三個 commit：
![](https://i.imgur.com/NINx58D.png)

結果執行了 `skip` 之後，發生衝突的 commit 直接被「跳過」了，當作這個 commit 不存在一樣。

## 選擇三：放棄他
就像分支合併篇介紹到的衝突解決方式一樣，當發生的衝突一時之間沒辦法解決，最保險的做法就是放棄他。

```sh
git rebase --abort
```

![](https://i.imgur.com/tjxwXpV.png)

執行這個指令，就好像從來沒執行過 rebase 一樣，會讓一切回到原點！

# 用 Fork GUI 執行 rebase
其實也沒什麼學問....
假如我要把 `Rem` 分支 rebase 到 master 分支，對著他點右鍵就能找到 rebase 選項

![](https://i.imgur.com/Ancmg9T.png)

說明文字還會很貼心的說，把 `Rem` 分支 rebase 到這邊，擔心你切錯分支，造成難以彌補的錯誤。
(其實也不會難以彌補，用 reset 篇章學到的「那招」就可以反悔了 XD)

選項點下去之後，如果沒有衝突，就結束了：
![](https://i.imgur.com/E5l18XT.png)

# 用 Fork GUI 解決 rebase 衝突

衝突的例子一樣用上面指令的例子示範，當 rebase 發生衝突， Fork 會把錯誤訊息噴出來給我們看：
![](https://i.imgur.com/FyiFQoP.png)

這段錯誤訊息，就是執行指令衝突時，會跑出來的錯誤訊息。

接著請到這裡選擇
1. Resolve ：你想要 **解決** 衝突
2. Abort： 你想要 **放棄** rebase

![](https://i.imgur.com/FEnZ1Y6.png)

放棄就不贅述了，點下去就當作 rebase 的動作沒有發生過。

而如果要解決衝突的話，GUI 會跑出他的衝突解決介面：
![](https://i.imgur.com/SHzce3E.png)

操作方法一樣，選擇一個你想要的版本，或是兩個版本全都要。  

如果在上面畫面中，直接點選 `merge` 按鈕， GUI 會再跳出一個介面給我們編輯資料順序：
![](https://i.imgur.com/6CdHDFz.png)

如果選了之後不滿意想移除，可以把滑鼠一到文字上，他會跳出 remove 按鈕給我們移除：
![](https://i.imgur.com/WxLFDep.png)

都沒問題的話，點選右下角 **Resolve** 按鈕：
![](https://i.imgur.com/JKIhSoN.png)

確認合併內容都沒問題，就點右下角 **Continue Rebasee** 按鈕：
![](https://i.imgur.com/TMTxcSo.png)

沒意外的話就完成囉！
![](https://i.imgur.com/nyjS1RK.png)

# 常見問題

## 1. Rebase 後悔了可以復原嗎？
可以。

這問題剛剛劇透了，用 reset 篇章教到的指令就可以復原了。

```sh
git reset -hard ORIG_HEAD
```

## 2. 什麼時候會用到 rebase 阿？
例如最一開始的兩個例子。

### 情境一：你要把別的資料更新回自己分支的時候

假如你今天跟大家一起開發需求，結果你卡了一段時間還沒完成，過程中所有合作夥伴已經把分支合併回主要分支，你是最後一個要合併的人。  
![](https://i.imgur.com/OzMWXnF.png)

此時你的分支已經不處於最新狀況了，如果等你開發完再合併回去，很有可能蓋掉別人的資料。  

這時候你就可以用 rebase 先把自己的分支重新定義基底到「主要分支上頭」
![](https://i.imgur.com/MyornJk.png)
等你開發完之後，合併到主要分支時，就可以避免掉衝突。  

不過事實上這個需求，你也可以在自己的分支，執行 `git merge 主要分支` 指令，先把別人的資料合併回來後，再繼續開發。

而且從上面的圖應該不難發現，rebase 指令跟 merge 指令取得資料所出現的線圖會長得不一樣。   

這裡示範一下一樣的情境使用 `merge master` 會出現的線圖：
![](https://i.imgur.com/CRznwuU.png)

而 `rebase` 出來的線圖是不是符合團隊的線圖標準，也是一個需要確認的問題。  

從這個問題的處理方式也帶到一個之前常說的觀念：**Git 只是一個工具，該怎麼使用他，是我們自己要去定義的。**  


## 情境二：你開發的分支已經被主要分支合併回去了，你想要重新利用這支分支開發新功能

假設你的分支已經被主要分支合併回去，他處於一個刪除也無所謂的狀態。  

今天你又遇到一個新的需求，除了重新開一個分支的做法之外，你也可以選擇 rebase 回去主要分支，改個分支名稱繼續開發。 

例如你在 `Emilia` 分支：
![](https://i.imgur.com/bC0Lllv.png)

`git rebase master` 分支之後，分支位置會跑到這：
![](https://i.imgur.com/U4LwifQ.png)

這麼一來就能以 master 分支上面的 commit 內容為基礎繼續開發。

## 3. rebase 使用上有什麼該注意的嗎？
有，rebase 會異動 commitID。

這題應該是只要修改歷史的指令就一定要說的事情
:::info no-icon
不要隨意用 rebase 修改已經推到遠端儲存庫的 commit
:::