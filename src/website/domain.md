# 關於網域的那檔事

`ckeisc.org` 跟 `ckefgisc.org` 這兩個網域是學長給我們的，使用 AWS 的 Route 53 管理。

## 如何登入控制台

登入網址是 https://yhchan.signin.aws.amazon.com/console
帳號和密碼另外會給你們
然後要輸入 MFA 驗證碼
初次登入可以跟我們要
但是接下來就要設定用自己的手機取得驗證碼

## 如何將我的手機新增為 MFA 裝置

登入後畫面右上角可以下拉
點「安全憑證」
進去後在 MFA 的地方點「指派 MFA 裝置」
裝置名稱自己取，認得出是誰的手機就好
選「Authenticator 應用程式」
然後你要在手機上裝 Google Authenticator
然後掃它給你的 QR Code
你會看到它給你一個六位數的驗證碼
而且每隔幾秒就會更新
然後就照它要的「填入兩個連續的代碼」就可以了
之後要登入被問驗證碼時
就在手機上打開 Google Authenticator
看數字是多少填進去就好了

## 如何管理網域

登入後點 Route 53
進去點「託管區域」
然後看你要管理哪個網域
要弄 A 紀錄還是 CNAME 幹嘛的
