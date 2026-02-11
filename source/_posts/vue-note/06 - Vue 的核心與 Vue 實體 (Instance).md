---
title: Vue 的核心與 Vue 實體 (Instance)
date: 2023-09-21 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# Vue 的核心
這個主題寫在這篇其實有點「晚」了

不過也因為寫在這篇，相信有追完前面幾篇的讀者，已經能體會 Vue 有多「漸進」了。

之前有提到，尤大大當初只是想使用「一部分」框架的功能，卻要引入「整個」框架，覺得很麻煩。

於是在設計 Vue 的時候，讓大家可以做到「小專案引入核心功能」，如果有需要再引入更多功能，後面帶到「路由(Routing)」、「狀態管理器(Pinia)」就是個例子。


而上段提到 Vue 的核心功能，其實大家已經認識了：  
「宣告式渲染」、「元件系統」。
![Alt text](image-4.png)

# Vue 實體
我們再把那個一直用到的例子搬出來看：  
如果你想線上玩，我放[這裡](https://codepen.io/imall/pen/wvRJGKd)
```html
<div id="app">
    <div>{{ message }}</div>
</div>
```
```js
    Vue.createApp({
        setup() {
            const message = '一段文字'
            return { message }
        },
    }).mount("#app");
```


再使用 Vue 的時候，「一定」會用到 `createApp` 這個函式，就是這個函式再建立 Vue 的「實體」。

如果對「實體」這個詞有疑問，你可以想像 Vue 是一個「機器人」，你要使用他，必須先把他「製造」出來，製造的過程必須規劃好他會做的事情，像是洗衣、煮飯之類。

這個機器人被製造出來後，必須被「安置」在某個家庭，他會專心一致的在這個家服務，沒辦法穿越到其他家庭。

`createApp` 就是在做這個「製造」Vue 機器人的函式，機器人會能做的事情，已經通通寫在 craateApp 函式裡面。  
執行完函式，Vue 機器人的「實體」就會被建立出來。

機器人被建立出來後，必須被安置在某個家庭，所謂家庭，其實就是在講 HTML 的某個節點。

透過 createApp 的 `mount` 方法，可以把「實體」掛到 HTML 的節點上。

事實上我們也可以透過一個變數把這個「機器人」記錄起來，再回頭使用機器人會做的事情，像是這樣：
```js
    const robot = Vue.createApp({
        setup() {
            const message = '一段文字'
            return { message }
        },
    });
    
    robot.mount("#app");
```

甚至你如果覺得那個 Vue 每次都要寫，也可以直接用 ES6 的語法把方法解構出來：

```js
    const { createApp } = Vue;
    const robot = createApp({
        setup() {
            const message = '一段文字';.
            return { message }
        },
    });
    
    robot.mount("#app");
```

除了 createApp 之外，未來還會看到 ref 、生命週期鉤子等，使用前都需要先解構

再更進階一點點點說，`createApp` 裡面的物件，也可以獨立出來：
```js
    const { createApp } = Vue;
    // 直接把物件塞到某個變數
    const MyRobotComponent ={
        setup() {
            const message = '一段文字';.
            return { message }
        },
    }
    // 再把這個變數塞到 createApp
    createApp(MyRobotComponent).mount("#app");
```
以上的寫法都能做到一樣的事情，我知道新手常常因為換了個寫法，就看不懂在寫什麼，這邊列幾個簡單的語法重構給新手們參考。

# 掛在 HTML 的名字，一定要叫 app 嗎？
這其實應該是很多新手不會注意到的問題，反正別人怎麼寫，照抄就是了

那個 `#app` 是因為官網範例總是這麼寫，後來大家也就跟著它使用了，實務上也不會影響到開發的功能。

不過事實上 `mount` 內容只要是有辦法抓到 HTML 節點的選取器都可以使用，：
```html
<div id="app"></div>
```

上一篇文章 「只讓部分結構交給 Vue 控制」的例子，我有刻意把 HTML 改了一下：
```html
    <div class="header">XXX</div>
    <div class="main-content">XXX</div>
    <div class="footer">© 鐵人賽 {{ Year }}</div>
```
例子中，`mount` 是透過 `".footer"` 選到 HTML 節點，來掛 Vue 實體：
```js
  Vue.createApp({
    setup() {
      const Year = new Date().getFullYear();
      return { Year }
    }
  }).mount(".footer");
```



# 總結
1. Vue 的核心，就是「宣告式渲染」、「元件系統」
2. Vue 的實體，由 `createApp` 函式所建構
3. 實體要掛到 HTML 上，是透過 `createApp` 的 `mount` 方法
4. `mount` 方法不一定要用 `#app` ，只要能選到 HTML 節點的選取器都可以


> 如果你對 JS `物件`、`函式`、`方法` 這些詞彙感到困惑，或是不太熟悉，推薦你看這本書： [0 陷阱！0 誤解！8 天重新認識 JavaScript！](https://www.tenlong.com.tw/products/9789864344130)。

> 如果你對 ES6 出現的語法不熟悉，像是文中的「解構」，可以從[這個連結](https://book.vue.tw/appendix/es6.html) 找到一些關鍵詞與說明。

