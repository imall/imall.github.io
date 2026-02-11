---
title: slot 插槽
date: 2023-10-10 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---
# 前言

不知道大家在開始使用元件的時候有沒有一個疑惑，明明元件在模版中的寫法跟一般的 HTML 標籤沒兩樣

```html
<my-component></my-component>
```

但我們如果試著在裡面添加文字，卻不會有任何效果

```html
<my-component>這句話寫在這裡不會呈現在畫面上</my-component>
```

原因其實是元件的內容，會是由定義元件時所寫的 `template` 決定，並不是由「元件標籤」的內容來定義，想想看要是元件的內容會在「使用元件」時定義，好像就失去了「拆元件」的本意了。

不過有時候如果能讓我們在使用元件的時候定義當下所需的資料，也是能讓元件的使用變得更彈性一些。

為此 Vue 提供了一個叫「插槽」的功能來達成這件事。

# slot

假設我們定義了一顆按鈕元件，我們可以在元件的 template 中使用一個特殊的標籤 `<slot>` 讓使用元件的時候，能將資料塞在裡面。

```js
app.component("fancy-button", {
  template: `
      <button>
        <slot></slot> <!-- 插槽 -->
      </button>`,
});
```

定義好 `<slot>` 之後，使用元件時，就可以在元件標籤內定義「插槽」要放的內容：

```html
<fancy-button>這是一顆重要的按鈕</fancy-button>
```

這就可以讓我們在每次使用元件時，定義元件的「部分」資料：

![](https://i.imgur.com/2ENYxuR.png)

# `<slot>` 標籤的預設值

或許有些功能只有在特定情況會需要更改插槽資料，絕大多數的情況在插槽中要塞的資料都會是固定的，這種情況就很適合設定插槽的「預設值」。

設定方式很簡單，就是在元件插槽中把預設值寫好就好：

```js
app.component("fancy-button", {
  template: `
      <button>
        <slot>預設內容</slot> <!-- 插槽 -->
      </button>`,
});
```

這麼一來在使用時，如果沒有寫內容的元件，就會以預設值處理了
![](https://i.imgur.com/lpRHhS9.png)

# 為 `<slot>` 標籤設定名稱

如果一個元件的用途是提供一個「架構」，而他的「內容」會是在使用元件時才決定，在上面的內容已經知道可以使用 `<slot>` 來處理。

不過要是元件的「內容」被拆了好幾個區塊，例如「`header`」、「`main`」，一個 slot 就不夠用了。

在定義多個 slot 時，必須針對每個 slot 命名，這樣使用時的內容才有依據能夠一一對應到對的插槽中。

插槽命名的方式，只要在`<slot>` 標籤中添加`name`屬性即可。 

```js
app.component("dialog-box", {
  template: `
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot name="main"></slot>
  </main>
</div>
`,
});
```

實際使用時，則會使用 `<template>` 標籤，加上`v-slot:名稱` 的指令來宣告每一個區塊的內容

```html
<dialog-box>
  <template v-slot:header>這是頭</template>
  <template v-slot:main>這是內容</template>
</dialog-box>
```

![](https://i.imgur.com/4B6IoAy.png)

> 範例程式提供於 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/55/)

# v-slot 的簡寫

跟其他指令一樣，`v-slot:`也有一個 `#` 作為簡寫，所以上面的內容也可以寫成下面這樣：

```html
<dialog-box>
  <template #header>這是頭</template>
  <template #main>這是內容</template>
</dialog-box>
```

![](https://i.imgur.com/JcebBgH.png)

>　範例程式提供於 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/57/)

# 在 slot 內容中寫 HTML 標籤

剛剛的範例中都是使用「純文字」當作 slot 的內容，不過slot 的內容也可以定義 HTML 標籤：

```html
<dialog-box>
    <template #header>
        <h1>這是標題</h1>
  </template>
  <template #main>
      <p>這是內容</p>
  </template>
</dialog-box>
```

![](https://i.imgur.com/e0fX3zZ.png)

>　範例程式提供於 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/60/)


# 未命名的 `<slot>` 標籤會成為預設的 slot
如果我們在元件中使用多個 `<slot>` 標籤 ，有些 `<slot>` 標籤沒有命名的話，Vue 會把他當成預設的 slot 

```js
app.component("dialog-box", {
  template: `
    <div class="container">
    <header>
        <slot name="header"></slot>
    </header>
    <main>
        <slot></slot>  <!-- 他會被當成預設 slot-->
    </main>
    </div>
    `,
});
```

如果要在模板中塞資料到這個 slot 中，可以用 `default` 的字樣來宣告：
```html
<dialog-box>
  <template #header>這是頭</template>
  <template #default>這是內容</template>
</dialog-box>
```
![](https://i.imgur.com/Ot56oXP.png)


不過還記得一開始沒對 `<slot>` 標籤命名的時候，我們是怎麼塞資料到 slot 的嗎？  
對，是直接把資料寫在元件標籤內：
```html
<fancy-button>這是一顆重要的按鈕</fancy-button>
```

換言之，如果我們直接在元件標籤寫資料，內容會直接塞到「預設 slot」裡面：

```html
<dialog-box>
  <template #header>
    <h1>這是標題</h1>
  </template>

  <p>這是內容1</p>
  <p>這是內容2</p>
  <p>這是內容3</p>
  <p>這是內容4</p>
</dialog-box>
```

![](https://i.imgur.com/tBEQBco.png)

>　範例程式提供於 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/63/)

# 在元件中先定義好資料，使用時再決定結構

一般的元件操作方式，通常會是「元件定義結構」，從外部使用元件時，再把資料塞到元件中，讓元件渲染出資料。

不過如果透過插槽，Vue 有辦法做到在元件中定義資料，使用元件時再決定結構，做法是在元件的 `<slot>` 標籤定義「屬性」，就像 `props` 那樣。

這裡定義一個 `text` 的屬性，並且把元件的 `msg` 變數綁上去。
```js
app.component("my-component", {
  template: `
    <div class="container">
        <slot :text="msg"></slot>
    </div>
`,
  setup() {
    const msg = Vue.ref("Hello World");
    return {
      msg,
    };
  },
});
```
在模版中，在 `v-slot` 定義一個名稱，即可以拿他取得 `text` 的資料
```html
<my-component v-slot="slotProps">&#123;&#123;slotProps.text&#125;&#125;</my-component>
```
![](https://i.imgur.com/8Tskmgd.png)

這個名稱不一定要叫 `slotProps` ，可以依當下需求定義。

>　範例程式提供於 [jsfiddle](https://jsfiddle.net/imall/kdpo5qyc/53/)

