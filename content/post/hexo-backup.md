---
title: Hexo 備份至 GitHub
date: 2018-10-07 14:22:01
categories:
    - 部落格
tags:
    - hexo
---

如果選擇 GitHub Pages 當部落格的 host，Hexo 雖然會將產生的靜態檔案 deploy 到 GitHub，但我們還是要將原始檔案備份起來，例如寫文章用的 Markdown，或是 Hexo 的設定檔。

以下就來說說如何在已經建立 `username.github.io` 儲存庫的情況下，另外開啟一個分支，來儲存我們部落格的原始檔。

<!--more-->

當然也可以在 GitHub 上開一個新的儲存庫，但在參考多方意見後，還是決定在相同的儲存庫下存放檔案。

首先進入到我們 blog 的資料夾，應該會有以下檔案及資料夾：

```sql
blog/
    .deploy_git/       --Hexo deploy to GitHub 用
    node_modules/      --npm 套件
    public/            --Hexo 產生的靜態檔案
    scaffolds/         --Hexo 樣板*
    source/            --原始文章的 Markdown*
    themes/            --Hexo 主題*
    .gitignore         --git 排除檔案清單*
    _config.yml        --Hexo 設定檔*
    db.json            --Hexo DB
    package.json       --npm 套件資訊*
    package-lock.json  --npm 套件資訊*
```

打星號的檔案要放到 GitHub 上，其他檔案因為 `npm` 或是 `Hexo` 都會幫我們自動建立，所以不需要特地放到 git 裡。

其實 Hexo 已經幫我們想好了，在 `.gitignore` 裡就有排除了不需要放到 git 裡面的檔案，如果你沒有這個檔案，自行新增一個即可。

```text
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

## 加入 git

再來就可以執行 git 指令來將資料夾加入 git：

```bash
#初始化
git init .
#建立新分支 hexo
git checkout -b hexo
#將檔案加入 git
git add .
git commit -m 'Initial commit'
#加入遠端儲存庫位址
git remote add origin https://github.com/username/username.github.io.git
#將 hexo 分支推送至遠端儲存庫
git push --set-upstream origin hexo
```

這樣就完成啦，GitHub 的 `username.github.io` 儲存庫應該就會有兩個分支， `master` 儲存 Hexo 產生的靜態檔案， `hexo` 分支儲存我們 blog 的原始檔。

以後寫完文章之後，就按照普通 git 流程，將檔案 push 至 GitHub 即可。

## 還原檔案

如果檔案不小心遺失或想要在另外一台電腦上寫作，也很簡單，步驟如下：

```bash
#將檔案 clone 回來
git clone https://github.com/username/username.github.io.git
cd username.github.io/
#切換到 hexo 分支
git checkout hexo
#依照 package.json 重新安裝 hexo 套件
npm install
#最後啟動 hexo server 看是不是可以正常瀏覽
hexo server
```

以上。

---

* `npm` version `6.4.1`
* `Hexo` version `3.7.1`
* `git` version `2.17.0.windows.1`
