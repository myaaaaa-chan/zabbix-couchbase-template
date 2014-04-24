Zabbix Couchbase template
=========================


Zabbix Couchbase templateはZabbixのCouchbase監視用テンプレートです。

　
##Usage - 使い方

**1.Userparameterの設定**

userparamter_couchbase.confを監視対象のサーバの/etc/zabbix/zabbix/zabbix_agentd.d/に配置する
_※外部コマンドが実行できるようにzabbix_agent.confのEnableRemoteCommandsを1に設定する_  

**2.sudoersの設定**

監視にはcouchbase-cliを使用するので、zabbixユーザがcouchbase-cliを使用できるように設定する  

$ visudo  
```
#Defaults    requiretty

zabbix ALL=(root) NOPASSWD: /opt/couchbase/bin/couchbase-cli
```
  
**3.テンプレートのインポート**

Zabbixにzbx_couchbase_templates.xmlをインポートする
　

**4.マクロを設定する**

監視対象のホストに以下のマクロを設定する

* {$USERNAME} -> 管理用ログインユーザ名
* {$PASSWORD} -> 管理用ログインパスワード
* {$BUCKET} -> 管理対象バケット名
* {$HDD_INDEX} -> 監視対象HDDインデックス(ex. 0)
* {$SSD_INDEX} -> 監視対象SSDインデックス(ex. 0)

　
##Monitoring Items - 監視項目


| 名前  | キー  | 内容 |
|:-----------|:------------|:------------|
| Bucket item count | cb.bucket.items[{$USERNAME},{$PASSWORD},{$BUCKET}] | バケット内のアイテムの数 |
| Bucket name | cb.bucket.name[{$USERNAME},{$PASSWORD},{$BUCKET}] | 監視対象バケット名 |
| Bucket ops | cb.bucket.ops[{$USERNAME},{$PASSWORD},{$BUCKET}] | 監視対象バケットへのOPS |
| Bucket used(percent) | cb.bucket.quotapercent[{$USERNAME},{$PASSWORD},{$BUCKET}] | バケット使用量(%) |
| Bucket size | cb.bucket.size[{$USERNAME},{$PASSWORD},{$BUCKET}] | バケットサイズ |
| Bucket type | cb.bucket.type[{$USERNAME},{$PASSWORD},{$BUCKET}] | バケットタイプ(couchbase or memcache) |
| Cluster status | cb.cluster.membership[{$USERNAME},{$PASSWORD}] | クラスタステータス(active or not) |
| Hdd free | cb.hdd.free[{$USERNAME},{$PASSWORD},{$HDD_INDEX}] | HDD空き容量 |
| Hdd quota total | cb.hdd.quotatotal[{$USERNAME},{$PASSWORD},{$HDD_INDEX}] | HDD Quota |
| Hdd total | cb.hdd.total[{$USERNAME},{$PASSWORD},{$HDD_INDEX}] | HDD容量 |
| Hdd used by data | cb.hdd.usedbydata[{$USERNAME},{$PASSWORD}] | HDD使用量 |
| Ram quota total | cb.ram.quotatotal[{$USERNAME},{$PASSWORD}] | メモリー quota |
| Ram quota used | cb.ram.quotaused[{$USERNAME},{$PASSWORD}] | メモリー quota |
| Ram total | cb.ram.total[{$USERNAME},{$PASSWORD}] | メモリー容量 |
| Ram used | cb.ram.used[{$USERNAME},{$PASSWORD}] | メモリー使用量 |
| Data index path on HDD | cb.strage.hdd.indexpath[{$USERNAME},{$PASSWORD},{$HDD_INDEX}] | データベース格納パス |
| Data index path on HDD | cb.strage.hdd.indexpath[{$USERNAME},{$PASSWORD},{$HDD_INDEX}] | データベースインデックス格納パス |
| Data path on SSD | cb.strage.ssd.datapath[{$USERNAME},{$PASSWORD},{$SSD_INDEX}] | データベース格納パス |
| Data index path on SSD | cb.strage.ssd.indexpath[{$USERNAME},{$PASSWORD},{$SSD_INDEX}] | データベースインデックス格納パス |
| Version of Couchbase running | cb.version[{$USERNAME},{$PASSWORD}] | Couchbaseバージョン |
| Couchbase running status | net.tcp.listen[11210] | Couchbaseステータス(1:OK,2:NG) |

　
##Trigger - トリガー

| 名前  | 深刻度 | 内容 |
|:-----------|:------------|:------------|
| Lack of free bucket quota warning on server {HOST.NAME} | 警告 | バケットの空き容量が30%未満になった場合に警告 |
| Lack of free bucket quota on server {HOST.NAME} | 軽度の障害 | バケットの空き容量が10%未満になった場合に警告 |
| Couchbase status is not healthy | 重度の障害 | ステータスがhelthy以外になったら警告 |
| Couchbase process is not running. | 致命的な障害 | tcp:11210にアクセスできなくなったら警告 |
　
