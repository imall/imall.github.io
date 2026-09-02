---
title: 在 Windows 使用 AutoHotkey 更改熱鍵
date: 2026-09-02 15:30:00
tags: [AutoHotkey]
categories: 雜記
comment: false
---

:::info no-icon
開始使用 mac 之後習慣了 mac 的鍵盤動作，換到 windows 時肌肉記憶依舊記得當時的動作  
為了減少差異習慣，又剛好覺得 mac 關閉視窗的鍵盤熱鍵頗為方便，因此也想在 windows 電腦設定一下
比照 Mac 的 `Command` + `Q` 與  `Command` + `W` 的位置，決定把 `Alt` + `Q` 與 `Alt` + `W` 改成相同行為
原本用 PowerToys 的 Keyboard Manager 設定，結果在這台電腦上怎麼樣都不生效，查了半天也沒查出原因
最後改用 AutoHotkey，兩行就搞定，寫個文章紀錄一下
:::


## AutoHotkey 是什麼

AutoHotkey 是 Windows 上的免費腳本工具，專門用來做鍵盤、滑鼠的自動化

最常見的用途大概是這幾種：

- 改鍵：把某個按鍵或組合鍵，換成另一個
- 巨集：按一個鍵，自動跑一連串動作
- 文字展開：打一段縮寫，自動換成完整句子

它的原理是掛一個很底層的鍵盤攔截，所以幾乎所有視窗都攔得到，這也是為什麼 PowerToys 攔不到的情況，換成 AutoHotkey 就好了

> AutoHotkey 有 v1 跟 v2 兩個版本，語法不太一樣，網路上很多舊文章都是 v1 的寫法
> 現在新裝的話直接用 v2 就好，這篇文章也都是 v2 的語法


## 安裝

打開 PowerShell，用 winget 安裝

```sh
winget install AutoHotkey.AutoHotkey
```

裝完之後不會有任何視窗跳出來，它只是一個執行腳本的程式，要寫腳本給它跑才會有作用


## 寫腳本

找個地方新增一個 `.ahk` 檔案，副檔名一定要是 `.ahk`

```ahk hotkey.ahk
#Requires AutoHotkey v2.0
!q::!F4
!w::^w
```

第一行是宣告這個腳本要用 v2 跑，避免不小心被 v1 執行

後面每一行就是一組對應，格式是 `原本的按鍵::換成的按鍵`

上面兩行的意思分別是：

- `Alt` + `Q` 換成 `Alt` + `F4`（關閉視窗）
- `Alt` + `W` 換成 `Ctrl` + `W`（關閉分頁）

寫好之後對檔案點兩下，它就會開始跑了，右下角系統列會多一個綠色的 **H** 圖示


## 修飾鍵符號

AutoHotkey 用符號代表修飾鍵，這幾個記起來就夠用了

| 符號 | 按鍵 |
| --- | --- |
| `!` | `Alt` |
| `^` | `Ctrl` |
| `+` | `Shift` |
| `#` | `Win` |

一般按鍵就直接寫字母，功能鍵寫 `F1` `F4` 這種，其他像 `Enter` `Esc` `Space` 也都是照名字寫

> 如果想確認某個鍵在 AutoHotkey 裡叫什麼，可以查官方的 [Key List](https://www.autohotkey.com/docs/v2/KeyList.htm)


## 開機自動執行

腳本只有在執行的時候才有效，重開機就沒了

最簡單的做法是把 `.ahk` 檔案丟到「啟動」資料夾，Windows 開機時就會自動執行

1. 按 `Win` + `R`
2. 輸入 `shell:startup` 按 Enter
3. 把 `.ahk` 檔案丟進去

> 這個資料夾的實際位置是
> `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`


## 改完腳本要重新載入

之後要再新增熱鍵，直接編輯 `.ahk` 檔案就好

不過改完不會自動生效，要對系統列的綠色 **H** 圖示按右鍵，選 **Reload Script** 才會套用新的設定
