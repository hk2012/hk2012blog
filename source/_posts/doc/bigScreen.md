---
title: 主动标识数据大屏动态接口配置
category: 文档
tag: 总结
abbrlink: 36943
date: 2022-10-21 15:27:53
---

### 概述
    该文档只针对于 http://172.17.1.39:9000 数据大屏动态接口的数据配置介绍，不介绍特效组成和最终的展示的参数配置。

- 主动标识数据大屏初步预览图
    ![主动数据标识数据大屏预览图](/img/主动数据标识数据大屏预览图.png)

- 动态接口配置步骤图
    ![动态接口配置步骤图](/img/动态接口配置步骤.png)
    ![接口数据处理步骤图](/img/接口数据处理步骤.png)

### 标识终端数量
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/list
```
- 请求方式
```js
    post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    return {
        "pageNo": 1,
        "pageSize": 1000
    }
}
```
- 数据处理
```js
function(data,params,refs){
    return {value:data.page.total}
}
```

### 设备统计
#### 数采网关统计
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/list
```
- 请求方式
```js
    post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    return {"code": "GWINET607"}
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = {label:'',value:'',data:''};
    dataResult.label = data.page.records[0].name;
    dataResult.value = data.page.total;
    dataResult.data = 240;
    return dataResult;
}
```
#### 通讯模组统计
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/list
```
- 请求方式
```js
    post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    return {"code": "D938INET8909"}
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = {label:'',value:'',data:''};
    dataResult.label = '通讯模组';
    dataResult.value = data.page.total;
    dataResult.data = 240;
    return dataResult;
}
```

### 设备列表
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/list
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    return{
        "pageNo": 1,
        "pageSize": 1000
    }
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = [];
    var records = data.page.records;
    for(var i = 0; i < records.length; i++){
        var option = {
            name:'',
            status:'',
            type:''
        };
        option.name = records[i].name;
        option.status = records[i].unionStatusName;
        option.type  = records[i].thingTypeName;
        dataResult[i] = option;
    }

    return dataResult
}
```
### 南京市地图
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/geo/positions
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
  return { 
    "onlineStatus": 1,
    "status": 1,
    "thingTypeCode":"inetdevice"
  }
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = [];
    var record = data.data;
    for(var i = 0; i < record.length; i++){
        var option = {name:'', value:'', lng:'', lat:'', zoom:''};
        option.name = record[i].name;
        option.value = 1;
        var position = record[i].propValue.split(',');
        option.lng = position[0];
        option.lat = position[1];
        option.zoom = 1;
        dataResult[i] = option;
    }
    return dataResult;
}
```

### 设备历史数据
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/onoff/stat/day
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    var date = new Date();  //当前时间戳
    var lastWeek = new Date(date.getTime() - 7 * 24 * 60 * 60 * 1000); //前7天的时间戳

    function padTo2Digits(num) {
        return num.toString().padStart(2, '0');
    }
    // 格式化日期 yyyy-mm-dd HH:MM:DD
    function formatDate(date) {
        return (
            [
                date.getFullYear(),
                padTo2Digits(date.getMonth() + 1),
                padTo2Digits(date.getDate()),
            ].join('-') +
            ' ' +
            [
                padTo2Digits(date.getHours()),
                padTo2Digits(date.getMinutes()),
                padTo2Digits(date.getSeconds()),
            ].join(':')
        );
    }
    return {
    "thingCode": "",
    "thingTypeCode": "",
    "startTime": formatDate(lastWeek),
    "endTime": formatDate(date)
    }
}
```

- 数据处理
```js
function(data,params,refs){
    let seriesSub = {name:'',data:[]};
    let dataResult = {categories:'',series:[]};

    let records = data.data;
    let categories = [];
    let deviceNum = [];
    for (let i = 0; i < records.length; i++){
        let category = records[i].statTime.split(" ");
        categories[i] = category[0];
        deviceNum[i] = records[i].total;
    }
    dataResult.categories = categories;
    seriesSub.name = '在线数量';
    seriesSub.data = deviceNum;
    dataResult.series[0] = seriesSub;
    return dataResult;
}
```
### 温度TOP3
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/prop/topn
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```

- 请求参数
```js
function(data){
    return {
    "productCode": [],
    "propCode": "temperature",
    "num": 3
    }
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = [];
    var records = data.data;
    for(var i = 0; i < records.length; i++){
        var option = {
            name:'',
            status:'',
            code:''
        };
        option.name = records[i].productName;
        option.status = '在线';
        option.code  = records[i].code;
        option.value = records[i].propValue+'°C';
        dataResult[i] = option;
    }
    return dataResult
}
```
### 湿度TOP3
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/prop/topn
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```
- 请求参数
```js
function(data){
    return {
    "productCode": [],
    "propCode": "humidity",
    "num": 3
    }
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = [];
    var records = data.data;
    for(var i = 0; i < records.length; i++){
        var option = {
            name:'',
            status:'',
            code:''
        };
        option.name = records[i].productName;
        option.status = '在线';
        option.code  = records[i].code;
        option.value = records[i].propValue+'%rh';
        dataResult[i] = option;
    }
    return dataResult
}
```
### 震动TOP3
- 接口地址      
```js
https://cloudtest.aivanlink.com/iotx/vandm/open/api/v1/openapi/device/prop/topn
```
- 请求方式
```js
post
```
- 请求头
```js
function(data){
    return {Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJMT0dJTl9VU0VSX0tFWSI6ImY0NjI3ODZjLTY3ZmEtNDIwMy05NWU0LTNlNzNkOTE3ZjQ4MSJ9.aN_5jkHdtsZhNPNplv1So5-F1qCtKVNhSwQ49r8N6VQ"}
}
```

- 请求参数
```js
function(data){
    return {
    "productCode": [],
    "propCode": "data",
    "num": 3
    }
}
```
- 数据处理
```js
function(data,params,refs){
    var dataResult = [];
    var records = data.data;
    for(var i = 0; i < records.length; i++){
        var option = {
            name:'',
            status:'',
            code:''
        };
        option.name = records[i].productName;
        option.status = '在线';
        option.code  = records[i].code;
        option.value = records[i].propValue+'mm';
        dataResult[i] = option;
    }
    return dataResult
}
```