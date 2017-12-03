# はじめに
本ドキュメントは、Windows エンジニアが、Linux 上で動作する SQL Server (SQL Server on Linux) を操作するために必要となるスキルアップデートについての情報を記載したものとなります。  
(ドキュメント作成者が Ubuntu 16.04 LTS で検証をしているため、公開当初は Ubuntu ベースの情報となっています)

SQL Server on Linux の SQL Server 部分のスキルについては、Windows / Linux 版で共通となりますが、OS 部分に関しては、Windows エンジニアのスキルアップデートが必要になるものが多数あります。  

本ドキュメントが、今まで Windows をメインに触ってきたが、Linux の SQL Server を触る必要がある / 興味を持ったエンジニアの方の一助になれば幸いです。


# サービス管理
Windows の場合、SQL Server は「Windows サービス」として管理が行われていましたが、Linux 版の場合は、Linux のシステム・サービスマネージャーである「[systemd](https://wiki.archlinux.jp/index.php/Systemd)」で管理が行われている。

本章では、Linux の SQL Server のサービス管理を実施するために必要となるコマンド等を記載している。

## サービス管理のファイル
SQL Server のサービス管理のファイルの実体は「/lib/systemd/system/mssql-server.service」となり、このファイルでサービス起動時の設定が行われる。  
- [9.6. システムのユニットファイルの作成および変更](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-managing_services_with_systemd-unit_files)

``
sudo systemctl enable mssql-server.service
``

を実行することで、「/etc/systemd/system/multi-user.target.wants/mssql-server.service」にシンボリックリンクが作成され、自動起動の設定が行われている。

## SQL Server の設定変更
SQL Server on Windows では、SSMS / SQL Server 構成マネージャー / sp_configure を使用して設定の変更を行う。  

SQL Server on Linux では、これらに加えて、一部の設定については「mssql-conf」を使用して設定を変更する。  
- [Configure SQL Server on Linux with the mssql-conf tool](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-mssql-conf)  

「mssql-conf」で変更可能な設定については、他の方法で変更が可能だったとしても、このツールから変更を行わないと、SQL Server のサービスを再起動することで、初期化されてしまうので注意が必要である。  
(SQL Server on Linux の基本部分はサンドボックス環境で動作しており、サービスの再起動を実施すると、初期状態に初期化されるた、ツールを使用して、起動時に設定を外部からインポートさせる必要がある)

## ファイアウォール
Windows の場合、Windows Firewall で SQL Server のポート (TCP:1433 (SQL Server) / UDP : 1434 (SQL Server Browser) のアクセス開放を実施している。  
Linux の場合、使用するディストリビューションに応じたファイアウォールにより、TCP 1433 (デフォルトインストール時) にアクセスするための設定を行う。

### Ubuntu
Ubuntu 16.04 LTS はデフォルトは FW は無効

|コマンド|ufw|

```
ufw allow 1433/tcp 
```

[ufwの基本操作](https://qiita.com/RyoMa_0923/items/681f86196997bea236f0)

### RHEL

|コマンド|firewalld|

[4.5. ファイアウォールの使用](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls)


## リモート接続
Windows の場合、「Remote Desktop」や、「クリップボード共有経由」等で管理やファイル転送を実施することが多い。  
Linux の場合、「SSH」「SCP」を使用して、リモート管理やファイル転送を実施する。

### リモート管理

|コマンド|ssh|

[インフラエンジニアじゃなくても押さえておきたいSSHの基礎知識](https://qiita.com/tag1216/items/5d06bad7468f731f590e)

### ファイルコピー

|コマンド|scp|

[SCP (1)](http://euske.github.io/openssh-jman/scp.html)

## ソフトウェア更新

```
# sudo apt update
# sudo apr upgrade
```

apt updateでパッケージ管理のデータベースを更新し、apt upgradeで実際にソフトウェアを更新する。


# ベストプラクティス
// TODO

# アカウント / グループ
// TODO

# パフォーマンスモニタリング
// TODO

# プロセス構成
// TODO

# ディレクトリ構成
// TODO

# データディスクのマウント
// TODO

# システムデータベースの操作
// TODO

# 可用性
Windows の場合は、OS に含まれている Windows Server Failover Cluser (WSFC) を使用して、OS 側の可用性環境の構築を行い、その上で SQL Server の可用性環境を構築することがある。

Linux 環境では WSFC を利用することはできないため、OS の可用性環境を構築する手法として、2017/12 時点では OSS のソフトウェアである「Pacemaker」を利用した方法が、Microsoft 社からドキュメントで公開されている。  
(今後、他のクラスターマネージャー/3rd 製品での対応等が行われる可能性もある)

Pacemaker の操作方法については、次の情報が参考となる。

- [High Availability Add-On リファレンス](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/)
- [第1章 Pacemaker を使用した Red Hat High Availability クラスターの作成](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/ch-startup-haaa)
- [第3章 pcs コマンドラインインターフェース](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/ch-pcscommand-haar)
- [付録B pcs コマンドの使用例](https://access.redhat.com/documentation/ja-JP/Red_Hat_Enterprise_Linux/6/html/Configuring_the_Red_Hat_High_Availability_Add-On_with_Pacemaker/ap-configfile-HAAR.html)
	

# ログファイル

// TODO

# オフラインインストール

// TODOs

# コマンド

// TODO