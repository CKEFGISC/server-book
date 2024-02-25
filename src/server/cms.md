# CMS 比賽平台管理與使用

## 文件

官方文件：<https://cms.readthedocs.io/en/>
一份寫得不錯的文件（我是根據他裝的）：<https://hackmd.io/@erichung0906/cms>

## 架設

我是根據這一篇文章，使用 docker 架設的：
https://hackmd.io/@erichung0906/cms
安裝完成之後，會預設導入一場比賽以及測試用使用者。

cms 一樣是使用 docker 架設。不過，由於是私人寫的 docker-compose 檔案，所以
`docker-compose.yml` 不規則很多。

在底下，有兩個 docker-compose 文件。`docker-compose.yml`會安裝、啟動 cms 系統並且啟動一場比賽以及計分板。而`docker-compose.empty.yml`則不會啟動比賽，只會啟動後台以及計分板。
![](https://hackmd.io/_uploads/rJuQPacv2.png)

其中，最重要的是第 41 行的地方，其中的指令為`cmsResourceService -a <CONTEST_ID>`。其中，`<CONTEST_ID>`要置換成 CMS 要啟動的比賽的 ID。例如，在截圖當中，可以看到 `<CONTEST_ID>` 為 `3`，而這代表的是 2023 上機考的 ID。`<CONTEST_ID>` 可以透過 admin 頁面的網址看到。
![](https://hackmd.io/_uploads/rkfd0lTOh.png)

而如果要用`docker-compose.empty.yml`啟動的話，則要使用以下指令：

```
docker-compose -f docker-compose.empty.yml up -d
```

之後，再使用以下指令啟動指定比賽：

```
docker exec cms cmsResourceService -a <CONTEST_ID>
```

## 比賽控制與管理

### 終端機操作（CLI）

CMS 有許多東西必須在終端機以指令方式操作。

#### 啟動 / 切換比賽

```
vim docker-compose.yml
```

將第 41 行的`cmsResourceService -a <CONTEST_ID>` 中 `<CONTEST_ID>` 置換成對的 ID。

```
docker-compose up -d
```

##### 單純啟動比賽

```
docker exec cms cmsResourceService -a <CONTEST_ID>
```

#### 建立使用者

如果要大量建立使用者，可以將`CreateByAaW` 資料夾當中的 `cms_user.csv.example`複製一份到`cms_user.csv`並且修改，然後再用`createUserFromCsv.sh`建立。

##### 指令：

```
cd ~/cms/CreateByAaW
./createUserFromCsv.sh
```

#### 將使用者加到比賽中

以下指令可以將`cms_user.csv` 中的所有使用者加到特定比賽當中：

##### 1. 修改 `addUserToContestCsv.sh`

```
cd ~/cms/CreateByAaW
vim addUserToContestCsv.sh
```

將第 8 行 `-c` 後面的數字改成比賽 ID，然後`:q`退出`vim`，執行指令：

```
./addUserToContestCsv.sh
```

#### 刪除個人計分板

進到 cms 裡面

```
docker exec -it cms bash
```

如要刪除使用者名稱為`userA`的計分板：

```
cmsRWSHelper delete user userA
```

#### 其他指令

請閱讀官方文件：https://cms.readthedocs.io/

### 後台操作（GUI）

#### problem

![](https://hackmd.io/_uploads/S1qCrdYwn.png)
按 create new task
![](https://hackmd.io/_uploads/r1nmLuYDn.png)
下面是題目簡稱、中文全名
statements 放題目敘述的 pdf（正常比賽的樣子，記得要先做好 LaTeX 模板）

![](https://hackmd.io/_uploads/HkyRvuYD3.png)
聯集給分

![](https://hackmd.io/_uploads/r1RbddYPn.png)
group min for 子題

```
[[第一組分數, 子題測資數量], [第二組分數,  子題測資數量]]
```

<br>

![](https://hackmd.io/_uploads/Sk1dO_twn.png)
上傳.zip 裡面只有編號規律的檔案（可以透過 input file names template 那邊更改你的檔案名稱格式）

##### 互動題

基本上有多種不同的，可以參考[這一篇](/cA70QEPVRROxvTxYujMTcw)

基本上互動題要準備兩個檔案：header 和 grader.cpp
並且在這裡上傳：
![](https://hackmd.io/_uploads/ryt_H-pOn.png)

這邊上傳的 header 使用者都可以 include 的到，另外，grader.cpp 會和使用者上傳的程式一起編譯。

而 checker 則為一個編譯過的執行黨，用來進行連續給分之類的，可以自行設計。

#### user

cms 的使用者從後台建立出來之後，
![](https://hackmd.io/_uploads/S1WPkqYw2.png)

修改介面
![](https://hackmd.io/_uploads/SJ5p1cKw3.png)
這邊可以將使用者加入比賽中(participation)

##### participant

要藏起來讓別人試用：hidden
超級使用者（可以再開賽前測機）：unrestricted

#### contest

#### anouncement

## cms-ranking-archiver

將比賽計分板放到社網的工具。
<https://github.com/CKEFGISC/cms-ranking-archiver>
指令：

```bash
./rws-archiver.py --noflags https://iscoj.fg.tp.edu.tw/cms/ranking/
```

產生的網址：

```markdown
https://ckefgisc.org/cms-ranking-archiver/<比賽名稱>
```

## public resources

放在/srv/cms 裡面的

- add user faces(他真的叫這個)
  - /srv/cms/heads
  - 裡面放 user_id.png/jpg 就會顯示了
- add team icon
  - /srv/cms/flags
  - 裡面放 team_code.png/jpg
  - 記得要先在 admin 頁面 create team
  - 然後在比賽中為 user 指定 team
