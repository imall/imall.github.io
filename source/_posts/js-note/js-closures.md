---
title: Closures
date: 2023-08-02 14:15:28
tags: [JavaScript]
comment: false
categories: JavaScript 筆記
---

:::info no-icon
一直覺得 JavaScript Closures (閉包) 屬於不好懂的 JS 觀念之一，但他跟**物件導向**這門學問很像，不懂的時候覺得很難，懂得時候會覺得很有趣
初次接觸 Closures，完全沒辦法理解這個名字本身的涵義，事實上當時也有很多人紀錄相關的文章，但即便我重複看了好幾次，依然有種似懂非懂的感受
最近再次認識 JavaScript 的時候，又看到 Closures 的環節，感覺現在終於有能力可以用自己的話語把這個觀念記錄下來，決定寫下這篇文章
:::

## 學習閉包所需要知道的三個觀念

當不懂程式觀念的時候，第一件是絕對是去看 [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Closures)

**根據 MDN 的說明：閉包（Closure）是函式以及該函式被宣告時所在的作用域環境（lexical environment）的組合。**
~~我其實看不懂這句話在說什麼~~
**以我的話來形容：當函式中包裹著另一個函式，裡面的那個函式就被稱為閉包（Closure）。**

MDN 同時帶到第一個觀念：

### 語法作用域（Lexical scoping）

MDN 的範例：

```js mark:7,9
function init() {
  var name = "Mozilla"; // name 是個由 init 建立的局部變數
  function displayName() {
    // displayName() 是內部函式，一個閉包
    alert(name); // 使用了父函式宣告的變數
  }
  displayName();
}
init();
```

MDN 的說明：`init()` 建立了局部變數 `name` 與 `displayName()` 函式。`displayName()` 是個在 `init()` 內定義的內部函式，且只在該函式內做動。`displayName()` 自己並沒有局部變數，不過它可以訪問外面函式的變數、因而能取用在父函式宣告的變數 `name`。

我會分幾個步驟來看這段 code：

1. 先看 `init()` 本身，並且注意到裡面包著 `displayName()`，`init()` 最後執行了`displayName()`，第一步先到這就好。
2. `init()` 內部宣告了一個變數 `name` ，而這個變數被使用在 `displayName()` 函數中。
3. 銜接第二點 ，`displayName()` 函式內沒有變數，但他可以使用 `init()` 宣告的變數。

> 觀念一 ： 當一個函式包裹著另一個函式，內層函式能使用外層函式的變數，包含外層函式的參數。 (內層函式 `displayName` 可以存取外層函式 `init` 定義的變數以及的參數)

我自己是覺得這個觀念很理所當然，即便函式外層不是被一個函式包裹著，而是全域變數的話，函式本身也可以使用到，這是 JS 基本的全域與區域的觀念。

### Closures 存活時間

銜接上面的例子，MDN 更改一點東西：

```js mark:6
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```

以執行結果來說，兩段 code 會產出一樣的結果，唯一不同的是，原本在 `init` 觸發 `displayName` 的執行，而這次則是把 `displayName` 這個函式本身回傳出來後，在外面執行。

換句話說，雖然 `init` 已經執行完畢了，但 `init` 所回傳的 `displayName` 函式，因為還沒執行的關係，是可以自由決定在 `init` 函式之外的任何時刻再執行。

> 觀念二 ： Closures 存活時間，可以比建立他們的函式還長。  (displayName 的存活時間 ，比建造他的 init 函式存活時間還長)

### Closures 所參考的變數

來看看這個例子：

```js mark:14-16
function box(initVal) {
  let val = initVal || 0;
  return {
    set: function (newVal) {
      val = newVal;
    },
    get: function () {
      return val;
    },
  };
}

let b = box(20);
console.log(b.get()); // 20
b.set(99);
console.log(b.get()); // 99
```

宣告一個 box 函式，並且回傳一個物件，物件中包含 `set`、 `get` 兩個方法，分別進行 **設定** 與 **取得** `val` 變數值
跟上面例子不同的地方是，這次函式回傳的閉包，是透過一個物件包含的兩個函式，這也是閉包的一種型態之一 (誰說閉包一次只能一個，需要的話就來個好幾個！)

在最一開始直接呼叫 `get` 方法會拿到初始值 `20`，但在 `15` 行對 `val` 進行設值之後，`16` 行重新呼叫 `get` 方法，卻能取得 `15` 行設定的資料值。


實際上 Closures 儲存的外層變數，並不是複製他的值，而是直接對外層變數進行 **參考**  ($references$)，因此對變數進行更新的動作，若還有辦法在外層訪問的話，所抓到的值也會是更新後的值。

> 觀念三 ： Closures 會儲存他所需要用到的變數的參考，並且能**讀取**或**更新**他所存取的變數。


## 常見的範例

### 加法製造機

隨波逐流的把閉包的範例給放上來：
```js
function makeAdder(x) {
  return function (y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2)); // 7
console.log(add10(2)); // 12
```

這邊宣告一個函式 `makeAdder`，直接回傳一個匿名函式，而這個匿名函式使用到自己的參數與外層函式的參數
意味著使用 `makeAdder` 函式時，要先傳進去一個參數，同時宣告一個變數去接收函式執行完所回傳的函式。

而這個變數因為是**接收函式**，所以變數本身也變成了一個函式，就是內層的那個匿名函式
再執行這個變數函式時，也要帶一個參數進去，這個參數會跟執行 `makeAdder` 傳入的參數進行相加後回傳


`makeAdder`就是個工廠函數的概念，他可以根據傳入的數字不同，製造出不同的 **加法機器**。

另一個類似的例子是三明治製造機：

### 三明治製造機
```js
function 三明治製造機(基本配料) {
    function make(醬汁) {
        console.log(`${基本配料}三明治 加 ${醬汁}`)
    }
    return make;
}

let ham = 三明治製造機('火腿蛋')
ham('番茄醬');

let corn = 三明治製造機('玉米蛋')
corn('沙拉醬');
```
事實上這換湯不換藥，透過外層函式先定義要做什麼口味的三明治，並且用一個變數去接傳出來的函式
最後用這個變數去定義，這份三明治要加什麼醬料。

這個寫法可以透過 ES6 的箭頭函式語法進一步的簡化
```js
let 三明治製造機 = (基本配料) => (醬汁) => console.log(`${基本配料}三明治 加 ${醬汁}`)

let ham = 三明治製造機('火腿蛋')
ham('番茄醬');
```


## 總結
其實了解最基本的觀念後，再回頭看 Closures 的語法，幾乎是大同小異，除了上面常見的例子之外
還有八股的面試題：寫一個迴圈，每隔一秒，依序印出 1~10。其中一個解法就是使用立即調用函式(IIFE)搭配 Closures 的觀念去解
除此之外，Closures 的第三個觀念(閉包參考變數)，也很常會在模組模式（Module Pattern）所看到

這篇文章主要只想說明最基礎的閉包觀念，上面提到的那些東西，未來有機會再來討論吧！