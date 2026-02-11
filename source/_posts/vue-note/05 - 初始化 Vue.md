---
title: 初始化 Vue
date: 2023-09-20 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

歷經了前面幾篇文章，讀者大概已經對框架有最基本的認識，
我們來 "Get our hands dirty"，開始寫扣吧！

# 建構 Vue 框架的幾種方式
建立起 Vue 的開發環境有幾種方式

## 透過 CDN 使用 Vue
如同我們學習 jQuery 一樣，CDN 是我們的好朋友。  
透過這個老友一樣可以讓我們享受到 Vue 的美好：

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

記得這段程式必須寫在自己程式的「上方」，這樣我們才能成功使用它。
然後我們就可以做到上一篇文章提到 Vue 程式所做的事情：
```html
<div id="app">
    <div>{{ message }}</div>
</div>
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
    Vue.createApp({
        setup() {
            const message = 'Hello Vue World'
            return { message }
        }
    }).mount("#app");
</script>
```
當然，寫法因專案而異：  
可以直接把 Script 寫在 HTML 中，例如[這樣](https://codepen.io/imall/pen/GRPWZRZ)。  
或是把 JS 的部分抽成另一個 JS 檔案，像是[這樣](https://codepen.io/imall/pen/wvRJGKd)

點進去連結後可以玩玩看「宣告式渲染」的概念：  
去更改 `message` 變數的資料，資料可以馬上反應在畫面上。


## 用 ES 模組引入

第二種引入的方法，有別於上面在`HTML`中寫`script` 引入 CDN
而是在 JS 裡面引入程式來使用。
```html
<div id="app">
    <div>{{ message }}</div>
</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'
  createApp({
    setup(){
        const message = 'Hello Vue World'
        return { message }
    }
  }).mount('#app')
</script>
```
這裡尤其要注意，如果是在 HTML 檔案裡面寫 `script` 節點，記得要多註記 `type="module"` 程式才不會掛掉。

這裡也附上這個方法在 HTML 檔案寫 JS，以及把 JS 單獨拉一個檔案出來寫的 codepen：
- [在 HTML 檔案寫 JS 的寫法](https://codepen.io/imall/pen/dywvMOP)
- [JS 單獨拉出去](https://codepen.io/imall/pen/gOZmrLL)


## 使用 vite

在資料夾目錄中打開終端機 (Terminal、CMD)執行指令，建構出開發所需的資料夾與檔案結構。
```sh
npm create vite@latest
```

關於 vite 的部分，一時之間講不完，之後再獨立文章來談。


# 選擇障礙

怎麼才設定個環境，就有一大堆方式？  


> 因為真實世界中，沒有辦法一招打天下。  

不知道剛入門的你們有沒有這種經驗，請教工程師朋友「XXX可以怎麼做」之類的問題   
你朋友會繼續追問你「情境」是什麼，不然就是回答「你想怎麼做都可以」，感覺故意不給你「標準答案」。  

這絕非在瞧不起新手，而是我們已經太習慣「遇到問題」=>「考慮情境」=>「決定做法」這套思考模式。

當我們程式的「問題」沒有「情境」，那麼「你想怎麼做，真的都可以」。

回頭來說
~~~
Vue 提供這麼多方式讓開發者建立架構，是為了因應各式各樣的情境。
~~~

簡單舉兩個例子來解釋解釋，什麼叫 做  情境

# 只讓部分結構交給 Vue 控制
想像你是社會新鮮人，公司有一個在 Vue 出現之前就已經寫好的網站，他使用的技術是 HTML、CSS、JavaScript，並且專案有引入 jQuery。

我們身為社會新鮮人，血氣方剛的，看到公司都在用舊技術，想用點新玩意兒在專案上。  
希望那些老屁股們也能感受到 Vue 的美好，讓他們也能覺得 Vue 好棒棒。  

但不行啊....全公司只有你會寫 Vue！   
你要是直接「砍掉重練」，到時候有任何問題，你都必須頂住，不然隔天你的門禁卡就刷不進公司大門了！  

想想後果真的是有點嚴重，不過還記得 Vue 官網那聳動的標題嗎？

~~~
The Progressive JavaScript Framework
~~~

我們「漸進式」一步一步來，不要一次就翻整個網站，先來把網站中最不重要的地方改寫：頁尾。

每個網站通常都會有一個 Copy Right 的說明，並且會放上年份，例如 `© 鐵人賽 2023`，年份一般會隨著時間而異動。

就先選擇這個地方改成 Vue ，就算一不小心改失敗了，應該還不至於到頂不住的階段。

程式碼可能會像這樣：  
原本的 header、content，都不動，就把 Vue 的實體 <sup>註1</sup> 掛在 `.footer` 上面就好。  
從此之後，這個年份就不用再手動更新了，是不是真的好棒棒！
```html
    <div class="header">XXX</div>
    <div class="main-content">XXX</div>
    <div class="footer">© 鐵人賽 {{ Year }}</div>
```
```js
  Vue.createApp({
    setup() {
      const Year = new Date().getFullYear();
      return { Year }
    }
  }).mount(".footer");
```

> 這個例子是從 [Alex 宅幹嘛](https://www.youtube.com/live/Y50_RSWpWkA?si=D8Mp-Wh4kjPq7DcO) 頻道中習得，當時聽到這個例子覺得這真的是太適合給初學的朋友認識，決定寫入系列筆記文之中。

> Vue 實體，這是 Vue 的核心觀念，之後會有一篇文章講解

# 整個畫面都交由 vue 去控制
身為新鮮人的我們，因為成功把頁尾翻新，公司那些老屁股們覺得 Vue 好像真的不錯，也開始學習 Vue 怎麼使用。

你終於不是公司唯一一個會用 Vue 的人了。

這時候 PM 跑來說有新的專案要做，一個單純叫 API 把後端資料綁上畫面的頁面
然後說，這需求對你們來說應該「很簡單」，應該很快就能完成了。

此時大家有志一同， Vue 這麼棒，就使用 Vue 來做吧。  

於是這專案不再使用 jQuery，取而代之的就是單純引入 Vue CDN。
此時的 HTML 就只有一個 `<div>`，在 JS 寫好 Vue 的實體後，就把它掛在這唯一的 div 身上。
因為 Vue 模板綁定的便利，很迅速地完成需求。

PM 為此感到很滿意，覺得公司的工程師各個都很棒！

後來...  
遇到專案越來越龐大，大家開始學習怎麼用 Vite 處理這個需求，不過那又是另一個故事了...

# 總結

1. 使用 Vue 的方式有兩個
  - 使用 CDN
  - 使用 Vite

2. 如果問題的需求不明確，往往無法用「一個答案」一言以蔽之

3. 專案中可以「部分」使用 Vue 開發，也可以「全部」透過 Vue 開發，取決於專案需求。  

