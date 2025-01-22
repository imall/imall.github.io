---
title: 元件之間的溝通：prop
date: 2023-10-06 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

這篇文章來記錄一下，當父層元件傳遞資料給子層元件的語法：`props`

# props

我們已經知道在模版中要使用元件，會是這樣的語法：

```html
<div id="app">
  <my-component></my-component>
</div>
```

要讓元件有辦法接收變數，需要先在元件中多加一個 `props` 屬性：

```js
app.component("my-component", {
  template: `
    <div>
      <div> {{ message }} </div>
    </div>`,
  // message 是自己定義的變數
  props: ["message"],
  setup() {},
});
```

這段定義好之後，就可以在父層的模板，把值傳到這個參數中，以上面定義的 `message` 參數為例子：

```html
<div id="app">
  <my-component message="簡單的小情歌"></my-component>
</div>
```

完整的程式可以到 [這裡看](https://jsfiddle.net/imall/kdpo5qyc/12/)

上面的例子可以看出， `props` 的寫法就好像在定義 HTML 標籤的屬性，既然想到 HTML 屬性就不能不提到一個指令 `v-bind`

沒錯， `props` 如果搭配 `v-bind` ，就可以在父層傳入變數給子層渲染。

如果父層的資料有更變，元件的資料也是有辦法響應式的更動，十分方便！

```html
<div id="app">
  <my-component :message="msg"></my-component>
  <button @click="changeData">更換資料</button>
</div>
```

```js
const app = Vue.createApp({
  setup() {
    const msg = Vue.ref("這是一筆資料");
    const changeData = () => {
      msg.value = "這是抽換的資料";
    };
    return {
      msg,
      changeData,
    };
  },
});

app.component("my-component", {
  template: `
    <div>
      <div> {{ message }} </div>
    </div>`,
  // message 是自己定義的變數
  props: ["message"],
});

app.mount("#app");
```

可以[到這](https://jsfiddle.net/imall/kdpo5qyc/17/)玩玩看。

# 物件形式的 props 宣告

上面 `props` 的宣告是採用 **陣列** 宣告，由於陣列並不是 key-value 的方式紀錄資料，使得這種宣告方式只能宣告名稱，沒辦法對 `props` 的需求定義得更細一點。

Vue 同時也提供 **物件** 的 `props` 宣告方式：

```js
app.component("my-component", {
  template: `
    <div>
      <h1> {{ name }} </h1>
      <p> {{ age }} </p>
    </div>`,
  // 以物件定義 props ，可以宣告資料的型別
  props: {
    name: String,
    phone: Number,
  },
});
```

用上面的方法，就可以再定義 `props` 的「型別」，這個型別是給開發人員看的，讓我們開發時可以迅速得知資料可能呈現的內容。

以上面例子來說，如果 `phone` 參數傳入了 **非 Number** 類型的資料：

```html
<div id="app">
  <my-component name="imall" phone="一段文字"></my-component>
</div>
```

那開發者工具會跑出這樣的訊息：
![](https://i.imgur.com/YUHTc2v.png)

從圖中可以注意到，Vue 並不會因為定義的型別，與實際的型別不同而無法渲染，他只是跳出一個 `warning` ，跟我們說型別好像不太對勁！

# 元件的 props 太多，也可以在父層定義物件傳入資料

如果一個元件定義了太多資料，要從父層傳資料到元件，用上面的語法可能會寫一脫拉庫的內容：

```html
<div id="app">
  <my-component
    name="imall"
    phone="0987654321"
    :age="18"
    hobby="釣魚"
    img="https://XXX.XXX.XXX"
    ...
    ...
    ...
  ></my-component>
</div>
```

我們都知道在模版中寫太多資料，很容易造成維護的困難，所以 Vue 為了這種狀況提供一種方式給我們處理：定義一個物件，使用 `v-bind` 把資料傳進去。

舉例來說，元件的內容長這樣：

```js
app.component("my-component", {
  template: `
    <div>
      <h1> {{ name }} </h1>
      <p> {{ age }} </p>
    </div>`,
  props: {
    name: String,
    age: Number,
  },
});
```

父層可以這樣定義變數：

```js
const app = Vue.createApp({
  setup() {
    // 這是預計要傳入元件的資料
    const msg = {
      name: imall,
      age: 18,
    };
    return {
      msg,
    };
  },
});
```

接著在模板這麼寫，資料就會自動在元件解構：

```html
<div id="app">
  <my-component v-bind="msg"></my-component>
</div>
```

可以到[這邊](https://jsfiddle.net/imall/kdpo5qyc/24/)玩玩看。

# 在元件中取得 props 的資料

元件能接收外部資料後，資料不一定每次都是直接渲染在模板上而不加工，總會有程式需要使用的狀況。

例如從外部透過元件的 `props` 傳入 ID ，每個元件拿到不同 ID 後去呼叫 API 取得資料這類的行為。

要在元件中取得資料，需要在元件的 `setup` 傳入 `props` 參數，就可以用物件取值的方式抓到元件的資料了。

```js
app.components("my-component", {
  template: `
    <div>
      <h1> {{ name }} </h1>
      <p> {{ age }} </p>
    </div>`,
  props: {
    name: String,
    age: Number,
  },
  // 這裡傳入 props 參數
  setup(props) {
    console.log(props.name);
  },
});
```

# 單向資料流

語法寫完之後，props 最重要的觀念，就是 props 應該要是一個 `readonly` 只能讀的資料。

也就是我們不可以在子元件更改 props 的資料，像是這樣的做法：

```js
app.components("my-component", {
  template: `
    <div>
      <h1> {{ name }} </h1>
      <p> {{ age }} </p>
    </div>`,

  props: {
    name: String,
    age: Number,
  },

  setup(props) {
    props.name = props.name.toUpperCase();
  },
});
```

這是因為，假如我們定義了十個元件好了，每個元件都能使用父層的某一個變數，然後父層還真的一次使用十個元件，並且傳入這個變數。

我們已經知道 `props` 會因為父層資料的異動產生響應的改變，如果子層能亂改資料的話，上面那個情境中，只要一個子元件改動資料，其餘十個元件的資料都會被更改。

雖然有些情況可能會是個蠻方便的特性，但絕大多數的情況只會造成資料的難以追溯。

但實際的情境中，或多或少還是會有需要對父層傳入的 `props` 進行加工，再渲染的可能，有這種情況的話，官方建議多加一個 `computed` ：

```js
app.components("my-component", {
  template: `
    <div>
      <h1> {{ name }} </h1>
      <p> {{ age }} </p>
    </div>`,

  props: {
    name: String,
    age: Number,
  },

  setup(props) {

    // 應該使用 computed 把 props 包裝起來之後再使用
    const UpperCaseName = Vue.computed(() => props.name.toUpperCase());
    return {
      UpperCaseName,
    };
  },
});
```


我們不直接更改 `props` 的資料，而是多包一層 `computed` ，把加工的內容回傳出來使用，這樣才能避免從元件內更改資料後，直接到影響元件外部的資料，導致變動難以追蹤的後果。

# 總結

這篇文章紀錄了從外層傳入資料給元件的語法 `props`，以及一個重要的觀念：`props` 應該要是 `readonly`，不可在元件內部隨意改動 `props` 的資料。

知道不能透過 `props` 來改動內部的資料，造成外部資料變動的概念之後，接著大概會遇到另一個問題：如果元件「需要」外部的資料變動怎麼辦？

下一篇文章預計來記錄，透過 `emit` 去告訴元件外的內容更改傳入的資料。