---
title: 更改 nuget 存放位置
date: 2024-07-11 20:41:02
tags: [nuget] # 文章標籤
categories: 關於 dotnet # 文章分類
comment: false # 關閉評論
---


# 前言

我們在使用 nuget 套件的時候，所安裝的套件會在主機存放快取  
開發時間一久，磁碟空間可能就會出現不夠用的問題

一個簡單的做法是執行清理快取的指令：

```sh
dotnet nuget locals all -c
```

只不過這卻是一個治標不治本的作法，當時間一久，被清除的快取檔案又會再被載回來

如果狀況允許，會建議把快取檔案放在獨立的槽裡面

這篇文章來說明一下該如何更改預設存取路徑


# 正文

首先，在我們執行上方指令的時候，基本上會看到這樣的畫面

```sh
dotnet nuget locals all -c
正在清除 NuGet HTTP 快取: C:\Users\imall\AppData\Local\NuGet\v3-cache
正在清除 NuGet 全域套件資料夾: C:\Users\imall\.nuget\packages\
正在清除 NuGet 暫存快取: C:\Users\imall\AppData\Local\Temp\NuGetScratch
正在清除 NuGet 外掛程式快取: C:\Users\imall\AppData\Local\NuGet\plugins-cache
已清除本機資源。
```

如果系統是設定英文版的話，可能會看到另一個畫面(雖然不是重點)

```sh
$env:DOTNET_CLI_UI_LANGUAGE = "en"  # 語系設定成英文
$env:DOTNET_CLI_UI_LANGUAGE = "zh-tw"  # 語系設定成中文
```

```sh
dotnet nuget locals all -c
Clearing NuGet HTTP cache: C:\Users\imall\AppData\Local\NuGet\v3-cache
Clearing NuGet global packages folder: C:\Users\imall\.nuget\packages\
Clearing NuGet Temp cache: C:\Users\imall\AppData\Local\Temp\NuGetScratch
Clearing NuGet plugins cache: C:\Users\imall\AppData\Local\NuGet\plugins-cache
Local resources cleared.
```

總之，其實會注意到 nuget 會有四個快取路徑

相關的介紹可以參考 [微軟對於 nuget 快取資料夾的介紹](https://learn.microsoft.com/zh-tw/nuget/consume-packages/managing-the-global-packages-and-cache-folders)


提及一下文章中可以得到本文的需要的部分：我們可以透過設定環境變數，來改寫快取路徑存放的位置

四個快取路徑分別是

1. global-packages：環境變數 `NUGET_PACKAGES`
2. http-cache：環境變數 `NUGET_HTTP_CACHE_PATH`
3. temp：環境變數 `NUGET_SCRATCH`
4. plugins-cache 4.8+：環境變數 `NUGET_PLUGINS_CACHE_PATH`

# 設定環境變數

window 作業系統設定環境變數的方式

1. 開啟「設定」
2. 點一下「系統」
3. 點一下「關於」
4. 看到右手邊「相關設定」區塊有一個「進階系統設定」，點他一下  
5. 會跳出一個這樣的視窗，點一下環境變數  
![環境變數](https://i.imgur.com/7SkeL8T.png)


# 更改 nuget 快取存放路徑

成功開啟環境變數後，可以依照自己的需求看是要更改這個使用者的 nuget 快取存放路徑，或是整台電腦所有使用者的路徑  
![nuget存放路徑設定](https://i.imgur.com/Q03rLDH.png)


設定完成後，記得要重新啟動電腦，變數才會真的生效


# 用指令更改環境變數

如果懶得找路徑的話，可以直接在 powershell 下指令

```sh
[Environment]::SetEnvironmentVariable("NUGET_PACKAGES", "D:\NuGet\GlobalPackages", "User")
[Environment]::SetEnvironmentVariable("NUGET_HTTP_CACHE_PATH", "D:\NuGet\http-cache", "User")
[Environment]::SetEnvironmentVariable("NUGET_PLUGINS_CACHE_PATH", "D:\NuGet\plugins-cache", "User")
[Environment]::SetEnvironmentVariable("NUGET_SCRATCH", "D:\NuGet\temp", "User")
```

# 同場加映，更改 temp 的預設位置

上面更改的路徑中有一個快取是存放在使用者底下的 Temp 資料夾
事實上這裡面常常存放一堆你沒事不會去打開的東西

我們也可以直接下指令更改這個資料夾的存放位置

```sh
# Set the path to the new location where you want to move the temporary folder
$newLocation = "E:\Temp"

# Get the current path of the temporary folder
$currentLocation = [System.IO.Path]::GetTempPath()

# Copy all the files from the current temporary folder to the new location
robocopy $currentLocation $newLocation /e /move

# Set the new path for the temporary folder
[Environment]::SetEnvironmentVariable("TEMP", $newLocation, "User")

# Set the new path for the TMP environment variable
[Environment]::SetEnvironmentVariable("TMP", $newLocation, "User")
```

這個指令主要是先記錄新舊位置的變數

並且用 robocopy 去把資料夾的內容進行搬移

最後再更改環境變數 TEMP 跟 TMP 的環境變數存放的位置