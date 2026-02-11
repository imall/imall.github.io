---
title: 指令：v-if 、v-else-if、v-else、 v-show
date: 2023-10-02 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

如果你知道程式的 if 、else if 、else 的操作方式，那麼這篇文章的內容應該很容易理解

程式的條件判斷指令，都會接一個布林值

```js
if (布林值) {
  // 一些邏輯操作
}
```

v-if 的功能也差不多是這樣，會接一個布林值，邏輯只會有一個：決定模板要顯示還是消失。

# v-if 語法

基本語法是這樣寫

```html
<div v-if="hasData">如果有資料，這裡要顯示</div>
```

```js
Vue.createApp({
  setup() {
    const hasData = Vue.ref(true);
    return { hasData };
  },
});
```

這麼一來，只要能控制 `hasData` 的真偽值，就能讓 HTML 某個標籤顯示或隱藏。

# v-else-if

跟 if、 else-if 語法一樣，v-else-if 用來處理多個條件判斷。

```html
<div v-if="BMI <= 24">BMI 正常</div>
<div v-else-if="BMI > 24">BMI 過高</div>
```

```js
Vue.createApp({
  setup() {
    const BMI = Vue.ref(18);
    return { BMI };
  },
});
```

# v-else

應該不用多說了，else 這就是用來當所有條件不符合的時候要處理的內容。  
跟 else 語法一樣，v-else 不能接條件，否則會抱錯。

```html
<div v-if="BMI <= 24">BMI 正常</div>
<div v-else-if="BMI > 24">BMI 過高</div>
<div v-else>請輸入正確數字</div>
```

```js
Vue.createApp({
  setup() {
    const BMI = Vue.ref(18);
    return { BMI };
  },
});
```

# v-if 特殊使用方式

如果有一大段沒有被標籤包起來的內容需要被隱藏，項是這樣：

```html
<div>這段需要依據某個條件來顯示或隱藏</div>
<div>這段的顯示隱藏條件跟上面一樣</div>
<div>這段也是</div>
```

一般來說可以這麼寫：

```html
<div v-if="條件">這段需要依據某個條件來顯示或隱藏</div>
<div v-if="條件">這段的顯示隱藏條件跟上面一樣</div>
<div v-if="條件">這段也是</div>
```

但如果要修改就真的很麻煩，這時候可以用一個特別的標籤先把他包起來

```html
<template v-if="條件">
  <div>這段需要依據某個條件來顯示或隱藏</div>
  <div>這段的顯示隱藏條件跟上面一樣</div>
  <div>這段也是</div>
</template>
```

template 標籤是 Vue 中的一個特殊標籤，使用他來處理 v-if 語法，標籤不會被出現在 Dom 中。

![](https://i.imgur.com/9ikpIQe.png)

# v-if v-else-if v-else 使用限制

如果要接續使用 v-if else，標籤必須在同一個層級，同時必須接續者撰寫，否則無效。

有效語法：

```html
<div v-if="條件">某些文字</div>
<div v-else>另一些文字</div>
```

以下都是無效語法：

- 不在同一個層級

```html
<div v-if="條件">某些文字</div>
<div>
  <div v-else>另一些文字</div>
</div>
```

- 沒有接續著寫

```html
<div v-if="條件">BMI 正常</div>
<div>中間有一段無關緊要的內容</div>
<div v-else>最後才有 v-else</div>
```

# v-show

這個指令效果其實就等同於 v-if，只不過他跟 v-if 的差異在於 Dom 隱藏方式

`v-if` 如果不成立的話，會是用 `element.remove()` 的方式把內容拿掉，換言之，被拿掉的內容用開發人員工具看查看 DOM ，會找不到！

`v-show` 如果不成立的話，會是使用 CSS `display:none` 的方式讓元素藏起來。


```html
<div id="app">
  <div v-if="condition">這是用 v-if 隱藏的</div>
  <div v-show="condition">這是用 v-show 隱藏的</div>
</div>
```

```js
Vue.createApp({
  setup() {
    const condition = Vue.ref(false);
    return {
      condition,
    };
  },
}).mount("#app");
```


兩者在 DOM 中看起來是這樣：
可以看到 v-if 的位置出現一個註解，讓我們知道這段曾經有個 v-if 只是被隱藏了
![](https://i.imgur.com/43Ep0UL.png)

# 結論
1. v-if v-else-if v-else 用法跟熟知的條件判斷一樣
2. 如果有一大段模板需要判斷條件來顯示隱藏，可以用 template 標籤操作  v-if
3. v-if 語法如果需要接續 v-else-if 或 v-else，標籤位置必須在同一個層級並且接續著寫。
4. v-show 語法跟 v-if 語法一樣，差異在 show 使用 CSS 隱藏內容，v-if 直接把 DOM 的資料拿掉。 