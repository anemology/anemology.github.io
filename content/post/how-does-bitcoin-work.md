---
title: 虛擬貨幣 (Bitcoin) 運作原理
date: 2017-12-03 15:02:09
categories:
    - 未分類
tags:
    - bitcoin
    - hash
    - sha-256
---

[Ever wonder how Bitcoin (and other cryptocurrencies) actually work?](https://www.youtube.com/watch?v=bBC-nXj3Ng4)

看完這部影片，可以了解 Bitcoin 背後的原理，以及挖擴到底是在挖什麼?

<!--more-->

##### 以下是個人簡單的筆記

1. Bitcoin 並沒有一個中心機構來記錄所有的交易行為，而是將所有交易都記錄在一本 **公開帳本** 中，而 Bitcoin 的本質就是這本帳本。

2. 所有人都可以在這本帳本上做紀錄，但要如何確保紀錄不會被造假?

    紀錄時會使用 **電子簽章** 來證明是該人所記錄，而每一筆紀錄都有唯一的序號，就算別人複製了同一筆交易紀錄，但序號不同，驗證的結果也不會通過。

3. 那誰來維護這本帳本呢?

    帳本會被切割成一個一個區塊，而每個區塊的開頭都會有前一個區塊的 hash 值，以此就可以判斷每個區塊正確的順序，而這也稱為區塊鏈。

4. 每個區塊除了帳本上記錄的交易行為，還有一個最重要的東西，就是 **proof of work**，就像每筆紀錄需要電子簽章，每個區塊都需要 proof of work 才能證明它是有效的。

5. proof of work 是一個特別的數字，該區塊加上 proof of work 算出來的 hash 值能讓 hash 值前面 n 位數為 0，就代表這個數字就是這個區塊的 proof of work。

6. 而挖礦就是在計算 proof of work 數字，第一個算出這個數字的人，就可以得到建立這個區塊的獎勵。

7. 第 5 點講到的 n 是不固定的，Bitcoin 會隨時改變 n，以確保每 10 分鐘都可以有一個區塊產生。

---

##### SHA-256 破解

Bitcoin 的 hash 值是用 SHA-256 雜湊演算法來運算，而 SHA-256 有多難破解，可以看看以下這個影片

[How secure is 256 bit security?](https://www.youtube.com/watch?v=S9JGmA5_unY)
