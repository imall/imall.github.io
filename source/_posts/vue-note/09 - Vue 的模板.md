---
title: Vue 的模板
date: 2023-09-24 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 模板語法
先說一個觀念：模板，其實不完全等於 HTML，只是剛好 Vue 使用的模板語法，完全支援 HTML。所以我們使用 Vue 的時候如果提到的「模板」，可以直接把他當成 HTML。

不過這其實有點太鑽牛角尖，在跟人討論 Vue 的時候，講 **模板** 或是 **HTML** ，大家應該都聽得出就是在操作那個東西。

回到官網看說明吧：
[Vue 官方文件](https://cn.vuejs.org/guide/essentials/template-syntax.html)的第一句話：
> Vue 使用一种基于 HTML 的模板语法，使我们能够声明式地将其组件实例的数据绑定到呈现的 DOM 上。所有的 Vue 模板都是语法层面合法的 HTML，可以被符合规范的浏览器和 HTML 解析器解析。

這段話隱含著三個觀念：
1. 我們在 Vue 實體掛載的那個 HTML 節點(`#app`)所撰寫的內容，已經不是「純」的 HTML，它只是一個「長得很像」HTML 的「模板語法」。
2. 由於語法「基於」HTML，所有 HTML 語法完全可以寫在 Vue 中。
3. 在「模板語法」中，可以透過「宣告式」的方式，把資料跟模板綁定。

怎麼宣告？雙大括號：

# Mustache 語法
Vue 支援的資料綁定語法，是透過一個叫「Mustache」的語法來處理。

這個語法的寫法很簡單，就是直接寫兩組大誇號，並且在裡面放資料，像這樣：
```html
{{ 只要會回傳一個值的資料都可以塞進來 }}
```

如果要在 HTML 標籤放資料，可以不用「只」放雙大括號，也可以把「寫死的資料」跟雙大括號結合，像這樣：

```html
<div id="app">
    <div> 歌名：{{ song }} </div>
</div>
```
```js
Vue.createApp({
    setup() {
        const song = 'あの夢をなぞって'
        return { song }
    },
}).mount("#app");

```

甚至如同 HTML 一般，我們可以不用任何標籤的包覆，即可使用雙大括號：

```html
<div id="app">
    {{ anyData }}
</div>
```
```js
Vue.createApp({
    setup() {
        const anyData = '987654321'
        return { anyData }
    },
}).mount("#app");
```


# 使用 JavaScript 表達式

在雙大擴號裡面能寫的東西，除了純粹的「變數」之外，也可以寫「JS表達式」。

換言之，只要雙大括號裡面的內容「有辦法回傳出一個值」，都是合法的資料。

例如：
```html
{{ 'hello' + 'world' }}

{{ 1 + 1 }}

{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}
```

這種寫法的好處是，我們在需要的時候可以把部分商業邏輯寫到模版裡頭。  

像是如果某個數字資料代表幣別，希望畫面上能呈現出貨幣格式，那可以這樣寫：
```html
<div id="app">
    <div> 價格： $ {{ price1.toLocaleString() }} </div>
    <div> 價格： $ {{ price2.toLocaleString() }} </div>
    <div> 價格： $ {{ price3.toLocaleString() }} </div>
</div>
```
```js
Vue.createApp({
    setup() {
        const price1 = 123456;
        const price2 = 654321;
        const price3 = 556888;
        return { price }
    },
}).mount("#app");
```

另外補充一下哪些語法屬於不合法的語法：
```html
{{ var a = 1 }}

{{ if (ok) { return message } }}
```
以上都算是不合法的語法，因為他們都不是「回傳值」，而是「宣告」或是「控制流程」。


# 指令
Vue 除了支援雙大括號的資料綁定語法之外，還有一種叫做「指令」的語法，通常長得像 v-XXX。 
像是：`v-on`、`v-bind`、`v-model`、`v-if`、`v-for` 等等。

而這些東西的寫法就會像 HTML 的屬性一樣，寫在 HTML 標籤中，例如：
```html
<div id="app">
    <input type="text" v-model="message">
    <div v-model="message"></div>
</div>
```
```js
Vue.createApp({
    setup() {
        const message = 'hello world'
        return { message }
    },
}).mount("#app");
```

其中可能還會有一些縮寫，例如 `v-bind` 可以縮寫成 `:`，`v-on` 可以縮寫成 `@`，像這樣：
```html
<div id="app">
    <div :class="classObject"></div>
    <div @click="doSomething"></div>
</div>
```

更詳細的使用方式，預計在後面的章節會再提到，這裡先記錄 Vue 的模板可以寫些什麼東西。


# 總結
1. Vue 使用「基於」HTML 的模板語法，只要是合法的 HTML 語法，都可以在 Vue 中撰寫。
2. Vue 的資料綁定語法使用 Mustache 語法，寫法是雙大誇號(`{{}}`)，可以單獨出現在模版中，不一定要跟 HTML 標籤寫在一起。
3. 在雙大誇號中，除了變數之外，也可以寫 JS 表達式。
4. Vue 指令的寫法跟 HTML 屬性很像，通常長得像 `v-XXX`，例如 `v-on`、`v-bind`、`v-model`、`v-if`、`v-for` 等等。