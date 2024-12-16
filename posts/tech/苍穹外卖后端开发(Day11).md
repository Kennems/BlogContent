---
title : '苍穹外卖后端开发(Day11)'
date : 2024-10-23T22:30:13+08:00
lastmod: 2024-10-23T22:20:13+08:00
description : "苍穹外卖后端开发(Day11)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day11)

## Apache ECharts

https://echarts.apache.org/zh/index.html



## 营业额统计



## 销量排名Top10

根据返回结果设计VO对象

`SalesTop10ReportVO`



```xml
    <select id="getSalesTop10" resultType="com.sky.dto.GoodsSalesDTO">
        SELECT name, sum(od.number) number
        from order_detail od, orders o
        where od.order_id = o.id
        and o.status = 5
        <if test="begin != null">
            and o.order_time &gt; #{begin}
        </if>
        <if test="end != null">
            and o.order_time &lt; #{end}
        </if>
        GROUP BY od.name
        ORDER BY number DESC
        LIMIT 10;
    </select>
```

