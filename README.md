1. 建立 `aws_rds_cluster_parameter_group` `aws_db_parameter_group` 讓新的 MySQL 8.0 DB instance 使用

2. 更改 MySQL 5.7 的 `aws_rds_cluster_parameter_group` 的 `binlog_format` = `MIXED`。要重啟 DB instance 才會生效

2.1 如果不想影響服務，要增加一台 reader ，讓 reader 替代 writer。
![image](rds_add_reader1.jpg)
![image](rds_add_reader2.jpg)
![image](rds_add_reader3.jpg)

3. 升級 原本的 DB instance spec (t3.micro > t3.medium)

4. 刪掉 RDS Proxy 或指到另一個 Database
![image](rds_bluegreen3.jpg)
![image](rds_bluegreen4.jpg)
![image](rds_bluegreen5.jpg)

5. 備份 MySQL 5.7

6. 建立 Blue Green Deployment
![image](rds_bluegreen1.jpg)
![image](rds_bluegreen2.jpg)

7. 使用這兩個 endpoint 測試服務試否正常
   MySQL 5.7 : dev-0.csmzgrqnkzxn.ap-northeast-1.rds.amazonaws.com
   MySQL 8.0 : dev-0-green-0n68ec.csmzgrqnkzxn.ap-northeast-1.rds.amazonaws.com
![image](rds_bluegreen6.jpg)

8. 正式切換
![image](rds_bluegreen7.jpg)
![image](rds_bluegreen8.jpg)
![image](rds_bluegreen9.jpg)
![image](rds_bluegreen10.jpg)

9. 刪除 MySQL 5.7
![image](rds_bluegreen11.jpg)

10. 刪除 Blue/Green Deployment
![image](rds_bluegreen12.jpg)
![image](rds_bluegreen13.jpg)

11. 刪除 MySQL 5.7 Cluster
![image](rds_bluegreen14.jpg)
![image](rds_bluegreen15.jpg)
![image](rds_bluegreen16.jpg)
![image](rds_bluegreen17.jpg)

11. RDS proxy 改回原本的 Database
![image](rds_bluegreen18.jpg)

12. 更改 `aws_rds_cluster` 設定
    `db_parameter_group_name` : `dev-db-parameter-group-8`
    `engine_version` : `8.0.mysql_aurora.3.05.1`

13. 更改 `aws_rds_cluster_instance` 設定
    `db_parameter_group_name` : `dev-db-parameter-group-8`
    `engine_version` : `8.0.mysql_aurora.3.05.1`
    `instance_class` : `db.t3.medium`
