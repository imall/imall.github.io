---
title: 從陣列中排除特定元素
date: 2024-01-02 13:45:13
tags: [JS] # 文章標籤
categories: JavaScript 筆記 # 文章分類
comment: false # 關閉評論
---


# 問題
我們在處理陣列資料的時候，偶爾會遇到這樣的需求：從 API 撈到一組資料後，需要將特定的資料進行移除

舉例來說，如果 API 給了四筆資料，這裡只單純給一個 ID，實際可能會包含其他欄位：

```js
const originalData = [
    { id: 1 },
    { id: 2 },
    { id: 3 },
    { id: 4 },
];

```

而我們可能會從 localStorage 或其他來源得到另一組資料，這裡很單純的給兩筆示意：

```js
const compareData = [
    { id: 3 },
    { id: 4 },
];
```

需求是要把原始資料跟比較資料進行比對，如果有相同資料則汰除 (或保留)

# 作法一
```js
const result = originalData.filter(item => !compareData.some(compareItem => compareItem.id === item.id));
console.log(result)
```
這個概念其實就是用 array 的 filter 搭配 some ，來去比對兩個陣列的 ID
在 some 的運作中，如果 compareData 資料內的 ID ，如果也存在於 originalData，則會先回傳 true
接著在filter 內容中加一個否定運算子 `!`，即可做到資料的汰除，而如果是要保留，只要將 `!` 拿掉即可

這個寫法確實能 work ，只不過會有兩個明顯的問題


1. 如果沒有特別解釋，第一眼我們可能沒辦法看出這段 code 在幹嘛
2. 這個寫法等同於跑了雙重迴圈，時間複雜度會是 `O(n^2)`

# 作法二
我們可以稍微調整一下，得到這樣的寫法：

```js
const filterIds = compareData.map(item => item.id);
originalData.filter(item => !filterIds.includes(item.id));
```

這個寫法把邏輯拆成兩段來做
1. 先把 `compareData` 的 `id` 篩出來
2. 用 `filter` 去篩選出資料

看起來已經解決了可讀性的問題，只不過時間複雜度呢....


事實上 `filter` 裡面使用 `includes` ，概念依然是跑了兩個迴圈


# 作法三

我們可以再調整一下

```js
const filterIds = new Set(compareData.map(item => item.id));
originalData.filter(item => !filterIds.has(item.id));
```

這次我們把 `id` 篩出來後，先丟到 `Set` 裡面，最後在 filter 裡面使用 Set 的 has 方法處理資料的判斷
由於 Set 的底層是使用 HashTable 實作的 (雖然看不到)，這也完美避開雙重迴圈導致 `O(n^2)` 的問題


# 執行時間比較
事實上這三個寫法，在資料筆數不到萬筆的時候，跑起來不會有太大的感覺，不過我們可以稍微模擬一下資料筆數有幾十萬、幾百萬的時間

首先先建立一千萬筆資料
```js
let data = [];

for (let i = 1; i <= 10000000; i++) {
    data.push({ id: i });
}
```

接著建立比較資料
```js
let data2 = [];

for (let i = 1; i <= 1000; i += 2) {
    data2.push({ id: i });
}
```

最後把上面三種寫法包成函數，並且加入時間搓記

```js

let origin = (data, data2) => {
    const start = new Date().getTime();
    data = data.filter(
        result => !data2.some(item => item.ID === result.ID)
    );
    const end = new Date().getTime();
    return end - start;
}
let arr = (data, data2) => {
    const start = new Date().getTime();
    const filterIds = data2.map(item => item.ID);
    data.filter(item => !filterIds.includes(item.ID));;
    const end = new Date().getTime();

    return end - start;
}
let set = (data, data2) => {
    const start = new Date().getTime();
    const filterIds = new Set(data2.map(item => item.ID));
    data.filter(item => !filterIds.has(item.ID));
    const end = new Date().getTime();
    return end - start;
}
```

實際跑出來的結果，一千萬筆資料跟五百筆資料進行比較過後的結果

```js
console.log("filter some 時間：" + origin(data, data2));
console.log("filter includes 時間：" + arr(data, data2));
console.log("filter set 時間：" + set(data, data2));
```

some 的寫法跟 includes 的寫法差不多，大概落在三四秒左右
而使用 set 之後，時間可以降到低於一秒

```sh
filter some 時間：3077
filter includes 時間：4525
filter set 時間：719
```


甚至如果用 一千萬筆資料，比對九百萬筆資料，set 的結果大概只有 5秒左右，而其他兩者慢到我一度懷疑系統掛了

```sh
filter set 時間：5553
```

# 總結

如果我們接觸到的資料筆數在一萬筆以內，這三種寫法其實沒什麼差異
只是如果資料量可能會增長到極大的話，就需要思考一下時間複雜度的問題了