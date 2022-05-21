---
title: Hexo介紹和常用指令
date: 2017-11-25 23:52:18
categories:
    - 部落格
tags:
    - hexo
---

##### Hexo 是什麼?

[Hexo] 是一個基於 Node.js 所建立的網誌框架，你不需要懂 Node.js，只要會下 command 就行了。
使用 `Markdown` 來編寫文章，[Hexo] 會將你寫的Markdown文章解析並產生靜態檔案，可以直接部署到 GitHub 上，完全不需要後端的支援。

<!--more-->

大多數人部署到 GitHub 的原因是，它可以免費置放靜態檔案，缺點是必須公開所有的程式碼，
而 [Hexo] 也有方便的指令可以將你的網誌部署到 GitHub 上。
當然其他網頁伺服器軟體也可以，但就要自行架設主機，建置環境。

我選擇 [Hexo] 的原因是...它有完整的繁體中文文件!
你也可以選擇很多人在用的 [Jekyll]、[Hugo]

[Hexo]: https://hexo.io/zh-tw/
[Jekyll]: https://jekyllrb.com/
[Hugo]: https://gohugo.io/

##### Hexo常用指令

    hexo server

啟動伺服器，瀏覽 [http://localhost:4000/](http://localhost:4000/) 就可以看到所產生好的網誌。

    hexo new post HelloWorld

建立一篇新的文章，在 `source\_posts` 底下會產生一個 `HelloWorld.md` 的檔案，編輯這個檔案來撰寫文章。

    hexo clean

刪除快取檔案 `db.json` 和放置靜態檔案的 `public` 資料夾。

    hexo generate

產生靜態檔案於 `public` 資料夾。

    hexo deploy

部屬網誌，需先於 `_config.yml` 中設定 `deploy` 相關參數。
