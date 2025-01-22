---
title: 使用 Vue Router
date: 2023-10-12 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

傳統網頁的網址，以往會是後端根據不同的頁面去配置不同的網址規則。

不過如果要使用 Vue 框架開發網頁，會是「單一頁面網站」 (Sigle Page Application)，俗稱的 SPA，就是說我們就只會有「一個頁面」。

這種狀況下沒辦法靠後端定義資料夾去控制路由，處理路由的工作就變成 Vue 的事情。  

Vue 為此提供 Vue Router 的工具讓我們加裝在 Vue 應用程式中，讓我們可以處理路由。

這篇文章將簡單介紹 Vue Router 的基本使用方式

# 安裝 Vue Router

Vue 官方提供兩種 Vue Router 的使用方式

1. 直接使用 CDN

```sh
https://unpkg.com/vue-router@4.2.5/dist/vue-router.global.js
```

2. 使用 `npm` 或是 `yarn`

**npm：**

```sh
npm install vue-router@4
```

**yarn：**

```sh
yarn add vue-router@4
```

# 從專案中引入 Vue Router

使用 Vue Router 這件事情具體上來說，是在 「Vue 實體」中 **使用** Router ，在那之前我們需要建立這個 Router。

1. 到 `main.js` 中，從 `vue-router` 引入 `createRouter` 以及 `createWebHistory` 方法 ：

```js
import { createRouter, createWebHistory } from "vue-router";
```

`createRouter` 是等等會用來建立 Router 實體的工具，而 `createWebHistory` 是讓 Router 可以使用 HTML5 History API 的方法。

2. 用`createRouter`方法定義出 Router 實體：

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [],
});
```

3. 在 Vue 實體中使用他：

```js
const app = createApp(App);
app.use(router); //這句
app.mount("#app");
```

或是用 「串」 的方式使用：

```js
createApp(App).use(router).mount("#app");
```

如此一來，基本配置就完成了。

# 建立路由元件

會需要路由的根本原因，是因為我們有不同的頁面需要以網址做區分，所以操作順序應該是，先有頁面，才有路由。

我們可以在 src 資料夾中定義一個 `pages` 資料夾，新增 `Home.vue` 、`About.vue` 檔案，預計作為兩個不同的頁面：
![](https://i.imgur.com/UcpZCuX.png)

接著回到 `main.js` 引入這兩個頁面元件：

```js
import Home from "./Pages/Home.vue";
import About from "./Pages/About.vue";
```

在剛剛定義的 router 內把路由寫進去：

```js
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: "/", component: Home },
    { path: "/about", component: About },
  ],
});
```

# 定義元件要出現的位置

還沒使用 Router 之前，如果要使用元件，我們會直接用元件名稱當作 HTML 標籤使用：

```html
<Home></Home> 
<About></About>
```

不過在使用路由之後，我們希望透過「網址」的異動來決定哪個元件要出現。

我們可以使用一個特殊的 `<router-view>` 標籤來達到這件事。

```html
<router-view></router-view>
```

此外，要做到網址的跳轉，原生我們會使用 `<a>` 標籤來完成，不過 Vue 提供一個叫做 `<router-link>` 的標籤來作為超連結的標籤，並且使用 `to` 屬性來定義跳轉路由：

```html
<router-link to="/">往 HOME 的通道</router-link>
<router-link to="/about">往 About 的通道</router-link>
```

上面的內容整合起來，我們在 `App.vue` 的 `template` 內容會變這樣

```html
<router-link to="/">往 HOME 的通道</router-link>
<router-link to="/about">往 About 的通道</router-link>

<router-view></router-view>
```

![](https://i.imgur.com/BjrmY62.gif)

# 把 Router 的程式整理一下

上面定義 Router 的方式，是直接把程式塞在 `main.js` 中，但如果隨著專案的龐大，Router 的內容很可能就佔了大半。  
 為了維護方便，實務上通常會將 Router 抽出來定義，在把這個內容引入回 `main.js`。

1.  首先，先到 `src` 資料夾中新增 `Router` 資料夾，並且在裡面新增 `index.js` 檔案：
    ![](https://i.imgur.com/nQBZYV0.png)

2.  把剛剛 router 需要用到的內容全部搬過去，並且用 `export default` 匯出：
```js
import { createRouter, createWebHistory } from 'vue-router';
import Home from '../Pages/Home.vue'
import About from '../Pages/About.vue'

const router = createRouter({
    history: createWebHistory(),
    routes: [
        { path: '/', component: Home },
        { path: '/about', component: About },
    ],
});

export default router;
```


3. 回到 `main.js` 引入：
```js
import router from './Router';

const app = createApp(App);
app.use(router);
app.mount('#app');
```

這樣就完成基本的設置了！

如果希望讓 router 內容可以更簡潔一些，也可以把 `routes` 抽出來定義：

```js
const routes = [
    { path: '/', component: Home },
    { path: '/about', component: About },
]

const router = createRouter({
    history: createWebHistory(),
    routes
});
```

日後如果路由增加，只需調整 `routes` 的內容即可。

