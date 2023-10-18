---
title: Git 初始化設定
date: 2023-09-19 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---


# 前言
這篇來說明要使用 Git 前「一定」要做的事情：設定使用者名稱、信箱。  

以及可以選擇性操作的：設定 Git 預設文字編輯器。

# 設定 Git 的使用者名稱與信箱
## 用指令更改：

我們開啟終端機，並且透過下面兩個指令來設定 Git 使用者資訊。  
(建議兩個指令「分開複製」到終端機執行)

```sh
git config --global user.name "這裡改你的名字"
git config --global user.email "這裡改你的信箱"
```
實際輸入的內容看起來會像這樣：
```sh
git config --global user.name "imall"
git config --global user.email "imall841110@gmail.com"
```

如果想輸入的資訊 **「沒有空格(空白鍵)」**，也可以不寫雙引號(`""`)：
```sh
git config --global user.name imall
git config --global user.email imall841110@gmail.com
```

要確認有沒有設定成功，可以直接輸入下列指令：
```sh
git config --global user.name
git config --global user.email
```

如果有成功，終端機就會顯示設定好的資料：
```sh
$ git config --global user.name
imall

$ git config --global user.email
imall841110@gmail.com
```

## 用 Fork 更改  
1. 請先選到 **file** > **perference**  
![Fork 更改全域使用者步驟1](https://i.imgur.com/C6qpJBR.png)
2. 找到 **Git** 的分頁，並且輸入您的想設定的名稱與信箱    
![輸入使用者資訊](https://i.imgur.com/t6xaOPu.png)
3. 關閉視窗就設定完成了。

# 更改 Git 預設的文字編輯器
要更改 Git 預設文字編輯器，可以使用下列的指令：  
```sh
git config --global core.editor "編輯器執行檔位置"
```

以設定為 `VSCode` 為例，輸入的指令會是這樣：  
```sh
git config --global core.editor "code --wait"
```

由於編輯器的設定可能會因為作業系統的不同，而須輸入不同的資訊。  

讀者們可以到 [GitHub 官方文件說明](https://docs.github.com/en/get-started/getting-started-with-git/associating-text-editors-with-git) 尋找適合自己的資訊操作。


> 補充：曾經有使用 Mac 的朋友將預設編輯器設定成 VSCode 後，在執行會用到文字編輯器的 Git 指令時，出現 `zsh:command not found: code` 的錯誤訊息，這是因為 mac 的終端機 `zsh` 還沒將 `VSCode` 加入環境變數的關係。  
>
> 若有遇到一樣情況的讀者，可以參考 [Visual Studio Code 官方文件說明](https://code.visualstudio.com/docs/setup/mac#_launching-from-the-command-line) 來處理。



# 常見問題

## 1. 為什麼使用 Git 還要設定使用者資訊與信箱？
為了讓 Git 能紀錄「發佈版本的人」。

一個人版控可能比較無感，不過如果是團隊協作版控，這個機制可以在「發現某個版本有問題」時，比較容易去追蹤是誰發佈的版本。

舉例來說，今天 `菜月昴`、`愛蜜莉雅`、`雷姆`、`拉姆`，在合作開發同一個專案，每個人被分配到不同的內容。  

`雷姆`、`拉姆`、`愛蜜莉雅`完成自己的資料後，`羅茲瓦爾`(主管) 看了覺得做得太完美了，就把她們開發的內容合併到 dev 裡面準備讓測試團隊測試。  

`羅茲瓦爾` 繼續 code review 的過程，看到 「`80153555`」編號的 commit，內容一蹋糊塗！ 

因為每個人使用 Git 都必須設定使用者資訊，`羅茲瓦爾` 從 Git 線圖的內容就能找到是 `菜月昴` 在雷，於是把他抓出來教訓一頓，要他重改！  
![線圖示意](https://i.imgur.com/f061mqr.png)


## 2. 在 Git 設定的信箱需要跟 GitHub 帳號一樣嗎？
可以不用。

初始設定的使用者、信箱的設定只是為了把發佈者資訊紀錄在版本內，與 GitHub 、GitLab 平台帳密之間沒有任何關係。

## 3. 使用者名稱跟信箱設定完，在 Git 會被綁定嗎？
不會。

這兩個資訊在設定完成後，依然可以修改，而且沒有任何修改限制。

~~如果會綁定還不能改，我上面那張圖的假資料根本做不出來 XD~~
## 4. 我打完指令後，什麼訊息都沒跑出來，所以是發生什麼事情了？
`.gitconfig` 被更動了。

上文提到的兩個指令是在設定 Git 的 `.gitconfig` 檔案  
Mac 可以在這個路徑找到： ` ~/.gitconfig`  
Windows 則是在這個路徑： `C:\Users\你的使用者名字\.gitconfig`  

如果讀者已經有成功設定使用者資料，用文字編輯器打開這個檔案 (記事本、notepad++、VSCode 都可以) ，應該可以在檔案內容看到框起來的文字。  
![Alt text](https://i.imgur.com/pP6y34a.png)

我們在下指令的時候看到的 `user.XXXX` 以及 `core.XXX`，其實就是對應 `.gitconfig` 裡面的 `[user]` 與 `[core]` 區域內的資訊 。

了解其背後的原理之後，讀者應該已經猜到，如果直接設定 `.gitconfig`，也可以完成下指令做的事情！


## 5. 指令有 global 參數，難道也有 local 參數嗎？
答案是「有」。

> 接下來會提到尚未介紹的 `.git` 資料夾，觀念會在「建立儲存庫」的章節介紹，這裡先帶讀者理解 Git 的「區域」與「全域」設定觀念。

上面的問題帶大家認識 `git config` 是操作設定檔的指令。  

如果帶上 `--global` 參數，會是設定 **「全域」** 設定檔，會去更動 `.gitconfig` 檔案；  
如果帶上 `--local` 參數，則是設定 **「區域」** 設定檔，會去更動不同專案中 `.git 隱藏夾` 裡面的 `config` 檔案。

`.git` 裡面的 `config` 跟 `.gitconfig` 用途一樣，都是處理 Git 的設定資訊，兩者只有「區域」跟「全域」的差異： `.gitconfig` 是整台電腦都會使用的設定，而 `config` 是個別工作目錄的專屬設定。

有了這個觀念後，就可以依據需求來決定 `git config` 要添加什麼參數了。
```sh
git config --global (某個設定)
git config --local (某個設定)
```

## 6. 能不能在不同專案使用不同使用者名稱跟信箱？
可以。

我們已經知道 `git config --global` 可以拿來設定全域的設定。  

如果希望在不同專案設定不同的使用者，例如 A 專案叫 **「冰結魔女愛蜜莉雅」** 、B 專案叫 **「擅長捉弄人的高木同學」** 之類的，可以透過 `--local` 參數定義不同專案的資訊。  

以下帶大家了解指令與 Fork 的設定方式：
### 用指令設定 local 使用者名稱與信箱
```sh
git config --local user.name "你的名字"
git config --local user.email "你的信箱"
```
跟一開始介紹的指令，只差在 `--local` 參數，其他設置都一樣。

### 用 fork 設定 local 使用者名稱與信箱
1. 點擊左上角 **Repository** >  **Settings for This Repository...** 
![儲存庫設定](https://i.imgur.com/6iyF5Gv.png)

2. 把 **Use global git credentials** 的勾勾拿掉  

3. 輸入專案的使用者資訊  
![輸入專案的使用者資訊 ](https://i.imgur.com/3NF1yMV.png)

4. 關閉視窗就設定完成囉！     


> 從 Fork 預設的畫面可以注意到，如果我們沒有特別設定專案的使用者，則專案就會直接抓全域使用者的資料。
>
> ![Fork 區域使用者預設抓全域資料](https://i.imgur.com/5PU6sRC.png)

## 7. 我用 `--local` 參數設定了使用者，想刪掉怎麼辦...
~~我知道新手毛很多~~  

開玩笑的，拜託先別走...  

這問題算是我還是新手時期最畏懼的事情，常常擔心跟著步驟走錯一步，電腦就會被我搞壞。

這題有兩種做法可以操作：

### 編輯 `config` 檔案將 local 使用者資料刪除：
1. 打開專案中的 `.git` 資料夾，開啟 `config` 檔案
2. 檔案應該會有 `[user]` 相關的文字  
![專案 config 使用者資訊](https://i.imgur.com/6rQ51G1.png)
3. 直接刪除這段然後「存檔」
4. 完成 ~

### 使用 Fork 刪除 local 使用者資料
1. 點擊左上角 **Repository** >  **Settings for This Repository...** (參考第6題的說明)
2. 把 **Use global git credentials** 的勾選框打勾  
![勾選專案使用全域使用者資訊](https://i.imgur.com/odzV4vv.png)
3. 完成 ~