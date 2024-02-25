# Github Organization

為了維護社團的所有程式碼，我建立了一個 CKEFGISC 的 Github 組織帳號。

> 網址：https://github.com/CKEFGISC
> 管理員：會交給學術長以及網管

這個地方主要提供給學術放置上課內容，以及網管如果開發新的專案可以放在這邊，也可以用來架設社網或相關網站。
目前有的內容：

- `CKEFGISC.github.io` ：就是社網的專案庫
- `cms` : CMS 上機考評測系統的原始碼，是從 https://github.com/erichung09060/cms fork 到吳亞倫的 github 帳號再 fork 過來的。目前為於伺服器上面的 cms 系統是接著這個 repo。詳情請見[CMS 比賽平台管理與使用](/SngEqSHATySgeTG8j312Gg)
- `cms-ranking-archiver`：用來把 cms 上面的計分板內容抓下來變成靜態網頁。使用方式請見[CMS 比賽平台管理與使用]()
- `CKEFGISC-Linked-List`：之前專案小社的內容
- `.github`：好像可以拿來寫組織的介紹之類的
- `translation`：從 IOI 抓來的東東，還不知道有什麼用

基本上，所有 github 的設定你們都可以亂動，if 你知道自己在做什麼。只要注意不要不小心把東西刪掉就好。

## Git

這邊我想要再花一小段篇幅講一下 git 的使用。

首先，如果你還不會用 git，我建議你一定要花時間把他學會，尤其網管對於 git 必須要十分熟悉。如果你覺得你對於指令式介面不熟，可以使用 github desktop 等等圖形化介面沒關係。

而最基本的，你要能夠理解 add、commit、push、fetch、pull 這些操作，並且能夠理解 branch、fork、merge 和 pull request 是什麼。

常用的指令紀錄在[2.4. Git 指令](linux-command/git-command.md)

可使用的資源：

- <https://ithelp.ithome.com.tw/articles/10193391>
- <https://github.com/twtrubiks/Git-Tutorials>
- <https://b0444135.medium.com/github-教學適用於小型協作專案-cd471733104a>
- <https://slides.com/aaw/github_pages/>

另外，在利用 github 進行共用協作時，可以遵守以下的規則：

1. 盡量保持 main branch 的穩定性：
   - 這代表你應該只有確認沒有問題的程式碼才能 merge 到 main 裡面
2. 以個人帳號 fork 出來之後，再 merge 回組織帳號
3. commit message 寫清楚，並且詳細寫下這筆 commit 包含哪一些更新
