---
title: 使用 Vite 開發 Vue 
date: 2023-10-11 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# Vite 的簡易介紹
之前所有的操作項目，都是直接使用 CDN 引入 Vue 之後進行開發，這種做法在一個小型的應用程式足以感受到 Vue 的方便性。

不過一個大型的網頁應用程式如果這樣開發，DX (開發者體驗) 其實並不會太好，通常前端框架都會有相對應的 CLI 工具讓開發者在使用框架時體驗可以更好一些。

Vite 是 Vue 的作者尤雨溪大大所建構出來的前端建構工具，這種工具也有人把它稱為「腳手架」、「鷹架」等，用意是協助開發者建構一個適合開發的環境，以及先處理好基本的架構。  

在 Vite 問世之前，Vue 的建構工具是 Vue Cli，這個改版對於開發者最有感的地方只有一個
~~~
快
~~~


實務上使用腳手架開發有幾個主要原因
1. 他會先幫我們建立一個基礎架構，我們可能不用在哪邊寫 `Vue.createApp()` 之類的函數，以即使用元件時，不用再寫 `app.component` 來定義，而是直接定義副檔名叫`.vue`的 SCF 檔案就可以定義完成。

2. 專案開發過程支援熱重載，讓我們在寫完 code 之後可以即時得知程式有沒有問題

3. 打包，雖然這應該不能算是「主要原因」，畢竟打包的行為是因為「使用了腳手架」所以需要打包，原因是瀏覽器其實看不懂 `.vue` 檔案，他只看得懂 JavaScript，我們需要透過腳手架的協助，幫我們把這些 `.vue` 檔案「翻譯」成瀏覽器看得懂的 JavaScript，我們稱這行為叫「打包」。


上述的第二點與第三點，其實都需要「時間」。以前使用 Vue Cli 的時候，這個時間短則一兩秒鐘，長則幾分鐘。

就拿熱重載這件事情來說好了，調整程式按下存檔的那個瞬間，會需要花費一兩秒鐘的時間讓 Cli 重新跑一下，網頁才會出現結果。  

雖然一兩秒鐘好像沒什麼，不過每次的調整都需要一兩秒鐘，搞到最後真的會很沒耐心...

不過自從用了 Vite 之後，那個等待時間幾乎可以當作不存在，快到一個不可置信，據官網的說明，他比過去的速度快了 10-100 倍，這也是筆者很推薦新手要入坑，直接開始玩 Vite 的原因。


# 開始使用 Vite
開始使用 Vite ，請直接找一個空資料夾開啟終端機，依照習慣的套件管理工具執行這些指令。  

以此系列文最一開始的說明，在裝好 node.js 之後，即可直接使用 `npm` 套件管理工具。

```sh
npm create vite@latest
```

執行指令後，依照提示選擇適合的配置：
```sh
npm create vite@latest
√ Project name: ... vue-practive
√ Select a framework: » Vue
√ Select a variant: » JavaScript

Scaffolding project in D:\GitHub\vue-practive...

Done. Now run:

  cd vue-practive
  npm install
  npm run dev
```

接著依照提示執行這三個指令，預設的範例程式就可以被啟用：

```sh
  cd vue-practive  # 切換資料夾
  npm install      # 安裝依賴套件
  npm run dev      # 執行開發用的虛擬伺服器
```

![](https://i.imgur.com/fG5p9lk.png)


其中最後一個指令 `npm run dev`，會是未來開發的起手式。


# 專案目錄介紹
![](https://i.imgur.com/kp9403x.png)

這是 Vite 初始化後的專案結構，在一開始我們只需要關注於幾個地方：


1. `index.html`，這就跟我們使用 CDN 開發時一樣，需要一個 HTML 檔案用來掛載 Vue 實體。打開檔案會看到那個熟悉的 `<div id="app"></div>`
![](https://i.imgur.com/ZPNjaob.png)
2. `src/main.js`，主要用來建立 Vue 實體的地方，專案主幹的 JavaScript 內容通常都會在這裡撰寫。在預設內容中我們也能看到建立 Vue 實體的 `createApp()` 方法。
![](https://i.imgur.com/SNBAdWo.png)
3. `src/App.vue`，剛剛的 `crateApp()` 方法中，過去我們會在裡面放一個物件，在 Vite 中，這個物件被抽出來變成一個 `App.vue` 的檔案，就是我們之前介紹的 SCF ，單一元件檔，這個檔案的內容，在[認識元件、Vue 的單一元件檔案 SFC](https://ithelp.ithome.com.tw/articles/10334030)文章中曾經拿來當作範例介紹結構。
![](https://i.imgur.com/I4bUE9c.png)
4. `src/components`，我們在 Vue 應用程式需要建構的元件，會直接建立 `.vue` 檔案放在這個資料夾裡面，需要使用時再把檔案 `import` 過去即可，如同上面 `App.vue` 的 `<script setup>` 裡面寫的那樣。 
![](https://i.imgur.com/wYkPWXe.png)


# 在 `script setup` 標籤中使用 `ref()` 、`computed()`
就如同當初 CDN 需要引入方法來使用一樣，在 `script setup` 標籤中使用 `ref()` 、`computed()` 之前，記得要先把兩個方法引入到元件中使用：

```js
import { ref, computed } from 'vue'
```
唯一不同的是，Vite 專案的 Vue 框架的原始碼在上面初始化時執行 `npm install` 的時候已經被安裝到 `node_modules` 資料夾中，所以引入的來源要寫 `'vue'` ，而不是 `Vue`

```js
import { ref, computed } from Vue //寫這樣會錯！
```

此外在 `script setup` 使用這兩個東西的最大好處，是不用再像當初 `setup()` 語法那樣把狀態 `return` 出去給模板使用了，模板自動偵測哪些狀態是可以被使用的。

![](https://i.imgur.com/DO1jwQP.png)

> 分割視窗兩邊是同一個檔案，純粹為了截圖方便而分成兩邊

# 在 `script setup` 標籤中使用 props
以前在 CDN 中寫 props 可能會是這樣的寫法：
```js
app.component("my-component", {
  props: ["message"],
});
```

不過在 SCF 單一元件檔案 的 `<script setup>`標籤內中定義 props ，會使用一個函式 `defineProps()`，他是一個可以「只接使用」的函式，不用特別引用(`import`)，在使用上就跟之前學習 props 一樣，可以用陣列定義資料，也可以用物件形式定義。

- 陣列形式：
```html
<script setup>
defineProps(['books'])
</script>
```
- 物件形式：
```html
<script setup>
defineProps({
  books: {
    type: Array,
    required: true,
  },
})
</script>
```

如果需要在 `<script setup>` 標籤內使用 props 的資料，需要再定義一個變數出來接 `defineProps()` 的回傳值，一般習慣上會把變數名稱命名為 `props`：

```html
<script setup>
// 變數名稱不一定要叫 props ，但習慣會這麼命名
const props = defineProps({
  books: {
    type: Array,
    required: true,
  },
})

// 透過 props.XXX 來使用資料
console.log(props.books)
```

# 在 `script setup` 標籤中使用 emits

過去在 CDN 中定義 emits 會是這樣定義：
```js
app.component("my-component", {
  emits: ["update"],
});
```

不過在 `script setup` 標籤中要定義 emits ，則是使用 `defineEmits()` 方法來定義，他一樣不用特別引入即可直接使用。
```html
<script setup>
defineEmits(['addBook'])
</script>
```

![](https://i.imgur.com/GkqlDLS.png)


# 總結

這篇文章簡單介紹了 Vite 的初始化方式，以及幾個與 CDN 使用上可能有些語法差異的功能。  

除了這些項目之外， Vite 還有許多細節，內容大概多到可以再寫一系列鐵人賽XD  
如果有興趣的讀者，可以參考一下 [Vite 官網中文文件](https://cn.vitejs.dev/)！
