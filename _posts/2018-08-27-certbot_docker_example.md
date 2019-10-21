---
layout: article
title: certbot docker example
image: /img/docker.png
tags: docker certbot SSL nginx web
category: docker
---

####docker command

```
docker run -it --rm --name certbot \
  -v /srv/nginx/certs/letsencrypt:/etc/letsencrypt \
  -v /var/log/letsencrypt:/var/log/letsencrypt \
  -v /srv/nginx/www/letsencrypt:/var/www/.well-known \
  quay.io/letsencrypt/letsencrypt -t certonly \
  --agree-tos --renew-by-default \
  --register-unsafely-without-email --agree-tos \
  --webroot -w /var/www \
  --staging \
  -d yourdomain.local -d yourdomain2.local
  ```

####nginx conf

```
upstream yourdomain1 {
    server 192.168.0.101:5601;
}

upstream yourdomain2 {
    server 192.168.0.102:9200;
}

server {
    listen               80;
    listen               443 ssl;
    server_name          yourdomain1.local;

    ssl_certificate     /etc/letsencrypt/live/yourdomain1.local/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain1.local/privkey.pem;

    location /.well-known/ {
        alias   /var/www/letsencrypt/;
        allow all;
    }

    location / {
                proxy_http_version 1.1;
                proxy_set_header   Host $host;
                proxy_set_header   X-Real-IP $remote_addr;
                proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass         http://yourdomain1;
    }
}

server {
    listen               80;
    listen               443 ssl;
    server_name          yourdomain2.local;


    ssl_certificate     /etc/letsencrypt/live/yourdomain2.local/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain2.local/privkey.pem;

    location  /.well-known/ {
        alias   /var/www/letsencrypt/;
        allow all;
    }

    location / {
                proxy_http_version 1.1;
                proxy_set_header   Host $host;
                proxy_set_header   X-Real-IP $remote_addr;
                proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass         http://yourdomain2;
    }
}
```
