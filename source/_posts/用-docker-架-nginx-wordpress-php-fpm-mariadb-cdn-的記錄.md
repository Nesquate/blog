---
title: 用 Docker 架 Nginx + WordPress (PHP-FPM) + MariaDB + CDN 的記錄
tags:
  - MariaDB
  - Nginx
  - VPS
  - WordPress
categories:
  - - 架站相關
date: 2023-03-31 15:41:19
---

### 釐清需求

*   需要有 HTTPS
*   **不要再 Reverse Proxy 了**
*   用 MariaDB
*   **需要活用 CDN 做圖片快取**

HTTPS 已經是現在網站的標配了，而用 MariaDB 只是單純討厭 Oracle...。

至於 Reverse Proxy 的部分，以前在用 VPS 架設 WordPress 的時候，因為 PHP-FPM 與 Nginx 設定上的卡關，所以最後都用 Reverse Proxy 含糊過去。雖然架站速度也確實是快，但是因為容器內也有一個 HTTP Server，頗吃系統資源。

最後一點是 CDN，由於之後想要藉由 WordPress 內的媒體庫功能管理所有媒體，所以希望在連上 /wp-content 底下相關內容時可以將上傳的內容全部給 CDN 做快取。
<!-- more -->
### docker-compose.yml 的撰寫

這邊直接從 docker-compose.yml 開始切入，至於怎麼安裝 Docker 則不在本文討論範圍。

因為需要 HTTPS，所以這邊採用不用花錢的 Let's Encrypt 方案，Docker 容器部署則是選擇整合了 Certbot 的 [docker-nginx-certbot](https://github.com/JonasAlfredsson/docker-nginx-certbot) 這個 Image，Environment 的部分也只要填寫 CERTBOT\_EMAIL 就好了。

WordPress 的 Image也是使用[官方版本](https://hub.docker.com/_/wordpress)就好，不過這邊我有多設WORDPRESS\_TABLE\_PREFIX 原因是我沒有找到可以在同個 MariaDB (或 MySQL) 容器上輕易建立兩個以上的 Database，考量到之後還有可能要再用同個資料庫安裝其他服務，就先加上去。

WordPress 官方 Image 並沒有處理 SMTP，這我之後再找時間處理看看...。

MariaDB 的部分目前就先用官方 Image 就好，不過這邊沒啥要注意的事項，就依照自己需求設定就好。

所以整個 docker-compose.yml 寫起來會變成這樣：

```yaml
version: "3.9"
services:
  web:
    restart: always
    image: jonasal/nginx-certbot:4.2.0
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - 
    environment:
      CERTBOT_EMAIL: 
    networks: 
      default:
        aliases:
          - 
  wordpress:
    restart: always
    image: wordpress:6.1.1-php8.2-fpm-alpine
    environment:
      WORDPRESS_DB_HOST: mariadb
      WORDPRESS_DB_USER: 
      WORDPRESS_DB_PASSWORD: 
      WORDPRESS_DB_NAME: 
      WORDPRESS_TABLE_PREFIX: 
    volumes:
      - 
    networks:
      default:
        aliases:
          - 
  mariadb:
    restart: always
    image: mariadb:10.11.2-jammy
    environment:
      MYSQL_DATABASE: 
      MYSQL_USER: 
      MYSQL_PASSWORD: 
      MYSQL_RANDOM_ROOT_PASSWORD: "1"
    volumes:
      - 
```

我把敏感資訊和 Volume 相關資訊全部拔掉了，有需要者自己翻文件填入。

實際安裝後我發現 cURL 會有 Error 28 ，以及 PHP 效能奇差的問題，後來找到 [stackoverflow 上](https://stackoverflow.com/questions/61490829/error-curl-error-28-resolving-timed-out-after-10000-milliseconds-http-request)有人解釋說 Nginx 容器的 network 要加上 alias，於是我 WordPress 和 Nginx 容器都加上去後就正常多了。

### Nginx 端的設定 - 先把 WordPress 搞定

至少 WordPress 要先能夠瀏覽才可以做其他設定吧？另外先設定好 Nginx 也是為了確保 [docker-nginx-certbot](https://github.com/JonasAlfredsson/docker-nginx-certbot) 這個 Image 是否能夠正常運作。

容器要能夠運作需要指定 TLS 憑證的儲存位置才行，所以像是 ssl\_certificate、ssl\_certificate\_key、ssl\_trusted\_certificate 和 ssl\_dhparam 這些參數一定要設定，詳情可以參考 [GitHub 上的 Example](https://github.com/JonasAlfredsson/docker-nginx-certbot/blob/master/examples/example_server.conf)。

設定 FPM Proxy 的時候要注意 fastcgi\_param SCRIPT\_FILENAME，網路上的各種教學所設定的值是 $document\_root$fastcgi\_script\_name，如果 docker-nginx-certbot 的 root 參數與 WordPress 的設定 (/var/www/html) 不同的話，請將 $document\_root 改成 /var/www/html 以避免連不上的問題。

整個 site.conf 設定結果大概像是這樣：

```properties
server {
# Listen to port 443 on both IPv4 and IPv6.
listen 443 ssl;
listen [::]:443 ssl;

# Domain names this server should respond to.
server_name <your_domain_name>;
root <dir>;
index index.php;

# Load the certificate files.
ssl_certificate         /etc/letsencrypt/live/.../fullchain.pem;
ssl_certificate_key     /etc/letsencrypt/live/.../privkey.pem;
ssl_trusted_certificate /etc/letsencrypt/live/.../chain.pem;

# Load the Diffie-Hellman parameter.
ssl_dhparam /etc/letsencrypt/dhparams/dhparam.pem;
   
location / {
try_files $uri $uri/ /index.php?$is_args$args;
}

location ~ \.php$ {
try_files $uri =404;

fastcgi_split_path_info ^(.+\.php)(/.+)$;
fastcgi_pass <wordpress_contain_name>:9000;
fastcgi_index index.php;

include fastcgi_params;
fastcgi_param SCRIPT_FILENAME /var/www/html$fastcgi_script_name;
fastcgi_param PATH_INFO $fastcgi_path_info;
}
}
```

記得檢查 docker-compose.yml 確保檔案有被掛載到 /etc/nginx/user\_conf.d 裡面。還有 /etc/letsencrypt 這容器目錄也要掛載到 Volume 或是 Bind mount 否則每次開啟容器都會重新產生 TLS 憑證 (然後就不給你產了，印象中 Let's Encrypt 同個網域 7 天後才能重新產生憑證)。

### PHP 與附件大小

WordPress 官方 Image 預設只給 2 MB 的上傳，雖然說可以提醒站長一定要將圖片壓縮再上傳（不然就算有 CDN 加持還是很危險），不過我還是把它設大一點比較省事。

建立檔名為 upload.ini (或其他檔名，副檔名建議為 .ini)，然後加入以下內容：

```ini
upload_max_filesize = 32M
post_max_size = 32M
```

32M 應該在各種場合上已經夠用，畢竟我也沒有要上傳影片的意思。

然後把 upload.ini 掛載到 WordPress 容器的 /usr/local/etc/php/conf.d/ 底下，這樣重啟容器就會套用了。

但這樣還不夠，Nginx 那邊也要設定，所以在 site.conf  的 server 區段內下新增：

```properties
client_max_body_size 32M;
```

這行就好，然後重啟 Nginx 容器。

### CDN 與 wp-content 的轉址

這部分是參考 [CR 研究室的文章](https://crlab.io/339) 來設定，所以有很多相似之處。

基本上就是對原本的 wp-content/uploads 做 HTTP 301，然後再建一個同 root 的但是不同 Domain 的 Virtual Host，並且把 .php 用 HTTP 403 擋光光。

這邊 CDN 使用 Cloudflare，所以除了記得要好好設定 [SSL 驗證模式](https://blog.nesquate.tw/2022/05/15/%e8%a8%ad%e5%ae%9a-cloudflare-%e5%b0%8e%e8%87%b4%e7%80%8f%e8%a6%bd%e5%99%a8%e5%87%ba%e7%8f%be%e3%80%8cerr_too_many_redirects%e3%80%8d%e7%9a%84%e9%8c%af%e8%aa%a4/)之外，還要設定 Page Rule：

![Cloudflare-Image-Cache-PageRule](/post_image/螢幕擷取畫面-2023-03-31-152104.png)

然後設定 Nginx，舉本站為例子：

```properties
server { # WordPress
client_max_body_size 32M;

# Listen to port 443 on both IPv4 and IPv6.
listen 443 ssl;
listen [::]:443 ssl;

# Domain names this server should respond to.
server_name blog.nesquate.tw;
root /websites/wordpress;

        ......
    
location ~ /wp-content/uploads/(?<media>.+) { # Redirect to CDN
          return 301 https://cdn-blog.nesquate.tw/wp-content/uploads/$media;
  }
     
        ......

}

server { # WordPress-CDN
client_max_body_size 32M;

# Listen to port 443 on both IPv4 and IPv6.
listen 443 ssl;
listen [::]:443 ssl;

# Domain names this server should respond to.
server_name cdn-blog.nesquate.tw;
root /websites/wordpress;

        ......
 
location ~ [^/]\.php(/$) { # No php excute
          return 403;
  }
}
```

個人建議 HTTP 301 的 location 區段要放在其他 location 區段的最上面，否則重新導向可能會無法正常運作。

最後關於 CR 研究室那邊提到的 CDN Enabler，因為該外掛改動滿大的，這邊截個圖說明一下：

![CDN-Enabler-New](/post_image/螢幕擷取畫面-2023-03-31-153050.png)

「CDN 包含項目」可以不用動，不過「CDN 排除項目」預設為空，這邊建議排除以下項目：

*   所有 PHP 檔案 (.\*php)
*   wp-includes/ (我沒有要快取整個網站)
*   wp-admin/ (理由同上)
*   wp-content/themes (有些佈景主題會設 [CORS](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/CORS) 的限制，基本上是[同源政策](https://developer.mozilla.org/zh-TW/docs/Web/Security/Same-origin_policy)，這時候不排除掉瀏覽器 Console 會跑出很多錯誤，PageSpeed Test 也會跟你說)
*   wp-content/plugins (理由同上，有些外掛也會有這問題)
*   wp-content/cache (「快取你的快取」很不 OK)

設定好之後，基本上你上傳到媒體庫的圖片都會被轉址到 CDN，且被 CDN 快取。

### 小結

很久沒有摸 VPS 了，這次摸了一下並把架設 WordPress 當作練習。

有些問題印象中過去並沒有碰到過，可能是因為 WordPress 官方 Image 的 Apache + Wordpress Image 包得好好的原因吧，這就不太清楚了。

架設好網站只是開始，後面還有很多東西要調整，例如 Nginx 的瀏覽器快取設定等。