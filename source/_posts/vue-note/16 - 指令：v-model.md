---
title: 指令：v-model
date: 2023-10-01 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

我們已經知道使用雙大括號，可以把資料綁到模板上，並且在 **程式中** 操作資料時，綁定的資料也會跟著異動。

不過有沒有一種可能，是要讓使用者操作畫面資料內容後，程式要去處理這些資料？

你猜對了，就是在說表單。

到目前為止介紹的內容，都是我們把資料渲染到畫面中，還沒有任何表單相關的操作。

一般來說，如果要把使用者輸入的資料塞回程式裡面使用，需要同時使用到這兩天說明的 `v-bind` 、`v-on` 指令：

```html
<input :value="message" @input="event => message = event.target.value" />
```

使用 input 的 value 屬性把變數綁在模版上，同時還要偵測輸入事件，在把資料改回變數中。

但因為表單的綁定太常用，Vue 提供一個語法糖 v-model 指令可以取代這件事。

# v-model

以上面的例子來說，可以用 v-model 改成這樣：

```html
<input v-model="message" type="text" />
<p>&#123;&#123; message &#125;&#125;</p>
```

```js
const { createApp, ref } = Vue;
const app = Vue.createApp({
  setup() {
    const message = ref("");

    return {
      message,
    };
  },
}).mount("#app");
```

直接宣告一個 message 變數寫在 v-model 中。
同時這個 message 也使用雙大括號把資料綁上模板。

這樣就能做到，當使用者輸入資料時，更動了 message 的內容，而 message 的內容又透過雙大括號渲染到模板中。

# 不同的資料類型

在定義輸入框 v-model 的變數時，使用的型別會是字串(`""`)，不過如果是其他類型的表單，像是多選框(checkbox) 、單選框(radiobox)，就會用到不同型別的變數：

## checkbox (多選)

要定義多選框，會使用陣列作為變數型別：

```html
<div>Checked names: &#123;&#123; checkedNames &#125;&#125;</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```

```js
const { createApp, ref } = Vue;
const app = Vue.createApp({
  setup() {
    const checkedNames = ref([]);

    return {
      checkedNames,
    };
  },
}).mount("#app");
```

## checkbox (單一勾選)

如果像是「是否已閱讀」 這種輸入框，則是用 Booleam 宣告變數：

```html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">&#123;&#123; checked &#125;&#125;</label>
```

```js
const { createApp, ref } = Vue;
const app = Vue.createApp({
  setup() {
    const checked = ref(false);

    return {
      checked,
    };
  },
}).mount("#app");
```

## radiobox

如果是單選框的話，也是使用字串(`""`)作為變數型別！

```html
<div>Picked: &#123;&#123; picked &#125;&#125;</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```

```js
const { createApp, ref } = Vue;
const app = Vue.createApp({
  setup() {
    const picked = ref("");

    return {
      picked,
    };
  },
}).mount("#app");
```

## select option 下拉選單

如果是使用下拉選單，也是使用陣列型別

```html
<span> Selected: &#123;&#123; selected &#125;&#125;</span>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

```js
const { createApp, ref } = Vue;
const app = Vue.createApp({
  setup() {
    const selected = ref("");

    return {
      selected,
    };
  },
}).mount("#app");
```

# 修飾子

跟 v-on 一樣，v-model 也有一些特別的修飾子可以使用。

## .number

事實上， 透過 v-model 處理過的資料型別預設會是「字串」，可以到 [這裡](https://jsfiddle.net/imall/q6btwn4a/29/) 玩玩看兩個數字相加會發生什麼事，應該就會知道這是什麼意思了。

要處理這個問題，可以在 v-model 加上修飾子：

```html
<div id="app">
  <input type="text" v-model.number="text1" />+
  <input type="text" v-model.number="text2" />= &#123;&#123; text1 + text2&#125;&#125;
</div>
```

結果我放在[這裡](https://jsfiddle.net/imall/q6btwn4a/32/)。

## .lazy
不知道大家在輸入表單有沒有遇過一個狀況，明明資料就還沒打完，畫面就一直跳出輸入訊息步符合規則的錯誤訊息，雖然不影響操作，但體感就變得很差....

.lazy 修飾子如他的名字一樣，很懶。

他會使得 focus 離開輸入框之後，才處理已經輸入的資料。

可以到[這裡](https://jsfiddle.net/imall/q6btwn4a/34/)操作看看。

## .trim

這個修飾子就是字串的 .trim() 方法，他可以幫我們自動移除頭尾的字串

可以到[這裡](https://jsfiddle.net/imall/q6btwn4a/38/)試試看輸入的資料頭尾加上空白時，有.trim 跟沒有 .trim 的差異。