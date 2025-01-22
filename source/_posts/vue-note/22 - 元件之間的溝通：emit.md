---
title: 元件之間的溝通：emit
date: 2023-10-07 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

前篇說到，在定義好元件之後，如果元件需要透過「外部」傳入狀態，可以使用 `props` 來傳遞，同時也提到 props 應該是 readonly 的特性，不該有能力更改外部的狀態。

但如果出現需要將元件的狀態傳遞給外部的情境，或是需要更改外部狀態的情境又該如何處理？

這時就會提到 Vue 中，元件溝通方式的口訣：「Props in, Event out」。

# 定義元件的事件

`emit` 可以讓我們定義「元件的事件」，透過這個事件我們可以做到兩件事：

1. 把元件內部的狀態傳出去外部
2. 讓外部決定事件被觸發時要做的事

第二點就像是我們可以針對某個 HTML 標籤定義 click 事件之後，去做某些事情一樣，可以更改變數資料，可以跳出一個 `alert()`，諸如此類。

我們先來定義一個元件吧：

```js
// 宣告實體
const app = Vue.createApp({
  setup() {
    const count = Vue.ref(0);
    return {
      count,
    };
  },
});

// 定義元件
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button>增加</button>
      `,
  props: ["count"],
});

app.mount("#app");
```

```html
<div id="app">
  <my-component :count="count"></my-component>
</div>
```

很單純的讓外部傳一個數字進去做渲染，並且設定一個按鈕，希望點擊過後數字會增加。

但問題一直就是，我們 **不可以** 直接在內部改 props，也就是不可以這麼寫：

```js
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button>增加</button>
      `,
  props: ["count"],
  setup(props) {
    const plus = () => {
      // 不可以直接改 props！！！！！！
      props.count++;
    };
    return {
      plus,
    };
  },
});
```

既然 props 是外部傳入的資料，我們可以定義一個 **事件**，要求外部把資料更新。

定義的方式是這樣，既然是點擊事件要讓數字增加，那麼就讓 **內部點擊事件觸發元件的事件**。

定義元件的事件，需要在元件的物件中 ，以 `emits` 屬性來宣告，這能告訴 Vue ，元件有哪些事件名稱。

接著需要在元件內把這個事件放在你想觸發的地方，以上面例子來說，就是 click 事件，所以語法是在 click 事件內寫 emit： `$emit('自己定義一個元件事件名稱')`

```js
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button @click="$emit('update')">增加</button>
      `,
  props: ["count"],
  // 宣告一個 emits
  emits: ["update"],
});
```

接著，就是在外層定義「更新資料的事件」：

```js
const app = Vue.createApp({
  setup() {
    const count = Vue.ref(0);
    const updateCount = () => {
      count.value++;
    };
    return {
      count,
      updateCount,
    };
  },
});
```

最後，在模版中把這兩件事情組合在一起！

```js
  <div id="app">
    <my-component :count="count" @update="updateCount"></my-component>
  </div>
```

完整的程式碼我放[這裡](https://jsfiddle.net/imall/kdpo5qyc/27/)

# 把元件內的資料往外傳

延續上面的例子，假使我希望增加的數字不要由外層決定，而是由元件自己定義，`$emit('元件事件名稱')` 方法可以讓我們把資料往外拋

做法也不難，把資料寫在事件名稱後面就好，假設我希望每點擊一次，外部就把資料 「+ 5」 ，那就把這段(`$emit('update',5)`) 寫在 click 事件中：

```js
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button @click="$emit('update',5)">增加</button>
      `,
  props: ["count"],
  // 宣告一個 emits
  emits: ["update"],
});
```

外部要接收資料，就是宣告參數來接就好：

```js
const app = Vue.createApp({
  setup() {
    const count = Vue.ref(0);
    // data 參數可以隨意定義，跟元件沒關係
    const updateCount = (data) => {
      console.log(data); // 你可以把資料印出來看看是不是元件丟出來的 5
      count.value += data;
    };
    return {
      count,
      updateCount,
    };
  },
});
```

完整的程式一樣提供給大家[玩玩](https://jsfiddle.net/imall/kdpo5qyc/28/)

# 把 emit 搬到 setup() 裡面撰寫

上面的例子只是單純在元件 click 事件觸發之後，讓外層觸發元件事件來傳遞資料的行為。

不過如果元件事件並不只「單純」的傳遞資料，而是還要做一些複雜的操作，可能就不適合直接把邏輯寫在模板上，這種邏輯內容就規劃上應該要寫在 setup() 之中。

寫法是這樣：

```js
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button @click="updateNumber">增加</button>
      `,
  props: ["count"],
  // 宣告一個 emits
  emits: ["update"],
  // 在 setup 加一個 cxt 參數(名稱也可以隨意定義)
  setup(props, ctx) {
    const updateNumber = () => {
      // 假設固定要把 props.count + 1 的數值傳回外層
      const data = props.count + 1;
      // 這句可以執行 emit 方法
      ctx.emit("update", data);
    };
    return {
      updateNumber,
    };
  },
});
```

要讓元件的 setup() 使用 emit，需要定義 setup()  第二個參數，官網定義為 `ctx` ，但實際上可以自己定義喜歡的名稱。

這個參數，也可以用解構的方式把 emit 解構出來用：
```js
app.component("my-component", {
  template: `
        <div>{{count}}</div>
        <button @click="updateNumber">增加</button>
      `,
  props: ["count"],
  emits: ["update"],
  // 用解構語法解構 emit
  setup(props, {emit}) {
    const updateNumber = () => {
      const data = props.count + 1;
      emit("update", data);
    };
    return {
      updateNumber,
    };
  },
});
```

無論如何，就是要在元件中再定義一個方法給去處理執行 emit 之前的邏輯，最後再把這個方法丟去模板中給 click 使用，就是元件模板中的這段：
```html
<button @click="updateNumber">增加</button>
```


這樣就完成啦，完整的程式[放這](https://jsfiddle.net/imall/kdpo5qyc/31/)

# 總結
這篇文章紀錄把元件把訊息告訴外部，以及要求外部更新訊息的方式：emit。

筆者覺得這對新手來說應該會很混亂，至少當時筆者接觸到這個觀念時，有點被外層事件、元件事件搞糊塗。

不過其中的原理，就只是定義元件的「事件」，讓外部去控制事件被觸發時，該做什麼邏輯判斷而已。

如果理解這個觀念後，emit 的用法應該就會變得好懂許多！