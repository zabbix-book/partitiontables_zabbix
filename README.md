# partitiontables_zabbix
This is a scripts for partitiontables of MySQL for zabbix2.2 3.0 3.2 3.4 4.0,test it on Zabbix 3.0 4.0 of MySQL 5.6 and MySQL 5.7  
本项目是《Zabbix企业级分布式监控系统》第2版中第3章节中的代码，如需使用，请注明出处即可，遵循Apache 2.0开源协议     

# 1. configration 
Before run this scripts,maybe you should modify it
```
# MySQL connect information
ZABBIX_USER="zabbix"
ZABBIX_PWD="zabbix"
ZABBIX_DB="zabbix"
ZABBIX_PORT="3306"
ZABBIX_HOST="127.0.0.1"
MYSQL_BIN="mysql"

# How days you will keep history days,default is 30
HISTORY_DAYS=30

# How months you will keep trend days,default is 12
TREND_MONTHS=12
```
# 2. run it
```
shell# bash partitiontables_zabbix.sh 
table history      create partitions p20180716
table history_log  create partitions p20180716
table history_str  create partitions p20180716
table history_text create partitions p20180716
table history_uint create partitions p20180716
table history      create partitions p20180717
table history_log  create partitions p20180717
table history_str  create partitions p20180717
table history_text create partitions p20180717
table history_uint create partitions p20180717
......
table trends       create partitions p201807
table trends_uint  create partitions p201807
table trends       create partitions p201808
table trends_uint  create partitions p201808
```
# 3. check partitions
```
shell# mysql -uzabbix -pzabbix zabbix
MariaDB [zabbix]> show create table history\G;
*************************** 1. row ***************************
Table: history
Create Table: CREATE TABLE `history` (
  `itemid` bigint(20) unsigned NOT NULL,
  `clock` int(11) NOT NULL DEFAULT '0',
  `value` double(16,4) NOT NULL DEFAULT '0.0000',
  `ns` int(11) NOT NULL DEFAULT '0',
  KEY `history_1` (`itemid`,`clock`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
/*!50100 PARTITION BY RANGE ( clock)
(PARTITION p20180716 VALUES LESS THAN (1531756799) ENGINE = InnoDB,
PARTITION p20180717 VALUES LESS THAN (1531843199) ENGINE = InnoDB,
PARTITION p20180718 VALUES LESS THAN (1531929599) ENGINE = InnoDB,
PARTITION p20180719 VALUES LESS THAN (1532015999) ENGINE = InnoDB,
PARTITION p20180720 VALUES LESS THAN (1532102399) ENGINE = InnoDB,
PARTITION p20180721 VALUES LESS THAN (1532188799) ENGINE = InnoDB,
PARTITION p20180722 VALUES LESS THAN (1532275199) ENGINE = InnoDB,
PARTITION p20180723 VALUES LESS THAN (1532361599) ENGINE = InnoDB) */
1 row in set (0.00 sec)
ERROR: No query specified
MariaDB [zabbix]>
```
if you see this,everything is OK.

# 4. set crontab
the crontab remove old partitions every day
```
shell# crontab -e
1 0 * * * /usr/sbin/partitiontables_zabbix.sh
Shell# chmod 700 /usr/sbin/partitiontables_zabbix.sh
```
# truncate table(only with delete data)
if you database is so big,you should clean data first,then run this scripts
```
mysql> use zabbix; 
mysql> truncate table history; 
mysql> optimize table history; 
mysql> truncate table history_str; 
mysql> optimize table history_str; 
mysql> truncate table history_uint; 
mysql> optimize table history_uint; 
mysql> truncate table trends; 
mysql> optimize table trends; 
mysql> truncate table trends_uint; 
mysql> optimize table trends_uint; 
```
