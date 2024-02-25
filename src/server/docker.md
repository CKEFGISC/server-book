# Docker

Docker 是一個好東西。

他是一個類似於虛擬機的東東，可以十分輕量化的建立一個服務，同時將應用程式安裝在不會影響到原先伺服器的設定。

教學文章：

- <https://ithelp.ithome.com.tw/articles/10199339>
- <https://larrylu.blog/step-by-step-dockerize-your-app-ecd8940696f4>

我在這邊簡單講一下下我對於 Docker 的各種理解

## image

基本上，就是一個公開的東西。
可以把它當作是安裝檔之類的，或是類似於一個 github repo。

常見的 image 有純 ubuntu、ubuntu+nginx 之類的。也有一些人會透過 Dockerfile 建立自己應用程式的 image 並且放在網路上，例如青島 OJ。

基本上我們在建立應用程式時，需要先將一個 image pull 下來。再透過他建立 container。
![](https://hackmd.io/_uploads/r1R5euFDh.png)

- `docker image list` : 檢視電腦裡有哪些 image
  ![](https://hackmd.io/_uploads/HJqrZBtwn.png)

## container

就是一著正在執行的，一個類似虛擬機的東東。

可以透過 `docker ps -a` 指令檢視正在執行的 container。
![](https://hackmd.io/_uploads/rJ21MBYv3.png)

## docker-compose

基本上，我們不太需要自己慢慢的用 docker run 之類的指令來執行 docker，由於我們都是使用別人打包好的應用程式，因此我們可以直接以 docker-compose 的相關指令來將一堆 docker 一次設定好。

教學文章：https://ithelp.ithome.com.tw/articles/10194183

所有 docker-compose 的相關指令與配置，都會記載在專案資料夾的 `docker-compose.yml` 文件當中。以青島 OJ 的文件為例：

```yml=
version: "3"
services:

  oj-redis:
    image: redis:4.0-alpine
    container_name: oj-redis
    restart: always
    volumes:
      - ./data/redis:/data

  oj-postgres:
    image: postgres:10-alpine
    container_name: oj-postgres
    restart: always
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=onlinejudge
      - POSTGRES_USER=onlinejudge
      - POSTGRES_PASSWORD=onlinejudge

  judge-server:
    image: registry.cn-hangzhou.aliyuncs.com/onlinejudge/judge_server
    container_name: judge-server
    restart: always
    read_only: true
    cap_drop:
      - SETPCAP
      - MKNOD
      - NET_BIND_SERVICE
      - SYS_CHROOT
      - SETFCAP
      - FSETID
    tmpfs:
      - /tmp
    volumes:
      - ./data/backend/test_case:/test_case:ro
      - ./data/judge_server/log:/log
      - ./data/judge_server/run:/judger
    environment:
      - SERVICE_URL=http://judge-server:8080
      - BACKEND_URL=http://oj-backend:8000/api/judge_server_heartbeat/
      - TOKEN=CHANGE_THIS
      # - judger_debug=1

  oj-backend:
    image: registry.cn-hangzhou.aliyuncs.com/onlinejudge/oj_backend
    container_name: oj-backend
    restart: always
    depends_on:
      - oj-redis
      - oj-postgres
      - judge-server
    volumes:
      - ./data/backend:/data
    environment:
      - POSTGRES_DB=onlinejudge
      - POSTGRES_USER=onlinejudge
      - POSTGRES_PASSWORD=onlinejudge
      - JUDGE_SERVER_TOKEN=CHANGE_THIS
      - FORCE_HTTPS=1
      # - STATIC_CDN_HOST=cdn.oj.com
    ports:
      - "0.0.0.0:8000:8000"
      - "0.0.0.0:1443:1443"
```

文件中大部分的東西都不用懂。首先我們可以看到的是`services`區段底下的四個大區段：`oj-redis`、`judge-server` 和 `oj-backend`。這分別代表 docker-compose 會啟動的四個容器。再次注意，一個 docker container 基本上就是一台虛擬機。

一般來說你們有可能需要動到的只有下列兩個地方。

1. port 區塊
   - 以第 64 行為例，代表的是要將 host 的 8000 阜映射到 oj-backend 這台虛擬機的 8000 阜
2. volumes
   - 以第 60 行為例，代表的是要將 host 的`./data/backend`資料夾共用到虛擬機的`/data`資料夾。

#### docker-compose 會用到的指令

> 注意，要先 cd 到有 docker-compose.yml 的那個資料夾

```bash
docker-compose up -d
```

執行應用程式，並且 detach (在背景中執行)

```bash
docker-compose up
```

執行應用程式，並且顯示 log 在 console 當中

```bash
docker-compose build
```

建立但不執行（up 時也會先 build）

```bash
docker-compose down
```

停止應用程式

```bash
docker-compose restart
```

重新啟動運用程式。但注意，此指令並不會更新到你對配置文件做的新跟動。如果有修改 yml 檔案之類的，請用先 down 再 up 的方式重啟。

## 其他常用指令

```bash
docker exec -it <CONTAINER-NAME> /bin/bash
```

進入虛擬機的 bash 裡面

```bash
docker ps -a
```

檢查所有容器的狀態
