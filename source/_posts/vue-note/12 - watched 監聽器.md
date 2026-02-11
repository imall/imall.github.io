---
title: watched 監聽器
date: 2023-09-27 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

今天來補完 Vue 資料加工的最後一塊拼圖，監聽器。

在 computed 的內容中有提到，如果在 computed 寫 set 語法，不要寫出有副作用的內容。

不過應用程式千變萬化，總會有機會需要寫含有副作用的邏輯，例如我們希望某個資料異動之後，可以連帶去改動其他狀態(資料)。

為了因應這樣的需求，Vue 提供一個叫「監聽器」的東西來完成這件事。

# Watch 監聽器

監聽器的概念，就像是監視器一般，時時刻刻的觀測著某個狀態，在狀態異動的時候要去做某些事情。

如果是要監聽一個 `ref()` 或 `computed` 的資料，可以這樣寫，例如下面這樣：

```js
const { createApp, watch, ref } = Vue;
createApp({
  setup() {
    const answer = ref("");

    // 直接把 answer 寫在第一個參數中
    // 第二個參數會是個 函式
    // 函式的第一個值會自動抓取「新的值」
    // 函式的第二個值則是記錄「舊的值」
    watch(answer, (newAnswer, oldAnswer) => {
      console.log(`答案從 "${oldAnswer}" 變為 "${newAnswer}"`);
    });
  },
}).mount("#app");
```

如果想要一次監聽好幾組資料，可以這樣寫：

```js
const { createApp, watch, ref } = Vue;
createApp({
  setup() {
    const answer = ref("");
    const question = ref("");

    watch(
      // 監聽 answer 跟 question
      [answer, question],
      // callback 第一個參數都是新值，第二個參數都是舊值
      ([newAnswer, newQuestion], [oldAnswer, oldQuestion]) => {
        console.log(`答案從 "${oldAnswer}" 變為 "${newAnswer}"`);
        console.log(`問題從 "${oldQuestion}" 變為 "${newQuestion}"`);
      }
    );
  },
}).mount("#app");
```

watch 的第一個參數會直接是個陣列，同時塞入多個資料。  
第二參數一樣還是函式，而函式吃的兩個參數也都變成陣列，第一個陣列放的是「新的值」，第二個陣列放的是「舊的值」。

# 深度監聽
如果要監聽的資料是個「物件」，預設情況下，只會監聽到物件的「參考」有沒有變動，而不會去監聽物件內部的值有沒有變動。

舉例來說，如果我們有一個物件，裡面有個屬性叫做「name」，我們改變 name 的值，監聽器並不會去觸發。

```js
const { createApp, watch, ref } = Vue;

createApp({
  setup() {
    const person = ref({
      name: "小明",
    });

    watch(person, (newPerson, oldPerson) => {
      console.log(`人物從 "${oldPerson}" 變為 "${newPerson}"`);
    });

    // 改變 name 的值，監聽器不會觸發
    person.value.name = "小華";
  },
}).mount("#app");
```

這時候我們就可以使用「深度監聽」的功能，讓監聽器可以觸發物件內部值的變動。

```js
const { createApp, watch, ref } = Vue;

createApp({
  setup() {
    const person = ref({
      name: "小明",
    });

    watch(
      person,
      (newPerson, oldPerson) => {
        console.log(`人物從 "${oldPerson}" 變為 "${newPerson}"`);
      },
      // 第三個參數是個物件，可以設定 deep: true
      { deep: true }
    );

    // 改變 name 的值，監聽器會觸發
    person.value.name = "小華";
  },
}).mount("#app");
```

不過根據官方的說法，使用深度監聽器的「開銷」會很大，除非是真的需要去監聽物件，否則盡量不要使用到他。



# 清除監聽器

監聽器的設定，是在 setup() 函式內，所以當元件被銷毀的時候，監聽器也會跟著被銷毀。
所以大多數情況下，我們不需要特別去清除監聽器。

不過如果真的有需要，我們可以寫一個變數去接 watch 的回傳值，並且在需要的時候，手動去清除監聽器：

```js
const { createApp, watch, ref } = Vue;
createApp({
  setup() {
    const answer = ref("");

    // 這裡的 watch 是寫在 setTimeout 的函式中
    // 這種情況下，watch 不會被主動銷毀
    const stopWatch = watch(answer, (newAnswer, oldAnswer) => {
      console.log(`答案從 "${oldAnswer}" 變為 "${newAnswer}"`);
    });

    // 在需要的時候，手動清除監聽器
    stopWatch();
  },
}).mount("#app");
```
