---
title: Hexo備份至GitHub
categories:
  - 部落格
date: 2018-10-07 14:22:01
tags:
  - hexo
---

如果選擇GitHub Pages當部落格的host，Hexo雖然會將產生的靜態檔案deploy到GitHub，但我們還是要將原始檔案備份起來，例如寫文章用的Markdown，或是Hexo的設定檔。

以下就來說說如何在已經建立 `username.github.io` 儲存庫的情況下，另外開啟一個分支，來儲存我們部落格的原始檔。

<!--more-->

當然也可以在GitHub上開一個新的儲存庫，但在參考多方意見後，還是決定在相同的儲存庫下存放檔案。

首先進入到我們blog的資料夾，應該會有以下檔案及資料夾：

```sql
blog/
    .deploy_git/       --Hexo deploy to GitHub用
    node_modules/      --npm套件
    public/            --Hexo產生的靜態檔案
    scaffolds/         --Hexo樣板*
    source/            --原始文章的Markdown*
    themes/            --Hexo主題*
    .gitignore         --git排除檔案清單*
    _config.yml        --Hexo設定檔*
    db.json            --Hexo DB
    package.json       --npm套件資訊*
    package-lock.json  --npm套件資訊*
```

打星號的檔案要放到GitHub上，其他檔案因為 `npm` 或是 `Hexo` 都會幫我們自動建立，所以不需要特地放到git裡。

其實Hexo已經幫我們想好了，在 `.gitignore` 裡就有排除了不需要放到git裡面的檔案，如果你沒有這個檔案，自行新增一個即可。

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

# 加入git

再來就可以執行git指令來將資料夾加入git：

```bash
#初始化
git init .
#建立新分支hexo
git checkout -b hexo
#將檔案加入git
git add .
git commit -m 'Initial commit'
#加入遠端儲存庫位址
git remote add origin https://github.com/username/username.github.io.git
#將hexo分支推送至遠端儲存庫
git push --set-upstream origin hexo
```

這樣就完成啦，GitHub的 `username.github.io` 儲存庫應該就會有兩個分支， `master` 儲存Hexo產生的靜態檔案， `hexo` 分支儲存我們blog的原始檔。

以後寫完文章之後，就按照普通git流程，將檔案push至GitHub即可。

# 還原檔案

如果檔案不小心遺失或想要在另外一台電腦上寫作，也很簡單，步驟如下：

```bash
#將檔案clone回來
git clone https://github.com/username/username.github.io.git
cd username.github.io/
#切換到hexo分支
git checkout hexo
#依照package.json重新安裝hexo套件
npm install
#最後啟動hexo server看是不是可以正常瀏覽
hexo server
```

以上。

---

* `npm` version `6.4.1`
* `Hexo` version `3.7.1`
* `git` version `2.17.0.windows.1`