---
title: Hexo 部署到 GitHub 之後失去 commit 紀錄
categories:
  - 部落格
date: 2019-06-23 10:55:32
updated: 1990-01-01 08:00:00
tags:
  - hexo
---

上禮拜寫完文章之後，用 Hexo deploy 到 Github 之後，發現 commits 全沒了！

只剩下今天的紀錄，一個 initial commit 和 新增文章的。

```bash
$ git log
commit 0c1e76b1e3c34dbc898efcf0be59743a4e715089 (HEAD -> master)
Date:   Sun Jun 16 14:10:19 2019 +0800

    new post - telegram firstbot using nodejs

commit dd80cc98ca5539898698b8277f18ba6b6c825e01
Date:   Sun Jun 16 14:10:18 2019 +0800

    First commit
```

<!--more-->

調查後發現，在本機有一個 `.deploy_git` 資料夾，是用來 deploy 用的資料夾。

在 deploy 時的動作是

1. 檢查有無 `.deploy_git` 資料夾，如無則建立，並做 initial commit。

2. 複製 `public` 資料夾內容到 `.deploy_git` 資料夾裡。

3. `commit` 並 `push` 至 Github 上。

會發生 commit 紀錄不見的原因有二

1. 換電腦，本機上沒有 `.deploy_git` 資料夾，導致 deploy 時重新建立。

2. deploy 時 `push` 到 Github 上，加了參數 `--force`。

---

最後我去舊電腦裡的資料夾，將 `.deploy_git` 資料夾弄回來，再重新 deploy 一次就行了。

若是沒有保留，那些紀錄就真的沒有了。

所以說要在不同電腦寫作時，還要額外備份 `.deploy_git` 資料夾，或是在 deploy 前，將 Github 上的紀錄先 `pull` 到本機上。

若是沒有換電腦還真的不會踩到這個雷... `git push --force` 真的要慎用。

## 參考網址

[Hexo git deployer removes commits history? Let's do something about that!](https://e.printstacktrace.blog/hexo-git-deployer-removes-commits-history-lets-do-something-about-that/)

[hexojs/hexo-deployer-git: Git deployer plugin for Hexo.](https://github.com/hexojs/hexo-deployer-git/blob/master/lib/deployer.js)
