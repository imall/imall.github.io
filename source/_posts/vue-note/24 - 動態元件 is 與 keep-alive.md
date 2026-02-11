---
title: 動態元件 is 與 keep-alive
date: 2023-10-09 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 動態元件

網頁中有一種常見的功能，是能做到某個區塊可以點選畫面中的按鈕(或標籤)進行資料的抽換，這種功能在電商網站就很常會看到。

在 Vue 中要實踐這種功能，最直覺的方式可能是用 `v-if` 來達成。


首先在 Vue 中宣告幾個元件，當作要動態切換的內容，接著在實體宣告一個變數，準備用來讓 `v-if` 判斷顯示與否的用途。 


```js
const app = Vue.createApp({
  setup() {
    const appear = Vue.ref("A");
    return {
      appear,
    };
  },
});
app.component("componentA", {
  template: `我是元件A`,
});
app.component("componentB", {
  template: `元件B です`,
});
app.component("componentC", {
  template: `元件C 的部分`,
});
app.mount("#app");
```

把元件綁上 `v-if` 之後，在模版中加上三個按鈕，設定點擊事件去控制變數的資料，就能做到一個十分簡易的動態元件效果。

```html
<div id="app">
  <div class="label">
    <button @click="appear='A'">A</button>
    <button @click="appear='B'">B</button>
    <button @click="appear='C'">C</button>
  </div>
  <component-A v-if="appear =='A'"></component-A>
  <component-B v-if="appear =='B'"></component-B>
  <component-C v-if="appear =='C'"></component-C>
</div>
```

我們這種操作叫做動態元件的切換，可以到 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/41/) 試試看。

除了使用 `v-if` 之外，在 Vue 中還有另一個做法可以完成這個效果

# v-bind:is

元件的定義不用做任何改變：

```js
app.component("component-a", {
  template: `我是元件A`,
});
app.component("component-b", {
  template: `元件B です`,
});
app.component("component-c", {
  template: `元件C 的部分`,
});
```

不過在模版中，我們可以使用一個叫 `<component>` 的標籤，並且綁上 `v-bind:is` 去定義要出現的「元件名稱」，並且定義三個按鈕，作用是要在點擊之後，更改定義「元件名稱」的變數資料：

```html
<div id="app">
  <div class="label">
    <button @click="appear='component-a'">A</button>
    <button @click="appear='component-b'">B</button>
    <button @click="appear='component-c'">C</button>
  </div>
  <!-- 使用 component 標籤，綁上 :is，資料則是要寫上「元件名稱」  -->
  <component :is="appear"></component>
</div>
```



Vue 實體變數宣告方式不變，不過變數的值會改成「元件名稱」，這裡的寫法表示預設要出現 `component-a`：

```js
const app = Vue.createApp({
  setup() {
    const appear = Vue.ref("component-a");
    return {
      appear,
    };
  },
});
```

我把上面的範例放 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/43/) 給大家參考。

# 讓寫法更精簡一點

這個範例阿，如果搭配之前文章提到的 `v-for` 、 `computed` ，就能讓程式更精簡且好維護一些。

目前的範例有三個元件，所以要寫三個 `<button>` ，就代表未來如果要增加到十個元件， `<button>` 就要寫十次。

而 `v-for` 最擅長處理這種要處理好幾次的東西，只要寫好資料，交給 `v-for` 去把重複的東西處理好即可。

首先我們有 `A` 、 `B` 、 `C` 三個元件，所以定義一個陣列記錄三個資料：

```js
const tabs = Vue.ref(["A", "B", "C"]);
```

接著我們需要紀錄「目前點選」的是哪個資料，所以再加一個 `currentTab` 的資料：

```js
const currentTab = Vue.ref("A");
```

到這邊為止，能跑 `v-for` 的要素都完成了，可以把它寫到模板上：

```html
<button v-for="tab in tabs" :key="tab" @click="currentTab = tab">
  &#123;&#123;tab&#125;&#125;
</button>
```

最後，我們需要定義 `v-bind:is` 要用到的元件變數，這裡使用 `computed` 搭配剛剛的 `currentTab` 來操作：

```js
const appearComponent = Vue.computed(() => {
  return `component-${currentTab.value.toLowerCase()}`;
});
```

然後把這個變數寫到模板中就完成啦！

```html
<component :is="appearComponent"></component>
```

完整的程式我放[jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/45/)給有興趣的讀者玩玩看。

這就是一個很標準的實現最初所說的「關注點分離」的寫法，未來如果有動態元件要增加，只要去處理 JS 的內容，模板就會自動增加一個元件。

舉例來說，我如果需要增加一個動態元件，先把它定義出來：

```js
app.component("component-d", {
  template: `意外出現的動態元件 D`,
});
```

然後只要在狀態中再增加元件的資料其實就加好了：

```js
const tabs = Vue.ref(["A", "B", "C", "D"]);
```

完整的[jsfiddle 範例]((https://jsfiddle.net/imall/kdpo5qyc/47/))一樣提供給大家，有興趣的讀者可以跟剛剛提供的[jsfiddle 範例](https://jsfiddle.net/imall/kdpo5qyc/45/)比較。

# keep-alive

活下去！

如果動態元件的內容都只有靜態資料，其實剛剛的寫法就很夠用了。  
不過如果元件中有些可以跟使用者互動的機制，例如輸入框之類的東東。當使用者操作完資料後，切換元件的過程，這些資料是不會被保留的！

這裡提供一個範例給大家瞧瞧：

如果元件中有輸入框，像這樣：

```js
app.component("component-a", {
  template: `輸入框A： <input type="text">`,
});
app.component("component-b", {
  template: `輸入框B： <input type="text">`,
});
```

在某個元件中輸入的資料，是會在切換元件的過程被清除的：
![](https://i.imgur.com/82xhF68.gif)

對於這種問題，Vue 提供一個叫做 `<keep-alive>` 的特殊元件標籤來給我們使用，只需要拿這個標籤把動態元件的 `<component :is="xxx"></component>` 包起來即可：

```html
<keep-alive>
  <component :is="appearComponent"></component>
</keep-alive>
```

![](https://i.imgur.com/37j2nfl.gif)


完整的程式放[jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/49/)，有興趣的讀者可以進到裡面把 `<keep-alive>` 標籤拿掉試試看效果。
