## Deploy on production

---
### CI/CD Service

Build Jenkins + Setup Git Action

- Tutorial: https://www.youtube.com/watch?v=_Qhe1rETqGg&t=291s
- Github Token: https://github.com/settings/tokens/new | https://www.youtube.com/watch?v=AYohbnOqox0
- Setup GitHub Webhooks: https://github.com/{username}/{project}/settings/hooks 
- Setup Jenkins: https://hackmamba.io/blog/2022/04/running-docker-in-a-jenkins-container/
- Setup Telegram: https://www.youtube.com/watch?v=8RdHSkOicok&t=377s
- Setup Dockerhub: https://app.docker.com/settings/personal-access-tokens
- Setup SSH-Agent target server
- Plugin: Blue Ocean, Stage View

```shell
# on dir setup/jenkins
docker image build --tag=jenkins-docker .
docker run -d --restart=always -it -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v /private/var/www/stock/setup/jenkins/jenkins_home:/var/jenkins_home jenkins-docker
```

---
Infisical (ENV Management)

https://app.infisical.com/

---
### Minio

```shell
mkdir ~/minio/data
docker run -d \
   -p 9000:9000 \
   -p 9001:9001 \
   --restart=always
   --name minio \
   -v ~/minio/data:/data \
   -e "MINIO_ROOT_USER=root" \
   -e "MINIO_ROOT_PASSWORD=12345678" \
   quay.io/minio/minio server /data --console-address ":9001"
```

----
### Redis


-----
### MySQL
Setup 4 service (auth, blog, stock, notify)

In directory db
-----
### MongoDB
For chat service

```shell
docker run --name mongodb -p 27017:27017 -d mongodb/mongodb-community-server:latest --replSet dbrs \                                        
  && sleep 5 \
  && docker exec mongodb mongo --eval "rs.initiate({_id: 'dbrs', members: [{ _id: 0, host: 'localhost:27017' }]});"
```
-----
### Kafka

-----
### Grafana


-----
### Redash
```yaml
version: '3'
services:
  redis:
    image: redis:5.0-alpine
    ports:
      - "6379:6379"
    networks:
      - stock
    restart: always

  postgres:
    image: postgres:12-alpine
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: "12345678"
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - stock
    restart: always

  server:
    image: redash/redash:latest
    depends_on:
      - postgres
      - redis
    ports:
      - "5000:5000"
    environment:
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_SECRET_KEY: "8f9e7f1b1245a6d89bb78245b12a0989f3e8a5f8d9675d5c0dbd123467a8e457"
      REDASH_DATABASE_URL: "postgresql://postgres:12345678@postgres/postgres"
      REDASH_COOKIE_SECRET: "70e9b9fbb2e4a2d85f1c56f55d43a6a8db738912345fd9f0871b5e7e1a3bc5e6"
    command: server
    networks:
      - stock
    restart: always

  worker:
    image: redash/redash:latest
    depends_on:
      - server
    environment:
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_SECRET_KEY: "8f9e7f1b1245a6d89bb78245b12a0989f3e8a5f8d9675d5c0dbd123467a8e457"
      REDASH_DATABASE_URL: "postgresql://postgres:12345678@postgres/postgres"
      REDASH_COOKIE_SECRET: "70e9b9fbb2e4a2d85f1c56f55d43a6a8db738912345fd9f0871b5e7e1a3bc5e6"
    command: worker
    networks:
      - stock
    restart: always


volumes:
  postgres-data:

networks:
  stock:
    external: true
    driver: bridge
```
