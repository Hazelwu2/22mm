---
title: Docker Network
date: 2019-12-08 20:51:12
tags:
- Docker
---
Docker Network 筆記及練習


## Docker Network 指令

### Container Port 與協議

```
docker port [container_ID]
```

### Docker Container IP
顯示Container IP
```
docker inspect [container_ID]
```

### 顯示所有 networks
```
docker network ls
``` 

### 篩選 network
篩選bridge的network
```
docker network ls -f driver=bridge
```
自訂格式 ID: Driver方式顯示
```
docker network ls --format "{{.ID}} : {{.Driver}}"
```
打這個指令結果應該會像下面這樣
```
ec113447cb40 : bridge
226c9faec426 : host
27315156c58a : null
1395d970d6de : bridge
2cad3a8f93b4 : bridge
```


### 建立 network
建立 network 在 host 上，會建立預設的 network bridge
```
docker network create
```

建立 bridge network
```
docker network create -d bridge my-bridge-network
```
檢查是否建立成功

```
docker network ls
```
你應該會看到多出一行 network 是 my-bridge-network

將network和container 連在一起
```
docker network connect network1 [container1]
```

[container1]可以輸入container的name或ID
連線成功後，[container1]即可和其他用my-bridge-network的容器溝通
### 建立容器並連到network
範例
```
docker network create -d bridge mynetwork
docker network ls
docker run -d --name my_nginx --network mynetwork nginx
docker network inspect mynetwork
```
檢查JSON有個container物件，有出現`"Name": "my_nginx"`，表示加入成功
![Docker my_ngix](../../../images/docker-network.jpg)

開新的容器叫cool，並連到mynetwork
```
docker run -d --name cool --network mynetwork nginx
docker network connect mynetwork cool
docker network inspect mynetwork
docker container inspect cool
```

### 中斷連線
cool Container與 mynetwork中斷連線
```
docker network disconnect mynetwork cool
```