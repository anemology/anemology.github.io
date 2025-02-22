---
title: "ACME Certificate Profile"
date: 2025-02-22T19:37:45+08:00
tags:
    - acme
    - certbot
    - certificate
    - letsencrypt
---

最近要將公司服務更新憑證的方式改用 ACME client，在測試的時候發現 pebble 簽發的憑證有效期限從原本的三個月變成了五天，這篇文章會稍微記錄一下原因。

<!--more-->

ACME (Automatic Certificate Management Environment) [RFC 8555](https://datatracker.ietf.org/doc/rfc8555/) 是為了自動化更新憑證而誕生的一個協定。

[pebble](https://github.com/letsencrypt/pebble) 是 [Let's Encrypt](https://letsencrypt.org/) 提供的 ACME 測試 server，可以簡單地在 CI 環境內或者 local 進行測試，雖然 Let's Encrypt 也有提供 [staging](https://letsencrypt.org/docs/staging-environment/) 環境，但是有一個 self-hosted 的測試環境再加上可以自訂設定還是比較方便。而這篇文章會用 ACME client [certbot](https://github.com/certbot/certbot) 搭配 pebble 測試。

首先用 docker 起一個 local 的 pebble server，使用 certbot 產生 example.com 憑證，Not Before 會是當下時間，而 Not After 則是三個月後：

```bash
$ openssl x509 -noout -text -in /etc/live/example.com/cert.pem | grep "Validity" -A2
        Validity
            Not Before: Feb 22 08:35:30 2025 GMT
            Not After : May 23 08:35:29 2025 GMT
```

再用一次 certbot 加上 `--force-renewal` 強制 renew 後，發現期限竟然變成了六天後：

```bash
$ openssl x509 -noout -text -in /etc/live/example.com/cert.pem | grep "Validity" -A2
        Validity
            Not Before: Feb 22 09:30:29 2025 GMT
            Not After : Feb 28 09:30:28 2025 GMT
```

所以就去查 pebble source code 看有沒有什麼關於有效期限的設定，最後在 [test/config/pebble-config.json](https://github.com/letsencrypt/pebble/blob/9a99831fce2f8f72cb1124b051da99dbe133fde9/test/config/pebble-config.json#L23) 內發現 `"validityPeriod": 518400`，剛好 518400 秒 == 6 天。

而 profiles 內有兩個設定，`default` 和 `shortlived`， pebble 是怎麼選擇的呢？如果 ACME client 在發出 order 時沒有指定 profile，pebble [wfe/wfe.go](https://github.com/letsencrypt/pebble/blob/9a99831fce2f8f72cb1124b051da99dbe133fde9/wfe/wfe.go#L1731) 就會隨機選...

---

[Profile selection](https://datatracker.ietf.org/doc/draft-aaron-acme-profiles/) 目前還只是 draft，而 Let's Encrypt 在[公告](https://letsencrypt.org/2025/01/09/acme-profiles/)內指出會由 server 決定，不確定各 CA 的 ACME server 會怎麼實作

> If the new-order request does not specify a profile, then the server will select one for it.

而 certbot 今天剛好在相關的 issue [#9261](https://github.com/certbot/certbot/issues/9261#issuecomment-2675915347) 內有更新，預計今年會實作 ACME profiles。

如果要固定 profile，暫時的解決方式是修改 pebble-config.json 只留下想要的，或是改用有支援的 client [lego](https://github.com/go-acme/lego/releases/tag/v4.22.0)。

最後，有興趣的也可以看看 Let’s Encrypt 最新的公告 [We Issued Our First Six Day Cert - Let's Encrypt](https://letsencrypt.org/2025/02/20/first-short-lived-cert-issued/)
