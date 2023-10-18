---
title: Git 分支：分支合併篇
date: 2023-10-04 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---

# 前言
開始使用分支之後，總是會需要把開發的內容合併回來的一天。

在使用分支之前，操作的指令不太需要在乎目前所在的分支在哪，儘管執行指令操作資料就是了，不過要執行分支合併的動作，代表我們會去處理「兩個分支」的資料，要在哪個分支執行指令就是一件很重要的事情了！

剛接觸分支合併會遇到的幾個疑問不外乎是這些：

指令該在哪個分支執行？
分支合併之後會發生什麼事情？
進行合併分支是不是一定會發生衝突？

種種的疑問將會在這篇文章解答！

直接進入今天的主題：
:::info no-icon
分支合併與衝突解決！
:::

# 執行合併指令前的注意事項
先來說一個觀念：分支合併的指令，目的是要把「別人的東西」收割回來，這個概念會影響到我們決定「誰」要執行指令。

假如我們有個 `master` 用來記錄「完成的資料」。  
接著我們建立 `dev` 用來進行「開發項目」。  

當 `dev` 完成後，是 **`master` 要收割 `dev`** 的內容，所以我們要以 `master` 的角度，把 `dev` 的內容拿回來。執行指令時，必須確保自己是位於 `master` 分支，這樣才能去把 `dev` 的資料合併回來。

分支合併的第二個觀念：雖然是說把別人的資料「收割」回來，但分支合併的行為並「不會刪掉」任何已經 commit 的資料，無論是否使用「快轉模式」進行合併，已經 commit 的內容都不會發生改變！

關於快轉模式，等等會介紹到，這邊先建立觀念即可！

# 合併分支指令
指令很單純，就這樣而已：
```sh
git merge 要被收割的分支
```

實際來操作一次吧：
1. 假設已經開了一個分支 (`imall/feature`) ，是用來開發新功能的分支：
![](https://i.imgur.com/ELGbrfZ.png)

2. 在分支上開發了兩個版本，於是他往前走兩個點點：
![](https://i.imgur.com/JlTnU2l.png)

3. `master` 要把 `imall/feature` 的資料合併(收割)回來，所以要先切換回 master 分支
![](https://i.imgur.com/9FGZTW2.png)

4. 執行指令
```sh
git merge imall/feature
```

5. 完成
![](https://i.imgur.com/80SQqXp.png)

# 合併分支的快轉機制 (fast-forward)
相信大家多少看過分支「岔出去」又「合回來」的這種線圖，像是下圖這樣。  
`dev` 把 `Emilia` 、`Rem`、`Ram` 合併之後，四條線依舊存在著，結果上面的範例可能已經跟一些新手所想的畫面不一樣了：怎麼執行完沒有看到這樣漂亮的分岔線圖，而是只有一條線？？？
![](https://i.imgur.com/QN5kUFE.png)


事實上分支合併的行為就像「雙人接力」賽，當 `dev` 往前走兩步， `master` 想吃掉他的內容，只要沿著 `dev` 走過的路再走一遍就好，根本不用浪費時間刻意繞一圈。  

而且因為 `master` 本來就跟 `dev` 在同一個位置起步，用這種方式追上 `dev` 的步伐，根本也不存在 「資料衝突」 的問題。

我們稱這種行為叫做「快轉機制」(fast-forward)，他是 `git merge` 的預設機制。

此時可能也有人疑惑了，上面那張圖為什麼沒有出現這種情況，我是不是在唬爛？

我沒唬爛，那圖片是為了讓範例看起來很像一回事，刻意**關掉**快轉機制。  
不信的話，我用預設行為用 `dev` 合併 `Emilia` 分支給你看看：
![](https://i.imgur.com/7k19A6G.gif)

# 合併時關掉快轉機制

如果你堅持每一個合併一定要看到兩個不同的分支線路，那你也可以關掉快轉機制，只要在合併時使用 `--no-ff` 參數即可：

```sh
git merge 分支 --no-ff
```
`--no-ff` 就是告訴 Git 不要快轉機制 (no fast forward) 的意思。


用起來會長這樣：
![](https://i.imgur.com/AWcB5Dv.gif)


同場加映，把上面 `dev` 合併 `Emilia` 分支的行為，用快轉機制跑一次給大家看：
![](https://i.imgur.com/Bqn5aq9.gif)

然後不要問我為什麼 `Emilia` 分支從綠色變成桃紅色，那是 GUI 自動處理的。  
~~愛蜜莉雅 分支應該要是 **紫色** 才對!!!!~~


# 合併衝突介紹
我們在 Git 分支觀念不清楚的情況去使用分支、合併分支，無論是一個人的使用或是團隊開發，都有機會遇到 **合併衝突** 的議題。

事實上我們進行分支合併的過程，並沒有辦法確保衝突一定不會發生，既然這是可能會發生的議題，那我們就要知道發生了應該怎麼處理。


知己知彼，百戰不殆，在學習解決衝突之前，先來說為什麼分支合併會發生衝突：  
原因只有一個：當兩個分支的 commit 中，存在 **同一個檔案** 的 **同一行** 內容不同，只要進行合併，無論是誰合併誰，都會發生衝突。

例如這個操作步驟：
1. 在 `master` 分支所在位置建立 `dev` 分支
2. `master` 修改 `readme.txt` 檔案的 **第一行**，並且 commit 一版
3. `dev` 也修改 `readme.txt` 檔案的 **第一行**，然後 commit 一版
4. `master` 合併 `dev` 分支時，就會衝突


由於兩個分支都修改了 **同一個檔案** 的 **同一行文字** ，Git 在合併的過程並不清楚兩個 commit 都改了同行的內容，應該以哪個為主，只好把決定權交給我們，於是就用「分支衝突」的方式叫我們去處理了。


這裡就先模擬上面的行為進行 Git 的操作：  
Git 在 `merge` 指令發生衝突時，會呈現出這樣的資訊，告訴我們 **某個檔案** 發生了衝突：  
```sh
$ git merge dev
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```
最後一句話最重要：「fix conflicts and then commit the result」，這是 Git 在提示我們 **先解決衝突，然後 commit 這個已解決的結果**。


此時我們用 `git status` 指令來看一下目前的狀態：
```sh
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)      

Unmerged paths:
  (use "git add <file>..." to mark resolution)      
        both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
這裡可以看到第三行提示一個以前沒看過的訊息：「You have unmerged paths.」，代表著目前正在 **尚未合併完成** 的狀態
# 解決衝突

一般來說解決合併衝突的方法有兩種

## 1. 放棄合併
直接在終端機執行這個指令放棄合併的行為：
```sh
git merge --abort
```

可能會有人覺得，放棄很可恥，既然合併了就是要成功不是嗎？  

事實上，~~放棄確實可恥，但卻很有用~~，在操作合併如果發生衝突，選擇放棄也是一個蠻正常的事情。  

舉例來說，你是負責 `master` 的人，同事負責 `dev` 分支。  

今天你在 `master` 把 `dev` 合併回來結果發生衝突，但衝突的內容因為是同事改的，你也不知道怎麼修比較好。  

這樣的狀況，選擇放棄合併，等同事回來再跟他討論如何修改，就會是很恰當的做法。 

畢竟如果改壞了，~~壞~~同事說不定還會甩鍋給你。

但如果這個同事在場，可以找到人進行討論的話，就可以透過第二種方式進行處理。

## 2. 手動處理合併衝突
回頭看一下剛剛 `git status` 的內容的第七行的內容，`Unmerged paths:`，這裡面會顯示尚未合併成功的路徑檔案：
```sh
Unmerged paths:
  (use "git add <file>..." to mark resolution)      
        both modified:   readme.txt  # <= 告訴我們這個檔案衝突了
```
只要這個區塊列出來的檔案，都要一個一個打開，然後處理這些檔案的合併。  

以這個例子來說，發生衝突的檔案是 **readme.txt**，此時我們把這個檔案打開，會看到原本的內容被 Git 進行修改
```sh
<<<<<<< HEAD
這是 master 修改的 readme.txt
=======
這是 dev 修改的 readme.txt
>>>>>>> dev

```

原本文件內容只有一行，發生合併衝突時，Git 會把 **原本的分支** 跟 **要合併的分支** 發生衝突的部分全部都列出來，並且會呈現很固定的格式：  

1. 開頭會是七個 `<` 符號，加上 `HEAD`，代表著目前的分支。
2. 結尾會是七個 `>` 符號，加上預計合併的分支名稱，代表預計合併的分支。
3. 兩者之間會有七個 `=` 符號，進行資料的相隔。

看懂了之後，我們就可以把下面三行內容刪掉，並且撰寫真正要留下來的內容：
1.  `<<<<<<< HEAD`
2. `=======`
3. `>>>>>>> dev` 

此時你有四個選擇：  
1. 保留 `master`的版本
2. 保留 `dev`的版本
3. 保留 **兩者** 的版本
4. 全部刪掉，自己編輯新的內容

無論你選擇哪一種，編輯完檔案然後 **存檔** 之後，就可以透過 `add` 指令把 **修改完的檔案加到暫存區**
```sh
git add readme.txt
```
當執行 `git add` 成功之後，執行 `git status` 指令看看：
```sh
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:
        modified:   readme.txt
```
**All conflicts fixed but you are still merging.** ，這是告訴我們衝突已經被解決了，但是我們依然處於正在合併的狀態。

此外，有看到一個很熟悉的 **準備提交**(Changes to be committed) 字樣嗎？

沒錯，下一步就是要叫我們執行 `git commit` 來提交版本。

```sh
git commit -m "將 master 跟 dev 進行合併"
```
這裡的 commit 內容，記得要清楚的寫上 `merge` 過程發生的事情，好讓後續能追蹤曾經有衝突發生！


到了這邊，我們手動解決衝突的步驟，就正式完成！

## 補充：為什麼合併衝突還要自己 commit？
不知道你有沒有發現一件事情，merge 指令如果關掉快轉機制，他就是一個 commit 行為？

如果你忘記了，我把上面的 demo 搬來讓你看看  ~~(聽話，讓我看看!!)~~：  
![](https://i.imgur.com/AWcB5Dv.gif)

原本只有「三個」點點，執行 `git merge` 之後竟然變成 「四個」，不覺得這就是一個 commit 行為嗎！！

既然他是一個 commit 行為，就表示你可以定義 commit 的訊息：
```sh
git merge 分支 --no-ff -m "我把 feature 分支的內容吃回來了！！！"
```
![](https://i.imgur.com/s8gNB1R.png)

如果合併沒有發生衝突，整個 commit 行為，Git 會幫我們把它處理掉。
但如果發生了衝突，Git 就必須把資料內容的決定權交給我們，讓我們自己 commit 了。

這也是為什麼，合併衝突的 SOP 會是這樣：
1. 打開合併檔案，處理合併內容
2. 執行 `git add` 指令將衝突檔案加到暫存區
3. 執行 `git commit` 提交合併的內容 


# 同場加映：實際情境的合併衝突
看完合併衝突的解決方式，可能沒什麼真實感，這裡示範一下實際情境可能會發生的衝突樣貌。

不過如果寫程式碼，不一定每個讀者都能看得懂，不如我們來寫歌詞吧！

來製造一個會發生衝突的情境：  
假設有兩個開發者 **愛蜜莉雅** 跟 **雷姆** ，他們分別要在自己的分支建立一個歌詞的檔案，把歌詞寫進去，commit 一版。  
**愛蜜莉雅** 被分配到 「童話」，**雷姆** 被分配到 「突然好想你」。  
等到兩個開發者編輯完歌詞檔案後，`master` 分支再分別把兩個開發者建立的分支合併回來。

如果依照上面的做法，因為兩個分支不會編輯到同一個檔案，理論上不會有衝突。  
但 **雷姆**  應該只負責「突然好像你」，但她卻也新增 「童話」檔案。

使得衝突一觸即發...

一步一步來看看兩個開發者的行為：

1. 在最初的 `master` 分支先來開兩個分支 `Emilia` 、`Rem`：
![](https://i.imgur.com/Aes2kCS.png)
2. **愛蜜莉雅** 在 `Emilia` 分支提交一個 **童話歌詞.txt** 檔案：
![](https://i.imgur.com/fvc7q67.png)
3. **雷姆** 在 `Rem` 分支同時新增「兩首」歌詞的檔案結果童話的歌詞寫錯了兩個字...
![](https://i.imgur.com/QUXYgis.png)
4. 執行合併的人員，先切換到 `master` 分支，合併 `Emilia` 分支：  
![](https://i.imgur.com/kUePh3i.png)
5. 接著再繼續合併 `Rem` 分支，然後不意外的，有一句話發生衝突了：  
![](https://i.imgur.com/CiPaesX.png)
6. 上面這個底色，是 VSCode 偵測到衝突的文字(`<`、`=`、`>`)時，自動會出現的內容，可以讓開發者選擇。   
- 接受目前變更：因為 `master` 在第四步先合併 `Emilia` 分支，所以 **目前變更** 意思是 `Emilia` 分支的內容。  (如果看不出來，你可以回頭看 **愛蜜莉雅** 開發的截圖)
- 接受來源變更：因為 `master` **正在** 合併 `Rem` 分支而發生衝突，所以 **來源變更** 代表 `Rem` 的內容。
- 接受兩者變更：表示要兩筆的內容全都要。
7. 由於正確的歌詞是「張開雙手 變成翅膀守護你」，所以選擇了接受 **目前變更**。
![](https://i.imgur.com/dfGuolB.gif)
注意此時只做到解決衝突三部曲的第一步：編輯衝突檔案。  
還有兩步要處理。  

8. 將 **童話歌詞.txt** 加到暫存區。 
```sh
git add 童話歌詞.txt
```
9. 提交暫存區資料 
```sh
git commit -m "處理 rem 分支合併衝突"
```
10. 完成
![](https://i.imgur.com/weoTUEi.png)



# 用 Fork GUI 操作分支合併

用 GUI 操作合併時觀念很重要，必須很清楚到底是 **誰要吃掉誰** 的資料？
我們是站在「要收割」的分支身上，去處理「被收割」的分支。

所以即便是用 GUI ，要執行 `master` 把 `dev` 合併回來的行為，一定要先切到 master 分支上。

Fork GUI 有兩種合併方式可以操作
## 對著要「被合併」的分支點右鍵選擇 merge
這裡從切換分支的行為開始示範，一直處理到合併完成：
![](https://i.imgur.com/qGF5QK2.gif)

當然你也可以關掉快轉模式 `--no-ff` 進行合併：
![](https://i.imgur.com/uEOKp96.gif)

## 用「拖曳分支」的方式合併
這種方式就很直覺，相信聰明的大家直接看操作就知道在幹嘛了：
![](https://i.imgur.com/5EbB7Jk.gif)


## 合併衝突處理
直接讓線圖長成這樣，來看看兩個分支合併會發生什麼事情：
![](https://i.imgur.com/fXXbLG1.png)

如字面上的意思，兩個分支在原本的位置，都編輯同一個檔案，並且發佈一個版本。
此時兩個分支進行合併後，已經達成發生衝突的條件：
> 當兩個分支 **同一個檔案** 的 **同一行** 內容不同，只要進行合併，無論是誰合併誰，都會發生衝突。

當執行合併，GUI 會很好心跳出警示，這個行為會發生衝突哦！
![](https://i.imgur.com/QJR0njX.png)

很不意外的，按了確定之後，衝突發生了  (~~\打架/ \打架/~~)
![](https://i.imgur.com/ic44wtz.png)

GUI 視窗此時會自動跳出這樣的畫面：  
在未暫存區顯示衝突的檔案，並且於主畫面中，讓我們決定想要留下哪個分支的內容。
![](https://i.imgur.com/ZArqmBF.png)

這裡先示範留下左邊分支的內容給大家看：
![](https://i.imgur.com/bbzRit9.gif)

如果想留下右邊的分支內容，依樣畫葫蘆，僅勾選右邊即可。


如果兩邊的內容都要留，GUI 會再跳一個視窗，讓我們操作內容先後順序：
![](https://i.imgur.com/O1LHY7y.gif)

完成之後，就是單純的執行 commit 操作了：
![](https://i.imgur.com/uXGou8e.gif)

## 放棄合併
放棄合併的操作就沒什麼學問，**Abort** 按鈕點下去就結束了：
![](https://i.imgur.com/rfB3onZ.png)


# 總結
分支合併是 Git 重要功能之一，同時也是基本分支功能的最後一哩路。  

這篇文章記錄了分支合併的指令，認識 Git 分支合併快轉機制 (fast-forward)，也學習了解決衝突的方式。   

希望這些內容有幫助到正在學習 Git 的朋友！  
