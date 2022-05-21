---
title: 使用 SSH Key 登入 Linux Server
date: 2017-01-07 03:41:45
categories:
    - Linux
tags:
    - linux
    - ssh
---

最近因為在玩 P2P 的東西，所以需要一台 24/7 的電腦來跑。

剛好 GitHub Education 之前有送 DigitalOcean $50，就先拿來用了。

<!--more-->

怎麼在 DigitalOcean 建立 Droplet 就不說了，新建立好像可以直接匯入 SSH Key，就不用手動加 SSH Key。

OS: Ubuntu 16.04.1 x64

1. root 連到Linux Server，建立一個新的使用者。

    ```bash
    add user whatever
    ```

1. 將使用者加入sudo群組。

    ```bash
    gpasswd -a whatever sudo
    ```

1. 使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 建立公私鑰，也可以在 Linux 上用 `ssh-keygen` 指令建立。

    ![ssh-key-login-linux-1](/img/ssh-key-login-linux-1.png)

1. 登入剛剛建立的新使用者，在家目錄建立 `.ssh` 目錄。

    ```bash
    su - whatever
    mkdir .ssh
    chmod 700 .ssh
    ```

1. 新增 `authorized_keys` 檔案，並將公鑰內容貼進去，`Ctrl+X` 存檔。

    ```bash
    nano .ssh/authorized_keys
    chmod 600 .ssh/authorized_keys
    ```

1. 輸入 `exit` 切換回 `root`，修改這個檔案。

    ```bash
    nano /etc/ssh/sshd_config
    ```

1. 拿掉這幾行前面的註解 `#`。

    ```text
    RSAAuthentication yes
    PubkeyAuthentication yes
    AuthorizedKeysFile %h/.ssh/authorized_keys
    ```

1. 將 `PasswordAuthentication` 設置為 `no`，就不能用密碼登入嚕。

    ```text
    PasswordAuthentication no
    ```

1. `Ctrl+X` 存檔後，重啟服務。

    ```bash
    service ssh restart
    ```

1. 接著就可以在 PuTTY，用剛剛存下來的私鑰做驗證登入了(不用密碼)。

    ![ssh-key-login-linux-2](/img/ssh-key-login-linux-2.png)

    ![img/ssh-key-login-linux-3](/img/ssh-key-login-linux-3.png)

&nbsp;

因為公鑰是存放在 whatever 的家目錄，所以只能用 whatever 登入。

要用 root 登入的話就要在 /root 做同樣的事情，或是調整 `sshd_config` 的設定，不建議啦。

* * *

參考資料：

[Initial Server Setup with Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04)
