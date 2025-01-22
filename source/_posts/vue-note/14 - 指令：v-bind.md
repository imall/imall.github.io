---
title: 指令：v-bind
date: 2023-09-29 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

今天來講 v-bind ，一個用來在 HTML 屬性綁資料的指令。

我們已經知道雙大括號的語法可以把「變數」綁在模板內，不過這個語法並不是用於 HTML 的屬性，也就是你不能這樣寫：

```html
<a href="{{ urlData }}"></a>
```

為了能在 HTML 屬性綁資料， Vue 提供 v-bind 指令給大家使用

# 基本語法

語法很簡單，使用 `v-bind:` 加上原本我們已經熟知的屬性即可。

```html
<img v-bind:src="imageSrc" />
```

由於每次都要寫 `v-bind:` 實在很麻煩，於是 Vue 提供一個縮寫(`:`)可以代替 `v-bind:`：

```html
<img :src="imgSrc" />
<!-- 語法等同於 -->
<img v-bind:src="imgSrc" />
```

# Class 的綁定

一般 HTML 屬性的資料往往是「一個」值，但 class 的資料可能會出現好幾個值，所以 Vue 提供了幾種不同的寫法：

1. 直接寫一個物件，並且把 class 名稱當作 key，值為布林值，當值為 true 時，該 class 會被加入，反之則不會。

```html
<div :class="{ red: isRed }"></div>
```

```js
Vue.createApp({
  setup() {
    const isRed = Vue.ref(true);
    return { isRed };
  },
}).mount("#app");
```

這個寫法的好處是，我們只要去控制 `isRed` 變數值，就可以動態更改模板的 class 名稱。


可以寫多組資料：
```html
<div :class="{ red: isRed , yellow:isYellow}"></div>
```


2. 用陣列塞入多個變數：
```html
<div :class="[textClass, modelClass]"></div>
```

```js
Vue.createApp({
  setup() {
    const textClass = Vue.ref("text-xl text-[#ccc]");
    const modelClass = Vue.ref("mt-5 p-10");
    return { classA, classB };
  },
}).mount("#app");
```

3. 物件跟陣列混用：  
只用陣列的語法，資料可以不用是純變數，也可以塞一個物件，在這個物件內就可以使用布林值判斷是否出現 class 名稱

```html
<div :class="[classA, { classB: isB, classC: isC }]"></div>
```

# Style 綁定

想綁 style 屬性也是有物件型寫法，跟陣列型寫法

1. 物件型寫法：  

```html
<div :style="{ fontSize: size + 'px' }"></div>
```

2. 陣列型寫法：  
```html
<div :style="[styleObjectA, styleObjectB]"></div>
```

此時的 JS 會是這樣紀錄：

```js
Vue.createApp({
  setup() {
    const styleObjectA = Vue.ref({
        'font-size': "16px",
        'font-weight': "bold",
    });
    const styleObjectB = Vue.ref({
        'margin':'auto',
        'padding':'10px'
    });
    return { styleObjectA, styleObjectB };
  },
}).mount("#app");
```

渲染的結果會長這樣；
```html
<div id="app" data-v-app>
    <div style="font-size: 16px; font-weight: bold; margin: auto; padding: 10px;"></div>
</div>
```

# 總結

1. v-bind 指令可以讓 HTML 屬性綁上變數
2. v-bind 可以用縮寫 `:` 代替
3. class、style 綁定法可以寫成物件形式，也可以寫成陣列形式 