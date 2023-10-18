---
title: 查看 log 紀錄
date: 2023-09-25 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---


# 前言
到目前為止，大家應該已經學會「提交一個版本」所需要的指令以及操作方式了。

不過在提交完版本之後，總是會有需要回頭查看之前 commit 的資訊的情況。
除非你執行 commit 就像「拿出手機拍下黑板上的筆記」一樣，只是拍個心安感，回家根本不會拿出來看，那我們另當別論...


在 **沒有 GUI 的世界**，commit 完的東西沒有漂漂亮亮的畫面可以讓我們看到。  
需要看歷史的話，只有那個冷冰冰的終端機可以使用。   

`git log` 就是用來做這件事情的指令，這篇文章將來介紹如何使用 `git log` 來查看歷史的提交資訊。 


老樣子，會先介紹指令，再來說明 Fork 介面。

# 查看版控歷程
**log**，除了是那個「永遠不會背叛你，因為他不會就是不會」的 **數學對數** 之外。  
在電腦科學的領域，**log** 也可以代表 **日誌** 或是 **紀錄** 的意思。  

所以我們說要查看 Git 的 log，也代表著要來查詢 **版本歷程**、**版本資訊**的概念。
通常會使用到 `git log` 的情境，會是想看一下之前提交的版本資訊，像是 commit 的 ID 、訊息，或是發佈人員是誰之類。

## 透過指令

### log 的基本指令
直接在終端機中執行 `git log` ，就可以看到所有版本的訊息

```sh
git log
```

```sh
$ git log
commit c1591821ce397d8a63836a48727583aa860ef0a8
Merge: 8ec01e1 4c723e9
Author: 羅茲瓦爾·L·梅札斯 <Roswaal_L_Mathers@gmail.com>
Date:   Thu Sep 14 11:03:18 2023 +0800

    Merge branch '拉姆' into dev

commit 0dc6f2079daacd690bc44adf5ac1af63053d61be (HEAD -> Rem)
Author: 雷姆 <Rem@gmail.com>
Date:   Thu Sep 14 10:49:42 2023 +0800

    完成雷姆介紹頁

commit 897789322aa57019c51dbcf8d1a800bc0fd99551 (Ram)
Author: 拉姆 <Ram@gmail.com>
Date:   Thu Sep 14 10:49:05 2023 +0800

    完成拉姆介紹頁

commit b71aeec201ac2a3cc56ffb0d26a929f009cb6907 (Emilia)
Author: 愛蜜莉雅 <Emilia@gmail.com>
Date:   Thu Sep 14 10:48:06 2023 +0800

    完成愛蜜莉雅介紹頁

    愛蜜莉雅醬真是天使！
```

`git log` 預設會列出五個資訊：
1. 版本的 commit ID，表示版本的獨立編號。 

2. 版本發布作者，這就是在 Git 初始化設定的內容。 

3. 發布時間。 

4. 執行 `git commit -m` 時所輸入的 **commit title** 以及 **commit description**，如果發布版本時沒輸入 **description**，則不會有資訊。

5. 如果版本當下有 **分支**，則會在 commitID 後頭顯示，反之則留空。
例如 `羅茲瓦爾` 那筆 commit 就沒有括號顯示分支資訊，而其他筆都有(註1)。


> 註1. 關於為什麼分支會出現神奇的字眼： **(HEAD -> Rem)** ，之後會有[文章](https://ithelp.ithome.com.tw/articles/10329649)來說明這件事。

**補充：**
資訊量太大時，終端機的最後會出現冒號(`:`)。  
如果想繼續列出訊息，可以按 `enter` (Mac 好像叫 `return`)。  
如果想結束，則按 `Q` 即可跳出。(記得輸入法要切換到英文輸入法。)

### 讓每一筆 commit 只呈現一行

因為介面大小有限，如果希望可以「一覽」較多的版本訊息，可以透過 `--oneline` 參數來設定：
```sh
git log --oneline
```

```sh
$ git log --oneline
c159182 Merge branch '拉姆' into dev
0dc6f20 (HEAD -> Rem) 完成雷姆介紹頁
8977893 (Ram) 完成拉姆介紹頁
b71aeec (Emilia) 完成愛蜜莉雅介紹頁
```

當 `git log` 多了 `--oneline` 參數之後，log 會把訊息精簡到只剩下一行，保留以下資訊：

1. 縮減過後的 commitID
2. 分支資訊
2. commit 的標題訊息，不含描述


指令到這邊為止，已經能查閱到主要資訊了，不過好像就是少了點什麼？  

對，就是 **ㄋㄟˋ個** 線圖，Git 版控資料最重要的 **ㄋㄟˋ個** 線圖勒！？
![](https://i.imgur.com/ajG7UdO.png)

~~Git Log：再給我一個參數...只要再一個參數，我就能完成線圖...~~  
~~Git 指令使用者：沒有線圖的 Log ，沒有查看的必要！！~~  
### 讓 log 顯示 Git 的線圖
`git log` 有一個參數可以讓終端機呈現「簡易」的線圖。  

在已經學到的指令中，疊加上`--graph` 參數：

```sh
git log --oneline --graph
```

```sh
$ git log --oneline --graph
*   c159182 Merge branch '拉姆' into dev
|\
| * 4c723e9 (Ram) 完成拉姆介紹頁
| * 8977893 新增跑馬燈卡片
| * 7093b32 新增拉姆html
* |   8ec01e1 Merge branch '雷姆' into dev
|\ \
| * | 4e16000 (Rem) 完成雷姆介紹頁
| * | 56db141 處理內容頁
| * | 0dc6f20 新增基本架構
| |/
* |   631d193 Merge branch '愛蜜莉雅' into dev
|\ \
| |/
|/|
| * b41c499 (Emilia) 完成愛蜜莉雅介紹頁
| * b71aeec 處理新功能
| * c22c3db 新增五項資訊
| * 7cee9b1 新增主視覺 banner
| * 2862c3a 新增主視覺html
|/
* 94262a6 處理XXfeature
```

在 Git 線圖中，**每個 Commit 一定能對應到一個「點點」**，log 會用 (`*`) 來代表。

而 log 的線圖，只是在原有的資訊中的左側添加而已，其他能呈現的資訊都與上述介紹的相同。

### 只列出前幾筆 log
如果 commit 版本太多，只希望列出前幾筆的話，可以加上 `-數字` ：

```sh
git log --oneline --graph -數字
```
```sh
git log --oneline --graph -5
*   c159182 Merge branch '拉姆' into dev
|\
| * 4c723e9 (Ram) 完成拉姆頁面
| * 8977893 新增跑馬燈卡片
| * 7093b32 新增拉姆html
* |   8ec01e1 Merge branch '雷姆' into dev
|\ \
```

### 客製化呈現想要的資訊
除了上述 log 預設能資訊之外，`git log` 其實有提供一個「客製化」資料功能：`--pretty` 參數。

不過由於細節太多，請讀者參考 [Git 官網](https://git-scm.com/docs/pretty-formats)。

如果只寫到這邊就太不負責任了！   
這裡提供一個含有 **「線圖」**、**「分支」**、**「訊息」**、**「時間」**、**「作者」** 的 format 指令給大家參考：
```sh
git log --graph --pretty=format:'%C(yellow)%h%Creset -%C(green)%d%Creset %s %C(magenta)%cD %C(bold cyan)<%an>%Creset'
```
![圖表示意](https://i.imgur.com/WsoDUdT.png)

有兩個地方大家可以玩玩看：
1. 顏色：根據 [官網](https://git-scm.com/docs/git-config#Documentation/git-config.txt-color) 的說法，要針對不同資訊設定顏色，有支援的顏色如下： `black`, `red`, `green`, `yellow`,` blue`, `magenta`, `cyan`, `white` and `default`。

2. 日期格式：也是根據 [官網](https://git-scm.com/docs/pretty-formats) 的資訊，上面的 「`%cD`」可以改成：
- 「`%cr`」(相對時間)：  
![%cr](https://i.imgur.com/ozljye3.png)
- 「`%cs`」( "YYYY-MM-DD" 的格式)：  
![%cs](https://i.imgur.com/fBL3Tlk.png)

## 透過 GUI 工具查看版本歷程
雖然洋洋灑灑寫了那麼多 `git log` 查看歷程的方式，我必須承認自己從開始使用 Git 版控到現在，除了練習指令之外，**從來沒有用過** `git log` 看過版本歷程。  


原因大家應該都猜得到：在終端機查看 log 資訊並不方便！  

一來，每次查看訊息都要輸入指令，且指令參數並不好記，也懶得打 `--online`、 `--graph` 這麼多字。  
二來，完全沒辦法再對這些資訊進行任何操作。  

說白了，使用指令看歷程，只能讓我們「讀」資料，沒辦法「操作」資料。  

一個好的 GUI 工具，預設就能看到 `git log` 那堆資訊，完全不用打指令。  

此外，直接「操作」GUI 的畫面，還可以執行常用到的 Git 功能，其中最方便的不外乎是「分支」的操作。  

Git 的「分支」與「圖表」兩者關係是密不可分，能直接在「圖表」操作「分支」，絕對是 GUI 的重要功能之一。  
操作的細節會在分支章節介紹，這裡先讓大家有個概念。

話不多說，來看怎麼用吧！

### 使用 Fork
GUI 畫面單純到，一時之間不知道該細講些什麼 XD ，不過還是針對每個區塊說明作用：
![fork GUI 示意](https://i.imgur.com/US10vgt.png)

1. 功能區，用來切換專案「工作目錄」的狀態，跟「歷程記錄」的資訊，如果要查看 Commit 歷程記錄，切換到「All Commits」頁籤。

2. 地端的所有分支 (註1)。

3. 專案的遠端追蹤分支 (註2)。

4. 專案歷程資料，也就是 git log 能查閱到的資訊。

5. 查看版本資訊頁籤：
 - Commit：該版本的基本資訊。
 - Change：該版本異動的檔案。
 - File Tree：該版本呈現的工作目錄內容。

6. 點擊某個版本後，會呈現該版本的基本資訊：
 - REFS：如果版本停留著分支，會在這裡呈現，反之則整欄不出現。
 - SHA：版本的 CommitID。
 - PARENTS：選到的版本的「上一個」版本，以圖中例子來說，因為選在「兩個分支合併後的 Commit」，所以「上一個」版本會有兩個(註3)。 
 
7. 該版本的 commit 訊息，如果有寫「Description」，會一併呈現於此。
8. 版本異動檔案，資料同第五點「Change」頁籤的內容。

> 註1：關於分支為什麼會有「資料夾」圖示，在分支章節會再說明。  
> 註2：關於地端分支、遠端分支的差異，之後會專門寫[一篇文章](https://ithelp.ithome.com.tw/articles/10338017)來跟大家講解。  
> 註3：關於「分支合併」的觀念與操作，之後會專門寫[一篇文章](https://ithelp.ithome.com.tw/articles/10334601)來跟大家講解。

### 用 Fork GUI 篩選特定資料
隨著時間推進，commit 版本太多可能導致資料不好找，這時候篩選功能就很重要了。

Fork GUI 可以做到簡單的篩選，除了篩選 commit 訊息之外，也可以篩選發佈版本的人員。  
~~其實指令也做得到，但我真心不想用他操作...~~

上面介面說明有一個地方沒說到，就是篩選功能，他落在下圖紅框中的 **放大鏡** 按鈕：
![](https://i.imgur.com/rnhGVTR.png)


#### 篩選 commit
直接拿個情境來示範，如果想挑出訊息中有 「完成」 的 commit，可以這樣做:

1. 點到放大鏡
2. 確認篩選值是 **commit message**
3. 把資料打進去

請看示範：  
![](https://i.imgur.com/augJXp2.gif)

#### 篩選發佈人員
跟上面依樣畫葫蘆，只是篩選值要改成 **Author**
![](https://i.imgur.com/oXZFGsD.gif)

從輸入框的提示應該可以發現，如果想找作者的話，也可以透過 email 來搜尋。
### 在 VSCode 透過 Git Graph 套件看 log
如果你也是 VSCode 的使用者，那麼使用 Git Graph 套件來查看歷程會是一件很方便的事情。  
安裝好套件後，點擊 VSCode 下方選單的「Git Graph」按鈕即可打開介面。  

映入眼簾的會是基本的 Git 圖表，一樣也會提供基本的 Git 。 
![git graph](https://i.imgur.com/sIQ2pkV.png)

 
在這個介面中，點擊每一個 commit 能看到更詳細的 commit 資訊：
![單一commit資訊](https://i.imgur.com/tFIEcWS.png)

由於筆者使用這個 GUI ，最常使用的是分支操作的功能，屆時文章再來細加說明。
# 總結
首先宣布今日 MVP ： Git GUI！  (掌聲加尖叫！！)

了解 `git log` 與「GUI 工具」兩者查看版本資訊的方式後，讀者們應該已經能體會到 GUI 工具強大之處，以及為何筆者當初所說「指令、GUI 全都要」的原因。  

無論指令還是 GUI ，對我們來說都只是「工具」，不用堅持只能用什麼方式操作 Git，只要是能方便我們完成版控的工具，都是好工具。

以「查看歷程」來說，比起去死記指令的參數，我更推薦大家直接使用習慣的 GUI 工具來達成這個目的。