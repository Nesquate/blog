---
title: 用 Dockerfile 建立最小的 FreshRSS 運行環境（PHP-FPM）
tags:
  - Docker
  - docker-php-ext
  - Dockerfile
  - PHP
categories:
  - - 架站相關
date: 2023-04-03 16:46:09
---

[FreshRSS](https://github.com/FreshRSS/FreshRSS) 是自建 [RSS](https://en.wikipedia.org/wiki/RSS) 訂閱蒐集網站的軟體，類似於 [Feedly](https://feedly.com/)。 

最近會需要 RSS 的原因是社群網站越看越焦慮。雖然演算法會提供新奇的東西或文章給我看，不過我現在一來覺得社群網站的演算法，比起幫助人們找到與自己興趣相同的東西，更容易造成_集體焦慮_，於是我要設法找出突破集體焦慮的破口；另外一方面，我認為**社群網站早已摧毀了人們閱讀長文、進一步思辨的能力**（更好笑的是[這些人還會去指責另外一個平台](https://g0v.social/@Nesquate/109804722780930842)），覺得繼續這樣下去不行，於是[自己開始整理過去所架設的部落格以及文章](https://blog.nesquate.tw/2023/03/30/%e4%bd%b5%e7%ab%99%e5%91%8a%e7%9f%a5/)，並重新開始執筆部落格，同樣的 RSS 也被我撿回來用。 

儘管 Blog 和 RSS 是老東西，且接收的消息可能沒有社群網站這麼廣泛，但我知道現在的自己需要慢下來，且專注在我想要的東西上。 

話題扯遠，來討論一下 FreshRSS 本身。 

[FreshRSS 本身有提供 Docker Image](https://github.com/FreshRSS/FreshRSS/tree/edge/Docker)，但無論是 Debian Image 還是 Alpine Image，**兩個都包含了 [Apache HTTP Server](https://httpd.apache.org/)**，由於自己已經[有還會幫我自簽 Let's Encrypt TLS 憑證的 Nginx 容器](https://blog.nesquate.tw/2023/03/31/%e7%94%a8-docker-%e6%9e%b6-nginx-wordpress-php-fpm-mariadb-cdn-%e7%9a%84%e8%a8%98%e9%8c%84/)，因此根本就不需要再多一個 HTTP Server（更何況我已經不想要再搞 Reverse Proxy），於是決定自己搞一個 Docker Image。 

翻閱 Docker 上的 [PHP Image 頁面](https://hub.docker.com/_/php)，以及 [Dockerfile 說明文件](https://docs.docker.com/engine/reference/builder/)之後，寫出以下 Dockerfile：

```apache
FROM php:fpm-alpine3.17

ENV freshrss_url="https://github.com/FreshRSS/FreshRSS/archive/refs/tags/1.21.0.zip"

RUN apk add --no-cache \
        zip libzip-dev curl-dev libxml2-dev sqlite-dev postgresql-dev gmp-dev --virtual .buildeps\
    && docker-php-ext-install -j "$(nproc)" \
        pdo_mysql \
        pdo_sqlite \
        pdo_pgsql \
        gmp \
        intl \
        zip \
    && apk del --purge .buildeps \
    && apk add --no-cache gmp-dev icu-dev libpq-dev libzip

RUN curl -o freshrss.zip -L ${freshrss_url} \
    && unzip freshrss.zip -d /var/www/html  \
    && mv /var/www/html/FreshRSS-1.21.0/* /var/www/html \
    && rm -r /var/www/html/FreshRSS-1.21.0 \
    && rm freshrss.zip

CMD [ "php-fpm" ]
```

這些內容目前還只能算是雛型，因為我後來發現掛載 /var/www/html 之後會把原本的東西給蓋掉。 

至於 apk 安裝移除以及依賴的部份，第一次安裝是負責安裝編譯的依賴，因為 docker-php-ext-install 會需要編譯 PHP 模組；至於第二次安裝則是安裝運行 PHP 模組所需要的依賴，在 Try & Error 的過程中會提示缺少的檔案，把檔名拿去 [Alpine Linux packages 網頁](https://pkgs.alpinelinux.org/packages)去搜尋就好。 

用此 Dockerfile 產出的 Image 大概為 107 MB，也算夠小了。 Dockerfile 做完之後就可以寫 docker-compose.yml、Nginx 的 site.conf 了...。