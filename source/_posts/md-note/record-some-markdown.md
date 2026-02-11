---
title: markdown 語法筆記
date: 2023-02-21 21:33:54
tags: [Markdown ] # 文章標籤
categories: MD 筆記 # 文章分類
sticky: true # 文章置頂
cover: assets/img/md.png # 文章封面
comment: false # 關閉評論
---

:::info no-icon
對於 markdown 語法一直處在 知其然不知所以然的狀態
最常用的是**有序列表**，對其他用法反而沒那麼駕輕就熟
趁這次件部落格，順便紀錄一下可能會用到的語法
:::

# 常用寫法

| 類別 | 用法 |編譯結果|
| - | - | - |
| 斜體（Italic）|\*斜體\*|*斜體*|
| 粗體（Bold）|\*\*粗體\*\*|**粗體**|
| 一級標題（Heading）|\# 標題|<h1>標題</h1>|
| 二級標題 |\#\# 標題|<h2>標題</h2>|
| 連結 |\[google](https://www.google.com.tw/)|[google](https://www.google.com.tw/)|
| 圖片 |\!\[\圖片alt](https://i.imgur.com/zmDlzgZ.png)|![加藤惠](https://i.imgur.com/zmDlzgZ.png)|
| 水平分隔線 |***|<hr>|
| 上標 | 這是一段文字 \<sup>上標\</sup>| 這是一段文字 <sup>上標</sup>|
| 下標 | 這是一段文字 \<sub>下標\</sub>| 這是一段文字 <sub>下標</sub>|
| 區塊引言（Blockquote）|\> 我思故我在。| <blockquote>我思故我在。</blockquote>|
| 無序列表 |\- first<br>\- second<br>\- third|<ul><li>first</li><li>second</li><li>third</li></ul>|
| 有序列表 |1. first<br>2. second<br>3. third|<ol><li>first</li><li>second</li><li>third</li></ol>|
| 行內程式碼 |\`npm install\`|`npm install`|
| 區塊程式碼 |\`\`\`<br>const a = 1;<br>console.log(a); // 1<br>\`\`\`| <pre><code>const a = 1;<br>console.log(a); // 1</code></pre> |



# 特殊寫法
## 代辦選單

```raw
- [ ] 這是一個小叉叉
- [x] 這是一個小勾勾

```

- [ ] 這是一個小叉叉
- [x] 這是一個小勾勾

## 區塊程式碼

### 一般寫法
```javascript 刪除陣列重複值的方法
//宣告一個陣列
const scapes = [
  { image: "uploads/images1", imageScapeName: "img1" },
  { image: "uploads/images2", imageScapeName: "img3" },
  { image: "uploads/images3", imageScapeName: "img1" },
  { image: "uploads/images5", imageScapeName: "img3" },
];
```
### 加上title
```javascript mark:3-6 javascript 刪除陣列重複值的方法
// ```javascript mark:3-6 javascript 刪除陣列重複值的方法

const result = [];
scapes.forEach((scape) => {
  if (!result.find((r) => r.imageScapeName === scape.imageScapeName)) {
    result.push(scape);
  }
});
```
```js mark:2-6 javascript 刪除陣列重複值的方法
// ```js mark:2-6 javascript 刪除陣列重複值的方法
const result = scapes.filter((scape, index, arr) => {
  return (
    arr.findIndex((s) => scape.imageScapeName === s.imageScapeName) === index
  );
});
console.log(result); 
// [
//    { image: 'uploads/images1', imageScapeName: 'img1' },
//    { image: 'uploads/images2', imageScapeName: 'img3' }
// ] 
```

## 文字特效

```raw
[紅橙黃綠藍靛紫]{.rainbow}
[紅色]{.red}
[粉紅色]{.pink}
[橙色]{.orange}
[黃色]{.yellow}
[綠色]{.green}
[靛色]{.aqua}
[藍色]{.blue}
[紫色]{.purple}
[灰色]{.grey}
快捷键 [Ctrl]{.kbd} + [C]{.kbd .red}
H~2~0
29^th^
```

[紅橙黃綠藍靛紫]{.rainbow}
[紅色]{.red}
[粉紅色]{.pink}
[橙色]{.orange}
[黃色]{.yellow}
[綠色]{.green}
[靛色]{.aqua}
[藍色]{.blue}
[紫色]{.purple}
[灰色]{.grey}
快捷键 [Ctrl]{.kbd} + [C]{.kbd .red}
H~2~0
29^th^

## 特殊標示

```raw
[default]{.label}
[primary]{.label .primary}
[info]{.label .info}
[:heavy_check_mark:success]{.label .success}
[warning]{.label .warning}
[:broken_heart:danger]{.label .danger}
```

[default]{.label}
[primary]{.label .primary}
[info]{.label .info}
[:heavy_check_mark:success]{.label .success}
[warning]{.label .warning}
[:broken_heart:danger]{.label .danger}

## 提示色塊

```raw
:::default
預設!!
:::
:::primary
基本基本
:::
:::info
提示提示
:::
:::success
成功成功
:::
:::warning
警告警告
:::
:::danger
危險
:::
:::danger no-icon
危險
:::
```

:::default
預設!!
:::
:::primary
基本基本
:::
:::info
提示提示
:::
:::success
成功成功
:::
:::warning
警告警告
:::
:::danger
危險
:::
:::danger no-icon
危險
:::

## 頁籤選單

```raw
;;;id2 1號卡片標題
:::success
這個是一號卡片內容
:::
;;;
;;;id2 2號卡片標題
:::success
這個是二號卡片內容
:::
;;;

```

;;;id2 1 號卡片標題
:::success
這個是一號卡片內容
:::
;;;
;;;id2 2 號卡片標題
:::warning
這個是二號卡片內容
:::
;;;

## 摺疊區塊

```raw
+++ 基本設定
內容
+++
```
;;;calp 紫色
> primary
;;;
;;;calp 藍色
info
;;;
;;;calp 綠色
success
;;;
;;;calp 黃色
warning
;;;
;;;calp 紅色
danger
;;;

+++ 預設這是一段文字
這是內容這是內容
+++

+++primary 紫色
這是紫色
+++

+++info 藍色
藍色
+++

+++success 綠色
這是綠色
+++

+++warning 黄色
這是黃色
+++

+++danger 紅色
這是紅色
+++

