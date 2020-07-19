---
title: 第一個 Telegram Bot
date: 2019-07-16 10:54:12
categories:
    - 未分類
tags:
    - telegram
    - nodejs
---

使用 Node.js 打造第一個 Telegram Bot，需要以下東西

1. Telegram 帳號
2. 一台可以對外服務的 Server 或 PC

<!--more-->

## 建立Bot

1. 於 Telegram 內，搜尋 [@botfather]，BotFather 是 Telegram 官方用來申請以及管理bot的機器人，要注意帳號要有藍色小勾勾。
2. 輸入指令 `/newbot`。
3. 依照指示，依序輸入 bot 的顯示名稱，以及 @username。
4. 最後會得到一組 token，像是 `110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw` 這樣的字串，用以驗證身份。

## setWebhook or getUpdates

Telegram Bot 有兩種接收更新的方法，

* setWebhook - 當 user 輸入指令或是按下按鈕時，Telegram 主動向 Bot `post` request。
* getUpdates - 每隔一段時間，由 Bot 主動向 Telegram 取得資料。

引用 [官網的一句話]

> getUpdates is a pull mechanism, setWebhook is push

選擇用 `setWebhook`，會比較即時。

## 建立自簽憑證 (Self-Signed Certificate)

Telegram [要求]，使用 Webhook 的 bot 伺服器要有 SSL 憑證，並且可以接受自簽憑證 ( 但憑證的 Common Name 必須是主機的 IP )，所以我們先用自簽憑證。

p.s. 若是有自己的 Domain Name，可以使用 [Let's Encrypt] 產生正式憑證。

使用 `openssl` 建立憑證，輸入指令 `openssl req -newkey rsa:2048 -sha256 -nodes -keyout key.pem -x509 -days 365 -out cert.pem`。

Common Name 必須填入主機的 IP，其餘全部直接按 `enter` 使用預設值即可。

## 撰寫程式

使用 [官網 Sample] 推薦，已經打包好的 [Node-Telegram-bot] API，這樣就不用自己去寫 Request 接官方 API。

先在主機上安裝 `Node.js` 及 `npm` ，再照以下安裝 `express` 及 `node-telegram-bot-api`。

```bash
# 建立資料夾
mkdir telegram-firstbot
# 移動到資料夾內
cd telegram-firstbot
# 使用 npm 建立專案
npm init
# 建立 index.js
touch index.js
# 安裝 express
npm install --save express
# 安裝 Node.js Telegram Bot API
npm install --save node-telegram-bot-api
```

`index.js`內容如下，記得替換掉以下參數

* @{YOUR_TOKEN} - [@botfather] 給你的 token
* @{YOUR_URL_WITHPORT} - 你的 bot 網址，例如 `https://1.2.3.4:8443`
* @{YOUR_PORT} - bot 使用的 port，Telegram 目前只接受 443, 80, 88, 8443
* @{YOUR_PRIVATEKEY_PATH\key.pem} - 於上個步驟產生的 `key.pem` 檔案路徑
* @{YOUR_CERTIFICATE_PATH\cert.pem} - 於上個步驟產生的 `cert.pem` 檔案路徑

```javascript
const TelegramBot = require('node-telegram-bot-api');
const express = require('express');
const bodyParser = require('body-parser');
const fs = require('fs');
const https = require('https');

const TOKEN = '@{YOUR_TOKEN}';
const url = '@{YOUR_URL_WITHPORT}';
const port = @{YOUR_PORT};

// certificate
const privatekey = fs.readFileSync( '@{YOUR_PRIVATEKEY_PATH\key.pem}' );
const certificate = fs.readFileSync( '@{YOUR_CERTIFICATE_PATH\cert.pem}' );

// No need to pass any parameters as we will handle the updates with Express
const bot = new TelegramBot(TOKEN);

// This informs the Telegram servers of the new webhook.
// because we use self-signed certificate, we must provide certificate in parameters.
bot.setWebHook(`${url}/bot${TOKEN}`,{
    certificate: `@{YOUR_CERTIFICATE_PATH\cert.pem}`,
});

const app = express();

// parse the updates to JSON
app.use(bodyParser.json());

// We are receiving updates at the route below!
app.post(`/bot${TOKEN}`, (req, res) => {
    bot.processUpdate(req.body);
    res.sendStatus(200);
});

app.get('/', function (req, res) {
  res.send('listening @3@');
});

// Start Express Server with certificates
https.createServer({
    key: privatekey,
    cert: certificate
}, app).listen(port);

// Just to ping!
bot.on('message', msg => {
    bot.sendMessage(msg.chat.id, 'You said: ' + msg.text);
});
```

## 測試

1. 執行 `node index.js`
2. 輸入網址，`https://1.2.3.4:8443` 看伺服器是否有回應 `listening @3@`。
3. 用 Telegram 找到你的 bot，`http://t.me/@yourbotusername`，測試看看會不會回應我們輸入的訊息。
4. 可以使用 `https://api.telegram.org/bot<token>/getWebhookInfo`，查看 `Webhook` 的設定是否正常。

正常的話回應如下

```json
{
    "ok": true,
    "result": {
      "url": "https://${ip or domain}:${port}/bot${apikey}",
      "has_custom_certificate": true,
      "pending_update_count": 0,
      "max_connections": 40
    }
}
```

有錯誤可以查看 `last_error_messag`

```json
{
  "ok": true,
  "result": {
    "url": "https://${ip or domain}:${port}/bot${apikey}",
    "has_custom_certificate": true,
    "pending_update_count": 2,
    "last_error_date": 1560603553,
    "last_error_message": "Failed to connect to ${ip or domain}:${port}",
    "max_connections": 40
  }
}
```

[@botfather]: https://telegram.me/botfather
[Let's Encrypt]: https://letsencrypt.org/
[要求]: https://core.telegram.org/bots/webhooks#a-domain-name
[官網的一句話]: https://core.telegram.org/bots/webhooks
[官網 Sample]: https://core.telegram.org/bots/samples
[Node-Telegram-bot]: https://github.com/yagop/node-telegram-bot-api

***

## 參考網址

[Bots: An introduction for developers - Telegram](https://core.telegram.org/bots)

[node-telegram-bot-api/express.js at master · yagop/node-telegram-bot-api](https://github.com/yagop/node-telegram-bot-api/blob/master/examples/webhook/express.js)

[Node.jsでTelegramのチャットボットを作る](https://qiita.com/neetshin/items/0e2f6fa3ade41adb77bc)

[How To Create an SSL Certificate on Apache for CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-apache-for-centos-7)

[node.js - How do I setup a SSL certificate for an express.js server? - Stack Overflow](https://stackoverflow.com/questions/11804202/how-do-i-setup-a-ssl-certificate-for-an-express-js-server)
