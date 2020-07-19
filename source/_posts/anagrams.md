---
title: Anagrams
date: 2016-06-29 18:22:11
id: 7
categories:
    - 演算法
tags:
---

面試時做線上測試遇到一個 Anagrams 的問題，所以在這裡筆記一下~

* * *

<!--more-->

原始題目網址：[https://www.testdome.com/Questions/PHP/AreAnagrams/4838?visibility=1&amp;skillId=5](https://www.testdome.com/Questions/PHP/AreAnagrams/4838?visibility=1&amp;skillId=5)

### 判斷兩個字串是否為 Anagrams (暫譯:變位詞)

Anagrams 的意思是兩個字串的組成是否相同

apple 和 pplea 會輸出 true

bird 和 biidd 則會輸出 false

解決方法

1. 判斷兩個字串長度是否相同，若不同就可以直接輸出false。
2. 將字串分割成陣列。
3. 將陣列依照字母順序排列。
4. 比對陣列裡的每個元素是否相同。
&nbsp;

* * *

參考網址:
[[HackerRank]Java變位詞(Java Anagrams) | MagicLen](https://magiclen.org/hackerrank-java-anagrams/)
