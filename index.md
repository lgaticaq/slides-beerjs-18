title: Beerjs #18
author:
  name: Leonardo Gatica
  twitter: lgaticaq
  url: https://about.me/lgatica
  email: lgatica@protonmail.com
output: index.html
theme: juanbrujo/cleaver-beerjs
style: style.css
controls: true

--

# Docker en producción y algunos tips

--

# Rancher para maneajr tu granja de contenedores

```shell
docker run -d --restart=always --name rancher-server -p 80:8080 rancher/server
```

--

![rancher](img/rancher.png "rancher")

--

# HAProxy para exponer varios contenedores en el puerto 80

```shell
docker run -d -e VIRTUAL_HOST=demo1.example.com --name demo1 my-image1
docker run -d -e VIRTUAL_HOST=demo2.example.com --name demo2 my-image2
docker run -d --link demo1:demo1 --link demo2:demo2 -p 80:80 dockercloud/haproxy:1.5.3
```

--

# Volumenes para no perder nada

```bash
docker volume create --name redis
docker run -d --name redis -v redis:/data redis:alpine
```

```bash
docker run -d --name redis -v /opt/redis-data:/data redis:alpine
```

--

# Respalda tu base de datos

```bash
docker run -d --name mongodump -e "MONGO_URI=mongodb://user:pass@host:port/dbname" \
  -e "AWS_ACCESS_KEY_ID=your_aws_access_key" -e "AWS_SECRET_ACCESS_KEY=your_aws_secret_access_key" \
  -e "AWS_DEFAULT_REGION=us-west-1" -e "S3_BUCKET=your_aws_bucket" -e "BACKUP_CRON_SCHEDULE=0 2 * * *" \
  lgatica/mongodump-s3
```

```bash
docker run -d --name mongorestore -v /tmp/backup:/backup -e "BACKUP_NAME=2016-09-20_06-00-00_UTC.gz" \
  -e "MONGO_URI=mongodb://user:pass@host:port/dbname" -e "AWS_ACCESS_KEY_ID=your_aws_access_key" \
  -e "AWS_SECRET_ACCESS_KEY=your_aws_secret_access_key" -e "AWS_DEFAULT_REGION=us-west-1" \
  -e "S3_BUCKET=your_aws_bucket" lgatica/mongorestore-s3
```

--

# Pon a dieta tus imagenes con Alpine linux

```
REPOSITORY        TAG     IMAGE ID      CREATED       VIRTUAL SIZE
kylemanna/openvpn alpine  01f29d545370  7 minutes ago 12.4 MB
kylemanna/openvpn latest  0e27d3a8ec58  3 hours ago   150.5 MB
kylemanna/openvpn 1.0     0bda1c2e0f07  11 weeks ago  218.4 MB
```

```
FROM alpine:edge

RUN echo http://dl-4.alpinelinux.org/alpine/edge/testing >> /etc/apk/repositories && \
  apk add --no-cache mongodb-tools py2-pip && \
  pip install pymongo awscli && \
  mkdir /backup
```

--

# P.D. Contribuye con imagenes en Docker Hub

```
docker login
docker build -t my-awesome-image:0.0.1 .
docker push my-awesome-image:0.0.1
```
