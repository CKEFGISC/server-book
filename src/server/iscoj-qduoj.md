# (舊) ISCOJ (青島 OJ) 伺服器端使用說明

> 注意：<br>
> 本章節包含過時資訊（ISCOJ 已經由 青島 OJ 轉移到 TIOJ 了）<br>
> 我們後續將儘速更新，抱歉造成困擾

ISCOJ 位在的資料夾：`/OnlineJudgeDeploy/`

一二學長姐架設時，使用的是青島 OJ。他具有好部署、架設簡單的特性。但是，相對來說，他的功能十分薄弱，很多東西都不好用，因此建議你們可以嘗試換一個 OJ。

## 文件

- GitHub: https://github.com/QingdaoU/OnlineJudge
- 官方說明文件：https://opensource.qduoj.com/

## 架設

https://github.com/QingdaoU/OnlineJudgeDeploy
非常簡單，只需要在一台裝有 Docker 的 Ubuntu 上輸入以下指令：

#### 環境準備

```bash=
# 安裝必要的依賴
sudo apt-get update && sudo apt-get install -y vim python3-pip curl git
pip3 install --upgrade pip
pip install docker-compose

## 安裝docker
sudo curl -sSL get.docker.com | sh
```

#### 安裝青島 OJ

```bash=
# 複製專案庫
git clone -b 2.0 https://github.com/QingdaoU/OnlineJudgeDeploy.git
cd OnlineJudgeDeploy

# 啟動OJ
docker-compose up -d
```

## 啟動

```bash=
# cd path/to/Onlinejudgedeploy

# 啟動OJ
docker-compose up -d
```

啟動完之後可以去 `localhost:80` 查看網頁（如果沒有動設定，在 server 上我改成`localhost:8000`。）

### 錯誤訊息

有些時候，你會看到以下此錯誤訊息：
![](https://hackmd.io/_uploads/ry7foFFPh.png)
這代表你有其他應用程式佔用了相同的 port。

如果在 server 上面出現此錯誤訊息，請先試著找找看是什麼東西在佔用，並且通知我處理。我再來告訴你們該怎麼解決此錯誤。

## 安全憑證

位於`/OnlineJudgeDeploy/data/backend/ssl`資料夾當中，公鑰名稱為`server.crt`，私鑰名稱為`server.key`，一定要是檔名相同的實體文件，不能是 symbolic link。請手動將 certbot 產生的安全憑證複製過來。

## 備份

青島 OJ 有內建的備份可以使用，為於 backup 目錄底下的`db_backup.sh`shell 檔可以直接用來備份。

```bash=
cd /OnlineJudgeDeploy/backup
./db_backup.sh
```

以上指令會生成一個黨名包含日期的`sql`檔案，可以將其下載到別的電腦當中備份。

## 復原

可參考官網的復原方式，經實測有用。
https://opensource.qduoj.com/#/onlinejudge/guide/backup

## 其他

有其他問題的話，請找 AaW 學長協助。
