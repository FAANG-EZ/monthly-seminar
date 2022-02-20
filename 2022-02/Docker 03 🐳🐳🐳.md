---
title: Docker 03 🐳🐳🐳
tags: Monthly-Seminar
slideOptions:
  theme: night
  transition: 'fade'
---

## Docker 03 🐳🐳🐳
#### Some practices for using docker

---

### 01. Where should we FROM in dockerfile?

----

```dockerfile=
FROM ubuntu

RUN apt-get install -y python

...
```

```dockerfile=
FROM python

WORKDIR /usr/src/app

...
```

```dockerfile=
FROM python:latest

WORKDIR /usr/src/app

...
```

```dockerfile=
FROM python:3.10.2

WORKDIR /usr/src/app

...
```

---

### 02. Optimize docker image

----

### 02-1. Choose docker image tag

----

![](https://i.imgur.com/gSQpLit.png)

----

```
buster   -> Debian 10
bullseye -> Debian 11
```

----

### [alpine](https://www.alpinelinux.org/)

----

![](https://i.imgur.com/dJmPmIs.png)


----

### 02-2. Caching image layers

----

```dockerfile=
FROM node:14.16.0-alpine
ENV NODE_ENV production
WORKDIR /usr/src/app
COPY ${PARENT_DIR} /usr/src/app
RUN npm install
```

----

```dockerfile=
FROM node:14.16.0-alpine        (cached)
ENV NODE_ENV production         (cached)
WORKDIR /usr/src/app            (cached)
COPY ${PARENT_DIR} /usr/src/app (missing)
RUN npm install                 (missing)
```

----

```dockerfile=
FROM node:14.16.0-alpine
ENV NODE_ENV production
WORKDIR /usr/src/app
COPY ${PARENT_DIR}/package.json /usr/src/app/
RUN npm install
COPY ${PARENT_DIR} /usr/src/app
```

----

```dockerfile=
FROM node:14.16.0-alpine                      (cached)
ENV NODE_ENV production                       (cached)
WORKDIR /usr/src/app                          (cached)
COPY ${PARENT_DIR}/package.json /usr/src/app/ (cached)
RUN npm install                               (cached)
COPY ${PARENT_DIR} /usr/src/app               (missing)
```

----

#### 02-3. Multi-Stage Builds

----

```dockerfile=
FROM golang:1.16.3-alpine
ENV GO111MODULE="on"
ENV CGO_ENABLED=0
WORKDIR /app
COPY . /app
RUN go build -o app
```

----

![](https://i.imgur.com/RwGBSL4.png)

----

```dockerfile=
# build stage
FROM golang:1.16.3-alpine as builder
ENV GO111MODULE="on"
ENV CGO_ENABLED=0
WORKDIR /app
COPY . /app
RUN go build -o app

# final stage
FROM alpine
WORKDIR /app
COPY --from=builder /app /app
```

----

![](https://i.imgur.com/wBWS1J3.png)

---

### 03. Don't forget .dockerignore

----

```dockerfile=
.git

vendor
node_modules

api-docs

storage/logs/laravel.log

.env
```

---

### 04. Use the Least Privileged User 

----

```dockerfile=
...

RUN adduser -D worker
USER worker

...

COPY --chown=worker:worker requirements.txt requirements.txt
RUN pip install --user -r requirements.txt

...

COPY --chown=worker:worker . .
```

---

### 05. Use docker scan

----

WTF...
![](https://i.imgur.com/FWVWWF9.png)

