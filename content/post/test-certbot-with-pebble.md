---
title: 使用 Certbot 與 Pebble 測試簽發憑證
date: 2025-03-01T15:38:22+08:00
tags:
    - acme
    - certbot
    - certificate
---

在上一篇提到可以使用 [Pebble](https://github.com/letsencrypt/pebble) 當作測試 ACME server，這篇會簡單介紹一下怎麼使用 docker 搭配 Certbot 在本地端進行測試簽發憑證。

<!--more-->

首先從 Pebble 的 GitHub 複製範例 [docker-compose.yml](https://github.com/letsencrypt/pebble/blob/307a947a4fde234ef74f6e54ce665b4c53beba40/docker-compose.yml)，這邊我多加上了 `PEBBLE_VA_ALWAYS_VALID` 環境變數，因為主要目的是產出憑證，這可以讓 challenge validation 總是通過，讓我們不用額外設定 HTTP server 或是 DNS。

```yml
services:
  pebble:
    image: ghcr.io/letsencrypt/pebble:latest
    command: -config test/config/pebble-config.json -strict -dnsserver 10.30.50.3:8053
    ports:
      - 14000:14000 # HTTPS ACME API
      - 15000:15000 # HTTPS Management API
    networks:
      acmenet:
        ipv4_address: 10.30.50.2
    environment:
      - PEBBLE_VA_ALWAYS_VALID=1 # Skipping Validation
  challtestsrv:
    image: ghcr.io/letsencrypt/pebble-challtestsrv:latest
    command: -defaultIPv6 "" -defaultIPv4 10.30.50.3
    ports:
      - 8055:8055 # HTTP Management API
    networks:
      acmenet:
        ipv4_address: 10.30.50.3
networks:
  acmenet:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 10.30.50.0/24
```

透過 docker compose 將服務跑起來：

```bash
docker compose up -d
```

取得 container 的名稱，等等執行 Certbot 時會使用到：

```bash
$ sudo docker compose ps | grep "pebble:latest"
certbot-pebble-1         ghcr.io/letsencrypt/pebble:latest                "/app -config test/c…"   pebble         7 days ago   Up 2 minutes   0.0.0.0:14000->14000/tcp, :::14000->14000/tcp, 0.0.0.0:15000->15000/tcp, :::15000->15000/tcp
```

以我的環境來說 Pebble container 名稱是 `certbot-pebble-1`，因為 docker compose 會自動在前面加上資料夾名稱，檔案放在 certbot 資料夾下，所以自動被加上 `certbot_` 前綴。

最後來準備 Certbot command：

```bash
sudo docker run -it --rm --name certbot \
    -v "./data/etc:/etc/letsencrypt" \
    -v "./data/lib:/var/lib/letsencrypt" \
    -v "./data/log:/var/log/letsencrypt" \
    --network container:certbot-pebble-1 \
    certbot/certbot certonly \
        --standalone \
        --domain "example.com,www.example.com" \
        --non-interactive \
        --email eng@example.com \
        --no-verify-ssl \
        --agree-tos \
        --server https://certbot-pebble-1:14000/dir \
        --verbose
```

我們將 Certbot 存放資料的地方，mount 在 `./data` 底下；使用 `--network container:certbot-pebble-1` 將 Certbot 與 Pebble 在相同的 docker network 環境下執行，可以直接透過 container 名稱來存取 Pebble，所以需指定 `--server https://certbot-pebble-1:14000/dir`。若是沒有指定，預設指向 Let's Encrypt [Production server](https://letsencrypt.org/docs/acme-protocol-updates/#api-endpoints)。

執行指令之後，不需要通過任何 challenge，會直接簽發憑證，有以下輸出：

```log
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/example.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/example.com/privkey.pem
This certificate expires on 2025-03-08.
These files will be updated when the certificate renews.
```

憑證可以在剛剛 mount 的資料夾 `./data/etc/live/example.com/` 內找到，web server 需要的就會是 `fullchain.pem` 和 `privkey.pem`。

如果要查看 log

1. Certbot: `cat ./data/log/letsencrypt.log`
2. Pebble: `sudo docker compose logs`
