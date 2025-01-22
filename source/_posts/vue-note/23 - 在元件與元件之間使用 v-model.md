---
title: 在元件與元件之間使用 v-model
date: 2023-10-08 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---
# 前言

之前提到，元件如果要接收資料，必須用 `props`
元件如果要向外傳資料，則是定義 `emit` 事件

假設我們的元件有個 `<input>` 標籤，我們希望元件內部的 `<input>` 可以跟外部的資料做到雙向綁定，那們根據之前提到的寫法，可能會這樣寫：

```js
const app = Vue.createApp({
  setup() {
    const message = Vue.ref("hello");

    const updateMsg = (val) => {
      message = val;
    };

    return {
      message,
      updateMsg,
    };
  },
});

app.component("my-component", {
  // :value 把 prop 綁到 input 的 value
  // 透過 @input 事件把使用者輸入的資料往外傳
  template: `
    <input :value="msg"
    @input="$emit('update', $event.target.value)"
  />
      `,
  props: ["msg"],
  emits: ["update"],
});

app.mount("#app");
```

並且模板中需要綁定資料(`:msg="message"`)，同時宣告事件(`@update="updateMsg"`)：

```html
<div id="app">
  <my-component :msg="message" @update="updateMsg"></my-component>
  &#123;&#123; message &#125;&#125;
</div>
```

提到表單事件，不得不提 `v-model` 功能。

上面的需求，Vue 可以讓我們用 `v-model` 來綁定元件。

# 元件中的 v-model

如果元件只需要處理一個 `v-model` 資料綁定，元件的 props 請定義成 `modelValue`，emit 則定義成 `update:modelValue`。

```js
app.component("my-component", {
  template: `
    <input :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
      `,
  props: ["modelValue"],
  emits: ["update:modelValue"],
});
```

Vue 實體則是寫這個內容，預計要把 `message` 跟內部表單綁在一起：

```js
const app = Vue.createApp({
  setup() {
    const message = Vue.ref("hello");
    return {
      message,
    };
  },
});
```

接著在外層使用元件時，就可以直接使用 `v-model` 把資料傳進去了：

```html
<div id="app">
  <my-component v-model="message"></my-component>
  &#123;&#123; message &#125;&#125;
</div>
```

語法看起來很複雜，但概念其實很單純

就是在外部宣告狀態，讓外部的狀態可以跟「元件內部」的表單綁定。

完整的範例可以到[這裡看](https://jsfiddle.net/imall/kdpo5qyc/39/)


# 外部多個狀態，綁定到多個元件表單

剛才的寫法，是元件只有一個表單時，預設使用名稱為 `modelValue` 的 prop ， 與 `update:modelValue` 的 emit

如果有多個表單需要被綁定外部狀態的話，可以這樣寫

```js
app.component("my-component", {
  template: `
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
      `,
  props: ["firstName", "lastName"],
  emits: ["update:firstName", "update:lastName"],
});
```

外層就可以綁定多個狀態了：

```html
<div id="app">
  <h1>&#123;&#123; first &#125;&#125; &#123;&#123; last &#125;&#125;</h1>
  <my-component v-model:first-name="first" v-model:last-name="last">
  </my-component>
</div>
```

記得要宣告綁定的狀態：

```js
const app = Vue.createApp({
  setup() {
    const first = Vue.ref("John");
    const last = Vue.ref("Doe");

    return {
      first,
      last,
    };
  },
});
```

完整的程式碼可以到這裡[試試](https://jsfiddle.net/imall/kdpo5qyc/35/)
