---
title: 使用SSH Key登入Linux Server
id: 31
categories:
  - Linux
tags:
  - Linux
  - SSH
date: 2017-01-07 03:41:45
---

最近因為在玩P2P的東西，所以需要一台24/7的電腦來跑。

剛好GitHub Education之前有送DigitalOcean $50，就先拿來用了。

<!--more-->

怎麼在DigitalOcean建立Droplet就不說了，新建立好像可以直接匯入SSH Key，就不用手動加SSH Key。

OS: Ubuntu 16.04.1 x64

1\. root連到Linux Server，建立一個新的使用者。

`# add user whatever`

2\. 將使用者加入sudo群組。

`# gpasswd -a whatever sudo`

3\. 使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 建立公私鑰，也可以在Linux上用`ssh-keygen`指令建立。

![](/images/ssh-key-login-linux-1.png)

4\. 登入剛剛建立的新使用者，在家目錄建立`.ssh`目錄。

`$ su - whatever`

`$ mkdir .ssh`

`$ chmod 700 .ssh`

5\. 新增`authorized_keys`檔案，並將公鑰內容貼進去，`Ctrl+X`存檔。

`$ nano .ssh/authorized_keys`

`$ chmod 600 .ssh/authorized_keys`

6\. 輸入`exit`切換回`root`，修改這個檔案。

`# nano /etc/ssh/sshd_config`

7\. 拿掉這幾行前面的註解`#`。

`RSAAuthentication yes`
`PubkeyAuthentication yes`
`AuthorizedKeysFile %h/.ssh/authorized_keys`

8\. 將`PasswordAuthentication`設置為`no`，就不能用密碼登入嚕。

`PasswordAuthentication no`

9\. `Ctrl+X`存檔後，重啟服務。

`# service ssh restart`

10\. 接著就可以在PuTTY，用剛剛存下來的私鑰做驗證登入了(不用密碼)。

![](/images/ssh-key-login-linux-2.png)

![](/images/ssh-key-login-linux-3.png)

&nbsp;

因為公鑰是存放在whatever的家目錄，所以只能用whatever登入。

要用root登入的話就要在/root做同樣的事情，或是調整`sshd_config`的設定，不建議啦。

* * *

偷貼個推廣連結，不喜歡就不要點吧～

使用此連結註冊DigitalOcean的話可以得到10USD的額度，不過GitHub Education的promo code不知道還能不能用就是了，我沒試過。

Everyone you refer gets $10 in credit. Once they’ve spent $25 with us, you'll get $25.

[DigitalOcean: Cloud computing designed for developers](https://www.digitalocean.com/?refcode=fe1c690430d5)

* * *

參考資料：

[Initial Server Setup with Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04)