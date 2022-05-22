---
title: "尋找 Plurk 噗文連結"
date: 2022-05-21T21:39:21+08:00
tags:
    - plurk
    - javascript
---

最近在改寫自己的 [噗浪下載程式](https://github.com/anemology/plurkdl)，發現噗文連結並沒有在 API response 內，因此尋找了一下，此篇文章則是記錄過程及思路。

<!--more-->

每篇噗文右下角向下箭頭點開，有個開啟網址的連結，可以連到單篇噗文的網頁，網址後段是 `/p/xxxxxx`。後面的 `x` 包含英數，看起來像是亂數或是雜湊值產生，初步猜想是後端生成。

## 尋找流程

1. 查看 `/TimeLine/getPlurks` (取得噗文列表)、`/Responses/get` (取得單篇噗文回應)，這兩支 API 回傳資料都沒有找到對應字串。

1. 使用瀏覽器 DevTools 的網路，看原始 html 內容，也不是後端 render 回傳，因此判斷是 JavaScript 產生。

1. 以下截圖皆為 Firefox，使用檢測元素指到 **開啟網址** 元素，class 是 `pif-outlink`，JavaScript 通常會使用 css selector + class name 來進行操作。

    ![find-plurk-permalink-1](/img/find-plurk-permalink-1.png)

1. 切到 DevTools 除錯器，按 Ctrl+Shift+F 搜尋現在網頁所載入的所有資源，在一個 minify 過的 js 內找到這個 class。點左下的大括號來將 js 排版一下，雖然變數命名沒有改變，但至少可以看到比較清楚的排版。

    產生連結的方式是 `o.toString(36)`，再往上找到 `o` 的定義，`o = r(s)`。

    ![find-plurk-permalink-2](/img/find-plurk-permalink-2.png)

1. 繼續往上，`r` 的定義是一個 function，`.data()` 是 jQuery 來取得某元素 data-attribue 的方法，因此可以判斷 `e(s)` 是某一個元素。

    最後可以得知，一開始的 `o` 會回傳某一個元素的 `data-pid` attribute。

    ![find-plurk-permalink-3](/img/find-plurk-permalink-3.png)

1. 回到 DevTools 檢測器，搜尋 `data-pid`，發現每一個噗文最外層 div 有這個 attribute，而這個 pid 其實就是 API 回傳的 `plurk_id`。

    ![find-plurk-permalink-4](/img/find-plurk-permalink-4.png)

1. 一開始產生連結的方法是 `o.toString(36)`，查了 [MDN 的定義](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toString)，是產生 n 進位的字串，把值丟進去跑，就可以產生對應的字串了。

    ```javascript
    $ Number(1500449470).toString(36)
    "otbu7y"
    ```

最後要轉成 Python code，Python 有內建 `hex()` 轉換 16 進位文字，但是沒有支援 36 進位，因此使用 mod 和整除的方式來 [簡單實作](https://github.com/anemology/plurkdl/blob/master/plurkdl.py#L162) 一下，完成！
