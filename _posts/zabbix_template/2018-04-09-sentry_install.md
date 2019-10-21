---
layout: article
title: sentry docker-compose install
image: /img/sentry.png
tags: docker compose installation sentry
category: sentry
---

Gen key :

```
docker run --rm sentry config generate-secret-key
```

replace generated key to !!!SECRET!!! in docker-compose.yml

docker-compose.yml :

```
version: '2'

volumes:
   pgdb:

services:
  redis:
    image: redis

  postgres:
    image: postgres
    environment:
      POSTGRES_USER: sentry
      POSTGRES_PASSWORD: sentry
      POSTGRES_DB: sentry
    volumes:
     - pgdb:/var/lib/postgresql/data

  sentry:
    image: sentry
    links:
     - redis
     - postgres
    ports:
     - 9000:9000
    environment:
      SENTRY_SECRET_KEY: '!!!SECRET!!!'
      SENTRY_POSTGRES_HOST: postgres
      SENTRY_DB_USER: sentry
      SENTRY_DB_PASSWORD: sentry
      SENTRY_REDIS_HOST: redis

  cron:
    image: sentry
    links:
     - redis
     - postgres
    command: "sentry run cron"
    environment:
      SENTRY_SECRET_KEY: '!!!SECRET!!!'
      SENTRY_POSTGRES_HOST: postgres
      SENTRY_DB_USER: sentry
      SENTRY_DB_PASSWORD: sentry
      SENTRY_REDIS_HOST: redis

  worker:
    image: sentry
    links:
     - redis
     - postgres
    command: "sentry run worker"
    environment:
      SENTRY_SECRET_KEY: '!!!SECRET!!!'
      SENTRY_POSTGRES_HOST: postgres
      SENTRY_DB_USER: sentry
      SENTRY_DB_PASSWORD: sentry
      SENTRY_REDIS_HOST: redis
```

initial sentry and create admin account : 

```
docker-compose exec sentry sentry upgrade
docker-compose restart sentry
```

reference link :

[https://gist.github.com/denji/b801f19d95b7d7910982c22bb1478f96](https://gist.github.com/denji/b801f19d95b7d7910982c22bb1478f96)
