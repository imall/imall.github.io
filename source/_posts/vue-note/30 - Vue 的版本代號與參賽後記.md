---
title: Vue 的版本代號與參賽後記
date: 2023-10-15 10:50:15
tags: [Vue]
categories: Vue 筆記
comment: false
render: false
---

# 前言

這篇文章來回收 [為什麼選 Vue？](https://ithelp.ithome.com.tw/articles/10321268) 所埋的伏筆。

我就先說了，這篇應該不會是什麼太正經的文章，純粹是最後一天放飛自我的內容 XD


# Vue 的版本代號
會想寫 Vue 的其中一個原因，就是他的版本代號真的很有趣。

2013 年發佈的第一個 Vue 版本是很正經的 `VueJS`，在那之後的版本，幾乎全部都是一些膾炙人口的動漫名稱：

| 版本號 | 發布時間       | 版本代稱                                                                                                                                                    |
| :----- | :------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.6    | 2013年12月8日  | VueJS                                                                                                                                                       |
| 0.9    | 2014年2月25日  | Animatrix ： [駭客任務動畫版](https://zh.wikipedia.org/zh-tw/%E9%BB%91%E5%AE%A2%E5%B8%9D%E5%9B%BD%E5%8A%A8%E7%94%BB%E7%89%88)                               |
| 0.10   | 2014年3月23日  | Blade Runner ： [銀翼殺手](https://zh.wikipedia.org/wiki/%E9%93%B6%E7%BF%BC%E6%9D%80%E6%89%8B)                                                              |
| 0.11   | 2014年11月7日  | Cowboy Bebop ： [星際牛仔](https://zh.wikipedia.org/wiki/%E6%98%9F%E9%9A%9B%E7%89%9B%E4%BB%94)                                                              |
| 0.12   | 2015年6月12日  | Dragon Ball ： [七龍珠](https://zh.wikipedia.org/wiki/%E4%B8%83%E9%BE%99%E7%8F%A0)                                                                          |
| 1.0    | 2015年10月27日 | Evangelion ： [新世紀福音戰士](https://zh.wikipedia.org/wiki/%E6%96%B0%E4%B8%96%E7%BA%AA%E7%A6%8F%E9%9F%B3%E6%88%98%E5%A3%AB)                               |
| 2.0    | 2016年9月30日  | Ghost in the Shell ： [攻殼機動隊](https://zh.wikipedia.org/wiki/%E6%94%BB%E6%AE%BC%E6%A9%9F%E5%8B%95%E9%9A%8A)                                             |
| 2.1    | 2016年11月22日 | Hunter X Hunter ： [獵人](https://zh.wikipedia.org/wiki/HUNTER%C3%97HUNTER)                                                                                 |
| 2.2    | 2017年2月26日  | Initial D ： [頭文字D](https://zh.wikipedia.org/wiki/%E9%A0%AD%E6%96%87%E5%AD%97D)                                                                          |
| 2.3    | 2017年4月27日  | JoJo's Bizarre Adventure ： [JoJo 的奇妙冒險](https://zh.wikipedia.org/wiki/JoJo%E7%9A%84%E5%A5%87%E5%A6%99%E5%86%92%E9%99%A9)                              |
| 2.4    | 2017年7月13日  | Kill la Kill ： [Kill la Kill](https://zh.wikipedia.org/wiki/KILL_la_KILL)                                                                                  |
| 2.5    | 2017年10月13日 | Level E ： [靈異E接觸](https://zh.wikipedia.org/wiki/%E9%9D%88%E7%95%B0E%E6%8E%A5%E8%A7%B8)                                                                 |
| 2.6    | 2019年2月4日   | Macross ： [超時空要塞](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%97%B6%E7%A9%BA%E8%A6%81%E5%A1%9E)                                                        |
| 2.7    | 2022年7月1日   | Naruto ： [火影忍者](https://zh.wikipedia.org/wiki/%E7%81%AB%E5%BD%B1%E5%BF%8D%E8%80%85)                                                                    |
| 3.0    | 2020年9月18日  | One Piece ： [海賊王](https://zh.wikipedia.org/wiki/ONE_PIECE)                                                                                              |
| 3.1    | 2021年6月7日   | Pluto ： [PLUTO ～冥王～](https://zh.wikipedia.org/wiki/PLUTO_%EF%BD%9E%E5%86%A5%E7%8E%8B%EF%BD%9E)                                                         |
| 3.2    | 2021年8月5日   | Quintessential Quintuplets ： [五等分的花嫁](https://zh.wikipedia.org/wiki/%E4%BA%94%E7%AD%89%E5%88%86%E7%9A%84%E6%96%B0%E5%A8%98)                          |
| 3.3    | 2023年5月11日  | Rurouni Kenshin ： [神劍闖江湖](https://zh.wikipedia.org/zh-tw/%E6%B5%AA%E5%AE%A2%E5%89%91%E5%BF%83)                                                        |
| 3.4    | 2023年12月28日 | Slam Dunk ： [灌籃高手](https://zh.wikipedia.org/zh-tw/%E7%81%8C%E7%AF%AE%E9%AB%98%E6%89%8B)                                                                |
| 3.5    | 2024年09月01日 | Tengen Toppa Gurren Lagann ： [天元突破 紅蓮螺巖](https://zh.wikipedia.org/zh-tw/%E5%A4%A9%E5%85%83%E7%AA%81%E7%A0%B4_%E7%B4%85%E8%93%AE%E8%9E%BA%E5%B7%96) |


筆者認為取得最好的代稱無非是 [OnePiece](https://blog.vuejs.org/posts/vue-3-one-piece)。  

相信大家都知道 Vue 曾經經歷 Vue2 到 Vue3 的大翻版，誕生了全新的 Composition API 語法，與此同時，有許多「大秘寶」等著我們這些開發者去挖掘，這其實也是筆者推薦新手直接從 Vue3 入門的原因。 ~~說不定我們可以比魯夫還要早成為海賊王~~


# 參賽後記
筆者最有感觸的大概是 Vue 3.2 版，[五等分的花嫁](https://blog.vuejs.org/posts/vue-3-2) ~~三玖我婆~~。 (我知道正式譯名是 `五等分的新娘`，不過還是比較喜歡 `五等分的花嫁` 這稱呼) 

我應該算是在 [海賊王](https://blog.vuejs.org/posts/vue-3-one-piece) 時期開始接觸 Vue，雖然這個版本開始已經可以使用 composition API 進行開發，不過當時工作需求還沒正式使用這個語法，所以我還是在用 [火影忍者](https://blog.vuejs.org/posts/vue-2-7-naruto) 那段時期的 2.6 、2.7 版本開發，當時跟 Vue2 的 Option API 語法奮戰了好一段時間。  

實際開始碰 Vue3，就是在 `五等分的花嫁` 版本的時期，毅然決然放棄成為火影，~~選擇開後宮~~，想當初大家問我為什麼要寫 Vue ，我都直接 Show 出 Vue 的版本代號給他們看，~~`五等分的花嫁` 欸 ，好框架，不用嗎？？~~，這個版本同時也陪我度過了快兩個年頭，直到今年得知 [`神劍闖江湖`](https://blog.vuejs.org/posts/vue-3-3) 發佈時，竟然還有那麼一絲絲感傷的心情出現， ~~重製版小薰 CV 是 **高橋李依**！！，推薦大家去看！！~~。

遙想多年前，筆者從零開始接觸程式的時候，總覺得這東西跟自己格格不入，好像進入另一個世界、另一個生活圈一樣，要跨越這個門檻真的很痛苦。記得當時常常為了理解那些學不懂的觀念，好費好幾天，每天腦袋累到像是跑完 800/1600 那樣，完全動不了。

到近期這種痛苦的學習感受似乎漸漸在消失(不知道是不是麻痺了)，以前看「官方文件」看個兩句話就投降了，現在在看前端領域的官方文件，似乎大致能理解該如何操作，這也讓我真正認知到，自己好像真的脫離「新手村」了。

在脫離新手村之後，開始參與前端社群的活動，又注意到 Vue 的版本號，好像感覺到自己跟工程師的世界似乎沒有那麼的「格格不入」，Vue 這個圈子有很多開發者都跟自己有相同的興趣，甚至有些還都是業界的大神們。

有了這一點認知後，似乎多了那麼一點動力推進自己去探索那些過往不敢(或是不會)去探索的領域的動力，確實也在真的踏入之後，才發現這些行為並沒有想像中的困難，就只是自己願不願意做罷了。

那感覺應該就跟「運動」一樣，動的時候很痛苦，不動的時候很舒服，但驅使自己去運動卻是一個能將自己推到更好的方向的舉動。


參加鐵人賽不外乎就是其中一個舉動，過往的我總是覺得自己腦中的那堆東西不值得分享出來給大家見笑，總認為寫技術文章這種事情應該是那些大神們才適合。

今年還真不知道哪根筋不對，參加了鐵人賽還一次報名兩場，這才意識到其實我算是蠻喜歡「教別人」自己已經會的東西。同時也在「講解」的過程中，注意到有些觀念明明用得很兇，卻沒能力把它講解清楚，才認知到這可能代表自己對於觀念的理解並不是那麼透徹，概就是所謂「教學相長」吧！

這系列的 Vue 筆記，絕大多數(幾乎全部)都是閱讀下列這三個網站的教學後，重新使用自己的文字來說明觀念：

1. [重新認識Vue.js: 008 天絕對看不完的Vue.js 3.0 指南](https://book.vue.tw/)
2. [Vue.js 官方網站](https://cn.vuejs.org/)
3. [Alex 宅幹嘛 YouTube 頻道](https://www.youtube.com/@AlexOtakuWhat)

需要學習完整的 Vue.js 內容，這三個連結的資訊一定會更完善，不過如同系列文開頭所說的，筆者希望這個筆記內容能成為幾乎完全不懂程式的新手的「敲門磚」，所以還是盡可能說明那些熟手習以為常，但新手可能有疑惑的地方。

總之，我的 Vue 筆記鐵人賽就此完結，如果你剛好是個想走馬看花認識 Vue 的讀者，希望這 30 篇的筆記能幫助到你。