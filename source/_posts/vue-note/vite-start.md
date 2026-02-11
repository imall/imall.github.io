---
title: 初始化 Vue3 專案的幾種指令
date: 2023-07-26 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
---

:::info no-icon
發現 [Vue 官網](https://vuejs.org/guide/quick-start.html#creating-a-vue-application) 與 [vite 官網](https://vitejs.dev/guide/#scaffolding-your-first-vite-project) 所記錄的開發起手式不太一樣，紀錄一下以不同指令進行 vue 的起手差異。
:::

由於目前為止(2023.7) Vue 推薦開發配套會是 [Vue3 script setup 語法](https://vuejs.org/api/sfc-script-setup.html) 、 [vite](https://vitejs.dev/) 與 [pinia](https://pinia.vuejs.org/) 作為主流，未來的文章將會以這幾種配套做為主。

另外，因為筆者習慣使用 npm 來設定套件，內容也多會以 npm 為主，如果習慣其他套件管理工具，例如 `yarn` 、 `pnpm` ，也是可以替換使用的！

## npm init vue@latest
```sh
$ npm init vue@latest

Vue.js - The Progressive JavaScript Framework

√ Project name: ... initVue3
√ Add TypeScript? ... No / Yes
√ Add JSX Support? ... No / Yes
√ Add Vue Router for Single Page Application development? ... No / Yes
√ Add Pinia for state management? ... No / Yes
√ Add Vitest for Unit Testing? ... No / Yes
√ Add an End-to-End Testing Solution? » No
√ Add ESLint for code quality? ... No / Yes

Scaffolding project in D:\GitHub\initVue3...

Done. Now run:

  cd initVue3
  npm install
  npm run dev
```

生成結果：
![](https://i.imgur.com/yiEu7q6.jpg)

## npm init vite@latest
```sh
$ npm create vite@latest

√ Project name: ... createVite
√ Select a framework: » Vue
√ Select a variant: » JavaScript

Scaffolding project in D:\GitHub\createVite...

Done. Now run:

  cd createVite
  npm install
  npm run dev

```
生成結果：
![](https://i.imgur.com/Ka3ersK.jpg)


## npm create vite@latest

```sh
$ npm init vite@latest

√ Project name: ... initVite
√ Select a framework: » Vue
√ Select a variant: » JavaScript

Scaffolding project in D:\GitHub\initVite...

Done. Now run:

  cd initVite
  npm install
  npm run dev
```

生成結果：
![](https://i.imgur.com/Ka3ersK.jpg)


## 結論
事實上三個指令大同小異，其中 [npm init vite](/vue-note/vite-start/#npm-init-vitelatest) 與 [npm create vite](/vue-note/vite-start/#npm-init-vitelatest)操作上完全一樣(畢竟都是 vite 官方文件的指令)

比較不同的是，使用 [npm init vue@latest](/vue-note/vite-start/#npm-init-vuelatest) 指令，操作步驟會額外詢問是否要建置 router 、 pinia，甚至一些測試框架

以結果來說，如果希望起手式就先幫忙配套好較為完整的內容，建議使用[init vue@latest](/vue-note/vite-start/#npm-init-vuelatest) 來建置


但如果希望起手式可以越簡單越好，其他功能之後再增加的話，直接使用 [npm init vite](/vue-note/vite-start/#npm-init-vitelatest) 或是 [npm create vite](/vue-note/vite-start/#npm-init-vitelatest) 即可
