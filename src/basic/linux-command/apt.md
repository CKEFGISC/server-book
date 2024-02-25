# APT 簡介

APT（Advanced Package Tool）是一種用於在基於 Debian 的 Linux 發行版中管理軟體包的軟體包管理工具。它是一種命令列工具，用於安裝、升級、刪除和管理軟體包，使得軟體包的管理變得簡單和高效。

### 常用指令

- `apt update`: 更新軟體包列表。
- `apt upgrade`: 升級所有可用的軟體包到最新版本。
- `apt install <package_name>`: 安裝指定軟體包。
- `apt remove <package_name>`: 卸載指定軟體包，但保留配置文件。
- `apt purge <package_name>`: 卸載指定軟體包，並刪除其所有相關的配置文件。
- `apt autoremove`: 自動刪除不再被其他軟體包依賴的無用軟體包。
- `apt clean`: 刪除已下載的軟體包文件，以釋放硬碟空間。
