---
title: force push 的使用時機
date: 2025-01-22 10:50:20
tags: [Git]
comment: false
categories: 一起來學 Git 吧！
---
## 前言 

初學 Git 的人，在學會 `git push` 指令之後，大概都聽過一句話：
> 不要隨便進行 **force push**

這件事大概讓很多新手一聽到 `git push --force` (或 `git push -f`) 指令，就不寒而慄

確實，這個指令就像是拿到系統的最高管理權限一般，可以讓你完全依照自己的意思去更動 git 的分支

雖然我們被要求 **不要隨便執行 `git push -f` 指令**，但什麼叫隨便執行呢？又什麼時機應該執行呢？

其實只要能理解 git 推送的正確觀念，**force push** 將會是一個很好用的指令！

讓我們進入今天的正題

:::info no-icon
force push 的使用時機
:::

## 為什麼沒辦法執行 `git push`

在了解 force push 之前，我們先來聊聊在什麼情況下會沒辦法 執行 `git push`

### 地端分支尚未建立遠端追蹤分支

第一種狀況，你還沒執行過建立遠端追蹤分支的指令：
```sh
git push -u origin 地端分支名稱:遠端分支名稱
```

還沒執行這個指令之前，單獨執行 `git push` 時，Git 並不知道這個分支應該被推送到哪  
這種情況下，還不需要動用 force push ，只要執行上面的指令，即可成功推送

### 分支被限制推送規則，或是分支保護

在專案的分支管理中，我們為了保護重要的分支不會隨便被弄亂，通常會針對重要的分支進行分支的保護
這個保護機制通常是遠端儲存庫提供的功能，舉凡 GitHub、GitLab 等常見的 Git 儲存庫，都會有這樣的設定

而我們透過軟體功能，限制 `main` 、`develop` 分支不能直接在地端執行 `git push` 指令  
因為這些分支在專案中往往有重要的意義：**上線**  

當這些分支有新的 commit ，同時也代表著，這個新 commit 將成為一個可以在線上運作的 **版本**  
如果隨便一個團隊成員都可以在地端推送新的 commit ，線上的版本一定會大亂！

所以我們往往會去針對這些分支設定 **分支規則**，限制只能透過 PR (pull request) 的方式增加新功能(feature)
> 如果您不知道什麼是 PR ，可以參考 [使用 GitHub - Pull Requests (PR)](https://imall.dev/git-learn/29%20-%20%E4%BD%BF%E7%94%A8%20GitHub%20-%20Pull%20Request%20(PR)/) 文章

這種狀況下，你完全不可能、也不該執行 `git push` ，即便執行了 `git push -f` ，理論上也會被遠端儲存庫(GitHub、GitLab 等) 限制執行。

而我們最常聽到的那句：不要隨便執行 `git push -f`，就是在講這個情境。

### 地端分支跟遠端分支不一致

這個狀況有兩個實際的情境

#### 使用 `git commit --amend`  修改最近一次的 commit 
如果您沒使用過這個指令，可以參考 [修改最近一次的 Commit 紀錄](https://imall.dev/git-learn/16%20-%20%E4%BF%AE%E6%94%B9%E6%9C%80%E8%BF%91%E4%B8%80%E6%AC%A1%E7%9A%84%20Commit%20%E7%B4%80%E9%8C%84/) 文章

由於 `git commit --amend` 指令會讓 commit id 重新生成，就會讓地端分支的 commit 跟 遠端分支的 commit 不一致，導致你沒辦法直接執行 `git push`

#### 使用 `git rebase` 重新定義基底
如果您還不知道什麼情境下可以用 rebase ，可以參考 [用 rebase 重新定義基底](https://imall.dev/git-learn/22%20-%20%E7%94%A8%20rebase%20%E9%87%8D%E6%96%B0%E5%AE%9A%E7%BE%A9%E5%9F%BA%E5%BA%95/) 文章

最重要的情境大概是這樣
1. 你在 main 或 master 建立新的功能分支開發功能，並且已經先把 **自己的分支** 推送到遠端 (注意，推的是自己的分支)
2. 你的同事在同一時間也做了一樣的事情
3. 你的同事完成開發，並且已經發了 pr 到 main 分支，也已經合進去 main 分支
4. 因為你的分支內容已經跟 main 分支有落差，需要先更新自己分支的內容

你可以用 `git merge` 也可以用 `git rebase` ，最大的差異是，後者能讓線圖變得更乾淨。

不過代價是，如果你已經執行過 `git push`，在 rebase 到 main 分支後，想再次執行 `git push`，就會發生錯誤，因為 **你的地端分支走向，已經跟遠端分支走向不同了**

上述的兩種情況，就是你可以大方執行 `git push -f` 的好時機


## 使用 Force Push

### 使用指令
指令很簡單：

```sh
git push --force
```

或是使用簡寫

```sh
git push -f
```
### 使用 Fork

使用 GUI 執行 Fork Push 的方式，通常都會在 Push 介面上給你一個 `Force Push` 的選項
![](https://i.imgur.com/QgQ2arT.png)

打勾之後再點選 Push ，就是在執行 Force Push 的行為了

注意到圖中特別標記一個警示符號，提醒你現在做的行為是需要特別注意的！