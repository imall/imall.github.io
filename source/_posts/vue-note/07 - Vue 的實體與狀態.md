---
title: Vue 的實體與狀態
date: 2023-09-22 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

標題又出現了一個看起來很難懂的專有名詞：狀態。

這篇文章來聊聊 Vue 的「狀態」是什麼意思。

在開始說狀態之前，先簡單說一下 `setup()` 生命週期鉤子。

# setup()

再把那個最初的範例請出來

```js
Vue.createApp({
    setup() {
        const message = '一段文字'
        return { message }
    },
}).mount("#app");
```
還記得之前把 `createApp` 比喻成製造機器人的函式，就如同我們熟知的「掃地機器人」、「洗衣機(器人)」，如果要使用它，通常都會有些按鈕、介面讓我們操作，在 Vue 的世界，setup() 就類似於這些按鈕介面的腳色。

setup() 函式，是用來啟動每一個 Vue 元件的「進入點」，我們對每個元件做的「操作」都會寫在這裡進行。
像是元件的狀態、方法、計算屬性(computed)、監聽器(watch)，還有元件的生命週期鉤子，都會在 setup() 函式內撰寫。


> 使用 setup() 宣告的資料、方法，記得要 `return` 出來，才能在模版中使用，否則會報錯。


# Vue 的狀態
在 Vue 的世界，可以做到「資料」、「畫面」分離，所以只要管理好「資料」就等同於控管好 Vue 應用程式。

於是我們就可以說，會影響到應用程式運作的「狀態」的東西，就是那些「資料」。

「資料」又是怎麼來的？這就會是我們在每個元件宣告的「變數」了。

總結來說，在 Vue 的世界如果聽到「狀態」，其實就是在講 「資料」，在講「變數」。

之後會提到「Vue 狀態管理」的議題，就是在說如何管理 Vue 應用程式的「變數」了。


# ref()
在 Composition API 的語法中，如果要宣告變數，Vue 官方推薦使用 `ref()` 函式來讓定義「響應式」的資料。

此時的你可能很困惑，我們之前不是已經把資料與畫面綁定了嗎？
事實上之前的例子只會做到「單次」的綁定，如果資料被更動之後，並不會 **重新渲染** 到畫面上，例如[這個例子](https://jsfiddle.net/imall/kdpo5qyc/5/)：
```js
const { createApp, ref } = Vue;
const app = createApp({
  setup() {
    let count = 0;
    const plus  = () => {
      count++;
      console.log(count);
    }
    return {
      count,
      plus
    }
  },
})
app.mount("#app");
```

即使我們已經寫好 function 讓 `count +1`， console 出來的變數也確實已經 +1，代表資料確實有被 +1，但畫面的資訊始終沒有更動。

因為 Vue 的 Composition API，並沒有把「寫死」的變數處理成「響應式資料」。

為此我們必須透過 `ref()` 函式來將資料包覆起來，讓資料與畫面達到響應的效果。

使用上有幾點要注意：

1. `ref` 跟 `createApp` 一樣，是 Vue 物件提供的方法，必須寫 `Vue.ref()` 來使用，或是直接解構出來用。 

2. 透過 `ref()` 包覆的資料，如果在「程式中」要拿來用，必須使用 `變數.value` 來操作，這是 Vue 定義的規則。

3. 定義為 ref() 的變數資料，如果要渲染在模版中，可以 **不用** 寫成 `變數.value` ，只需要在雙大括號寫變數即可：{{ 變數名稱 }}，Vue 會自動幫我們解構。 

3. 由於定義的資料已經被 Vue 「封裝」起來，操作 ref() 的 `變數.value` 已經不會單純的「更改變數值」，所以宣告變數時可以直接使用 `const`。

4. 只要是 JS 的資料型別，都可以放到 `ref()` 函式內。例如：`String`、`Number`、`Boolean`、`Array`、`Object` 。

```html
<div id="app" >
    {{ count }}

    <button @click="plus">點選讓數字加一</button>
</div>
```

```js
const { createApp, ref } = Vue;
const app = createApp({
    setup() {
    // 定義 ref 資料要用的變數，可以使用 const
    const count = ref(0);
    const plus  = () => {
        // 以 ref 定義的資料，要用 [變數.value] 操作資料
        count.value++;
        console.log(count.value);
    }
    return {
        count,
        plus
    }
    },
})
app.mount("#app");
```
上面的程式可以到 [這裡](https://jsfiddle.net/imall/kdpo5qyc/7/) 玩玩看



> 題外話：如果有新手訝異為什麼提供的連結不用引入 CDN 就可以寫 Vue ，其實還是有引入，只是引入點是這個平台提供的，不是直接寫 script 引入：  
![](https://i.imgur.com/Gn1SBGS.png)
# ref 與 reative 

如果你有聽到有人在討論 `ref()` 與 `reative()` 之間的選擇。  
直接說結論：無腦 `ref()` 用下去即可。

根據尤雨溪大大的說法，`reative()` 其實是為了那些已經習慣 Vue2 語法的開發者定義出來的函式。
如果對語法有興趣，可以參考[官網](https://cn.vuejs.org/guide/essentials/reactivity-fundamentals.html#reactive)的說明

官方目前已建議直接使用 `ref()`，不用特別去比較這兩者的差異。
![](https://i.imgur.com/AMqi1jN.png)


不過還是要說，這兩個寫法都還是很常見，沒有誰被棄用的問題，如果有看到同事或是同學在寫 `reative()` 也不要隨便嗆人家跟不上時代，我們要尊重包容理性和平！

尤雨溪大大也提到說，這兩個東西的用法，團隊成員約定好即可，詳細的內容，大家可以直接聽 [尤雨溪大大的說明](https://www.youtube.com/watch?v=e8Wlv4AGJjk)。

