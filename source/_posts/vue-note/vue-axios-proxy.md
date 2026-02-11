---
title: 在Vue3使用Vite設定proxy整合axios攔截器
date: 2023-03-18 10:11:27
tags: [Vue]
categories: Vue 筆記
comment: false
cover: https://i.imgur.com/uD1DYit.jpg
---

:::info no-icon
Vite proxy 、 axios 攔截器 等議題，很容易在網路上查到資料
但自己實際在使用的時候，總會有一些細節沒注意到
這篇記錄實際使用了 vite proxy 、 整合 axios 攔截器 以及設定攔截器功能的內容
:::

## 大綱

1. Vite 設定 proxy 方法
2. axios 攔截器如何設定 URL
3. 實際使用 axios 的 API

## Vite 設定 proxy 方法

在 Vite 中，我們可以通過在`vite.config.js`文件中設定一下，就可以做好應用程式的 proxy，讓 Vite 透過 proxy 呼叫 API

Vite 預設已經有個 defineConfig 方法
直接在在裡面寫一個 server 物件 ，設定好相關參數就完成啦

```js vite.config.js
export default defineConfig({
  host: "0.0.0.0", //這一行是為了讓其他裝置可以連到本機的開發環境，沒這需求可以忽略
  server: {
    proxy: {
      "/api": {
        target: "https://www.test.com/", // 後端API 進入點
        changeOrigin: true,
        secure: false,
        ws: true,
        rewrite: (path) => path.replace(/^\/api/, ""),
      },
    },
  },
});
```

設定過程中採到最大的雷，是那個`/api`字樣，其實是自己定義的任意參數!!
這只會在開發過程中使用到，這點等等說明到 axios 攔截器設定時，看 code 去比對就會比較清楚

## 設定 Axios 實體 (Axios Instance)

剛開始學習使用 axios 的時候，相信大家都是 cdn 或是 npm 設定好 axios 後
就直接`axios.get()`、`axios.post()`開始使用。在小型專案這麼用，並沒有什麼太大的問題
但如果今天開發的專案量大，需要呼叫的 API 一多，相同的邏輯很有機會要寫好幾次，如果需求一更動，就容易牽一髮動全身

而 Axios Instance 就是一個解決這個問題很好用的工具
所以...

### 為什麼要用 Axios Instance

接續前言，Axios Instance 可以提供以下功能讓管理 API 更方便

1. 集中設定 Request Config
2. 支援攔截器，可在 then/catch 前做額外處理
3. 封裝 API 易於管理

廢話不多說，上 code

```js plugin/axios.js
// 首先，先import npm 安裝好後的axios
import axios from "axios";
```

一般來說，如果沒有搭配本篇文章主題 `Vite proxy整合 axios攔截器`
實體的宣告會長這樣，直接將 API 的`絕對路徑`寫在 `baseURL` 參數中

```js plugin/axios.js
const instance = axios.create({
  baseURL: "https://www.test.com/",
  headers: { "Content-Type": "application/json" },
  timeout: 50000,
});
```

但因為我們已經在 vite 設定好 proxy
所以這段 code 可以改寫成這樣

```js plugin/axios.js
const service = axios.create({
  baseURL: import.meta.env.DEV ? "/api" : "https://www.test.com/",
  headers: { "Content-Type": "application/json" },
  timeout: 50000,
});
```

其中 `import.meta.env.DEV` 是 vite 所提供用來判斷開發模式的寫法
`DEV`是用來判斷是否運行在開發環境，其餘的內容可以參考[這裡的說明](https://cn.vitejs.dev/guide/env-and-mode.html)

另一個關注點是 開發環境寫了 `"/api"` ，非開發環境則是寫 `"https://www.test.com/"`
先前有提到 `"https://www.test.com/"` 是實際的運行的 API 進入點
而 `"/api"` 則是開頭在 proxy 設定的 `"/api": {}`參數，由於有寫一段 `rewrite: (path) => path.replace(/^\/api/, "")`，所以在開發環境呼叫 API 時，所有 API url 中，包含`/api`的字樣都會被取代掉

### Axios 攔截器

設定好實體之後，就可以透過下列的 code 建立 `request` 與 `respones`

第一個函數 `instance.interceptors.request` 會運行在後續呼叫 API 時的初始
也就是所有 request 資料在拋給後端之前，會先在這裡被擋下來
可以在這裡做一些資料送出前預設處理，例如設定 api 需不需要設定 token？需要的話在 header 增加參數之類

第二個函數 `instance.interceptors.request` 方便之處，在於可以統一管理系統發生錯誤時
預設希望處理的事情，像是跳出警訊、重新導向頁面等

```js plugin/axios.js
instance.interceptors.request.use(
  (config) => {
    // Do something before request is sent
    return config;
  },
  (error) => {
    // Do something before request is sent
    return Promise.reject(error);
  }
);

instance.interceptors.response.use(
  (response) => {
    // Do something with response data
    return response;
  },
  (error) => {
    if (error.response) {
      switch (error.response.status) {
        case 400:
          console.log("something wrong");
          break;
        case 404:
          console.log("你要找的頁面不存在");
          // go to 404 page
          break;
        case 500:
          console.log("程式發生問題");
          // go to 500 page
          break;
        default:
          console.log(error.message);
      }
    }
    if (!window.navigator.onLine) {
      alert("網路出了點問題，請重新連線後重整網頁");
      return;
    }
    return Promise.reject(error);
  }
);

export default instance;
```

## 封裝 api

上述都設置好後，就可以這樣使用 api

```js models/api.js
import request from "@/plugins/axios";

export function Login(data) {
  return request({
    url: "/login",
    method: "post",
    data,
  });
}
```

在.vue 檔案需要呼叫 api 時，就可以這麼寫

```js App.vue的 script setup 裏頭 mark:4-7
import { Login } from "@/models/api";

const loginHandler = async () => {
  const res = await login({
    identifier: identifier.value,
    password: password.value,
  });
  console.log("login", res);
};
```

> 值得注意的是我們的開發網址雖然是 `http://localhost:5173/`
> 而且 axios baseURL 在開發環境設定了 `/api`
> 但是因為設定 vite proxy，實際上運行了這個`login()`
> 呼叫的完整網址會是 `https://www.test.com/login` 而不會是 `http://localhost:5173/api/login`

打完收工~~

:::info no-icon
有個細節要特別注意，proxy 的設定，基本上只是為了開發環境的方便，可以讓我們在開發環境不用去設定 CORS
在 `npm run build` 之後，把程式部屬到正式環境，會發現，`/api` 這個字樣還是會存在於網址中
如果你的 API 進入點是 `https://www.test.com/api`
那麼在正式環境，你的 API 進入點就會變成 `https://www.test.com/api/api`
所以正式上版前，記得要把 proxy 的設定註解掉，或是直接刪除

以本文的寫法來說，就是設定 axios 的 baseURL，判斷如果不是開發環境，就直接將 baseURL 設定為絕對路徑
:::

## 補充

補充一下如果需要在 resquest 時判斷 API 要不要加 token
可以這樣寫

```js plugins/axios.js mark:4-7
import { getToken, setToken } from "@/utils/auth";
instance.interceptors.request.use(
  (config) => {
    if (config.needToken) {
      const token = getToken();
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    // Do something before request is sent
    return Promise.reject(error);
  }
);
```

而 api.js 中如果有 api 需要 token 的話，就可以這樣設定

```js models/api.js mark:6
export function updateProfile(data) {
  return request({
    url: "/me/profile",
    method: "post",
    data,
    needToken: true,
  });
}
```

而這是在 `plugins/axios.js` 有用到`getToken` 與 `setToken`方法的實際內容

```js utils/auth.js
import Cookies from "js-cookie"; //要npm 安裝js-cookie
const TOKEN_KEY = "asscess_token"; //設定cookie 中要存token的key
const EXPIRES_AT = "expires_at"; //設定 token存在cookie的效期，一般是跟後端約定好過期時效

//設定token
export const setToken = (token, expires_at) => {
  Cookies.set(TOKEN_KEY, token);
  Cookies.set(EXPIRES_AT, expires_at);
};
export const getToken = () => Cookies.get(TOKEN_KEY); //取得token
export const getExpiresAt = () => Cookies.get(EXPIRES_AT); //取得時效

//確認目前瀏覽器的token過期沒
export const isTokenExpired = () => {
  const accessToken = getToken();
  const expiresAt = getExpiresAt();
  if (!accessToken || !expiresAt) {
    return true;
  }
  const now = new Date().getTime();
  return now > Number(expiresAt);
};
```
