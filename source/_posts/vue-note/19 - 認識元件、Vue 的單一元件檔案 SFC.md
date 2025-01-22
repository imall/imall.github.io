---
title: 認識元件、Vue 的單一元件檔案 SFC
date: 2023-10-04 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 認識元件

在 Vue 幫我們解決的事情這篇文章中，已經用一個簡單的方式介紹元件的概念。

當時只是提到模板結構跟資料抽離，並且讓結構重複使用，帶上不同的資料。

有沒有覺得這東西跟什麼很像？

對... 就是上一篇文章的 v-for 在做的事情。

實際上這只是一個最淺的概念來讓新手去「感受」什麼叫做元件，但 v-for 在做的事情，純粹只是讓畫面重複被渲染，嚴格說起來不能稱之為元件。

Vue 的元件，除了能讓模板「重複使用」之外，更重要的還是能在整個 Vue 應用程式中的任何位置使用。

而且除了「畫面」之外，還會可以包含一些資料加工與邏輯整合。

我們剛學習網頁的開發方式，就是你我都熟知的三個檔案：`index.html`、`app.js`、`style.css` 的做法，基本上已經做到表現層級的關注點分離，不過只要單一頁面的資料量過於龐大，上面說的這三個檔案就是無止盡的變大，看到幾百行原始碼算是家常便飯，更誇張的還可能會到幾千行。

雖然這樣做不是不可行，不過時間不用太久，寫完程式一個月後，遇到需求修改，要從「程式山」中找到要改的地方絕對不會是一件容易的事情。

於是我們可以把網頁畫面當成拼圖一樣切割，Header 當作一塊，主要內容當成另一塊，Footer 再切一塊，拆成不同的區塊看待。

甚至還可以依據各區塊內容的獨特性，再讓拼圖的大小更小一些。

最後把這些內容整理成一個一個的元件檔案，我們稱作 **單一元件檔** SCF (sigle component file)。

# SCF 單一元件檔案

Vue 是一個漸進式框架，前幾篇用到的程式寫法也都可以用來定義元件，預計下一篇文章會寫到。

不過元件最方便，還是直接使用 Vue 的腳手架(vite)時，可以直接使用 `.vue` 副檔名的檔案，每一個這種檔案，我們就可以稱他叫做單一元件檔案。

以初始化 vite 時會看到的內容來說，會看到這樣的長相：

```
<scrip setup>
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <HelloWorld msg="Vite + Vue" />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
```

一個單一元件檔案分成三個標籤組成

1. script setup 標籤：  

他就是我們寫 setup() 的地方，那個 setup 是 Vue 提供的語法糖，尤雨溪大大曾說這很黑魔法，他可以讓我們直接省略 setup() 函數，而且變數不用再 return 出來，模板即可使用。

也就是如果只有寫 `<script></script>` ，就還是必須寫這樣的內容：

```js
<script>
import { ref } from 'vue'
export default {

  setup() {
    const count = ref(0)
    return {
      count
    }
  }
}
</script>
```

有了 `script setup` 語法之後，就可以省略成這樣：  
很乾淨俐落吧 XD

```js
<script setup>
    import {ref} from 'vue';
    const count = ref(0);
</script>
```


此外，vite 的例子中， style 的內容長這樣：
```js
<scrip setup>
import HelloWorld from './components/HelloWorld.vue'
</script>
```
有看到那個 import 嗎，沒錯！ Vue 的 SCF 檔案適用於 ES Module 語法。  
我們可以在 script 中引用其他元件來使用，也可以用語法引用其他套件來用。


2. template 標籤  

模板，他就是我們寫 HTML 的地方，跟之前的文章範例中，寫在 `#app` 裡面的那些東西一樣。

當然，如果使用 v-if 或是 v-for 時有需要，這個標籤也還是可以再寫一個 template 標籤，完全是沒有問題的。

3. style scoped 標籤  

就是寫 CSS 的地方。

我們在寫原生的 CSS ，即使拆分成多個檔案，只要掛到同一份 HTML中，那所有檔案都會影響到這份 HTML，沒辦法只限定特定區域。

不過 SCF 的 Style 標籤，可以多寫一個 scoped 屬性，表示要讓內容只限定在這個檔案內，其他地方不適用。

反之，如果沒有寫 scoped 屬性的話，寫的資料又會變成全域的了。