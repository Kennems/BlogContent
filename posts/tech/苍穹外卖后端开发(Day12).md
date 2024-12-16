---
title : '苍穹外卖后端开发(Day12)'
date : 2024-10-24T22:30:13+08:00
lastmod: 2024-10-24T22:20:13+08:00
description : "苍穹外卖后端开发(Day12)"  
categories : ["Java后端"]
tags : ["JavaWeb项目-苍穹外卖"]
---

# 苍穹外卖后端开发(Day12)

## 工作台

名词解释：

- 营业额：已完成订单的总金额
- 有效订单：已完成订单的数量
- 订单完成率：有效订单数 / 总订单数 * 100%
- 平均客单价：营业额 / 有效订单数
- 新增用户：新增用户的数量



## [Apache POI](https://poi.apache.org/)

Apache POI 是一个处理Microsoft Office 各种文件格式的开源项目。

```java
@SpringBootTest
public class POITest {
    public static final String filePath = "D:\\test\\test.xlsx";

    /**
     * 通过POI向文件内写入内容
     */
    public static void write() throws Exception {

        XSSFWorkbook excel = new XSSFWorkbook();
        XSSFSheet sheet = excel.createSheet("info");
        // rowNumber 从 0 开始
        XSSFRow row = sheet.createRow(1);
        XSSFCell cell = row.createCell(0);
        row.createCell(1).setCellValue("姓名");
        row.createCell(2).setCellValue("城市");

        row = sheet.createRow(2);
        row.createCell(1).setCellValue("张三");
        row.createCell(2).setCellValue("北京");

        row = sheet.createRow(3);
        row.createCell(1).setCellValue("李四");
        row.createCell(2).setCellValue("南京");

        // 通过输出流将内存中的excel文件写入到硬盘中
        FileOutputStream outputStream = new FileOutputStream(new File(filePath));
        excel.write(outputStream);

        outputStream.close();
        excel.close();
        System.out.println("写入成功！");
    }

    public static void read() throws Exception{
        // 读取已有的excel文件
        FileInputStream inputStream = new FileInputStream(new File(filePath));
        XSSFWorkbook excel = new XSSFWorkbook(inputStream);
        // 读取excel文件中的第一个sheet页
        XSSFSheet sheet = excel.getSheetAt(0);

        // 获取sheet中最后一行行号
        int lastRowNum = sheet.getLastRowNum();

        for (int i = 1; i <= lastRowNum; i++) {
            XSSFRow row = sheet.getRow(i);
            // 获取单元格对象
            String stringCellValue1 = row.getCell(1).getStringCellValue();
            String stringCellValue2 = row.getCell(2).getStringCellValue();
            System.out.println(stringCellValue1 + " " + stringCellValue2);
        }
        inputStream.close();
        excel.close();
    }

    public static void main(String[] args) throws Exception {
        write();
        read();
    }
}
```

## 导出运营数据Excel报表

实现一个无返回值的接口

```java
    /**
     * 导出运营数据报表
     * @param response
     */
    @GetMapping("/export")
    @ApiOperation("导出运营数据报表")
    public void export(HttpServletResponse response) throws IOException {
        reportService.exportBusinessData(response);
    }
```

准备好创建好格式的Excel文件：

![image-20241024212131927](https://raw.githubusercontent.com/Kennems/blog-image/main/image-20241024212131927.png)

### Service实现层完成相关操作

```java
    /**
     * 导出运营报表
     *
     * @param response
     */
    public void exportBusinessData(HttpServletResponse response) {
        // 查询数据库，获取营业数据--查询最近30天的运营数据
        LocalDate dateBegin = LocalDate.now().minusDays(30);
        LocalDate dateEnd = LocalDate.now().minusDays(1);

        // 查询概览数据
        BusinessDataVO businessDataVO = workspaceService.getBusinessData(LocalDateTime.of(dateBegin, LocalTime.MIN), LocalDateTime.of(dateEnd, LocalTime.MAX));

        // 通过POI将数据写入到Excel文件中
        InputStream inputStream = this.getClass().getClassLoader().getResourceAsStream("template/运营数据报表模板.xlsx");

        try {
            XSSFWorkbook excel = new XSSFWorkbook(inputStream);

            XSSFSheet sheet = excel.getSheet("Sheet1");

            // 填充数据-时间
            sheet.getRow(1).getCell(1).setCellValue("时间：" + dateBegin + "至" + dateEnd);

            XSSFRow row = sheet.getRow(3);
            row.getCell(2).setCellValue(businessDataVO.getTurnover());
            row.getCell(4).setCellValue(businessDataVO.getOrderCompletionRate());
            row.getCell(6).setCellValue(businessDataVO.getNewUsers());

            row = sheet.getRow(4);
            row.getCell(2).setCellValue(businessDataVO.getValidOrderCount());
            row.getCell(4).setCellValue(businessDataVO.getUnitPrice());

            for (int i = 0; i < 30; i++) {
                LocalDate date = dateBegin.plusDays(i);
                // 查询某一天的营业数据
                BusinessDataVO businessData = workspaceService.getBusinessData(LocalDateTime.of(date, LocalTime.MIN), LocalDateTime.of(date, LocalTime.MAX));
                // 获得某一行
                row = sheet.getRow(7 + i);
                row.getCell(1).setCellValue(date.toString());
                row.getCell(2).setCellValue(businessData.getTurnover());
                row.getCell(3).setCellValue(businessData.getValidOrderCount());
                row.getCell(4).setCellValue(businessData.getOrderCompletionRate());
                row.getCell(5).setCellValue(businessData.getUnitPrice());
                row.getCell(6).setCellValue(businessData.getNewUsers());
            }

            ServletOutputStream outputStream = response.getOutputStream();
            // 通过输出流将Excel文件下载到客户端浏览器
            excel.write(outputStream);

            //关闭资源
            outputStream.close();
            excel.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

