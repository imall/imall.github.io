---
title: Vue 元件實體生命週期
date: 2023-09-23 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

你可能會疑惑，我們還沒接觸元件，為什麼現在在講元件的生命週期

事實上，一個 Vue 實體，就是一個元件，所以我們只要開始使用 Vue 之後，就必須跟元件的生命週期打交道了。

# 元件的生命週期

之前應該有說明過「元件」是啥東東了，現在又出現了看起來很困難的詞彙：生命週期。

人的生老病死，月的陰晴圓缺，這種生命週期相信大家應該都能體會。

沒辦法理解的地方大概是，「程式」的生命週期是什麼東西....

事實上就跟你我理解的生命週期一樣，Vue 元件的生命週期，就是在探討元件掛到畫面上一直到元件從畫面消失過程會發生的事情。

如同人的生老病死一般，元件掛載到畫面的過程，也會許多「細節」需要被「產生」、「更動」、「移除」。

認識元件的生命週期，就是要去了解每個細節發生的時間點。

Vue 根據這些關鍵時間點，提供一些「特殊的函式」給我們用，讓我們可以針對特殊時間點操控元件。  
我們稱這些函式叫作「生命週期鉤子 (hook)」。

![](https://i.imgur.com/FfdApC3.png)

來聊一下這張表的內容，哪些地方是關鍵時間點：

## setup()

![Alt text](https://i.imgur.com/XMbus3s.png)
我們已經知道 `createApp` 是用來產生 Vue 實體的函式，並且知道實體要透過 mount 方法把實體掛到某個 Dom 上。
這裡的一個重點是，即便 createApp 裡面已經寫好 setup() 的內容，只要沒有執行 `.mount('#app')` ，那麼 setup 就不會被啟動。
像是下方的寫法，那個 console.log 是不會被執行的。

```js
Vue.createApp({
  setup() {
    console.log("我是 setup!!!");
  },
});
```

必須在寫了 .mount 之後， setup() 才會啟動。

```js
Vue.createApp({
  setup() {
    console.log("我是 setup!!!");
  },
}).mount("#app"); //這個 mount 決定 setup 會不會被執行
```

## `Has pre-compiled template`：

![Alt text](https://i.imgur.com/pGoGaZS.png)

在 Vue3 的世界中，這個判斷點之上只需要討論 `setup()`，其他都可跳過了。

這個判斷點是要判斷我們寫的「實體」中，有沒有包含 `template`，像這樣：

```js
Vue.createApp({
  // ：哈哈  template 就是我啦   (設計對白)
  template: `
    <h1> 這是template裡面的內容</h1>
    `,
  setup() {
    console.log("我是 setup!!!");
  },
}).mount("#app");
```

```html
<div id="app">
  <span>這是一個因為 template 有寫東西，而永遠不會被看到的內容</span>
  <span>好啦，除非你打開瀏覽器，點了「檢視原始碼」才看得到我</span>
  <div></div>
</div>
```

我想說的話都寫在 HTML 了，我們繼續下面的內容。

## 實體是否已經與模板綁定

![Alt text](https://i.imgur.com/vaYaU6t.png)

接下來這塊要一起看，我們在模板寫的內容總是需要渲染到瀏覽器，其中一個關鍵動作，就是 DOM 節點是不是已經渲染到瀏覽器中。

這個時間點，大概可以想成 jQuery 中的 `$( document ).ready()` 或是 純 JS 的 `DOMContentLoaded`事件。

在這裡認識兩個重要的生命週期鉤子 `beforeMount` 跟 `mounted`。  
就是設計在 實體與模板綁定的「前」與「後」。

在 `beforeMount` hook 內可以寫一些 DOM 節點還沒完成前就可以做的初始化動作。  
在 `mounted` hook 中，就會寫 DOM 節點生成好後，才能做的事情。這大概也是最常使用的生命週期鉤子了。

實際的語法是這樣寫，記得生命週期鉤子都是從 Vue 引入的，並且在函式裡面寫 callback，撰寫想要處理的邏輯。

值得注意的是，下面範例有刻意將撰寫順序跟生命順序相反，Vue 的生命週期鉤子都是非同步的，所以即使順序相反，也會依據生命週期順序來執行。

可以到 [這裡](https://jsfiddle.net/imall/q6btwn4a/26/) 玩玩看。

```js
const { onBeforeMount, onMounted } = Vue;
Vue.createApp({
  setup() {
    // mounted  鉤子
    onMounted(() => {
      console.log("onMounted");
      console.log(document.querySelector('#app').innerHTML)
    });

    // beforeMount 鉤子
    onBeforeMount(() => {
      console.log("onBeforeMount");
      console.log(document.querySelector('#app').innerHTML)
    });
  },
}).mount("#app");
```

> 如果對 「callback」 、「非同步」的觀念不太了解的讀者  
> 推薦閱讀 Kuro 寫的書： [0 陷阱！0 誤解！8 天重新認識 JavaScript！](https://www.tenlong.com.tw/products/9789864344130)

## 狀態的更新

![Alt text](https://i.imgur.com/xiS4oC5.png)
在 mounted hook 結束之後的下一步，就是真的把元件渲染到畫面上了，此時就會開始探討狀態有沒有被更新。

正如其鉤子名稱一般， `beforeUpdate` 跟 `updated` 就是當元件的任何狀態有被更動時，就會觸發的鉤子。

```js
const { onUpdated, onBeforeUpdate, ref } = Vue;
Vue.createApp({
  setup() {
    const data = ref(0);
    const plus = () => {
      data.value++;
    };
    onUpdated(() => {
      console.log("updated");
      console.log(data.value);
    });
    onBeforeUpdate(() => {
      console.log("before update");
      console.log(data.value);
    });
    return {
      data,
      plus,
    };
  },
}).mount("#app");
```

不過你可以進 [這裡](https://jsfiddle.net/imall/oamb8wLp/3/) 玩玩看，如果你點擊 +1 之後，你認為`beforeUpdate` 跟 `updated` 會跑出什麼東西。

(我沒辦法在文章中等你，就直接講答案了)

兩個鉤子吃到的資料都是「狀態更動後」的結果：
![Alt text](https://i.imgur.com/Bii4fZb.png)

原因在於那個 `when data changes` 這句話，這兩個鉤子，都是在「資料改變」之後才觸發，兩個鉤子的切割點並不是資料更動前與後，而是 `re-render and patch` ，畫面「重新渲染」前與後。

不過在 Vue 中要去監控某個狀態，還有 watch 可以用，實務上真的很少(應該說幾乎沒有)用過這兩個鉤子，但圖上都寫了，身為一個稱職的筆記，就把它紀錄下來。

## 元件的消逝...

![Alt text](https://i.imgur.com/Ue8OlBU.png)

元件什麼時候會消逝? 一般來說就是關閉瀏覽器的時候
此外還有一種狀況，未來指令的內容會提到一個叫 `v-if` 的指令，他能去控制元件在 DOM 中是否要被移除。

廢話不多說，[玩玩看](https://jsfiddle.net/imall/kdpo5qyc/10/) 吧。


```js
const { onBeforeUnmount, onUnmounted, ref } = Vue;
var app = Vue.createApp({
  setup() {
    const isShow = ref(true);
    return {
      isShow,
    };
  },
});
app.component("hello", {
  template: `<div>hello component</div>`,
  setup() {
    onBeforeUnmount(() => {
      console.log("onBeforeUnmount");
    });
    onUnmounted(() => {
      console.log("onUnmounted");
    });
  },
});
app.mount("#app");
```

# 總結

這篇介紹了元件的生命週期鉤子

除了 setup() 之外，常見的生命週期鉤子還有這些：

1. beforeMount
2. mounted
3. beforeUpdate
4. updated
5. beforeUnmount
6. unmounted
