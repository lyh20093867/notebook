# Docker-getting-started

## 下载getting-started项目

https://github.com/docker/getting-started

mkdir my_path

cd my_path

git clone https://github.com/docker/getting-started.git

## 构建getting-started的镜像

#### 需要进入app目录下

cd my_path/getting-started/app

#### 创建Dockerfile

vim Dockerfile

```shell
 # syntax=docker/dockerfile:1
 FROM node:12-alpine
 RUN apk add --no-cache python g++ make
 WORKDIR /app
 COPY . .
 RUN yarn install --production
 CMD ["node", "src/index.js"]
```

```shell
docker build -t getting-started .
docker images
```

#### 启动一个容器应用

```shell
docker run -dp 3000:3000 getting-started
```

浏览器访问

http://localhost:3000/

