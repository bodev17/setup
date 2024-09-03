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

### Redis