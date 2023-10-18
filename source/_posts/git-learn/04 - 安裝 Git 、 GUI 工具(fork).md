---
title: 安裝 Git 、 Git GUI 工具
date: 2023-09-18 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---


# 前言
到了目前為止，大家應該對於 Git 已經有基本的認識，並且知道 Git 是一套在自己電腦就能運作的軟體。

這篇文章，一起來安裝 Git ，以及 Git GUI 工具吧！

# 安裝 Git
## 在 Windows 系統安裝 Git 
在 Windows 環境安裝 Git ，其實無腦下一步就能裝好，不過筆者想帶大家認識每一步在做的事。  

由於步驟之繁雜，如果是想節省時間 ~~懶得看~~ 的讀者，只要稍微注意 **「第六步」** 、 **「第八步」** 的設定，其他步驟都可以直接點下一步，直到安裝完成即可。

1. 請先進入 [Git 的官網](https://git-scm.com/)，找到下面這個按鈕點進去。  
![官網安裝圖示](https://i.imgur.com/uIuHbte.jpg)

2. 可以直接點擊「click here to download」安裝最新版 64 位元版本的 Git，或是依據需求下載 32 位元或是 64 位元的執行檔。  
![下載安裝檔](https://i.imgur.com/diZXI0z.png)

> 如果不確定自己電腦位元，可參考 [微軟官網位元查詢教學](https://reurl.cc/QZ7ANp) 的步驟查詢

3. 打開執行檔，點擊執行。  
![開啟執行檔](https://i.imgur.com/OtRMdEU.png)

4. 一些 Git 有關的資訊，繼續點擊下一步。  
![Git 基本資訊](https://i.imgur.com/2Q8OzXq.png)

5. 選擇想要把 Git 安裝在哪個目錄中，如果沒特殊需求，放在預設目錄即可。  
![選擇安裝路徑](https://i.imgur.com/q8bZDYO.png)

6. 前面依照步驟點擊下一步後，會遇到這個畫面，建議調整兩個選項：    
- 「取消勾選」 Git GUI 工具，這是 Git 內建的 GUI，不過未來用不到。
- 「勾選」 `check daily for git for windows updates`，讓系統每天自動檢查 Git 更新，未來如果有安全性更新，就可以隨時接收到消息 。  
![選擇基本安裝項目](https://i.imgur.com/B1EpBih.png)

7. 這步能設定 Git 相關的軟體要放在 Windows 的「開始選單」的哪個資料夾內，預設是幫我們新增一個 「Git」 資料夾將軟體放在裡面，如果想把資料放在不同位置，可以在這步設定。
![於開始選單存放位置](https://i.imgur.com/qC5jlVV.png)

8. 這一步是要選擇 Git 預設的編輯器， Git 預設使用的編輯器是 `Vim`，如果跟他不熟，建議更改成習慣的編輯器。   
例如想把預設編輯器改成 VSCode，可以選擇 `Use Visual Stuido Code as Git's default editor`。  
這個設定在安裝完成後若想更改也可以，後續的文章會說明更改步驟。  
![選擇預設文字編輯器](https://i.imgur.com/9eS2otH.png)

9. 這裡讓我們設定「預設分支」名稱。  
第一個選項(預設)會把分支設定成 `master`。  
第二個則能讓我們自定義，例如有人希望預設分支叫 `main`，可以在這裡設定。  
這個設定如果未來有需要，也可以去 Git 的設定檔添加設定。
![設定預設分支名稱](https://i.imgur.com/b9osvp9.png)

10. 這步特別重要，預設的選項(紅框)會幫我們把 Git 工具加到 「命令提示字元」的環境變數，讓我們在 `CMD` 或 `PowerShell` 也能執行 Git 指令，這步記得要 **維持預設值**。  
![設定 Git 在命令提示字元的環境變數](https://i.imgur.com/ddJ6QKk.png)

11. 再來是詢問我們要用預設還是外部 SSH，這裡 **維持預設值** 即可。  
![預設或外部 SSH](https://i.imgur.com/yfpsKfK.png)

12. 這步是要設定我們在「地端儲存庫」要與「遠端儲存庫」連線時預設使用的機制，若沒特殊需求(例如須透過 AD 網域連線)，**維持預設值** 即可。  
![地端儲存庫與遠端儲存庫預設連線機制](https://i.imgur.com/wpPnhTw.png)

13. 在團隊如果同時有 Mac 以及 Windows 電腦的使用者，這裡的選擇就會影響彼此的合作。由於 Windows 在文件的換行符號是 CRLF ，而 Mac 電腦則是 LF ，預設選項在檔案紀錄在儲存庫時，會自動把換行符號轉換成 LF，保持一致性，所以這步建議一定要 **維持預設值** 。  
![選擇 window commit 轉換換行符號](https://i.imgur.com/2Vz38t2.png)

> 對 CRLF 跟 LF 有興趣的讀者，可以參考保哥寫的 [這篇文章](https://blog.miniasp.com/post/2013/09/15/Git-for-Windows-Line-Ending-Conversion-Notes)

14. 這步在設定 Git Bash 環境指令(預設是linux指令) 是否要改成 windows 的指令。  
如果有這個需求可以在這裡更改，用不到 ~~(或看不懂這在說啥)~~ 的話，**維持預設值** 就好。  
![設定 git bash 是否更換預設指令](https://i.imgur.com/LLb8qND.png)

15. 這步在設定預設的 `git pull` 要用什麼方式，一樣 **維持預設值** 值即可。  
![設定 git pull 模式](https://i.imgur.com/eA1mIq9.png)

16. 這步是讓我們設定預設的密碼管理工具，用來記憶在 Git 輸入的密碼，**維持預設值** 即可。  
![設定密碼管理工具](https://i.imgur.com/litdv9G.png)

17. 這步是設定一些額外的操作，像是第一個選項可以對檔案進行一些快取，加快我們使用 Git 的體感。  
![設定額外操作](https://i.imgur.com/Yu2QFVL.png)

18. 最後則是一些實驗性功能選項，畢竟還沒穩定，我們就 **維持預設值** 不勾選，直接點擊「安裝」按鈕。  
![設定實驗性選項](https://i.imgur.com/Bv760oS.png)

19. 安裝完成後會看到這個畫面，筆者習慣取消勾選 「View Release Notes」 再按「結束」。
![Git 安裝完成畫面](https://i.imgur.com/cElSAMt.png)

20. 安裝完成之後，我們可以打開 「Git Bash」或是「命令提示字元」。
輸入 `git -v` 看到「版本資訊」，或是輸入 `git`，看到一堆像「亂碼」的內容，就代表 Git 已經安裝成功。  
![確認 Git 已安裝完成](https://i.imgur.com/vrWLH7l.png)

> 若 Git 官方推出新的版本，安裝過程可能會跟上述步驟有落差，若有讀者發現步驟不同，還是依照讀者實際的步驟為主。  

## 在 Mac 系統安裝 Git 
Mac 系統有兩種安裝方式，先說結論，推薦使用 **HomeBrew** 來安裝，因為速度會比較快。

### 1. 在終端機執行 `git --version`
1. 打開 MAC 終端機(Terminal)，位置在「應用程式/工具程式/終端機」

2. 輸入 `git --version` 後點選確定

3. 此時視窗會跳出是否要安裝 `Xcode` ，因為 `Xcode` 這套軟體已內建好 Git 的命令，所以安裝  `Xcode` 的過程也會安裝 Git 命令列工具，這個步驟直接選擇安裝即可，

4. 等待時間依使用者電腦不同而不同，根據使用 Mac 的朋友的說法，視窗可能會顯示 40 小時之久，若執行環境較佳的話，約莫 15 ~30 分鐘即可安裝完成。

5. 安裝完成後，於終端機再次輸入 `git --version`，如果跳出版本訊息，則代表安裝成功。


### 2. 用 HomeBrew 安裝
1. 進入 [Homebrew 官網](https://brew.sh/zh-tw/)

2. 複製官方網站上的指令來安裝 HomeBrew，如果讀者已經安裝過 HomeBrew ，可以直接跳到第四步。
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

3. 打開終端機(Terminal) 貼上第二步複製的資料，點確定安裝 HomeBrew。

4. 安裝好 HomeBrew 後，在終端機中下 Git 安裝指令，即可安裝 Git。  
```sh
brew install git
```
5. 安裝過程結束後，在終端機輸入 `git --version` ，如果有看到版本資訊，代表 Git 已經安裝成功。

# 安裝 Git GUI
## Fork
Fork 的安裝方式相對於 Git 來說就很單純，它跟一般軟體安裝一樣，儘管按下一步直到安裝完成即可，這邊就不特別列步驟說明，請各位點選 [Fork](https://git-fork.com/) 連結進到官網安裝吧！

雖然系列文是以 Fork 介紹，不過市面上的 Git GUI 工具並不是只有它，比較常見的還有 `SourceTree` 、`GitKraken`、`GitHub Desktop`，讀者可以選一個自己喜歡的使用。

> fork 除了是一個 GUI 軟體工具之外，系列文最後會介紹到 GitHub 也有一個叫 `fork` 的功能，這兩者並沒有任何關係，純粹只是名稱取得一樣，請各位讀者千萬切記。

## VSCode 中的套件 - Git Graph
如果你也是 VSCode 的使用者，推薦你安裝 VSCode 中的套件 [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)

這個套件把 Git GUI 整合在 VSCode 之中，讓我們可以在開發過程不用刻意在切到其他視窗(例如 Fork) 處理 Git 的事情，是筆者很喜歡的一個套件之一。