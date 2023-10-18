---
title: 使用 GitHub - Pull Requests (PR)
date: 2023-10-13 10:50:20
tags: [Git , GitHub]
comment: false
categories: 一起來學 Git 吧！
---
# 前言
在 [版控的類別](https://ithelp.ithome.com.tw/articles/10320622) 一文中，曾經有聊到「分散式版控系統」無法針對開發人員進行權限控管，因為所有人在地端都會擁有一個「完整的」儲存庫。

當時曾經很隱含的說，這個缺點其實有辦法克服，而克服的方式就是本文所要介紹的

:::info no-icon
提取要求
:::

> 注意！這一篇文章「不會」提到 Fork GUI ，文章中的 `Fork` 一詞，是 GitHub 的 `Fork` 功能，與 Fork GUI 完全無關。
# 什麼是「提取要求」？

提取要求，英文是 Pull Requests，俗稱 PR，是一個能讓開發者對於遠端儲存庫的原始碼提出「修改建議」的功能，做法是讓開發者 **要求將自己的「開發分支」併入遠端儲存庫的「主要分支」** ，這個功能一般都是「遠端儲存庫」所提供，跟地端儲存庫沒有任何關係。  

既然「提取要求」是遠端儲存庫提供的，代表操作「提取要求」前，會需要把地端分支的資料先推到遠端儲存庫，才能到遠端儲存庫的網頁介面上操作。

此外，依據遠端儲存庫的不同，「提取要求」可能會有不同的名稱，像是「Pull Request」與「Merge Request」之類，都是在講「提取要求」的意思。在 GitHub 平台上，「提取要求」被稱為 Pull Request ，為了方便起見，下文會以「PR」稱呼。


在 GitHub 中，專案權限的不同會有不同的 PR 操作方式：  

- 第一種情境：  
你是專案的成員，你的 GitHub 帳號有被加入 Repository 開發者名單中，所以你有權限將你的開發分支推到遠端儲存庫。  
如果要「發 PR」，只要把分支推上遠端之後，在 GitHub 操作 PR 流程即可。

- 第二種情境：  
你不是專案的成員，你想要對某個開源專案提出修改建議，此時你不會有操作專案的權限，所以沒辦法將你的程式碼 push 到這個遠端儲存庫，你必須進行下列幾個步驟來完成「發PR」 的動作：
1. 使用 GitHub 的 `Fork` 功能，把專案 Repository 複製回你的 GitHub 帳號。
2. clone 你的 Repository  回到地端開發。
3. 把開發完成的分支推到你的 Repository。
4. 操作 PR 流程。


> 再次強調，雖然此系列文的 Git GUI 工具是使用一個名叫 Fork 的軟體介紹，不過本文的 `Fork` 一詞都是指 GitHub 的 Fork 功能，跟 Fork GUI 軟體完全無關，只是名稱相同而已，請讀者們千萬不要搞混！！


接下來我們一邊模擬上述兩個情境，一邊示範 GitHub 的 Pull Request 要如何操作。

# 在自己的專案中使用 PR

我們先來建立一個開發流程的共識，以便理解等等 PR 的操作動作：
1. 開發人員必須使用 `開發人員代稱` 作為分支命名規則，並且建立分支進行開發。  
2. 會有一個 `dev` 分支是「開發階段」的主要分支，用來合併開發好的分支。  
3. 要將 `開發人員代稱` 分支合併到 `dev` 分支時，**不能** 在地端使用 `git merge` 指令，必須先將 `開發人員代稱` 分支推到遠端，再發 PR 到 dev 分支要求合併。
4. 專案負責人 code review 完之後，在 GitHub 上「同意 PR 」，`開發人員代稱` 分支才能併入 `dev` 分支

以上規定都沒都沒問題的話，我們來以實際情境說明完整的「發 PR」流程與操作方式：  

假設目前 `愛蜜莉雅` 已經開發完成，她把 `Emilia` 分支推送到 GitHub。   

```sh
git push -u origin Emilia
```

到了 GitHub 介面，依照步驟開始操作 PR

1. 首先，`愛蜜莉雅` 應該能看到醒目的黃色提醒，提示她要不要對這個分支操作 PR：
![](https://i.imgur.com/y9bgQQA.png)

2. 此時操作 PR 的方式有兩個選擇，點擊綠色 `Compare & pull request` 或是上方 `Pull requests` 按鈕
![](https://i.imgur.com/KikhlAe.png)
這兩個按鈕主要的差別在於：
-  `Compare & pull request` 按鈕會以 `Emilia` 分支設定 PR 的配置項目。  
-  `Pull Requests` 按鈕則需要自己從頭操作要處理哪個分支的 PR。  
這裡用上面那顆 「`Pull Requests` 按鈕」示範較為完整的 PR 操作。


3. 進入 `Pull Requests` 介面後，請再點擊 `New pull request` 按鈕：
![](https://i.imgur.com/8qduVk8.png)

4. 我們要關注紅框框的 **兩個** 下拉選單，左邊的選單要選擇「基底分支」，右邊的選單要選擇「比較分支」，根據選單之間的箭頭(`<-`)提示，這裡意思是要將「右邊的分支」**併入**「左邊的分支」，這件事情同時也是 Pull Requests 的精隨所在。  
![](https://i.imgur.com/jWUh3q4.png)

5. 因為要將 `Emilia` 分支併入 `dev` 分支，所以「右邊」選擇 `Emilia`，「左邊」選擇 `dev`。選完之後畫面會因此有所改變，再繼續點選 `Create pull request` 的按鈕：
![](https://i.imgur.com/wtOz0tY.png)


6. 接著會出現兩個輸入框，上方框框中可能會有預設的資料，他是用來定義目前操作的 PR 要在遠端儲存庫中顯示的名稱，下方的輸入框則是對 PR 進行一些詳細說明，能讓審核人員更清楚知道「開發分支」做的事情，可以選擇性輸入。
![](https://i.imgur.com/G6HcTKT.png)


7. 輸入完之後，點 `Create pull request` 按鈕
![](https://i.imgur.com/6sE9Eub.png)

8. 這樣就完成了一個「提取要求」了
![](https://i.imgur.com/RMPBhfv.png)


**備註：**  
如果一開始是直接點綠色的 `Compare & pull request`按鈕，會直接跳到 「第四步」：  
![](https://i.imgur.com/yPl9AOx.png)


# 成為他人專案的共同開發者
假設 `雷姆` 對上面那個 GitHub 專案很有興趣，但她並不是專案既有的團隊成員，那她不會有權限把程式碼推送到這個專案中。  

不過她可以使用 GitHub 的 「Fork」 功能先把專案複製回自己的 GitHub 帳號，並且 `git clone` 「自己的 Repository」回地端開發。

等到開發完成之後，先把程式推回去「自己的 Repository」，再使用提取要求(Pull Request) 功能，要求把「自己 Repository 的分支」合併入 「原本專案的主要分支」。


## Fork 他人的專案回自己的 GitHub
1. 首先第一步，點選 `Fork` 按鈕，把專案複製回自己的 GitHub 帳號：
![](https://i.imgur.com/PJSlGs0.png)

2. 跳轉頁面中，會把這個專案的資料「複製一份」到 `雷姆` 的 GitHub 帳號中：
![](https://i.imgur.com/UgX2Ae5.png)


3. 上面點選 `Create Fork` 之後，這個專案就會被建立在 `雷姆` 的專案中了：
![](https://i.imgur.com/Sst4rWp.png)


## Fork 回來後，原本的專案有更新...
假設 `雷姆` Fork 他人的專案回自己的 GitHub 之後，原本的專案團隊更新了主要分支內容，`雷姆` 可以在「Fork 回來的 Repository」 看到像是下圖紅框提示，告訴 `雷姆` 對方的 Repository 有新的 commit。
![](https://i.imgur.com/FpIW8Pe.png)


`雷姆` 要把資料同步回來的話，須執行下列兩個步驟：

1. 到 Fork 回來的 Repository，找到 `Sync Fork` 的選單
![](https://i.imgur.com/OiKWKFz.png)

2. 點綠色的 `Update branch` 按鈕就完成囉！
![](https://i.imgur.com/pqPQ0p5.png)





## 發 PR 到原本的專案
假設 `雷姆` 開發完畢，已經將她的分支 `Rem` 推送到「自己」的 GitHub 儲存庫中。

1. 要發 PR 回原本的專案， `雷姆` 可以選擇箭頭兩個按鈕來操作，這次我們直接選擇綠色的 `Compare & pull request` 按鈕：
![](https://i.imgur.com/QknVEw0.png)

2. 這一步的選項比上面複雜一些：  
「右邊」的選項要選擇「自己的 Repositroy」的「自己的分支」  
「左邊」的選項要選擇「原本專案的 Repository」的「原本專案的分支」。  
概念一樣是「右邊分支」要 **併入** 「左邊分支」。  
選擇好之後，點選 `Create pull request` 按鈕。  
![](https://i.imgur.com/q2frH9q.png)
> 有一個小地方可以注意一下：**左邊選項** 選擇誰的 Repository，上方就會出現誰的 Repository 資訊。

3. 看到下圖的畫面，代表 PR 完成！
![](https://i.imgur.com/Kfo4Yxj.png)




# 發了 PR 之後...發現有東西沒寫好...

不管是自己的專案，還是 Fork 他人的專案，發完 PR 之後可能還是會看到需要修改的地方。  

如果這個 PR 已經被專案擁有者給關閉，就只能重新發 PR 了。

如果這個 PR 還沒關閉，做法就是在地端修改一版之後， push 到遠端儲存庫即可。

銜接剛剛 `雷姆` 的例子來說，`雷姆` 發完 PR 之後，發現好像還有東西需要調整，她只要在原本的專案中處理完之後，執行下列指令即可

```sh
$ git add .
$ git commit -m "fix: 處理畫面跑版"
$ git push
```

完成之後，分支更新的內容除了在 「`雷姆` 的 Repository」 會更新之外， PR 的 commit 也會自動更新：
![](https://i.imgur.com/qOf1hBa.png)





# PR 審核

這裡假設專案的負責人看到有人發了 PR ，要開始進行「審核」與「合併」。  
此時會因為「**專案分支** 是否有 PR 保護規則」而影響能不能直接「合併」。    
分支最基本的 PR 保護規則，會是需要至少有一個人「PR 審核」通過後，才能執行「PR 合併」。

除此之外，還有許多不同的分支規則可以設定，由於設定分支規則不是本篇文章的主軸，對此有興趣的讀者請參考 [GitHub 分支保護規則文件](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule#creating-a-branch-protection-rule)。


## 專案分支「沒有」 PR 保護規則



如果 PR 的分支沒有建立審核規則，專案負責人可以直接依照下列步驟操作合併動作：

1. 點選 `Merge pull request`：
![](https://i.imgur.com/uZwxFBk.png)

2. 按鈕點選完之後，會出現輸入框輸入 PR 標題與 PR 描述，GitHub 預設會帶入一些標題與描述，這個訊息將會留存於 Git 儲存庫中，就像 `git commit -m` 指令撰寫的「訊息標題」與「訊息描述」一樣：
![](https://i.imgur.com/QTei29N.png)


3. 完成之後，系統會跳出 PR 成功的訊息，並且會出現一顆按鈕讓我們刪除已合併的分支：
![](https://i.imgur.com/B3mMr8Q.png)



## 專案分支「有」 PR 保護規則

如果分支有建立規則，專案負責人需要先「審核」PR 之後，才能「合併」提取要求：
![](https://i.imgur.com/7nWHW8V.png)


操作步驟如下：

1. 專案負責人有兩種方式可以進行 Review：
- 針對特定 commit 進行 Review
- 點擊 `Add your review` 對「開發結果」的進行 Review   

這裡使用第二種做法，點選 `Add your review` 進行審核行為
![](https://i.imgur.com/qUitlss.png)

2. 進入原始碼 Review 介面後，若有需要針對特定行數評論，可以在視窗中點選加號(`+`)拖曳想評論的原始碼，這個行為不限定於「審核人員」，「開發人員」也可以用這種方式留言。
![](https://i.imgur.com/pwBUDn9.gif)

3. 評論完成之後，點選右上角的 `Finish your review` ，會彈出一個視窗：
![](https://i.imgur.com/ZXBsj37.png)
視窗主要分兩個區塊：
- 輸入框：可以為整個內容做一個總結的訊息
- 選擇這次 review 的動作：
    - `Commemt`：只留下評論，但不做任何事情
    - `Approve`：留下評論，並且審核通過(Approve) 這個 PR
    - `Request changes`：留下評論，而且要求開發者「調整」 PR 內容 (表示審核未通過)  

如果要讓 PR 能繼續執行，要選擇 **Approve** 選項後，點選 **Submit review**。

4. 跳到主頁面之後，剛剛留下的訊息會在頁面中出現：
![](https://i.imgur.com/preYZ0i.png)


5. 同時，剛剛一堆紅字的區塊，也會出現已有人審核的樣貌：
![](https://i.imgur.com/fNyxzDY.png)

6. 注意！這個時候 PR 還不算完成，只是處於「可以合併」的狀態而已。  
要再點選綠色的 `Merge Pull request` 按鈕，才算是完成 PR。
![](https://i.imgur.com/mTa8WHN.png)

7. 點擊之後，一樣會有輸入框讓我們確認訊息，完成之後點選 `Confirm merge`(註1) 就完成 PR 囉：
![](https://i.imgur.com/54BUktx.png)

8. 完成 PR 之後，可以透過框框的按鈕刪除 `Emilia` 分支：
![](https://i.imgur.com/z57LNns.png)



> 註1.  從這個綠色按鈕的字樣 `Confirm merge`，我們也可以再次得知， PR 的行為其實就是 **「要求『主要分支』將『開發分支』的內容合併回去」** 的功能，所以在 PR 完成之後，回到地端將 `dev` 分支 `git pull` 回來，可以從線圖中看到跟「在 `dev` 分支執行 `git merge Emilia` 指令」一樣的線圖：  

![](https://i.imgur.com/fRKMft5.png)



# 常見問題

## 1. 都有專案權限了為什麼還要用 PR 來合併分支？

想像一下，如果一個專案有很多個參與人員，要是讓所有人都能將專案的主要分支 `master` 或是 `dev` push 到遠端，只要有一個人操作失誤，就會造成分支內容「髒掉」。  

這時候就很適合要求團隊成員都使用 PR 的方式要求將自己的開發分支合併進去主要分支，而專案的負責人進行 Code Review 之後，再把每個人的開發分支合併回去主要分支。

多了這一層的控管，也比較能保障主要分支的資料正確性，讓分支內容不會因為人多手雜的原因而被搞亂！


# 總結

Pull Request 是一個很常見的控管分支資料的方法，雖然可能不是每個團隊都會要求使用 PR 合併分支，不過他確實是個能解決分散式管理系統無法控管權限的方法。

由於 GitHub  Pull Requests 細節之多，本篇文章無法涵蓋所有 PR 的內容，如果有興趣的讀者請參考 [GitHub Pull Requests 文件](https://docs.github.com/pull-requests)，並且以文件內容為主。