---
title: 二级节点同步数据优化至每日1500万
category: 笔记
tag: 总结
abbrlink: 31853
date: 2022-11-17 15:27:53
---

###  背景介绍

1. 二级节点：相当于标识解析系统的上级，标识解析系统所产生的<产品模板>和<产品标识>的数据都要同步到2级节点平台上
2. 同步：二级节点为我们提供对外的api，我们通过api文档上传对应的数据
3. 同步顺序：先同步产品模板，后同步产品标识。因为同步的标识会关联到对应的产品模板.


###  优化历程

1. 将同步二级节点的接口，从同步单个接口换成了批量同步接口(每次最多同步2k个)
2. 将前缀索引查询换成偏移量进行查询优化
3. 将for循环里的sql语句，修改为批量同步和查询，减少系统和数据库的请求次数。节省大量创建连接和断开连接的时间。

### 方案

1. 将每一个可能消耗时间的步骤都添加了时间消耗统计，分析一下代码每次同步2k条数据消耗时长大概在12s左右
- 原来的代码
``` java
    private void updateStatus(String status, List<RowValue> values) {
        try {
            // 这个values是从数据库查询出来的2000条数据
            for (RowValue rowValue : values) {
                String update = "update irs_single_product_data set status= " + status + "  where "
                        + "handle ='" + rowValue.getFieldValue("handle").toString() + "'";
                wapiBaseService.customSql(update, "db_irs", null);
            }
        } catch (Exception e) {
            LOGGER.error("更新标识状态异常，", e);
        }
    }
```
- 优化后的代码
```java
mapper层:
    /**
     * 通过handle批量修改状态
     *
     * @param status
     * @param handles
     * @return
     */
    int updateBatchStatusByHandle(@Param("status") Integer status, @Param("handles") List<String> handles);


mapperxml层：
    <update id="updateBatchStatusByHandle" parameterType="java.util.List">
        <!--@mbg.generated-->
        <foreach  collection="handles" item="item" index="index" open="" close="" separator=";">
            update irs_single_product_data
            <set>
                 status = #{status}
            </set>
            where handle = #{item}
        </foreach>
    </update>


service层：
    private void updateStatus(String status, List<RowValue> values) {
        try {
            List<String> handle = values.stream().map(x -> x.getFieldValue("handle").toString()).collect(Collectors.toList());
            int statusInteger = Integer.parseInt(status);
            irsSingleProductDataMapper.updateBatchStatusByHandle(statusInteger, handle);
        } catch (Exception e) {
            LOGGER.error("更新标识状态异常，", e);
        }
    }

最后还要再jdbc url配置文件拼接 &allowMultiQueries=true ，还要在mysql中配置max_allowed_packet 大小，我目前设置的是1G
```
- 此次优化将时间控制在5s左右

2. 在同步多家不同的企业发现，在同步和验证数据的时候发现花费的时间不同，有长有短

- 原来的代码：
``` java
    private String synchSingleDatas(String state_url, String token, List<RowValue> templates) {
        String url = state_url + "/identityv2/data/batchCreate";// 批量注册
        try {
            // 查询prefix关联模板
            List<SynchCreateHandle> handles = new ArrayList<>();
            Map<String, List<RowValue>> map = new HashMap<>();
            Map<String, String> productMap = new HashMap<>();
            String tempId = "1";
            for (RowValue template : templates) {
                List<RowValue> resultlist = new ArrayList<>();
                String piciHandle = template.getFieldValue("pici_handle").toString();
                String proHandle = template.getFieldValue("product_handle").toString();
                if (StringUtils.isEmpty(productMap.get(proHandle))) {
                    resultlist = wapiBaseService
                            .customQuerySql(
                                    " select * from irs_standerd_register_data where handle = '"
                                            + proHandle + "' and opreate=1 and state=1",
                                    "db_irs", null);
                    if (resultlist.size() > 0) {
                        tempId = resultlist.get(0).getFieldValue("template_id").toString();
                        productMap.put(template.getFieldValue("product_handle").toString(), tempId);
                    } else {
                        continue;
                    }
                } else {
                    tempId = productMap.get(template.getFieldValue("product_handle").toString())
                            .toString();
                }
                formatCreateSingleHandleContent(template.getFieldValue("handle").toString(), tempId,
                        handles, map, piciHandle);
            }
            String content = JSON.toJSONString(handles);
            Map<String, Object> responses = irsHttpService.batchRegisterHandle(url, token, content);
            LOGGER.info("同步接口返回： {}", JSON.toJSONString(responses));
            return (String) responses.get("status");
        } catch (Exception e) {
            LOGGER.error("标识数据同步失败,", e);
            return "99";
        }
    }


    private String formatCreateSingleHandleContent(String product, String tempId,
                                                List<SynchCreateHandle> handles, Map<String, List<RowValue>> map, String piciHandle) {
    SynchCreateHandle handle = null;
    try {
        handleValue value = null;
        HandleData data = null;
        handle = new SynchCreateHandle();
        List<handleValue> values = new ArrayList<>();
        handle.setHandle(product);
        handle.setTemplateVersion("单品模板" + tempId);
        List<RowValue> datas = new ArrayList<>();
        if (StringUtils.isEmpty(map.get(tempId))) {
            datas = wapiBaseService
                    .customQuerySql("SELECT * FROM irs_template_elements WHERE template_id = "
                            + tempId + " AND property != 'private'", "db_irs", null);
            map.put(tempId, datas);
        } else {
            datas = map.get(tempId);
        }
        int index = 2000;
        if (datas != null && !datas.isEmpty()) {
            for (RowValue temp : datas) {
                // 查询handle的value值
                value = new handleValue();
                data = new HandleData();
                value.setType(temp.getFieldValue("English_name").toString());
                value.setAuth("");
                value.setIndex(index);
                data.setFormat("string");
                data.setValue(temp.getFieldValue("value").toString());
                value.setData(data);
                values.add(value);
                index++;
            }
            handle.setValue(values);
            handles.add(handle);
        }
    } catch (Exception e) {
        LOGGER.error("单品模板异常,", e);
    }
    return JSON.toJSONString(handles);
}

```

- 优化后的代码

``` java
private String synchSingleDatas(String state_url, String token, List<RowValue> values) {
        String url = state_url + "/identityv2/data/batchCreate";// 批量注册
        StopWatch stopWatch = new StopWatch("同步接口返回耗时");
        stopWatch.start();
        try {
            List<SynchCreateHandle> handles = new ArrayList<>();
            Map<String, List<String>> collect = values.parallelStream()
                    .collect(Collectors.groupingBy(e -> e.getFieldValue("product_handle").toString(),
                            Collectors.mapping(e -> e.getFieldValue("handle").toString(), Collectors.toList())));
            AtomicReference<List<RowValue>> datas = new AtomicReference<>();
            collect.forEach((productHandleKey, singleHandleValues) -> {
                try {
                    List<RowValue> resultlist = wapiBaseService.customQuerySql("select template_id from irs_standerd_register_data where handle = '"
                            + productHandleKey + "' and opreate = 1 and state = 1", "db_irs", null);
                    String tempId = resultlist.get(0).getFieldValue("template_id").toString();
                    datas.set(wapiBaseService
                            .customQuerySql("SELECT * FROM irs_template_elements WHERE template_id = "
                                    + tempId + " AND property != 'private'", "db_irs", null));
                    for (String singleHandle : singleHandleValues) {
                        SynchCreateHandle handle;
                        handleValue value;
                        HandleData data;
                        handle = new SynchCreateHandle();
                        List<handleValue> elementValues = new ArrayList<>();
                        handle.setHandle(singleHandle);
                        handle.setTemplateVersion("单品模板" + tempId);
                        int index = 2000;
                        for (RowValue temp : datas.get()) {
                            // 查询handle的value值
                            value = new handleValue();
                            data = new HandleData();
                            value.setType(temp.getFieldValue("English_name").toString());
                            value.setAuth("");
                            value.setIndex(index);
                            data.setFormat("string");
                            data.setValue(temp.getFieldValue("value").toString());
                            value.setData(data);
                            elementValues.add(value);
                            index++;
                        }
                        handles.add(handle);
                    }
                } catch (Exception e) {
                    LOGGER.error("单品模板异常,", e);
                }
            });
            String content = JSON.toJSONString(handles);
            Map<String, Object> responses = irsHttpService.batchRegisterHandle(url, token, content);

            LOGGER.info("同步接口返回： {}", JSON.toJSONString(responses));
            stopWatch.stop();
            LOGGER.info(stopWatch.getTotalTimeMillis());
            return (String) responses.get("status");
        } catch (Exception e) {
            LOGGER.error("标识数据同步失败,", e);
            return "99";
        }
    }
```
- 此次优化将时间控制在2s左右


