---
title: 指令：v-for
date: 2023-10-03 10:50:15
tags: [vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

看到 for 這個字樣，應該已經知道他在做啥了！

沒錯，跑迴圈。

在網頁畫面中常常會有清單資料，像是購物網的商品清單，或是 google 搜尋完的每一筆資訊，都算是清單資料的一種。

這種結構相同，但資料不同的內容，就很適合拿 v-for 來處理

# v-for 語法

最基本的 v-for 語法長這樣

```html
<li v-for="item in items">&#123;&#123; item.message &#125;&#125;</li>
```

`items` 參數跟 `item` 都可以自己命名，你想叫做 「ABC」 還是 「告嘎低」都可以，不過通常會取一個跟資料相關的命名。

像是書本清單，就會這樣寫：

```html
<li v-for="book in books">&#123;&#123; book.message &#125;&#125;</li>
```

商品清單，可能就會這樣命名：

```html
<li v-for="product in products">&#123;&#123; product.message &#125;&#125;</li>
```

無論哪種命名方式，只要名字取好了，模板就會用取好的名字來抓資料。

## 參數

v-for 語法的參數也支援索引，位置是寫在 `in` 前面，跟 **單筆資料** 寫在一起：

```html
<li v-for="(item, index) in items">
  &#123;&#123; parentMessage &#125;&#125; - &#123;&#123; index &#125;&#125; - &#123;&#123; item.message &#125;&#125;
</li>
```

如果 JS 這樣搭配的話：

```js
const parentMessage = ref("Parent");
const items = ref([{ message: "Foo" }, { message: "Bar" }]);
```

畫面會長這樣

```
Parent - 0 - Foo
Parent - 1 - Bar
```

# 巢狀 v-for
如果有需要在 v-for 中跑 v-for ，概念就跟一般 for 迴圈一樣，內層可以吃到外層的資料：


```html
<li v-for="item in items">
  <span v-for="childItem in item.children">
  <!-- item 是外層的變數，但內層可以吃得到 -->
    &#123;&#123; item.message &#125;&#125; &#123;&#123; childItem &#125;&#125;
  </span>
</li>
```

# 用數字跑 v-for

v-for 除了跑資料之外，也可以直接定義一個數字：

```html
<div v-for="n in 10">&#123;&#123; n &#125;&#125;</div>
```

而數字的初始值會是 `1`

上面這段程式跑出來的畫面會長這樣：
![](https://i.imgur.com/RPAes3f.png)


# template 的 v-for

這跟上一篇 v-if 說的  template 概念一樣

如果希望某**一包**標籤重複處理的話，就可以用 `template` 語法來使用 v-for

```html
<ul>
  <template v-for="item in items">
  <!-- 裡面兩個 li 就會重複顯示 -->
    <li>&#123;&#123; item.msg &#125;&#125;</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

# 不要同時使用 v-for 跟 v-if 在同一個標籤

由於 v-for 跟 v-if 都是在處理綁定標籤資料渲染的指令
Vue 不支援這兩個標籤同時被使用，所以不要這樣寫：
```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  &#123;&#123; todo.name &#125;&#125;
</li>
```


不過如果想要同時享用 v-for 的效果，又希望渲染完資料後要加上判斷。   
我們可以靠 `template` 來協助：

```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    &#123;&#123; todo.name &#125;&#125;
  </li>
</template>
```

這段程式的意思是，先讓畫面跑完 v-for 迴圈，在確定跑完之後，每個標籤再掛上 v-if 來判斷要不要讓資料顯示。


# v-for 的 key 值
如果只是單純的把資料跑 v-for 渲染到畫面的話，上面的寫法就足夠了。

不過如果資料順序可能因為一些操作而有順序的移動或是資料的移除的話，那就必須要在 v-for 綁一個 key 的屬性。

原因是如果有順序或資料異動的話， Vue 是採用「就地更新」的策略移動資料，意思是說， Vue 不會隨便移動 Dom 的順序，而是在原本已經渲染好的節點上，去更換資料值。

如果原本的資料有什麼「狀態」的話，Vue 沒事不會幫我們處理狀態。

例如這個[例子](https://jsfiddle.net/imall/q6btwn4a/45/)

這是一個很簡單的 todolist ，如果從最後一個項目開始打勾，由於資料本來就會消失，看起來不會有什麼大問題。

不過如果按照順序去勾選，就會發現狀態變得很奇怪，就是因為上面 Vue 處理 v-for 的方式導致。
![](https://i.imgur.com/g2xuhUh.png)

所以只要有這種操作需求，都建議要在 v-for 中綁上 key 值，像 [這樣](https://jsfiddle.net/imall/q6btwn4a/50/)。
![](https://i.imgur.com/mtBtOEJ.png)