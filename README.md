# 建北電資網管傳承文檔
> <https:/serverbook.ckefgisc.org/>


## 簡介
這一篇筆記，是 AaW 為了傳承所有一三曾經管理的網路相關服務所寫的（類）技術文件。

在本書當中，我們主要針對社網、ISCOJ 伺服器、以及其他相關我們所做的事情做傳承。不過，由於也是我們第一次撰寫如此巨大的傳承文件，因此難免有一些缺漏或不清楚之處。學弟妹如有需要可以向我詢問或是協助我們補齊不清楚之處。

這是一個使用 mdBook 建立的文件專案。所有原始碼和頁面（除了 AaW 留給學弟妹的話）都開放你們在 Github 上進行編輯，希望此文件可以一路傳承下去。

## 使用說明
### Prerequirement
要構建 mdbook 執行文件，您首先需要安裝 Rust 和 Cargo。請按照 [Rust 安裝頁面](https://www.rust-lang.org/tools/install) 上的說明進行操作。目前，mdBook 至少需要 Rust 版本 1.71。

### 安裝 mdBook
如果您尚未安裝 mdBook，可以使用以下命令進行安裝：

```bash
$ cargo install mdbook
```

### 建置文件

在專案根目錄執行以下命令以建置文件：

```bash
$ mdbook build
```

### 檢視文件

建置完成後，您可以使用以下命令檢視文件：

```bash
$ mdbook serve
```

然後在瀏覽器中打開 `http://localhost:3000`。

## 貢獻

如果您想為修改本書或是這個專案做出貢獻，歡迎直接發 Issues 或 Pull Requests。
關於 mdBook 的編寫方式，請閱讀[官方說明文件](https://rust-lang.github.io/mdBook/index.html)

## 授權條款

這個專案基於 MIT 授權條款。詳細信息請參閱 [LICENSE](LICENSE) 文件。
