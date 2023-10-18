---
title: 一個好用的時間處理套件 - Dayjs
date: 2023-03-25 21:11:06
tags: [JavaScript]
comment: false
categories: JavaScript 筆記
---

:::info no-icon
接了前人的 code，整包程式幾乎所有功能只要能用套件就用套件，其中發現一個套件叫 dayjs，用起來還不錯，來記錄一下常用功能
:::

# Dayjs 介紹

Dayjs 是一個輕量的 JavaScript 時間日期處理的函數庫，具有以下特點

1. 輕量
   > 程式碼只有 2kb 大小，並且沒有任何依賴，可以直接在瀏覽器中使用，也可以在 Node.js 中使用
2. 易上手
   > 實際使用的感覺比原生的 Date 物件更簡單，並且提供了一些常用的功能，例如：格式化時間、計算時間差、時間比較等等
3. 相依性低
   > Dayjs 只依賴於 ES5 的函數庫，因此可以在任何環境中使用，包括 Node.js、React、Vue、Angular 等等
4. 支援多國語系
   > Dayjs 支援多國語系，可以根據不同的語言來顯示不同的時間格式，例如：英文、中文、日文、韓文等等

# 安裝方法

1. CDN
   > 直接在 HTML 中引入 CDN，並且在全域中使用 Dayjs
   ```html
   <script src="https://unpkg.com/dayjs@1.8.21/dayjs.min.js"></script>
   <script>
     console.log(dayjs().format("YYYY-MM-DD HH:mm:ss"));
   </script>
   ```
   :::warning
   除了 unpkg 之外，也有其他 CDN 連結可進行使用，如：cdnjs.com (opens new window), unpkg (opens new window)，jsdelivr (opens new window)和 bootcdn.cn (opens new window)等引入
   :::
2. NPM
   > 使用 NPM 安裝 Dayjs，並且在全域中使用 Dayjs
   ```bash
   npm install dayjs --save
   ```
   ```js
   import dayjs from "dayjs";
   console.log(dayjs().format("YYYY-MM-DD HH:mm:ss"));
   ```

# 常用方法

## 取得現在的時間

> 直接使用`dayjs()`就可以取得現在的時間

```js
dayjs();
```

這個函數預設回傳一個物件，把目前時間的年、月、日、時、分、秒、毫秒等資訊都包含在內
寫法等同於

```js
dayjs(new Date());
```

## 取得特定時間

> 可以在`dayjs()`中傳入參數來取得特定的時間

```js
dayjs("2023-01-01");
dayjs("2023/01/01");
dayjs("2023,01,01");
dayjs("2023 1 1");
dayjs("2023*1*1");
```

只要是能夠被解析的日期格式都可以，所以上述的格式都能使用在`dayjs`中，不用特別的思考分隔符號應該用什麼。
此外，也可以傳入以毫秒為單位的時間戳，例如發文這天的時間戳是`1616680000000`，也可以直接傳到參數抓時間

```js
dayjs(1616680000000);
```

## 取得特定時間的年月日時等資訊

> 使用 `year()`、`month()`、`date()`、`hour()`、`minute()`、`second()`、`millisecond()` 可以取得特定時間的年、月、日、時、分、秒、毫秒等資訊

```js
dayjs().year(); // 取得年
dayjs().month(); // 取得月(0~11)，所以要顯示月份的話要 +1
dayjs().date(); // 取得日
dayjs().day(); // 取得星期幾
dayjs().hour(); // 取得時
dayjs().minute(); // 取得分
dayjs().second(); // 取得秒
dayjs().millisecond(); // 取得毫秒
```

:::warning

1. 如果要取得月份的話，因為資料月份是從 0 開始，所以要顯示月份的話要 +1
2. 取得幾「日」的話記得要用`date()`，而不是`day()`，`day()`是取得星期幾
   :::

## 格式化時間

> 使用 `format()` 可以將時間格式化為指定的格式，銜接上方取得特定時間的年月日的方法，直接透過格式化時間，也是一種取得特定時間的方式

```js
dayjs().format("YYYY-MM-DD HH:mm:ss"); // 2023-03-25 21:11:06
dayjs().format("YY"); // 23
dayjs().format("M"); // 3
dayjs().format("MM"); // 03
dayjs().format("MMM"); // 三月
dayjs().format("DD"); // 25
dayjs().format("hh"); // 09 (12 小時制)
dayjs().format("HH"); // 21 (24 小時制)
dayjs().format("mm"); // 11
dayjs().format("ss"); // 06
```

基本上就是很標準的把取出來的時間轉成指定的格式，常用格式如下表所示 (以`2023-03-25 21:11:06`為例)
| 單位 | 說明 | 呈現 |
| :----: | :----: |:----: |
| `YYYY` | 完整西元年 | 2023 |
| `YY` | 省略兩位數的西元年 | 23 |
| `M` | 省略十位數 0 的月份 |3|
| `MM` | 完整月份 | 03 |
| `MMM` | 地區月分寫法 |三月 (以台灣為例)|
| `DD` | 天 |25|
| `hh` | 小時(12 小時制) |09|
| `HH` | 小時(24 小時制) |21|
| `mm` | 分 |11|
| `ss` | 秒 |06|

若想讀取更詳細的格式可以參考[官方文件](https://day.js.org/docs/en/display/format)

## 加減時間

> 使用 add() 可以加上時間，並且可以使用 subtract() 減去時間

```js
dayjs().add(1, "day");
dayjs().subtract(1, "day");
```

註：常用的單位
| 單位 | 縮寫 | 說明 |
| :----: | :----: |:----: |
| `day` | `d` | 日|
| `week` | `w` |周|
| `month` | `M` |月|
| `year` | `y` |年|
| `hour` | `h` |小時|
| `minute` | `m` |分鐘|
| `second` | `s` |秒|

## 計算時間差

> 使用 `diff()` 計算兩個時間的時間差

```js
dayjs("2023-03-25").diff(dayjs("2023-03-24"), "day"); //1
dayjs("2023-03-27 10:30").diff(dayjs("2023-03-27 11:00"), "minute"); //-30
dayjs("2023-03-27 10:30").diff(dayjs("2023-03-27 10:00"), "minute"); //30
```

第一次使用這個函數的時候總是搞混，不知道會出現正數還是負數，這邊特別註記一下
`dayjs()` 搭配 `diff(dayjs())` 的使用方式是， `dayjs() - diff(dayjs())` 的結果

所以上面的例子 `dayjs("2023-03-27 10:30").diff(dayjs("2023-03-27 11:00"), "minute")`
意思是 `2023-03-27 10:30` - `2023-03-27 11:00` 差距多少 `minute`，則答案會是 `-30` 分鐘
反之則為 `30` 分鐘

## 起始時間 / 結束時間

> stratOf() 可以取得當天的起始時間，endOf() 可以取得當天的結束時間

```js
dayjs().startOf("day"); // 2023-03-25 00:00:00
dayjs().endOf("day"); // 2023-03-25 23:59:59
dayjs().startOf("month"); // 2023-03-01 00:00:00
dayjs().endOf("month"); // 2023-03-31 23:59:59
dayjs().startOf("year"); // 2023-01-01 00:00:00
dayjs().endOf("year"); // 2023-12-31 23:59:59
```

這個主題我比較常遇到的情境會是，我有一個日期，而我需要取得這個日期月份的第一天以及最後一天
第一天通常是那個月的 1 號，而最後一天又因為不同月份而不同，透過 `dayjs().startOf("month")` 以及 `dayjs().endOf("month")` 就可以很方便地做到這件事

註：常用單位與縮寫
| 單位 | 縮寫 | 說明 |
| :----: | :----: |:----: |
| `day` | `d` | 日|
| `month` | `M` |月|
| `year` | `Y` |年|

## 轉換成 unix 時間戳記

> 使用 `valueOf()` 可以將時間轉換成 unix 時間戳記

```js
dayjs().valueOf(); // 1616681066000
```

## 小技巧

> 以上所介紹的方法，由於都會回傳時間格式，所以可以持續串接著好幾個想操作當方法後，再用 `format()` 來轉換成想要的格式

```js
dayjs("2023 03 31").add(1, "month").startOf("month").format("YYYY-MM-DD"); // '2023-04-01'
```

# 總結

這篇就大概說了一下 `dayjs` 我常用到的功能，如果想要更深入的了解 `dayjs` 的使用方式，可以參考[官方文件](https://day.js.org/docs/en/)
