---
title: Hexo 及 NexT 主題升級紀錄
date: 2020-07-19 11:52:27
categories:
    - 部落格
tags:
    - hexo
    - next
---

從去年開始， GitHub 的 [dependabot] 就會自動偵測 repositories 內 npm 使用的相依套件，如果有安全性問題，bot 就會自動通知並發起 pull request，提醒要更新。

因我的部落格也很久沒更新了，索性就一次將 [Hexo] 及 [NexT] 的主題一起更新到最新版，順便紀錄一下作法方便之後再更新。

* Hexo `3.8.0` -> `4.2.1`
* NexT `5.1.3` -> `7.8.0`

<!--more-->

大版本更新通常會有 breaking changes，更新後發現有不少問題，因 config 自訂的地方也不多，決定整個打掉重練。

## 步驟

1. 建一個全空的 hexo 部落格

    ```bash
    # 更新 hexo-cli
    npm install hexo-cli -g
    # 初始化 hexo 至 update_blog 資料夾
    hexo init update_blog
    cd update_blog
    # 安裝部署使用的套件 hexo-deployer-git
    npm install hexo-deployer-git
    ```

1. 打開 blog 資料夾底下的 `package.json`，確認 hexo 版本 (`"hexo": "^4.2.1"`)
1. 將原本部落格資料夾內的 `scaffolds`、`source` 資料夾複製到 `update_blog` 內
1. 將原本部落格資料夾內的 `themes/next` 資料夾複製到相對應之路徑
1. 刪除沒有用到的預設主題 `themes/landscape`
1. 下載最新的 NexT 主題

    ```bash
    cd update_blog
    # 因這邊要保留原本舊的 next 主題，所以將新版主題放到不同資料夾
    git clone https://github.com/theme-next/hexo-theme-next themes/hexo-theme-next
    ```

1. 調整設定檔，這邊都直接使用新版的 yml 檔案
    1. 比對新舊 `_config.yml`，有 [調整過的設定] 我用 `#@~@` 註記起來，以後比較好找

1. 調整主題設定檔
    1. 比對 `themes\hexo-theme-next\_config.yml` 和 `themes\next\_config.yml`，將有調整過的設定改寫至根目錄底下 `_config.yml` 的 `theme_config:` [區塊]
        好處是主題內的設定檔就維持預設的樣子，以後可以整個資料夾更新，統一在同個設定檔案也比較好維護
    1. 另外 [NexT 文件] 內有提到，也可以將設定寫到 `source/_data/next.yml`，就看個人選擇

1. 完成之後可以在本機下 `hexo server`，看一下有無問題
1. 最後就可以將變更 push 到遠端 hexo 分支，[之前設定的 Travis CI] 就會自動幫我們部署到 GitHub Pages 啦~

## 其他

* 去掉 post 內的 `updated` 日期，之後有更新再寫就好，[Next 主題修改更新時間的顯示規則] 就不用了
    不確定這是新版還是舊版 NexT 行為，現在預設是 `date` 與 `updated` 不同天，才會顯示更新時間，可以參考 [another_day 設定]
* 可以看一下 `_config.yml` 的 [commit 紀錄]，會比較清楚

## 參考網址

[Update from NexT v5.1.x](https://github.com/theme-next/hexo-theme-next/blob/master/docs/UPDATE-FROM-5.1.X.md)

---

* `npm` version `6.9.0`
* `Hexo` version `4.2.1`
* `NexT` version `7.8.0`
* `git` version `2.22.0.windows.1`

[dependabot]: https://github.com/apps/dependabot
[Hexo]: https://github.com/hexojs/hexo
[NexT]: https://github.com/next-theme/hexo-theme-next
[調整過的設定]: https://github.com/anemology/anemology.github.io/blob/7305473a75e8ce6cd38e96adfdfe0311cbd21bbb/_config.yml#L6
[區塊]: https://github.com/anemology/anemology.github.io/blob/7305473a75e8ce6cd38e96adfdfe0311cbd21bbb/_config.yml#L107
[NexT 文件]: https://github.com/theme-next/hexo-theme-next/blob/master/docs/DATA-FILES.md
[之前設定的 Travis CI]: /post/setup-travis-ci-for-hexo-on-github/
[Next 主題修改更新時間的顯示規則]: /post/next-edit-time/
[another_day 設定]: https://github.com/theme-next/hexo-theme-next/commit/9f4f0143aff6e7e804e3396f33acdcf9a109efd0
[commit 紀錄]: https://github.com/anemology/anemology.github.io/blob/7305473a75e8ce6cd38e96adfdfe0311cbd21bbb/_config.yml
