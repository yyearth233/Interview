# Sqoop

将关系型数据库中数据与 HDFS 导入导出的工具。

可以将关系型数据库导入 HDFS，也可以从 HDFS 导入到关系型数据库。

导入到 HDFS
```shell
./sqoop import –connect
jdbc:mysql://10.28.168.109:3306/compression –username=hadoop
–password=1234 –table HADOOP_USER_INFO -m 1 –target-dir
/user/test
```




