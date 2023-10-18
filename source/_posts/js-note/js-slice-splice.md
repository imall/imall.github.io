---
title: Array 中的 slice() 與 splice()
date: 2023-07-31 11:04:31
tags: [JavaScript]
comment: false
categories: JavaScript 筆記
---

:::info no-icon
在 JavaScript 陣列中，有兩個東西是我常常知道他們的存在，知道他們的用途，但每次都必須查 MDN 才能確認到底怎麼用
一個是 `slice` ， 另一個是 `splice`

這篇來紀錄一下這兩個陣列方法，看能不能加深一下自己的印象，未來用到的時候不用再去查 MDN
:::

## Array.prototype.slice()

[定義](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)：`slice()` 方法會回傳一個新陣列物件，為原陣列選擇之 begin 至 end（不含 end）部分的淺拷貝（shallow copy）。而原本的陣列將不會被修改。

slice 中文是「切片」，既然是用在陣列的方法，代表是把陣列的某些部分切出來，像是切長條蛋糕那樣
方法可以傳兩個參數給他，告訴他要傳哪個部分，參數分別是「開始的索引值」、「結束的索引值」
而回傳的部分，將會是：**開始索引值的資料** 到 **結束索引值前一個資料**

實際語法是這樣：

```js
arr.slice([begin[, end]])
```

例如下面的例子：

```js
const animals = ["ant", "bison", "camel", "duck", "elephant"];

console.log(animals.slice(2)); // 回傳第三個元素之後的元素
// Expected output: Array ["camel", "duck", "elephant"]

console.log(animals.slice(2,3)); // 回傳第三個元素
// Expected output: Array ["camel"]

console.log(animals.slice(2, 4)); // 回傳第三個元素與第四個元素
// Expected output: Array ["camel", "duck"]

console.log(animals.slice(-2)); // 回傳倒數第二個元素到最後一個元素
// Expected output: Array ["duck", "elephant"]

console.log(animals.slice(2, -1)); // 回傳第三個元素到倒數第二個元素
// Expected output: Array ["camel", "duck"]

console.log(animals.slice()); // 淺拷貝所有元素並且回傳
// Expected output: Array ["ant", "bison", "camel", "duck", "elephant"]
```

## Array.prototype.splice()

[定義](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)：`splice()` 方法可以藉由刪除既有元素並／或加入新元素來改變一個陣列的內容。

splice 的中文翻譯為 「拼接」，代表著可以任意去操作陣列內各個部分的資料，包含刪除、提取、新增

語法是這樣：

```js
array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```

幾個參數意思分別是：

- start：起始的位置 (陣列索引值)
- deleteCount：從起始位置後，總共要切出幾筆資料，如果設定為 0，則不會切除，如果不設定，則會從起始元素切到最後一個元素。
- 一堆的 item：從起始位置要塞入的值，如果有刪除的資料，會先把資料刪除之後再塞這些東西

一步一步來示範參數的使用：

1. 設定 start

```js
let data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const result = data.splice(5);
console.log("result", result); // [5, 6, 7, 8, 9, 10]
console.log("data", data); // [0, 1, 2, 3, 4]
```

先定義一組數列資料，從 `0` 一直到 `10`，再來使用 `splice` 方法在第一個參數只設定 `5`。

結果：
方法本身會把第五個參數到最後一個參數，回傳出來
並且原始的陣列，只會剩下沒被選到的資料

2. 設定 start 跟 deleteCount

```js
let data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const result = data.splice(5, 3);
console.log("result", result); // [5, 6, 7]
console.log("data", data); // [0, 1, 2, 3, 4, 8, 9, 10]
```

結果：
方法會把 `index = 5` 開始往後抓 3 筆資料回傳出來
原始的陣列一樣會只剩下沒被選到的資料

3. 設定 start 、deleteCount 與 item 參數

```js
let data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const result = data.splice(5, 3, "第一個 item", "第二個 item");
console.log("result", result); // [5, 6, 7]
console.log("data", data); // [0, 1, 2, 3, 4, '第一個 item', '第二個 item', 8, 9, 10]
```

結果：
輸出資料與第二個設定一樣是`index = 5` 開始往後抓 3 筆資料
不過原始的陣列在「被拿掉資料後」，在 splice 方法第一個參數(`index = 5`)位置，塞了兩筆資料進去

4. 取代某個位置的資料的值

我們可以依據第三種作法所學到的概念，進而做到把某個 index 的資料值取代掉
例如要把 `index = 7` 的資料，改變成 `"7代目"` 的話，可以這樣寫

```js
let data = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
data.splice(7, 1, "7代目");
console.log("data", data); // [0, 1, 2, 3, 4, 5, 6, '7代目', 8, 9, 10]
```

如果不知道方法參數的用意，第一次看到這個函數大概不知道為什麼是這樣設定
`data.splice(7, 1, "7代目");` 意思是，從第 `index = 7` 開始，先刪掉 `1` 筆資料 ，並且在 `index = 7` 的位置塞入 `"7代目"` 字串


以上就是 slice 與 splice 的基本用法，希望做完筆記的我之後不會再忘記 XD
