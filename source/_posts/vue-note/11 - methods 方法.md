---
title: methods 方法
date: 2023-09-26 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言
今天聊聊 Vue 實體中的 methods， 中文叫做「方法」。

如同我們在說「物件的方法」一樣，Vue 世界如果說到「方法」，其實就是在講函式(function)，而且 methods 的用途就跟 JavaScript 函式一樣，是拿來封裝邏輯。

如果方法有個回傳值，那它就可以寫在模版中。

除此之外，就像 JS 的 function 一樣，他也可以被拿來封裝「事件」要做的事情。

這篇文章會稍微用到 `v-on` 的指令 ，簡寫 `@`，雖然還沒寫到，不過語法就還蠻直覺的，應該很容易懂。  
~~(其實之前的文章就有出現過，沒這東西實在有點難寫例子)~~
# method 撰寫位置
Vue 元件的方法，會寫在 `setup()` 函式裡頭：

```js
const { createApp, ref } = Vue;
createApp({
  setup() {
    const count = ref(0);
    // plus 就是「方法」
    const plus = () => {
      count.value++;
      console.log(count);
    };
    return {
      count,
      plus,
    };
  },
}).mount("#app");
```

```html
<div id="app">
  <button @click="plus()">&#123;&#123; count &#125;&#125;</button>
</div>
```

# method 的使用

## 封裝邏輯

通常我們會使用到函式(function)，往往是希望將某部分的商業邏輯進行整合，在需要的時候可以拿來重複使用。

記得上一篇提到，雙大括號裡面可以撰寫簡單的商業邏輯，但即便再簡單，只要寫多了就一定不好維護。

試想在模版中四處都寫了一樣的邏輯，被要求更改的時候就只能一個一個改，那有多麻煩。

在模板的大括號裡面，只要「能回傳一個值」的內容都可以寫進去。  

我們可以將邏輯包覆在「方法」中，在方法內寫好回傳的資料，再把它丟到模版上使用：

```html
<div>
  <div class="plus">&#123;&#123; plus() &#125;&#125;</div>

  <div class="plus">&#123;&#123; plus() &#125;&#125;</div>
</div>
```

```js
const { createApp, ref } = Vue;
const app = createApp({
  setup() {
    const dataA = ref(123);
    const dataB = ref(456);
    const plus = () => dataA.value + dataB.value;
    return {
      plus,
    };
  },
});
app.mount("#app");
```

當然，這個方法也能帶入參數，讓功能更彈性：

```html
<div id="app">
    <div> 價格： $ &#123;&#123; plus(price1) &#125;&#125; </div>
    <div> 價格： $ &#123;&#123; plus(price2) &#125;&#125; </div>
    <div> 價格： $ &#123;&#123; plus(price3) &#125;&#125; </div>
</div>
```

```js
const { createApp, ref } = Vue;
const app = createApp({
  setup() {
    const price1 = 123456;
    const price2 = 654321;
    const price3 = 556888;
    // 把邏輯封裝起來，在模版中重複使用
    const plus = (data) => data.toLocaleString();
    return {
      plus,
      price1,
      price2,
      price3
    };
  },
});
app.mount("#app");
```

> 實務上這樣寫其實還是不好維護，不過 **v-for 指令** 還沒講到，這裡先用「暴力法」示範方法的使用。

## 在事件上使用

除了封裝邏輯，方法也會用在事件上。  
所謂的事件，就是你我都熟悉的 `document.addEventListener` 會綁定的那些東西，最常見的事件大概就是點擊事件 `click` 了。


實際寫法其實在文章開頭就見過了：

```html
<div id="app">
  <button @click="plus()">&#123;&#123; count &#125;&#125;</button>
</div>
```

```js
const { createApp, ref } = Vue;
createApp({
  setup() {
    const count = ref(0);
    const plus = () => {
      count.value++;
    };
    return {
      count,
      plus,
    };
  },
}).mount("#app");
```


# 總結

1. Vue 世界中的 **方法**，跟物件的方法一樣，就是個函式。
2. 方法常用於封裝邏輯，讓維護更容易。
3. 需要撰寫事件方法，也是在 setup() 撰寫好函式，綁到 v-on 指令中。