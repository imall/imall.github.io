---
title: 用 Vue 寫一個翻牌計數器元件
tags:
  - Vue
  - CSS
  - JavaScript
comment: false
categories: CSS 筆記; Vue 筆記
date: 2026-04-12 20:27:04
---

## 前言

幾年前剛入職的時候，進行了一場 **新人試煉** ，內容是要將一個 JavaScript、CSS 寫的網頁翻寫成 Vue 的版本
網頁畫面本身不難，比較吸睛的地方在於，它的動畫效果特別多 (據原作者表示，當時他也只拿到一個靜態畫面，所有動畫效果都是她自由發揮的 XD)

那些動畫包含卷軸滾動時的物件飛入效果、跑馬燈動畫、對話泡泡放大縮小的無限輪播等等
而這其中讓我覺得最困難，花最多時間研究的，是翻牌計數器。

最近因為又有新人分享了這場試煉，同事們紛紛憶起當年自己是怎麼完成的，好不熱鬧(?)

回頭看了一下當初的 code ，覺得這個功能有許多小技巧值得被記錄下來

這篇文章將從零拆解這個翻頁元件的設計邏輯，包含 HTML 的解構、CSS 的圖層翻轉技巧

## 翻轉物件

一切的開始，必須先認識一個可以讓 HTML 物件翻轉的屬性：`transform`  

`transform` 的 `rotateX` 方法，做到往下翻的效果：

```html
  <style>
    .counter-box{
      width: 300px;
      height: 300px;
      background-color: #af8;
      display: flex;
      font-size: 250px;
      justify-content: center;
    }

    .counter-box:hover{
      transform: rotateX(-180deg);
    }
  </style>

  <div class="counter-area">
    <div class="counter-box">5</div>
  </div>
```

[點我看效果](https://codepen.io/imall/pen/myrQRvz)

這邊用 hover 狀態來帶入 `transform` 屬性，div 物件在 hover 狀態，在 `transform` 屬性的疊加之下，會順著 X 軸翻轉 180 度 
翻轉的度數設定會影響到正面視覺上是往前翻或往後翻轉，需要有點空間的概念去思考這件事情  
回憶一下國中數學的平片直角坐標系的畫面，橫向是 X 軸，縱向是 Y 軸  
當 rotateX 設定為正數，則會以 X 軸為軸心，順時鐘方向旋轉，從螢幕看過去，會是往後翻的感覺  
而設定為負數，則會反過來

我們後續要做的翻頁時鐘會是往前翻的，因此度數需要設定為負數

只不過在沒有過場動畫的設定下，看起來會是直接換了個位置，完全看不出效果  
不過沒關係，我們簡單加個  `transition` 讓翻轉過程多一點過場效果：

```html
  <style>
    .counter-box{
      width: 300px;
      height: 300px;
      background-color: #af8;
      display: flex;
      font-size: 250px;
      justify-content: center;
    }

    .counter-box:hover{
      transform: rotateX(-180deg);
      transition: all 0.5s;
    }
  </style>

  <div class="counter-area">
    <div class="counter-box">5</div>
  </div>
```

[點我看效果](https://codepen.io/imall/pen/MYjzJxe)

到這邊為止其實可以看得出好像有...翻轉，但完全看不出是往前翻還是往後翻...

為了看到這個效果，我們可以在父層加上透視屬性 `perspective`，這個屬性的用意會是：我們與物件~~惡~~之間的距離  
嚴格說起來是 「視點與 Z=0 平面的距離」 

簡單來說，就是我們距離多遠看著這個物件，數值越小越近，越大越遠  
眾所皆知，物品離自己越近，看到的移動效果就越明顯，因此數值越小，看到物件的翻轉效果就會越強烈  

這裡簡單設定個 500px ，這會讓裡面的卡片在做 `rotateX` 旋轉時，產生「靠近鏡頭」的 3D 迫近感，而不只是平面的縮放。

```html
  <style>
    .counter-area{
      width: 300px;
      height: 300px;
      perspective: 500px;
    }
    .counter-box {
      width: 300px;
      height: 300px;
      background-color: #af8;
      display: flex;
      font-size: 250px;
      justify-content: center;
      align-items: center;
    }

    .counter-box:hover {
      transform: rotateX(-180deg);
      transition: all 3s;
    }
  </style>

  <div class="counter-area">
    <div class="counter-box">5</div>
  </div>
```
[點我看效果](https://codepen.io/imall/pen/qEaQRvp)

眼尖的朋友在翻面的過程應該有注意到一件事：範例數字 5 在翻面完成後，是上下顛倒的  
我們因此可以知道一件事，一般來說 HTML 物件如果沒有特別設定，在翻轉過程會是透明狀態，就像是一張透明壓克力板一樣，翻轉過後依然看得到字樣
這樣的效果如果出現在翻頁計數器上，肯定會影響到視覺  

好險 CSS 有一個屬性，可以設定物件在翻面後的可視度：`backface-visibility`  
多了這個屬性後，就可以做到：只有翻到正面的時候才看得到物件

```html
    <style>
    .counter-area{
      width: 300px;
      height: 300px;
      perspective: 500px;
    }
    .counter-box {
      width: 300px;
      height: 300px;
      background-color: #af8;
      display: flex;
      font-size: 250px;
      justify-content: center;
      align-items: center;
      backface-visibility: hidden;
    }

    .counter-box:hover {
      transform: rotateX(-180deg);
      transition: all 3s;
    }
  </style>

  <div class="counter-area">
    <div class="counter-box">5</div>
  </div>
```
[點我看效果](https://codepen.io/imall/pen/MYjzJMj)

這次的效果翻轉到一半就不見了...是不是壞掉了?!  ~~你才壞掉，你全家都壞掉~~
其實不然，這恰巧是我們想要的效果：只看得到正面，看不到背面


## 兩個物件疊加的翻轉效果

有了上述的一些技巧後，就可以先簡單做一個兩個物件疊加的翻轉效果  
想像一下兩張撲克牌，數字那一面朝外，背對背~~擁抱~~相疊後，沿著 X 軸旋轉後，可以看到背後牌面的感覺

要做到這個效果  
我們先定義一個區塊，並且包裹用來放卡片的 div 物件 `.card`， 這個物件一樣放置兩個 div，當作兩張卡片

```html
<div class="counter-area">
  <div class="card">
    <div class="counter-box front">5</div>
    <div class="counter-box back">6</div>
  </div>
</div>
```
 
我們讓卡片區塊設定相對定位，而兩張卡片設定為絕對定位，做到兩張卡片重疊的效果    
同時我們把數字六先做到 180 度翻轉，讓他能在逆時鐘翻轉後變成正面

```css
    .card {
      width: 100%;
      height: 100%;
      position: relative;
    }

    .counter-box {
      position: absolute;
      width: 100%;
      height: 100%;
      display: flex;
      font-size: 250px;
      justify-content: center;
      align-items: center;
      /* 翻到背面時隱藏 */
      backface-visibility: hidden; 
      border-radius: 20px;
    }

    /* 正面：5 */
    .front {
      background-color: #af8;
      /* 一開始正面朝向我們，所以不需要特別設定 rotate */
    }

    /* 背面：6 */
    .back {
      background-color: #8af;
      /* 一開始就先把它翻面，背對背貼在 5 的後面 */
      transform: rotateX(180deg);
    }
```


緊接著就可以控制父層空間被 hover 之後，整個卡片空間沿著 X 軸，逆時針翻轉(往前翻) 180 度

```css
  /* 將 hover 判定綁在外層，只要滑鼠在區域內，就翻轉整張卡片 */
    .card:hover {
      transform: rotateX(-180deg);
    }
```


[點我看效果](https://codepen.io/imall/pen/vEXQgqV)

就會發現它壞掉了....空間看起來是往前翻了，但我的 6 呢!!!  6 怎麼不見了!!!

為什麼 6 會不見了呢？  

在預設的狀況下，HTML 的所有物件都是 2D 的畫面  
可以想像我們的 `.card` 是一張平面的紙，沒有厚度、空間的感覺，翻轉時，是翻轉這個「圖層」  

而 `.card` 裡面的 5 跟 6 呢？
物件 5 沒有懸念，因為他本來就是朝前的狀態，一定會被渲染出來  
物件 6 的部分，由於我們對卡片都共同設定了：不要渲染背面(`backface-visibility: hidden; `)的特性   
同時又先將 6 翻轉 180 度，讓背面朝前  

在 2D 圖層的世界裡，渲染引擎的計算邏輯是：「這個物件背對著我 + 它規定背面不可見 = 跳過繪製這個物件」 


這導致 `.card` 這個圖層上，從頭到尾就只畫了 5 這個物件，完全沒有 6 的存在。

因此當我們對 `.card` 翻轉，只會看到 5 反過來，而 6 則是直接消失。



藉著這個例子我們帶到一個在 CSS 3D 世界中的重要屬性：

### `transform-style: preserve-3d;`


既然 2D 的世界看不到，那我就告訴瀏覽器，我要在 3D 的世界裡面完成這個作業  

設定的位置則是 `.card` 物件，我們要把它當成一個「3D 空間」 看待這件事


```css
.card {
  /*讓裡面的 5 和 6 保持 3D 空間關係*/
  transform-style: preserve-3d; 
}
```

接著就是   Do~ Re~ Mi~ So~

[點我看效果](https://codepen.io/imall/pen/VYKVpmV)



## 用 Vue 實作一個翻頁計數器元件


### HTML 結構與 CSS 設計

說了這麼多廢話，終於可以開始寫一個翻頁計數器元件了

第一步，我們來想想 HTML 結構應該怎麼設計  

想像一下我們真實的翻頁計數器，其實分作上半部跟下半部  
當上半部往下翻時，會完成下一個數字的下半部

因此我們針對單一一個數字的結構，需要設計四片卡片：


```html
<span class="counter-area">
    <span class="counter-box is-now-top">...</span>
    <span class="counter-box is-now-bottom">...</span>
    <span class="counter-box is-next-top">...</span>
    <span class="counter-box is-next-bottom">...</span>
</span>
```


其中每個卡片都扮演不同的腳色：

* **`is-now-top` (目前數字的上半部)**：目前使用者看到的上半部，動畫開始時，它會**往前蓋下**。
* **`is-now-bottom` (目前數字的下半部)**：目前使用者看到的下半部，動畫過程中它會墊在最底層，最後被新掉下來的卡片蓋住。
* **`is-next-top` (下一個數字的上半部)**：藏在 `is-now-top` 的正後方。當 `is-now-top` 往前翻開後，它就會露出臉來。
* **`is-next-bottom` (下一個數字下半部)**：初始化時，它預先被 **「往上折 180 度」** 藏在上面。動畫開始時，它會像是落下一樣，**往下拍合**。


接著，我們利用 `z-index` 來處理這四片卡片的疊加順序：
```css
.counter-box.is-now-top    { z-index: 3; } /* 蓋在最上面 */
.counter-box.is-now-bottom { z-index: 2; } /* 墊底，準備被蓋住 */
.counter-box.is-next-top   { z-index: 1; } /* 藏在 now-top 後面 */
.counter-box.is-next-bottom{ z-index: 4; transform: rotateX(180deg); } /* 預先折到上面，背對螢幕 */
```


再來，需要設計翻頁被觸發後的 CSS `.is-active`，也就是實際的翻頁效果完成之後的狀態：

```css
/* 目前數字的上半部：往下翻蓋 (-180 度) */
.counter-area.is-active .counter-box.is-now-top {
    transform: rotateX(-180deg);
}

/* 下一個數字的下半部：原本預先被往上折，要從上面翻下來回到原位 (0 度) */
.counter-area.is-active .counter-box.is-next-bottom {
    transform: rotateX(0deg);
}

/* 記得每張卡片背面都要隱藏 */
.counter-box {
    backface-visibility: hidden;
    transition: transform 0.8s;
}
```


這邊值得注意的地方是：`backface-visibility: hidden;` 屬性是整個效果的關鍵所在

當 `is-now-top` 往下翻轉超過 90 度時，它的「背面」會朝向螢幕，此時它會瞬間變透明  
同時，預先折在上面的 `is-next-bottom` 往下掉，轉過 90 度的瞬間轉回「正面」並顯示出來。  
兩者的交接完美無縫，形成一張卡片翻落的視覺錯覺。


### 數字處理

由於是設計成一個元件，數字應該是從父層傳進來  

針對這個元件設計一個 count 屬性接收數字，並且定義總共有幾位數，預設為 7

```javascript
props: {
  count: { type: Number, required: true },
  digits: { type: Number, default: 7 }
}
```


剛剛的例子到現在，我們都只有處理單一數字，這也是翻頁動畫的精神所在，我們並不是針對整個數字去跑翻頁  
而是把每一位數拆解成獨立個體，並且計算是否需要呈現翻頁動畫    

為此我們將數字轉換成字串，依照位數補零

```javascript
const formattedString = Vue.computed(() => {
  return String(props.count).padStart(props.digits, '0');
});
```

接著我們得將每個位數進行拆解，記錄著下方資訊：
- 目前數字
- 下一位數
- 是否需要過場動畫 (數字不同需要翻轉)

這堆資訊最後會組成一個陣列  


假設目前數字是 `12345`，下一個數字會為 `12346`，則資料會長這樣：

```javascript
const allCount = ref(
  [
    { countNow: 1, countNext: 1, isActive: false },
    { countNow: 2, countNext: 2, isActive: false },
    { countNow: 3, countNext: 3, isActive: false },
    { countNow: 4, countNext: 4, isActive: false },
    { countNow: 5, countNext: 6, isActive: true  },
  ]
);
```

針對 HTML 結構，我們就跑個 `v-for` 完成他

```html
<div class="counter">
    <span class="counter-area" :class="{ 'is-active': count.isActive }" v-for="(count, i) in allCount" :key="i">
        <span class="counter-box is-now-top">
            <span class="counter counter-top"><span>{{ count.countNow }}</span></span>
        </span>
        <span class="counter-box is-now-bottom">
            <span class="counter counter-bottom"><span>{{ count.countNow }}</span></span>
        </span>
        <span class="counter-box is-next-top">
            <span class="counter counter-top"><span>{{ count.countNext }}</span></span>
        </span>
        <span class="counter-box is-next-bottom">
            <span class="counter counter-bottom"><span>{{ count.countNext }}</span></span>
        </span>
    </span>
</div>
```

緊接著我們持續監聽每次的資料變動，在每次資料異動時重複執行上述動作


```javascript

const resetData = (valStr) => {
  allCount.value = [];
  valStr.split('').forEach((val) => {
    allCount.value.push({
      countNow: val,
      countNext: val,
      isActive: false,
    });
  });
};

watch(() => formattedString.value, (newVal, oldVal) => {
  if (!oldVal) {
    resetData(newVal);
    return;
  }

  allCount.value = [];
  oldVal.split('').forEach((val, i) => {
    allCount.value.push({
      countNow: val,
      countNext: newVal[i],
      isActive: (val !== newVal[i])
    });
  });
}, { immediate: true });


```

在這樣的設計下，理論上... 應該可以組成完美的翻牌計數器動畫了才對...


[點我看效果](https://codepen.io/imall/pen/emdQvxM)


等等....   為什麼點了一次之後... 第二次只剩下數字的異動，卻再也沒有動畫了？


事實上這也是開場時提到這個效果對我來說最困難的原因之一  
當我已經做到旋轉、過場、3D 翻頁、數字轉換 

**我卻沒辦法持續地讓動畫效果在每次數字異動時都呈現**

這背後的原因是因為，當你已經對一個物件加上了 `.is-active` class 疊加上了 transition 屬性之後

是沒辦法持續疊加屬性出現動畫的！

從這可以帶入完成這個動畫效果的最後一塊拼圖：

### 瀏覽器的 `transitionend` 事件  

根據 [MDN 文件](https://developer.mozilla.org/en-US/docs/Web/API/Element/transitionend_event)，`transitionend`  事件會在 CSS transition 完成之後被觸發。

有了這個事件後，我們就可以在過場動畫完成之後，透過上方的 `resetData()` 方法調整資料，進一步觸發 `:class="{ 'is-active': count.isActive }` 將 `is-active` 屬性拿掉，讓下一次的數字變換時，可以重新遞補上 `.is-active` 觸發翻頁動畫



```javascript
// 當 CSS 翻轉動畫結束 (@transitionend) 時，將畫面重置為最新的靜止狀態
const handleTransitionEnd = () => {
  resetData(formattedString.value);
}
```


記得在加上事件處理：

```html
<span class="counter-area" 
      :class="{ 'is-active': count.isActive }" 
      v-for="(count, i) in allCount" 
      :key="i" 
      @transitionend="handleTransitionEnd"> <!-- 加上他!!-->

...

</span>
```


[點我看效果](https://codepen.io/imall/pen/myrQmWr)



## 後記

江湖一點訣，說破沒價值  

其實把所有的功能拆解過後，整個動畫效果的設計並不困難  
只不過在沒有任何提示之下要完成這個功能，真的不是很容易啊!!

事實上這篇文章的內容與步驟，正是當時我遇到這個題目時，將問題持續拆小後所得到的精隨所在  
把每一個小小功能組出來，就能完成一個看起來很困難的功能  
這也是我認為寫程式好玩的地方之一 

或許現在請 AI 把這功能寫出來，只需要花 30 秒就能完成  
不過在那個還沒有 AI 的年代，工程師們要將這個效果寫出來，不得不說真的是一門程式設計的藝術呢！