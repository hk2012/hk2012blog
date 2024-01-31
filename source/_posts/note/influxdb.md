---
title: influxdb 使用手册
catagory: 笔记
tag: 总结
abbrlink: 58050
date: 2022-10-15 15:27:53
---

influx -username jshsmad -password G5fhY5lB85J% -port 28086 -database hsm

// 可以将时间转化为 横杠形式的日期
precision rfc3339

select * from malicious_info where  time>now()-1d tz('Asia/Shanghai');

select * from malicious_info limit 10;


influx -username jshsmad -password G5fhY5lB85J% -port 28086 -database hsm -precision rfc3339 -execute "select * from malicious_info where time>now()-1d tz('Asia/Shanghai')" -format csv  >>  hello.csv

maven  gradle node.js  源 -> 

select * from malicious_info where hashppoe='8503' and ppoe='02357890348' and time>now()-7 order by time desc tz('Asia/Shanghai')
select count(*) from malicious_info where hashppoe='8237' and ppoe='07356337160' and time>now()-7d order by time desc tz('Asia/Shanghai')


select sum("txBytes")/1024 as txBytes from flow_push where hashDeviceID='3940' and deviceID=25506910 and time>now()-1d group by time(20m) fill(0) tz('Asia/Shanghai')

select * from flow_push where hashDeviceID='3940' and deviceID=25506910 and time>now()-7d group by time(20m) fill(0) tz('Asia/Shanghai')

select * from malicious_info where hashppoe='8237' and ppoe='07356337160' and time>now()-7d order by time desc tz('Asia/Shanghai')

SELECT count(*) FROM USER_TRAILRECORD where time>now()-1d tz('Asia/Shanghai')



select count(*) from malicious_info where  time>now()-7d tz('Asia/Shanghai')
select * from malicious_info where  time>now()-7d tz('Asia/Shanghai')

select count(*) from camera_abnormal where   time>now()-7d tz('Asia/Shanghai')


SELECT count(*) FROM "flow_alarm" WHERE time > '2021-09-13T00:00:00Z' and time < '2021-09-19T23:59:59.999999999Z'
select count(*) from malicious_info where time > '2021-09-13T00:00:00Z' and time < '2021-09-19T23:59:59.999999999Z' tz('Asia/Shanghai')

