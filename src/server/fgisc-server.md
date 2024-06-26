# 北資的伺服器

> 注意：<br>
> 本章節包含過時資訊（ISCOJ 已經由 青島 OJ 轉移到 TIOJ 了）<br>
> 我們後續將儘速更新，抱歉造成困擾

> 伺服器的主管人為北一女**何雪溱老師**，如果針對伺服器有任何疑慮可以向老師詢問。<br>
> 另外，如果希望針對裡面內容進行大幅度修改，請**務必向雪溱老師報備之後**才可以進行！<br>
> 何雪溱老師聯絡方式：<br>
> email : sjher@gapps.fg.tp.edu.tw

目前，我們是跟北一女中借用了一台伺服器。該伺服器為一台運行在虛擬機上的 Ubuntu Server 20.04 LTS。

![](https://hackmd.io/_uploads/r1SGl9dDn.png)

這台伺服器由於是學長們自己維護的，因此，東西的穩定性無法到非常的完整。所以如果要進行任何操作務必要先在別的地方以虛擬機進行確認之後，再著手進行。

### 現在伺服器有哪些東西

#### 營運中網路服務

以下所有服務皆是使用 docker 架設

1. ISCOJ(已退役)

   - 精確地來說，是名稱為 iscoj 的一個[青島 OJ 系統](https://github.com/QingdaoU/OnlineJudge)。
   - 檔案位置：`/OnlineJudgeDeploy/`
   - 詳細使用請見 [ISCOJ 伺服器端使用說明](/-lloL9MSTautzUUFW-5M2w)

2. CMS 比賽評測系統 (Contest Management System)
   - 分為 前台 (port 8888)、後台 (port 8889)、計分版 (port 8890)三大服務
   - 檔案位置：`~/cms/`、`/cms_docker_postgresql_data/`

3. TIOJ Infor Online Judge
    - 本社目前使用的oj，檔案位置為`~/tioj-new/`，由建中學長開發，各式文檔並不齊全(伺服器端的大部分根本不存在)，請自行熟悉ruby on rails以及sandbox後再進行修改

4. Online Code Editor
    - 不知道為什麼架的，但可以在上面共編東西，檔案位置 `~/vscode`，網址是 https://iscoj.ckefgisc.org/vscode


#### 其他服務

1. nginx
    - 處理各式轉址以及效能平衡
2. certbot
    - 自動更新SSL憑證


基本上，家目錄裡面的東西（除了上述提到的服務），很多都是測試用途，可以嘗試看看
但是，根目錄(`/`)底下的資料夾，==不懂的人沒事不要上去亂動！==

### 網域

- 伺服器 ip 位址：`203.64.52.132`
- 網址：`iscoj.fg.tp.edu.tw`、`fgiscoj.fg.tp.edu.tw`、`iscoj.ckefgisc.org`
  - 如果需要更多網域登入學長的aws後就可以新增A record 指向ip即可

### 使用者名稱

使用者名稱為 fgisc，可透過 sudo 具有 root 權限，請小心使用。
密碼將放置在另外一個檔案教給網管。

### 連線

- 請使用 ssh 指令進行連線
- 指令：`ssh fgisc@203.64.52.132`
- windows 使用者可以去下載 putty
- `scp`指令：將檔案複製上去伺服器/複製回來你的電腦
  - 教學：https://blog.gtwang.org/linux/linux-scp-command-tutorial-examples/

### PORT 端口

> 通訊埠（英語：port），又稱為連接埠、端口、協定埠（protocol port）在電腦網路中是一種經由軟體建立的服務，在一個電腦作業系統中扮演通訊的端點（endpoint）。每個通訊埠都會與主機的 IP 位址及通訊協定關聯。通訊埠以 16 位元數字來表示，這被稱為通訊埠編號（port number）。
> --- 維基百科

簡單來說，在電腦當中，所有的`服務`都會佔用一到多個端口。
例如，網際網路服務(http)預設的端口是`80`，而`https`是`443`、`ssh`是`22`，小佛用的 telnet 是`23`。

在我們的伺服器當中，以下 port 是對外開啟的：

- 22: ssh
- 80: http
- 443: https
- 8888、8889、8890: 測試用

另外可能還有一些別的 port，但我沒用過也不確定。
如果你們希望開啟其他的 port 作為測試用途，例如`8000`，可以請雪臻老師幫忙。

在所有網際網路服務當中，使用到了以下 port：

- 平常啟用
  - 80：對外所有 http
  - 443:對外所有 https
  - 8000：青島 oj 的 http
  - 1443：青島 oj 的 https
  - 8890：CMS 使用者比賽頁面
  - 8891：CMS admin
  - 8892：CMS ranking

### nginx 與 反向代理 (reverse proxy)

因為伺服器當中對外開放的 port 有點少，原本只有 80 和 443。當初我為了要架設 cms，所以特地去學反向代理。

在我們伺服器當中，我用了 nginx 作為反向代理的 server。那 nginx 這個東西蠻強大的，也很複雜，我這裡無法細講太多，有興趣的可以自己再去搜尋。

nginx 配置文件皆位於`/etc/nginx/`資料夾底下。

1. 主配置文件為`/etc/nginx/nginx.conf`
2. ISCOJ 與 CMS 配置文件：
   - `/etc/nginx/conf.d/iscoj.conf`
   - 內容解說位於下一段

#### 我的 nginx 配置文件寫了什麼？

```nginx=
## 上游設定
upstream oj {
	server 0.0.0.0:1443;
	keepalive 16;
}

upstream cms {
	server 0.0.0.0:8888;
	keepalive 16;
    # 以這邊為例
    # 底下配置文件當中寫的 https://cms/ 會代理到 port 8888
}

upstream cms-admin {
	server 0.0.0.0:8889;
	keepalive 8;
}

upstream cms-rank {
	server 0.0.0.0:8890;
	keepalive 8;
}

upstream cmspython {
	server 0.0.0.0:8788;
	keepalive 16;
}

upstream cmspython-admin {
	server 0.0.0.0:8789;
	keepalive 8;
}

upstream cmspython-rank {
	server 0.0.0.0:8790;
	keepalive 8;
}

# Default server configuration
#
server {

	# SSL configuration
	#
	listen 443 ssl default_server;
	listen [::]:443 ssl default_server;

  	# 憑證與金鑰的路徑
    ssl_certificate /etc/letsencrypt/live/iscoj.fg.tp.edu.tw/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/iscoj.fg.tp.edu.tw/privkey.pem; # managed by Certbot

	# Note: You should disable gzip for SSL traffic.
	# See: https://bugs.debian.org/773332
	#
	# Read up on ssl_ciphers to ensure a secure configuration.
	# See: https://bugs.debian.org/765782
	#
	# Self signed certs generated by the ssl-cert package
	# Don't use them in a production server!
	#
	# include snippets/snakeoil.conf;

	root /var/www/html; # 不太重要，因為我們所有東西都是反向代理

	# Add index.php to the list if you are using PHP
	# index index.html index.htm index.nginx-debian.html;

	server_name iscoj.fg.tp.edu.tw fgiscoj.fg.tp.edu.tw;

    ## 反向代理區段
    # iscoj
	location / {
		proxy_pass https://oj/;

		# 把 IP、Protocol 等 header 都一起送給反向代理的 server
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
	}

	# 沒有加斜線的要轉址成有斜線，才能正確的反向代理到subfolder
	location = /cms {
		return 302 /cms/;
	}
    # 反向代理的程式
	location /cms/ {
		proxy_pass http://cms/;
	}

	location = /cms/admin {
		return 302 /cms/admin/;
	}
	location /cms/admin/ {
		proxy_pass http://cms-admin/;
	}

	location = /cms/ranking {
		return 302 /cms/ranking/;
	}
	location /cms/ranking/ {
		proxy_pass http://cms-rank/;
		proxy_buffering off;
	}

	location = /cmspython {
		return 302 /cmspython/;
	}
	location /cmspython/ {
		proxy_pass http://cmspython/;
	}

	location = /cmspython/admin {
		return 302 /cmspython/admin/;
	}
	location /cmspython/admin/ {
		proxy_pass http://cmspython-admin/;
	}
	location = /cmspython/ranking {
		return 302 /cmspython/ranking/;
	}
	location /cmspython/ranking/ {
		proxy_pass http://cmspython-rank/;
		proxy_buffering off;
	}


	#上機考 得題
	location /cms/<ANS> {
		return 302 https://hackmd.io/_uploads/rJMJQzcNn.png;
	}
	location /cms/96 {
		return 302 https://hackmd.io/@QYi6gllvSum0RZrW3jaysg/Sy75nf9Vn;
	}
	location /cms/96/ {
		return 302 https://hackmd.io/@QYi6gllvSum0RZrW3jaysg/Sy75nf9Vn;
	}
	location /cms/<96>/ {
		return 302 https://hackmd.io/@QYi6gllvSum0RZrW3jaysg/Sy75nf9Vn;
	}
}


server {
    if ($host = iscoj.fg.tp.edu.tw) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = fgiscoj.fg.tp.edu.tw) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


	listen 80 default_server;
	listen [::]:80 default_server;

	server_name iscoj.fg.tp.edu.tw fgiscoj.fg.tp.edu.tw;
    return 404; # managed by Certbot

}
```

如果你們希望更動 nginx 的配置文件，可以，但請記得要小心&先備份原本的設定檔。
注意，最好不要更動或刪除到我已經配置完的文件。
更動完設定之後，可以用以下指令讓 nginx 重新啟動：
`systemctl restart nginx`

### systemctl 系統服務

這有點複雜，反正就是如果你寫好一個文件指令交給 systemctl (其實應該說交給 systemd)，他會讓你的程式在電腦背景以“服務”形式在運作。
基本上我額外設定的服務有：

1. nginx
2. certbot 自動更新
3. iscoj 的開機自動啟用，在 iscoj 區段會有詳細介紹

#### systemctl 常用指令：

> 記得大部分都要 sudo，所以請小心使用

- `systemctl start <SERVICE_NAME>.service`
  - 啟動`<SERVICE_NAME>` 這個服務，例如`ststemctl start nginx.service`
- `systemctl stop <SERVICE_NAME>.service`：停止這個服務
- `systemctl restart <SERVICE_NAME>.service`：重啟這個服務
- 其他的自己 google，但理論上你只會用到這幾個

#### 自行設定服務

一般來說，在 systemctl 裡面的服務都是你安裝某個應用程式之後，系統自動建立的。
不過呢，有些時候我們會希望建立自己的服務，例如 iscoj 那個。
這時候你可以參考這幾篇教學文章來進行。

- https://blog.gtwang.org/linux/linux-create-systemd-service-unit-for-python-echo-server-tutorial-examples/
- https://unix.stackexchange.com/questions/236084/how-do-i-create-a-service-for-a-shell-script-so-i-can-start-and-stop-it-like-a-d
- https://medium.com/@benmorel/creating-a-linux-service-with-systemd-611b5c8b91d6

### [ISCOJ]()

### [CMS]()

### SSL 安全認證

所謂的 SSL 安全認證，代表這個網站可以被 CA 認證，擁有 https 加密連線。原本 SSL 的認證文件都是透過雪臻老師協助申請取得，但後來雪臻老師提供了一個自動申請憑證的方式：certbot 機器人。

#### certbot

老師當初給我的教學是 apache 版本的，[文件連結](https://www.dropbox.com/s/2yuf427ay7eybh8/%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%89%E8%A3%9D%20SSL%20%E6%86%91%E8%A8%BC%28Ubuntu%29.txt?dl=0)

我自己目前是看這一篇教學設定的：
https://blog.hellojcc.tw/setup-https-with-letsencrypt-on-nginx/



certbot如果要登記新的網域需重新啟動，此時他會覆蓋掉原本的nginx執行緒，並佔用 80 PORT，即使查看執行緒直接kill仍無法重啟nginx，原因未知，如果可以的話希望你們能找出來，惟重開機可解決。

#### 目前檔案路徑

憑證位於`/etc/letsencrypt/live/iscoj.fg.tp.edu.tw`目錄底下，需有 root 權限才可以瀏覽。
`fullchain.pem`為公鑰，`privkey.pem`是私鑰
