---
title: kafka使用手册
category: 笔记
tag: 总结
abbrlink: 58060
date: 2022-10-16 15:27:53
---

systemctl start kafka
systemctl start zookeeper

zookeeper启动：
cd zookeeper home
例如：
cd /home/user/app/zookeeper/bin
sh zkServer.sh start

1、启动kafka
nohup bin/kafka-server-start.sh config/server.properties &

2、创建topic：
bin/kafka-topics.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 \
 --create --partitions 1 --replication-factor 1 --topic flowpush
测试环境中
bin/kafka-topics.sh --bootstrap-server 192.168.199.83:22390 \
 --create --partitions 1 --replication-factor 1 --topic flowpush

bin/kafka-topics.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 \
 --create --topic goods --partitions 1 --replication-factor 2 
 
bin/kafka-topics.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 \
 --create --topic provider --partitions 1 --replication-factor 2 

bin/kafka-topics.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 \
 --create --topic orders --partitions 10 --replication-factor 2
 
3、删除topic
bin/kafka-topics.sh --delete --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 --topic orders

3、查看topic
bin/kafka-topics.sh --list --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390
测试环境中
bin/kafka-topics.sh --list --bootstrap-server 192.168.199.83:22390

bin/kafka-topics.sh --describe --bootstrap-server 192.168.199.83:22390 --topic orderSec-3

4、发送消息到topic = orders  ------
bin/kafka-console-producer.sh --broker-list 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 --topic orders

5、从头查看topic=orders的消息
bin/kafka-console-consumer.sh --bootstrap-server 192.168.199.83:22390,192.168.199.83:22390,192.168.199.83:22390 --from-beginning --topic test
bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:22390  --from-beginning --topic leakdatas
6、查看topic最近的消息
查看最新：
bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:22390 --topic notes

7、查看每个分区的offset
./bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 192.168.139.199:22390,192.168.139.209:22390,192.168.139.210:22390 --topic test1
./bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list  127.0.0.1:22390 --topic leakdatas


8、分区扩展
bin/kafka-topics.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 --alter --topic orders --partitions 10



配置jks访问：
查看topic
./kafka-topics.sh --list --zookeeper kafka.broker.com:60065


9、移动偏移量
注意：执行此命令前需先断开消费者
bin/kafka-consumer-groups.sh --bootstrap-server 192.168.199.83:22390 --group report --reset-offsets --all-topics --to-latest --execute
修改topic所有分区偏移量为指定偏移量：
bin/kafka-consumer-groups.sh --bootstrap-server 192.168.199.83:22390 --group report --topic reportData --reset-offsets --to-offset 10 --execute
修改topic指定分区的偏移量：
bin/kafka-consumer-groups.sh --bootstrap-server 192.168.199.83:22390 --group report --topic reportData:0 --reset-offsets --to-offset 15 --execute


10、查看group描述

删除group记录
./kafka-consumer-groups.sh --bootstrap-server kafka.broker.com:60065 --delete --group export

bin/kafka-consumer-groups.sh --bootstrap-server 192.168.199.95:22390,192.168.199.81:22390,192.168.199.120:22390 --group leakinfo --describe

11、查看topic描述
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic lx_test_topic --describe
bin/kafka-topics.sh --bootstrap-server 192.168.199.95:22390 --describe --topic reportData
bin/kafka-topics.sh --bootstrap-server 127.0.0.1:22390 --describe --topic reportData


12、修改日志保留时间
2.3版本：
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name smsMessage --alter --add-config retention.ms=864000000    //10天
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name quickRecognise --alter --add-config retention.ms=259200000   //3天   
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name leakdatas --alter --add-config retention.ms=259200000
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name leftDevices --alter --add-config retention.ms=259200000
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name reinstall --alter --add-config retention.ms=259200000                                                                                                                                                                              
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name flowpush --alter --add-config retention.ms=604800000     
2.5版本：
bin/kafka-configs.sh --bootstrap-server localhost:9092 --entity-type topics --entity-name my-topic --alter --add-config retention.ms=864000000

13、导出topic所有信息到文件
nohup bin/kafka-console-consumer.sh --bootstrap-server 172.17.1.8:22390,172.17.1.9:22390,172.17.1.10:22390 --from-beginning --topic testlog >> test.out &
nohup bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:22390 --from-beginning --topic leakdatas >> test.out &

14、查看topic配置参数
bin/kafka-configs.sh --zookeeper 192.168.199.95:22392,192.168.199.81:22392,192.168.199.120:22392 --entity-type topics --entity-name flowpush --describe

bin/kafka-console-consumer.sh --bootstrap-server 192.168.199.83:22390 --topic leak_plugin12 --from-beginning


4、发送消息到topic = leak_plugin12  
bin/kafka-console-producer.sh --broker-list 192.168.199.83:22390 --topic leak_plugin12



3、查看topic
bin/kafka-topics.sh --list --bootstrap-server 192.168.199.95:22390,192.168.199.81:22390,192.168.199.120:22390

