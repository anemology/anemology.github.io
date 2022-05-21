---
title: MS SQL Server 啟用 sa 帳戶與 1433 連接埠
date: 2018-06-09 17:36:29
categories:
    - 資料庫
tags:
    - sql-server
---

版本: ***Microsoft SQL Server 2012 R2***

## 1433 port

開始功能表 -> Microsoft SQL Server 2012 R2 -> 組態工具 -> SQL Server 組態管理員 -> SQL Server 網路組態 -> MSSQLSERVER 的通訊協定 -> TCP/IP -> 已啟用 -> 是 -> IP 位置 -> IPALL -> TCP 通訊埠 -> 1433

## 混合驗證模式

伺服器右鍵 -> 屬性 -> 安全性 -> 伺服器驗證 -> SQL Server 及 Windows 驗證模式

## sa 帳戶

伺服器 -> 安全性 -> 登入 -> sa -> 右鍵內容 -> 設定密碼 -> 狀態 -> 設定 -> 登入 -> 已啟用

所有設定完成之後，記得重啟 SQL Server 服務
