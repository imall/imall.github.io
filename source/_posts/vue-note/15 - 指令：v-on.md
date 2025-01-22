---
title: 指令：v-on
date: 2023-09-30 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

今天來聊聊 Vue 中事件的寫法。

# 基本語法

如果你有看過怎麼在 HTML 寫事件，像是 `onclick="functionToExecute()"` 之類的，那你對於 Vue 提供的是鍵語法應該會很熟悉。

基礎與法式使用 `v-on:[事件名稱]` ，例如 `v-on:click="func()"` 、 `v-on:keydown="func()"`。
這個指令也有縮寫：`@`。也就是說可以寫成 `@click="func()"` 、`@keydown="func()"`

在模板上看起來會長這樣：

```html
<button @click="plus()">+</button>
```

# 事件的參數

說到事件，不得不提到事件的參數，在`方法`的篇章已經有聊到 v-on 指令常會搭配 「方法」一起使用。

當時沒有講到的是， v-on 綁定的 method 支援一個特殊的參數 `$event`，可以讓我們取得事件的 event 物件。

這個物件在傳統 JS 來說可能會寫個 `e` 來接這個參數，像這樣：

```js
const $btn = document.getElementById("btn");
$btn.addEventListener("click", function (e) {
  console.log(e);
});
```

而如果是 v-on 指令，則可以這樣寫：

```html
<div id="app">
  <!-- 一定要是 $event 不能是其他值  -->
  <a href="https://google.com.tw" @click="plus($event)">{{data}}</a>
</div>
```

```js
Vue.createApp({
  setup() {
    const data = Vue.ref(0);
    const plus = (e) => {
      //模板有寫 $event 這裡就能抓到了
      e.preventDefault();
      data.value++;
    };
    return { data, plus };
  },
}).mount("#app");
```

不過如果方法中需要帶其他參數，就要記得，模板寫的參數位置，跟方法中的參數位置必須一樣：

```html
<div id="app">
  <!-- 因為在實體中寫的參數，第一個是接「事件參數」，第二個是某個資料值，所以兩者順序不能換 -->
  <a href="https://google.com.tw" @click="plus($event , 5)">{{data}}</a>
</div>
```

```js
Vue.createApp({
  setup() {
    const data = Vue.ref(0);
    const plus = (e, value) => {
      //模板有寫 $event 這裡就能抓到了
      e.preventDefault();
      data.value = value;
    };
    return { data, plus };
  },
}).mount("#app");
```

# 修飾子

## 常見通用修飾子
針對一些特殊的事件操作，Vue 支援一些特殊的修飾子。

```
.stop
.prevent
.capture
.self
.once
```

一個一個來介紹

### .stop

他就是 `.stopPropagation()` ，阻擋事件冒泡用的。

我們都像是這種 HTML 結構，如果在 `.inner` 綁點擊事件，點了他之後，同時也會讓 `.outer` 也觸發點擊事件。
一般我們就會使用 `e.stopPropagation()` 來阻擋事件冒泡。

```html
<div class="outer">
  這是外面
  <div class="inner">這是裡面</div>
</div>
```

因為很常用，所以 Vue 提供了 `.stop` 語法糖讓我們使用，用法就是直接在 event 名稱接 `.stop` 即可。

```html
<div id="app">
  <div class="outer" @click="console.log('outer')">
    這是外面
    <div class="inner" @click.self="console.log('inner')">這是裡面</div>
  </div>
</div>
```

### .prevent

如果你懂 JS ，可能也不用多說什麼了，這個修飾子就是 `e.preventDefault()` ，阻擋預設行為用的。

例如上面在講 $event 的範例，methods 接了 a 標籤的點擊事件後，做了 `e.preventDefault()` 的行為。

事實上在 Vue 中可以直接這樣寫：

```html
<a href="https://google.com.tw" @click.prevent="console.log('hello!!!')"
  >點我啊</a
>
```

而且 Vue 的事件就像 jQuery 語法一樣，是可以接續著寫下去的：

```html
<a href="https://google.com.tw" @click.stop.prevent="console.log('hello!!!')"
  >點我啊</a
>
```

### .capture

我們再把這個結構請出來用：

```html
<div class="outer">
  這是外面
  <div class="inner">這是裡面</div>
</div>
```

一般來說，如果沒有做任何設定，點擊 inner 時，會先觸發 inner 的 click ，才會觸發 outer 的 click 事件。
這是預設的 JS 事件冒泡的機制。

不過如果我們在「外層」加一個 .capture，那整個操作行為就會反過來，變成先觸發 outer ，才觸發 inner
。

```html
<div class="outer" @click.capture="console.log("hello!")">
    這是外面
    <div class="inner"  @click="console.log('裡面!')">
        這是裡面
    </div>
</div>
```

### .self

self 中文是 自己。

所以當事件加上這個修飾子，就會限定只點自己才有效。

剛剛在 .stop 的地方有講到事件冒泡，點擊內層結構也會觸發外層結構的事件。

以常見的「燈箱效果」來說，如果這個結構是一個燈箱效果，外面那層是整個黑幕，裡面才是實際的燈箱，當燈箱跳出來的時候，我們希望點擊黑幕才觸發關閉事件，點到內容不做任何事，那我們就可以直接在外面那層加上 `.self`

```html
<div class="outer">
  這是外面
  <div class="inner">這是裡面</div>
</div>
```

好啦...我知道我說得很清楚，你卻聽得很模糊，直接[進去](https://jsfiddle.net/imall/q6btwn4a/23/)看程式，試試看把 .self 拿掉，打開燈箱，然後點點看「這是裡面」，看會有什麼變化吧。

> 可能有人會把 .self 跟 .stop 搞混，會以為 在內層設定 .self 可以阻止冒泡。
> 這裡必須強調 **.self 不是拿來阻止冒泡行為的！！！！**

### .once

這個就跟 v-once 的概念很像， v-once 是資料綁到模板之後，資料怎麼更動後都不會再重新渲染。
.once 指令，就是讓事件只觸發一次，然後就不會再有然後了。


.once 跟 v-once 不太一樣的地方是，之前講 v-once 的時候，示範畫面沒有異動，但資料還是有被異動。

不過.once 控制的是「事件」，換言之是讓那個 function 只觸發一次，所以如果裡面有改動資料，就也只會改動那一次，之後怎麼執行事件，事件的函式就是不會再執行。

```html
<div id="app">
  <button @click.once="plus()">{{count}}</button>
</div>
```

```js
Vue.createApp({
  setup() {
    const count = Vue.ref(0);
    const plus = () => {
      count.value++;
      console.log(count.value);
    };
    return { count, plus };
  },
}).mount("#app");
```

## 鍵盤類型修飾子

除了通用修飾子，針對鍵盤， Vue 也提供一些不錯用的修飾子

例如執行 @keydown 事件時，只想讓 enter 鍵才執行某個行為，則可以寫

```html
<div @keydown.enter="console.log('enter')"></div>
```

其他常見的還有這些：

~~~
.enter
.tab
.delete
.esc
.space
.ctrl
.shift
~~~
