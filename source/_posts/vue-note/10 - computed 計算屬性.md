---
title: computed 計算屬性
date: 2023-09-25 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

我們已經知道模板語法中可以直接寫 JS 表達式，也知道表達式的邏輯可以抽方法來重複使用。

實務上還有一種情況，是需要「依據資料」整理出「對資料的說明」，簡單來說，就是希望透過一包「資料」來產出相依的「資料」。

舉例來說，我們有一份「陣列資料」，除了把資料呈現到畫面上之外，還要特別把「是否有資料」也呈現在畫面。

這就很適合使用 Vue 提供的計算屬性：computed。

# computed 計算屬性

這個計算屬性如同 `ref()` 一樣，是 Vue 物件提供的方法，需要使用時，記得從 Vue 解構，
並且在使用時，需要用 `變數.value` 取值

```js
const { computed } = Vue;
```

舉例來說，需要針對一筆書本清單，判斷陣列裡面有沒有內容。  
就是一個標準的透過「資料(書本清單)」產出「資料(是否有內容)」的例子。

實際程式碼可能會長這樣：

```js
const { createApp, ref, computed } = Vue;
createApp({
  setup() {
    // 這裡定義資料
    const author = ref({
      name: "kuro 大神",
      books: [
        "0陷阱！0誤解！8天重新認識 JavaScript",
        "008 天絕對看不完的 Vue.js 3 指南",
      ],
    });

    // 這裡是 computed 屬性
    const hasBook = computed(() => {
      return author.value.books.length > 0 ? true : false;
    });

    // 模板要用到的資料記得 return 出來
    return { author, hasBook };
  },
}).mount("#app");
```

```html
<div id="app">
  <h1>&#123;&#123; author.name &#125;&#125;出的書</h1>
  <ul>
    <li v-for="book in author.books">&#123;&#123; book &#125;&#125;</li>
  </ul>
  <!-- 你可以把 computed 當成一個「變數」使用，不用寫 () 執行 -->
  <div>是否還有庫存 ： &#123;&#123; hasBook &#125;&#125;</div>
</div>
```

# 計算屬性 VS 方法

這個問題應該是所有學 Vue 的人會有疑惑的。
為什麼上面的例子，不直接用方法就好？
像是這樣寫：

```js
const { createApp, ref, computed } = Vue;
createApp({
  setup() {
    const author = ref({
      name: "kuro 大神",
      books: [
        "0陷阱！0誤解！8天重新認識 JavaScript",
        "008 天絕對看不完的 Vue.js 3 指南",
      ],
    });
    // 直接寫方法
    const hasBook = () => (author.value.books.length > 0 ? true : false);

    return { author, hasBook };
  },
}).mount("#app");
```

```html
<div id="app">
  <h1>&#123;&#123; author.name &#125;&#125;出的書</h1>
  <ul>
    <li v-for="book in author.books">&#123;&#123; book &#125;&#125;</li>
  </ul>
  <!-- 如果是「方法」，記得要寫 () 執行 -->
  <div>是否還有庫存 ： &#123;&#123; hasBook() &#125;&#125;</div>
</div>
```

用方法來完成最初的例子，可以完成相同的結果。  
唯一不同的地方在於，computed 會依據「相依性」的資料是否有變動，來決定要不要暫存資料。

意思是說，如果在 computed 使用到的變數沒有被異動的話，computed 會記錄原本已產出的資料值，當畫面渲染時重新使用它。

而 method 只要在畫面有重新渲染時，就會重新執行一次。

例如這個例子：

```js
const { createApp, ref, computed } = Vue;
createApp({
  setup() {
    const author = ref({
      name: "kuro 大神",
      books: [
        "0陷阱！0誤解！8天重新認識 JavaScript",
        "008 天絕對看不完的 Vue.js 3 指南",
      ],
    });
    const count = ref(0);
    const plus = () => {
      count.value++;
    };

    const hasBook_method = () => {
      console.log("method!!!!");
      return author.value.books.length > 0 ? true : false;
    };

    const hasBook_computed = computed(() => {
      console.log("computed!!!!");
      return author.value.books.length > 0 ? true : false;
    });
    return { author, hasBook_computed, hasBook_method, plus, count };
  },
}).mount("#app");
```

```html
<div id="app">
  <h1>&#123;&#123; author.name &#125;&#125;出的書</h1>
  <ul>
    <li v-for="book in author.books">&#123;&#123;book&#125;&#125;</li>
  </ul>
  <div>是否還有庫存(hasBook_computed) ： &#123;&#123; hasBook_computed &#125;&#125;</div>
  <div>是否還有庫存(hasBook_method) ： &#123;&#123; hasBook_method() &#125;&#125;</div>

  <button @click="plus">&#123;&#123;count&#125;&#125;</button>
</div>
```

在 JS 中，使用方法跟計算屬性做一樣的事情，寫個 `console` 判斷是否有運行。  
同時寫一個毫無相干的 plus 方法去控制按鈕內的數字。

持續點擊按鈕的過程，會發現 `hasBook_method` 都會被重新執行，但 `hasBook_computed` 不會！
![Alt text](image-6.png)

# computed 的 getter 跟 setter

上面的例子，我們都只有「Get」 computed 處理好的資料，沒有「Set」 這個資料。

換個方式說明：我們只有讀取 computed 變數，沒有去改這個變數值。

事實上這個變數值預設也是「readonly」的，如果去改它，Vue 會「溫腥提醒」，邁歐北亂來。

![Alt text](image-7.png)

這段畫面的 JS 長這樣：

```js
const { createApp, ref, computed } = Vue;
createApp({
  setup() {
    const author = ref({
      name: "kuro 大神",
      books: [
        "0陷阱！0誤解！8天重新認識 JavaScript",
        "008 天絕對看不完的 Vue.js 3 指南",
      ],
    });

    const plus = () => {
      // 如果要在 JS 取用 computed 的值，跟 ref 一樣要 + .value
      hasBook_computed.value = "這是我亂改的資料";
    };
    const hasBook_computed = computed(() =>
      author.value.books.length > 0 ? true : false
    );
    return { author, hasBook_computed, plus };
  },
}).mount("#app");
```

如果我們要去「設定」computed 的值，語法會是這樣：

```js
const hasBook_computed = computed({
  get: () => (author.value.books.length > 0 ? true : false),
  set: (value) => {
    author.value.books.length = value;
  },
});
```

1. 把原本 computed() 的內容從「函式」換成「物件」。
2. 這個「物件」可以有兩個屬性，`get` 跟 `set`。
3. `get` 跟 `set` 能設定的值，都是「函式」。
4. `get` 的函式，就是最初的範例中， computed() 內的那個函式。
5. `set` 的函式，用於「設定了 computed 資料」之後要做的事情。
6. `set` 函式能帶一個參數，用來使用 computed 「被設定的值」。

完整的程式大概是這樣，可以到[這裡](https://jsfiddle.net/imall/hx15zykL/10/)玩玩看：

```js
const { createApp, ref, computed } = Vue;
createApp({
  setup() {
    const author = ref({
      name: "John Doe",
      books: ["book1：JavaScript", "book2：Vue"],
    });

    // 這個方法用來「寫入」計算屬性
    const changeBookCount = () => {
      hasBook.value = 0;
    };

    // 改寫 只能「讀取」的計算屬性，讓這個「變數」能讀取
    const hasBook = computed({
      get: () => (author.value.books.length > 0 ? true : false),
      set: (value) => {
        author.value.books.length = value;
      },
    });

    // 一樣記得，模板有用到的變數都要回傳出去
    return { author, hasBook, changeBookCount };
  },
}).mount("#app");
```

```html
<div id="app">
  <h1>&#123;&#123; author.name &#125;&#125;出的書</h1>
  <ul>
    <li v-for="book in author.books">&#123;&#123;book&#125;&#125;</li>
  </ul>
  <div>是否還有庫存： &#123;&#123; hasBook &#125;&#125;</div>

  <button @click="changeBookCount">將書本數量歸零</button>
</div>
```

computed 的 setter 語法能讓我們針對 computed 設定某個資料值，在取得資料值後，一般會在 set 方法中寫點邏輯。

不過也因為彈性變大的關係，官方有建議在 set 方法不要寫出有「副作用」的邏輯。

所謂的副作用，意思是不要在 setter 中操控跟這個變數本身沒關係的資料。

例如這樣的寫法：

```js
createApp({
  setup() {
    const books = ref(["book1：JavaScript", "book2：Vue"]);
    const anotherData = ref("朋友一生一起走");
    const hasBook = computed({
      get: () => (books.value.length > 0 ? true : false),
      set: (value) => {
        anotherData.value = "那些日子不再有";
        books.value.length = value;
      },
    });
    return { books, anotherData, hasBook };
  },
}).mount("#app");
```

以這個邏輯來說，hasBook 應該是跟 books 有關連的資料，卻在 setter 之中，去設定其他無關緊要的資料，這就叫做「副作用」。
這樣的寫法很容易在出問題的時候，因為「沒想到問題會出在這」，增加 Debug 的難易度。


事實上 computed 的本質也是方便我們能用「純粹的資料」產出「純粹的資料」，如果我們寫出有副作用的 computed ，也等於失去使用 computed 的意義了。


# 總結
1. computed 計算屬性擅長於「資料」產出「資料」
2. computed 會依據「相依性」的資料是否有變動，決定是否將計算結果進行快取緩存。
3. computed 也可以使用 getter/setter 語法，不過在 set 裡面不要寫出有副作用的內容。