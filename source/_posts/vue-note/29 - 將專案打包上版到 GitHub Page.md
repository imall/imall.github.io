---
title: 將專案打包上版到 GitHub Page
date: 2023-10-14 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

多年前筆者初次接觸框架時，不對...應該是開始寫網頁程式時，最大的疑惑莫過於那個在 localhost 執行的東西，該怎麼實際讓人家有辦法透過網只看到內容？

這篇文章來記錄一下，當我們將網頁應用程式開發完成之後，發佈程式的其中一種方式：將程式上版到 GitHub Page

# 什麼是 GitHub Pages？

GitHub Pages 是透過 GitHub 託管的公開網頁，只要將程式碼上傳到 GitHub ，到 GitHub 設定一下，就可以讓我們開發的程式藉由 GitHub Pages 展示出來。

我們可以使用這個機制放一些開源的專案、建立部落格，甚至將履歷放在上面。

上版的程式不一定要使用 Vue 開發，純粹的 `index.html` 、`app.js` 、`style.css` 也可以 (事實上等等的操作也是要放這些檔案而已)。

# 專案打包

在介紹打包做法之前，想補一下 `package.json` 中， `scripts` 的設定
![](https://i.imgur.com/Ls4DPKV.png)

這段的資料長這樣：

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
```

這其實是在定義 node 專案中，執行 `npm run [XXX]` 的快捷指令。

所以事實上我們執行 `npm run dev` ，意思等同於直接在終端機輸入 `vite`。

舉一反三一下：
`npm run build` 等同於 `vite build`。
`npm run preview` 等同於 `vite preview`。

知道原理之後，如果還有什麼特殊需求，就可以自己定義這個 script，例如這樣：

```json
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build-dev": "vite build --mode dev",
    "build-prod": "vite build --mode prod",
    "preview": "vite preview"
  },
```

# 專案打包
指令在剛剛出現過了，在終端機執行這個指令即可：

```sh
npm run build
```


執行完指令後，終端機應該會跑出類似這樣的東西：
```sh
npm run build

> vite-project@0.0.0 build
> vite build

vite v4.4.11 building for production...
✓ 31 modules transformed.
dist/index.html                  0.45 kB │ gzip:  0.29 kB
dist/assets/index-a42069a7.css   0.18 kB │ gzip:  0.15 kB
dist/assets/index-71501830.js   79.15 kB │ gzip: 31.89 kB
✓ built in 1.34s
```

於此同時，專案目錄中會出現一個 `dist` 資料夾存放著編譯完成的程式碼：
![](https://i.imgur.com/QJZF8XR.png)

一般來說，如果這包程式執行起來有問題，並不會去修改 dist 資料夾的內容，而是修改 Vue 的程式後重新打包。


我們試著比較一下專案中原本的 HTML 跟 `dist` 內的 `index.html`
![](https://i.imgur.com/GpZ9xGX.png)
左邊是原本的檔案，右邊是打包過後的檔案，原本的 `<script type="module" src="/src/main.js"></script>` 不見了，取而代之的是 `<head>` 標籤引入 JS 與 CSS。



注意到引入的方式是使用絕對路徑 `/assets` 。

# 調整基底路徑

我們之後使用 GitHub Pages 時，會希望 `index.html` 以「相對路徑」去抓 JS 跟 CSS，雖然直接更改打包過後的 `index.html` 也可以，不過比較保險的方式還是調整 Vite 的設定檔後再重新打包。

原因是 `npm run build` 執行下去，`dist` 資料夾的內容會被完全取代掉，手動編輯的檔案並不會被留存。

要修改基底路徑的方式是去 `vite.config.js` 檔案中修改設定：
```js
export default defineConfig({
  base:'./',  // <=================新增這行
  plugins: [vue()],
})
```
![](https://i.imgur.com/5AAx6Ck.png)

接著重新執行 `npm run build`，順利的話 `dist/index.html` 的設定應該就變成相對路徑了：
![](https://i.imgur.com/M52evEX.png)


# 建立 GitHub 儲存庫

1. 首先進到 [GitHub 官網](github.com) ，登入帳號後找到這顆綠色的 `new` 按鈕，點進去：  
(這顆按鈕常常因為 GitHub 畫面改版而換位置，不過他始終長這樣)  
![](https://i.imgur.com/CHFjFR8.png)

2. 在 Repository Name 輸入框中輸入喜歡的名稱，這個名稱未來會成為網址的一部分，輸入完之後點 `Create repository` 按鈕。
![](https://i.imgur.com/p0nLIRw.png)

3. 點選 `uploading an existing file` ，下面指令可以不用管他，那是[隔壁棚](https://ithelp.ithome.com.tw/articles/10337536)的事情
![](https://i.imgur.com/eszdf38.png)

4. 把 `dist` 資料夾的內容上傳到 GitHub 上

![](https://i.imgur.com/D6l8t6R.gif)

# 設定 GitHub Pages
1. 點右上角的 `settings`
![](https://i.imgur.com/OmsdZRV.png)


2. 點左邊 `Pages`
![](https://i.imgur.com/PaVcyr2.png)

3. 到 `Branch` 區塊選擇 main 分支
![](https://i.imgur.com/fri1w3X.png)

4. **等個幾分鐘 (多則 10 分鐘)**

5. 重新整理這個頁面，應該就會看到 GitHub Pages 的網址：
![](https://i.imgur.com/ypvdj2N.png)

6. 獲得一個[能展示成品的網頁](https://imall.github.io/Vue-Demo/)
![](https://i.imgur.com/sazeLvh.png)