---
title : 'Java20天速成——进阶课程(3)'
date : 2024-04-19T22:30:13+08:00
lastmod: 2024-04-19T22:20:13+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---

# Java20天速成——进阶课程(3)

## 文件操作

### `File`对象

### 常用方法

```java
public static void main(String[] args) {
    // 创建File对象，表示文件test.txt的绝对路径
    File f1 = new File("D:/JAVA/JavaSeProMax/file-io-app/src/com/showguan/FileDemo/test.txt");
    // 创建File对象，表示文件夹JavaSeProMax的绝对路径
    File f2 = new File("D:/JAVA");
    // 创建File对象，表示不存在的文件路径t.txt
    File f3 = new File("D:/JAVA/t.txt");

    // 输出文件f1的长度（字节数）
    System.out.println(f1.length());
    // 输出文件夹f2的长度，由于f2表示文件夹，而不是文件，因此返回值没有意义
    System.out.println(f2.length());
    // 输出文件f3的长度，由于文件路径不存在，因此返回0
    System.out.println(f3.length());
    // 检查文件f3是否存在，返回false，因为文件路径不存在
    System.out.println(f3.exists());

    // 创建File对象，表示相对路径下的文件test.txt
    // 相对路径默认从当前工程的根目录开始寻找，因此文件路径不存在
    File f4 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\test.txt");
    // 检查文件f4是否存在，返回false，因为文件路径不存在
    System.out.println(f4.exists());
    // 输出文件f4的长度，由于文件路径不存在，因此返回0
    System.out.println(f4.length());
}
```

```java
public static void main(String[] args) {
    // 创建File对象，表示相对路径下的文件test.txt
    File f1 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\test.txt");
    // 创建File对象，表示文件夹file-io-app的路径
    File f2 = new File("file-io-app");

    // 检查文件f1是否存在，返回true，因为文件路径存在
    System.out.println(f1.exists());

    // 检查f1是否表示一个文件，返回true
    System.out.println(f1.isFile());

    // 检查f1是否表示一个文件夹，返回false
    System.out.println(f1.isDirectory());

    // 检查f2是否表示一个文件夹，返回true
    System.out.println(f2.isDirectory());

    // 获取文件f1的名称，输出test.txt
    System.out.println(f1.getName());

    // 获取文件f1的长度（字节数）
    System.out.println(f1.length());

    // 获取文件f1最后修改时间
    long time = f1.lastModified();
    // 格式化最后修改时间并输出
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
    System.out.println(sdf.format(time));

    // 获取文件f1的路径（创建时使用的路径），输出相对路径file-io-app\src\com\showguan\FileDemo\test.txt
    System.out.println(f1.getPath());

    // 获取文件f1的绝对路径，输出完整的绝对路径
    System.out.println(f1.getAbsolutePath());
}
```

### 创建文件、目录

```java
import java.io.File;
import java.io.IOException;

public static void main(String[] args) throws IOException {
    // 创建File对象，表示新文件test2.txt的路径
    File f1 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\test2.txt");
    // 创建新文件test2.txt，如果文件不存在则创建并返回true，否则返回false
    System.out.println(f1.createNewFile());

    // 创建File对象，表示新文件夹aaa的路径
    File f2 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\aaa");
    // 创建新文件夹aaa，如果文件夹不存在则创建并返回true，否则返回false
    System.out.println(f2.mkdir());

    // 创建File对象，表示新文件夹路径a\b\c
    File f3 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\a\\b\\c");
    // 创建新文件夹路径a\b\c，如果路径中的文件夹不存在则递归创建，创建成功返回true，否则返回false
    System.out.println(f3.mkdirs());

    // 删除文件test2.txt，删除成功返回true，否则返回false
    System.out.println(f1.delete());

    // 删除文件夹a，由于文件夹非空，因此删除失败返回false
    File f4 = new File("file-io-app\\src\\com\\showguan\\FileDemo\\a");
    System.out.println(f4.delete());
}

```

```java
public static void main(String[] args) {
    // 遍历方法，只能遍历一级文件
    File f1 = new File("D:\\JAVA");
    // 获取D:\JAVA目录下的所有一级文件名
    String[] names = f1.list();
    // 遍历打印所有一级文件名
    for (String name : names) {
        System.out.println(name);
    }

    // 获取D:\JAVA目录下的所有一级文件对象
    File f2 = new File("D:\\JAVA");
    // 获取D:\JAVA目录下的所有一级文件对象
    File[] files = f2.listFiles();
    // 遍历打印所有一级文件对象的绝对路径
    for (File file : files) {
        System.out.println(file.getAbsoluteFile());
    }

    // 尝试获取不存在的目录的一级文件对象，返回null
    File f3 = new File("D:\\JAVA1");
    System.out.println(f3.listFiles()); // null

    // 尝试获取非文件夹的一级文件对象，返回null
    File f4 = new File("D:\\JAVA\\test1");
    // 因为f4代表的路径不是文件夹，因此listFiles()返回null
    File[] files4 = f4.listFiles();
    System.out.println(Arrays.toString(files4)); // null
}
```

### 递归查找

```java
    public static void main(String[] args) throws IOException {
        searchFile(new File("D:\\文档"), "23暑期青训营笔记"); // 调用搜索文件的方法
    }

    /**
     * 递归搜索指定目录下的文件，并打印找到的文件的绝对路径，并使用系统默认程序打开该文件。
     * @param dir 要搜索的目录
     * @param fileName 要搜索的文件名
     * @throws IOException 如果发生I/O错误
     */
    public static void searchFile(File dir, String fileName) throws IOException {
        // 如果目录为空、不存在或者是文件，则直接返回
        if(dir==null || !dir.exists() || dir.isFile()){
            return ;
        }
        // 获取目录下的所有文件和子目录
        File[] files = dir.listFiles();

        // 如果目录下存在文件
        if(files!=null && files.length>0){
            // 遍历目录下的所有文件和子目录
            for (File file : files) {
                // 如果是文件
                if(file.isFile()){
                    // 判断文件名是否包含指定的文件名
                    if(file.getName().contains(fileName)){
                        System.out.println("找到了！" + file.getAbsoluteFile()); // 打印找到的文件的绝对路径
                        Runtime runtime = Runtime.getRuntime();
                        runtime.exec(file.getAbsolutePath()); // 使用系统默认程序打开该文件
                        return; // 找到文件后直接返回
                    }
                }else{
                    // 如果是目录，则递归调用搜索文件的方法
                    searchFile(file, fileName);
                }
            }
        }
    }
```

### 递归删除

```java
    public static void main(String[] args) throws IOException {
        File file = new File("D:\\JAVA\\test1\\test"); // 要删除的目录的路径
        deleteDir(file); // 调用删除目录的方法
    }
    
    /**
     * 递归删除目录及其所有子目录和文件。
     * @param dir 要删除的目录
     */
    public static void deleteDir(File dir){
        // 如果目录为空或者不存在，则直接返回
        if(dir==null || !dir.exists()){
            return ;
        }
        // 如果是文件，则直接删除并返回
        if(dir.isFile()){
            dir.delete();
            return;
        }
        // 获取目录下的所有文件和子目录
        File[] files = dir.listFiles();
        // 如果目录为空，则直接返回
        if(files==null){
            return;
        }

        // 遍历目录下的所有文件和子目录
        for (File file : files) {
            if(file.isFile()){ // 如果是文件，则直接删除
                file.delete();
            }
            deleteDir(file); // 递归删除子目录
        }
        dir.delete(); // 删除当前目录
    }
```

## 字符集

`UTF-32`

- 每个字符占4个字节，存储空间太大，没有普及

`UTF-8`

- 是Unicode字符集的一种编码方案，采取可变长编码方案，共分为四个长度区：一个字节，两个字节，三个字节，四个字节
- 英文字符，数字等只占一个字节（兼容标准`ASCII`编码），汉字字符占用3个字节。

![image-20240419160507675](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240419160507675.png)

- `ASCII`字符集：只有英文、数字、符号等，占1个字节
- `GBK`字符集：汉字占2两个字节，英文，数字占1个字节
- `UTF-8`字符集：汉字占3个字节，英文，数字占1个字节

```java
import java.io.UnsupportedEncodingException;
import java.util.Arrays;

public static void main(String[] args) throws UnsupportedEncodingException {
    String data = "a啊a";
    
    // 使用默认字符集（通常是UTF-8）将字符串转换为字节数组
    byte[] bytes = data.getBytes();
    System.out.println(Arrays.toString(bytes));

    // 使用指定字符集（例如GBK）将字符串转换为字节数组
    byte[] bytes1 = data.getBytes("GBK");
    System.out.println(Arrays.toString(bytes1));

    // 将字节数组转换为字符串，使用默认字符集进行解码
    String s = new String(bytes);
    System.out.println(s);
    
    // 将字节数组转换为字符串，使用指定字符集进行解码
    String s1 = new String(bytes1, "GBK");
    System.out.println(s1);
}
```

## IO流

按流的方向分为

- 输入流
- 输出流

按流中数据的最小单位，分为：

- 字节流
  - 适合操作所有类型的文件
    - 比如：音频，视频，图片，文本文件的复制，转移等
- 字符流
  - 适合操作纯文本文件
    - 比如：读写`txt`, `java`文件等

![Snipaste_2024-04-19_16-23-08](https://cdn.jsdelivr.net/gh/kennems/blog-image/Snipaste_2024-04-19_16-23-08.png)

### `FileInputStream`(文件字节输入流)

- **作用**：以内存为基准，可以把磁盘文件中的数据以字节的形式读入到内存中去

```java
   public static void main(String[] args) throws IOException {
        // 用多态的形式创建文件输入流
        InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.txt");
        
        int b;
        // 读取文件内容，直到遇到文件末尾(-1)
        while((b = is.read()) != -1){
            System.out.print((char) b); // 将读取到的字节转换为字符并打印到控制台
        }
        is.close(); // 关闭文件输入流
    }
```

```java
    public static void main(String[] args) throws IOException {
        InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.txt"); // 创建文件输入流

        byte[] buffer = new byte[3]; // 创建缓冲区
        int len1 = is.read(buffer); // 从文件中读取字节到缓冲区中
        System.out.println("当前读取的长度:" + len1); // 打印当前读取的长度
        String s = new String(buffer); // 将缓冲区中的字节转换为字符串
        System.out.println(s); // 打印字符串
    }
```

**文件字节输入流**：一次读完全部字节

- **方式一**：定义一个字节数组与被读取的文件大小一样大，然后使用该字节数组，一次读完文件的全部字节。

- **方法二**：Java官方为`InputStream`提供了`readAllBytes()`方法， 可以直接把文件的全部字节读取到一个字节数组中返回。


```java
    public static void main(String[] args) throws IOException {
        // 从文件读取数据的两种方法：使用 InputStream 读取和使用 readAllBytes() 方法读取
        
        // 方法一：使用 InputStream 读取文件数据
        InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.txt"); // 创建文件输入流
        File f = new File("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.txt"); // 创建文件对象
        long size = f.length(); // 获取文件大小
        byte[] buffer = new byte[(int) size]; // 创建字节数组，用于存储文件内容，长度为文件大小（需要将 long 类型强制转换为 int 类型）
        int len = is.read(buffer); // 读取文件内容到字节数组，返回实际读取的字节数
        System.out.println(new String(buffer)); // 将字节数组转换为字符串并打印输出
        System.out.println(size); // 打印文件大小
        System.out.println(len); // 打印实际读取的字节数
        
        // 方法二：使用 readAllBytes() 方法读取文件数据
        InputStream is1 = new FileInputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.txt"); // 创建新的文件输入流
        byte[] buffer1 = is1.readAllBytes(); // 使用 readAllBytes() 方法读取文件内容到字节数组
        System.out.println(new String(buffer1)); // 将字节数组转换为字符串并打印输出
        
        is.close(); // 关闭文件输入流
    }
```

### `FileOutputStream`(文件字节输出流)

- **作用**：以内存为基准，把内存中的数据以字节的形式写出到文件中去

```java
    public static void main(String[] args) throws IOException {
        // 创建文件输出流，指定文件路径，并设置为追加模式（true）
        FileOutputStream os =
                new FileOutputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test2.txt", true);
        
        os.write(97); // 向文件中写入一个字节
        os.write('a'); // 向文件中写入一个字符
        byte[] bytes = "今天是20240419".getBytes(); // 将字符串转换为字节数组
        // 向文件中写入字节数组的一部分内容
        os.write(bytes, 0, 15); // 从字节数组的索引0开始，写入长度为15的字节数据
        os.write("\r\n".getBytes()); // 向文件中写入换行符
        os.close(); // 关闭文件输出流
    }
```

### 文件复制

```java
public class FileCopyDemo {
    
    /**
     * 程序入口点，复制一个文件的内容到另一个文件。
     * @param args 命令行参数
     * @throws IOException 如果发生I/O错误
     */
    public static void main(String[] args) throws IOException {
        InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\ByteStreamDemo\\test.jpg"); // 创建文件输入流，读取源文件
        OutputStream os = new FileOutputStream("D:\\JAVA\\test1\\img2.jpg"); // 创建文件输出流，写入目标文件
        byte[] buffer = new byte[1024]; // 创建缓冲区
        int len; // 定义变量用于保存读取的字节数
        // 循环读取源文件内容，并写入目标文件
        while((len = is.read(buffer)) != -1){
            os.write(buffer, 0, len); // 将缓冲区中的字节写入目标文件
        }
        os.close(); // 关闭文件输出流
        is.close(); // 关闭文件输入流
        System.out.println("复制完成！"); // 打印复制完成的消息
    }
}
```

## 释放资源的方式

### `try`-`catch`-`finally`

- finally代码区的特点：无论try中的程序是否正常执行了， 还是出现了异常，最后都一定会执行finally区， 除非JVM终止
- **作用**：一般用于在程序执行完成后进行资源的释放操作(专业级做法)

```java
public static void main(String[] args) {
    // 声明输入和输出流对象为null，以确保在try块中可以访问这些对象
    InputStream is = null;
    OutputStream os = null;
    try{
        // 故意产生的异常，用于演示异常捕捉
        System.out.println(10/0);
        // 创建一个FileInputStream来读取文件
        is = new FileInputStream("file-io-app\\src\\com\\showguan\\ResourceDemo\\test.txt");
        // 创建一个FileOutputStream来写入文件
        os = new FileOutputStream("file-io-app\\src\\com\\showguan\\ResourceDemo\\test1.txt");
        System.out.println(10/0);
        // 创建一个字节数组用于暂存从文件读取的数据
        byte[] buffer = new byte[1024];
        int len;
        // 循环读取并写入文件，直到文件结束
        while((len=is.read(buffer))!=-1){
            os.write(buffer, 0, len);
        }
        System.out.println("复制完成！");
    }catch (IOException e){
        // 捕捉并处理IO异常
        e.printStackTrace();
    }finally {
        // 在finally块中释放资源，确保无论是否有异常发生，资源都被释放
        try {
            // 关闭输出流资源
            if(os!=null)
                os.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        try {
            // 关闭输入流资源
            if(is!=null)
                is.close();
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### `try-with-resource`

```java
    public static void main(String[] args) {
        // 使用try-with-resources语法，自动关闭输入流和输出流
        try (
                InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\ResourceDemo\\test.txt"); // 创建文件输入流，读取源文件
                OutputStream os = new FileOutputStream("file-io-app\\src\\com\\showguan\\ResourceDemo\\test1.txt"); // 创建文件输出流，写入目标文件
        ) {
            byte[] buffer = new byte[1024]; // 创建缓冲区
            int len; // 定义变量用于保存读取的字节数
            // 循环读取源文件内容，并写入目标文件
            while ((len = is.read(buffer)) != -1) {
                os.write(buffer, 0, len); // 将缓冲区中的字节写入目标文件
            }
            System.out.println("复制完成！"); // 打印复制完成的消息
        } catch (Exception e) { // 捕获可能发生的异常
            e.printStackTrace(); // 打印异常信息
        }
    }
```

**注意**：()里面只能放置资源对象，资源都会实现`AutoCloseable`接口

资源用完之后，会自动调用`close`方法。

## IO字符流

### `FileReader`

```java
    public static void main(String[] args) throws FileNotFoundException {
        // 使用try-with-resources语法，自动关闭字符流
        try (
                Reader fr = new FileReader("file-io-app\\src\\com\\showguan\\ResourceDemo\\test.txt"); // 创建字符输入流，读取文件
        ) {
//            int c;
//            while((c=fr.read())!=-1){
//                System.out.println((char) c);
//            }
            char[] buffer = new char[3]; // 创建缓冲区
            int len; // 定义变量用于保存读取的字符数
            // 循环读取文件内容，并打印到控制台
            while((len=fr.read(buffer))!=-1){
                System.out.print(new String(buffer, 0, len)); // 将缓冲区中的字符转换为字符串并打印
            }
        } catch (Exception e) { // 捕获可能发生的异常
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### `FileWriter`

```java
    public static void main(String[] args) {
        // 使用try-with-resources语法，自动关闭字符流
        try (
            FileWriter fw = new FileWriter("file-io-app\\src\\com\\showguan\\CharStreamDemo\\test.txt", true); // 创建字符输出流，写入文件
        ){
            fw.write("\r\n"); // 向文件中写入换行符
            fw.write('a'); // 向文件中写入字符
            fw.write("\r\n"); // 向文件中写入换行符
            fw.write(98); // 向文件中写入字符对应的ASCII码
            fw.write('英'); // 向文件中写入字符
            fw.write("\r\n"); // 向文件中写入换行符

            fw.write("\r\n"); // 向文件中写入换行符
            fw.write("我是的话"); // 向文件中写入字符串
            fw.write("\r\n"); // 向文件中写入换行符
            fw.write("fw.write"); // 向文件中写入字符串
            char[] buffer = {'黑', '马', 'a'}; // 创建字符数组
            fw.write(buffer); // 向文件中写入字符数组的内容
            fw.write(buffer, 0, 2); // 向文件中写入字符数组的一部分内容
        } catch (Exception e) { // 捕获可能发生的异常
            e.printStackTrace(); // 打印异常信息
        }

    }
```

### `flush()`

```java
    public static void main(String[] args) throws IOException {
        Writer fw = new FileWriter("file-io-app\\src\\com\\showguan\\CharStreamDemo\\test1.txt"); // 创建字符输出流，写入文件
        fw.write('a'); // 向文件中写入字符 'a'
        fw.write('b'); // 向文件中写入字符 'b'
        fw.write('c'); // 向文件中写入字符 'c'
        fw.flush(); // 手动刷新缓冲区，将缓冲区中的数据写入文件
        fw.write('a'); // 向文件中写入字符 'a'
        fw.close(); // 关闭字符输出流，会自动刷新
    }
```

## 缓冲流

缓冲流：提高流读写数据的性能

### 字节缓冲流

- **原理**：字节缓冲输入流自带了8KB缓冲池，字节缓冲输出流也自带了8KB缓冲池

```java
    public static void main(String[] args) throws IOException {
        // 使用缓冲流进行文件复制操作
        
        File f = new File("file-io-app\\src\\com\\showguan\\BufferedStream\\test1.txt"); // 创建要复制到的新文件
        f.createNewFile(); // 如果文件不存在，则创建新文件
        
        try (
                // 创建文件输入流和缓冲输入流，用于读取原始文件内容
                InputStream is = new FileInputStream("file-io-app\\src\\com\\showguan\\BufferedStream\\test.txt");
                InputStream bis = new BufferedInputStream(is, 8192 * 2); // 使用 BufferedInputStream 进行缓冲
                // 创建文件输出流和缓冲输出流，用于写入复制后的内容到新文件
                OutputStream os = new FileOutputStream("file-io-app\\src\\com\\showguan\\BufferedStream\\test1.txt");
                OutputStream bos = new BufferedOutputStream(os, 8192 * 2); // 使用 BufferedOutputStream 进行缓冲
        ) {
            byte[] buffer = new byte[1024]; // 创建字节数组作为缓冲区
            int len;
            while ((len = bis.read(buffer)) != -1) { // 循环读取原文件内容到缓冲区
                bos.write(buffer, 0, len); // 将缓冲区的内容写入到新文件中
                System.out.println("复制完成"); // 输出提示信息，表示复制完成
            }
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### 字符缓冲流

#### 输入流

```java
import java.io.*;

public class Main {
    public static void main(String[] args) {
        // 两种方式读取文件内容并逐行打印：

        try (
                Reader fr = new FileReader("file-io-app\\src\\com\\showguan\\BufferedStream\\test.txt"); // 创建 FileReader 对象以打开文件输入流
                BufferedReader br = new BufferedReader(fr); // 创建 BufferedReader 对象，并将 FileReader 对象传递给它，用于缓冲读取操作
        ) {
            // 普通方式
//            char[] buffer = new char[3];
//            int len;
//            while((len=br.read(buffer))!=-1){
//                System.out.println(new String(buffer, 0, len));
//            }
            // 使用 readLine() 方法逐行读取文件内容，并将每行内容打印输出
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line); // 打印每行内容
            }
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
}
```

#### 输出流

```java
    public static void main(String[] args) throws IOException {
        // 从一个文件读取数据，排序后写入到另一个文件

        File f = new File("file-io-app\\src\\com\\showguan\\BufferedStream\\test3.txt");
        f.createNewFile(); // 如果文件不存在，则创建新文件

        try (
                // 创建 BufferedReader 读取原文件内容，创建 BufferedWriter 写入到新文件
                BufferedReader br = new BufferedReader(new FileReader("file-io-app\\src\\com\\showguan\\BufferedStream\\test2.txt"));
                BufferedWriter bw = new BufferedWriter(new FileWriter("file-io-app\\src\\com\\showguan\\BufferedStream\\test3.txt"));
        ) {
            List<String> data = new ArrayList<>(); // 创建 List 用于存储文件内容

            String line;
            while ((line = br.readLine()) != null) { // 逐行读取原文件内容并添加到 List 中
                data.add(line);
            }

            Collections.sort(data); // 对 List 中的数据进行排序

            for (String ln : data) { // 遍历排序后的数据
                System.out.println(ln); // 打印每行内容
                bw.write(ln); // 将每行内容写入到新文件
                bw.newLine(); // 写入换行符
            }
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### 不同流之间的性能差异

```java
public class Main {
    public static final String SRC_FILE = "D:\\JAVA\\test1\\video.mp4";
    public static final String DEST_FILE = "D:\\JAVA\\Test\\t";

    public static void main(String[] args) {
//        try {
//            File f = new File(DEST_FILE + "");
//            f.createNewFile();
//        } catch (Exception e) {
//            e.printStackTrace();
//        }
//        copy1();
        copy2();
//        copy3();
        copy4();
    }

    public static void copy1() {
        // 使用一个一个字节的方式复制文件
        long startTime = System.currentTimeMillis();
        try (
                InputStream is = new FileInputStream(SRC_FILE);
                OutputStream os = new FileOutputStream(DEST_FILE + "1.mp4");
        ) {
            int b;
            while ((b = is.read()) != -1) {
                os.write(b);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
        long endTime = System.currentTimeMillis();

        System.out.println("一个一个字节流的形式耗时:" + (endTime-startTime) + "ms");
    }
    public static void copy2() {
        // 使用字节数组的方式复制文件
        long startTime = System.currentTimeMillis();
        try (
                InputStream is = new FileInputStream(SRC_FILE);
                OutputStream os = new FileOutputStream(DEST_FILE + "2.mp4");
        ) {
            int len;
            byte[] buffer = new byte[1024];
            while ((len = is.read(buffer)) != -1) {
                os.write(buffer, 0, len);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
        long endTime = System.currentTimeMillis();

        System.out.println("1kb字节流的形式耗时:" + (endTime-startTime)+ "ms"); //1kb字节流的形式耗时:141
    }
    public static void copy3() {
        // 使用缓冲流一个一个字节的方式复制文件
        long startTime = System.currentTimeMillis();
        try (
                FileInputStream is = new FileInputStream(SRC_FILE);
                BufferedInputStream bis = new BufferedInputStream(is);
                FileOutputStream os = new FileOutputStream(DEST_FILE + "3.mp4");
                BufferedOutputStream bos = new BufferedOutputStream(os);
        ) {
            int b;
            while ((b = bis.read()) != -1) {
                bos.write(b);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        long endTime = System.currentTimeMillis();

        System.out.println("缓冲流使用一个一个字节复制耗时:" + (endTime-startTime)+ "ms");
    }
    public static void copy4() {
        // 使用缓冲流字节数组的方式复制文件
        long startTime = System.currentTimeMillis();
        try (
                FileInputStream is = new FileInputStream(SRC_FILE);
                BufferedInputStream bis = new BufferedInputStream(is);
                FileOutputStream os = new FileOutputStream(DEST_FILE + "4.mp4");
                BufferedOutputStream bos = new BufferedOutputStream(os);
        ) {
            byte[] buffer = new byte[1024];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        long endTime = System.currentTimeMillis();

        System.out.println("缓冲流使用字节数组复制耗时:" + (endTime-startTime) + "ms"); //缓冲流使用字节数组复制耗时:45
    }
}
```

## 转换流

### 字符输入转换流

```java
    public static void main(String[] args) {
        try (
                InputStream is = new FileInputStream(("file-io-app\\src\\com\\showguan\\BufferedStream\\test2.txt")); // 创建文件输入流
                Reader isr = new InputStreamReader(is, "GBK"); // 创建 InputStreamReader 对象，指定字符编码为 GBK
                BufferedReader br = new BufferedReader(isr); // 创建 BufferedReader 对象，用于缓冲读取操作
        ) {
            // 使用 BufferedReader 逐行读取文件内容，并打印输出
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### 字符输出转换流

```java
    public static void main(String[] args) {
        try(
                OutputStream os = new FileOutputStream("file-io-app\\src\\com\\showguan\\BufferedStream\\test6.txt",true); // 创建文件输出流，指定追加模式为 true
                Writer osw = new OutputStreamWriter(os, "GBK"); // 创建 OutputStreamWriter 对象，指定字符编码为 GBK
                BufferedWriter bw = new BufferedWriter(osw); // 创建 BufferedWriter 对象，用于缓冲写入操作

        ){
            bw.write("美好"); // 写入字符串到文件
            bw.write("真美好"); // 再次写入字符串到文件

        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }

    }
```

## `PrintStream`/`PrintWriter`(打印流)

- 作用：打印流可以实现更方便， 更高效的打印数据出去，能实现答应什么出去就是什么

### `PrintStream`

```java
    public static void main(String[] args) {
        // 使用 PrintStream 向文件写入内容，并指定字符编码为 GBK

        try (
                // 创建 PrintStream 对象，指定字符编码为 GBK
                PrintStream ps = new PrintStream("file-io-app\\src\\com\\showguan\\PrintDemo\\test.txt", Charset.forName("GBK"));
        ) {
            // 使用 println 方法写入字符和字符串
            ps.println('3');
            ps.println('a');
            ps.println("你好啊");

            // 使用 write 方法写入字符
            ps.write('z');
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

### `PrintWriter`

```java
    public static void main(String[] args) {
        // 使用 PrintWriter 向文件写入内容，并指定字符编码为 GBK

        try (
                // 创建 PrintWriter 对象，指定字符编码为 GBK
                PrintWriter pw = new PrintWriter("file-io-app\\src\\com\\showguan\\PrintDemo\\test.txt", "GBK");

                // 创建 PrintWriter 对象，通过 OutputStreamWriter 包装 FileOutputStream，并指定字符编码为 GBK
                PrintWriter pw1 = new PrintWriter(new OutputStreamWriter(new FileOutputStream("file-io-app\\src\\com\\showguan\\PrintDemo\\test.txt", true), "GBK"));
        ) {
            // 使用 println 方法写入字符和字符串
            pw.println('3');
            pw.println('a');
            pw.println("你好啊");

            // 使用 write 方法写入字符和字符串
            pw.write('z');
            pw.write("abcdefghijklim");
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

**对比**：

- 打印数据的功能上是一模一样的：都是**使用方便，性能高效**（核心优势）
- `PrintStream`继承自字节输出流`OutputStream`， 因此支持写字节数据的方法
- `PrintWriter`继承自字符输出流`Writer`， 因此支持写字符数据出去

### 输出重定向

```java
    public static void main(String[] args) {
        // 将输出重定向到文件

        System.out.println("你好"); // 在控制台打印输出
        System.out.println("志在千里"); // 在控制台打印输出

        try (
                // 创建 PrintStream 对象，将输出重定向到文件
                PrintStream ps = new PrintStream("file-io-app\\src\\com\\showguan\\PrintDemo\\test.txt");
        ) {
            System.setOut(ps); // 将标准输出流重定向到 PrintStream 对象

            System.out.println("烈士暮年"); // 将输出重定向到文件中
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        }

    }
```

## 数据流

### 数据输出流

```java
    public static void main(String[] args) {
        // 使用 DataOutputStream 写入不同类型的数据到文件

        try (
                // 创建 DataOutputStream 对象，用于写入数据到文件
                DataOutputStream dos = new DataOutputStream(new FileOutputStream("file-io-app\\src\\com\\showguan\\DataStreamDemo\\test.txt"));
        ){
            // 写入不同类型的数据到文件
            dos.write(91); // 写入一个字节
            dos.writeInt(10); // 写入一个整数
            dos.writeDouble(99.8); // 写入一个双精度浮点数
            dos.writeBoolean(true); // 写入一个布尔值
            dos.writeUTF("999黑马程序员"); // 写入一个 UTF-8 编码的字符串

        }
        catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }


    }
```

### 数据输入流

```java
    public static void main(String[] args) {
        // 使用 DataInputStream 读取不同类型的数据

        try (
                // 创建 DataInputStream 对象，用于读取数据
                DataInputStream dis = new DataInputStream(new FileInputStream("file-io-app\\src\\com\\showguan\\DataStreamDemo\\test.txt"));
        ) {
            // 依次读取不同类型的数据并打印输出
            int b = dis.read();
            System.out.println(b);

            int i = dis.readInt();
            System.out.println(i);

            double d = dis.readDouble();
            System.out.println(d);

            boolean bool = dis.readBoolean();
            System.out.println(bool);

            String rs = dis.readUTF();
            System.out.println(rs);

        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }
    }
```

## 序列化流

`ObjectOutputStream`/`ObjectInputStream`对象字节输出流

- 可以把Java对象进行序列化：把java对象存入到文件中去

### 序列化

```java
//User
public class User implements Serializable {
    private String loginName;
    private String userName;
    private int age;
    private transient String passWord;
    }
```

```java
    public static void main(String[] args) {
        // 使用 ObjectOutputStream 将对象序列化到文件

        try (
                // 创建 ObjectOutputStream 对象，用于将对象序列化到文件
                ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("file-io-app\\src\\com\\showguan\\ObjectStreamDemo\\test1.txt"));
        ) {
            // 创建 User 对象并初始化
            User u = new User("admin", "K", 18, "1919");

            // 打印 User 对象的字符串表示
            System.out.println(u.toString());

            // 将 User 对象序列化到文件
            oos.writeObject(u);

            System.out.println("序列化成功！"); // 打印序列化成功消息
        } catch (IOException e) {
            throw new RuntimeException(e); // 抛出运行时异常并打印异常信息
        }

    }
```

### 反序列化

```java
    public static void main(String[] args) {
        // 使用 ObjectInputStream 从文件反序列化对象

        try (
            // 创建 ObjectInputStream 对象，用于从文件读取对象并反序列化
            ObjectInputStream ois = new ObjectInputStream(new FileInputStream("file-io-app\\src\\com\\showguan\\ObjectStreamDemo\\test1.txt"));
        ){
            // 从文件中读取对象并强制转换为 User 类型
            User u = (User) ois.readObject();

            // 打印反序列化得到的 User 对象的字符串表示
            System.out.println(u.toString());
        } catch (Exception e) {
            e.printStackTrace(); // 打印异常信息
        }

    }
```

**如何序列化多个对象？**

用一个`ArrayList`集合存储多个对象， 然后直接对集合进行序列化即可（`ArrayList`集合已经实现了序列化接口）

**序列化：**

```java
    public static void main(String[] args) {
        try (
                ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("file-io-app\\src\\com\\showguan\\ObjectStreamDemo\\test1.txt"));
        ) {
            List<User> users = new ArrayList<>();
            for (int i = 0; i < 100; i++) {
                User u = new User("admin" + i, "K" + i, 18, "1919" + i);
                users.add(u);
            }

            // 使用循环逐个打印每个用户对象的字符串表示
            for (User user : users) {
                System.out.println(user.toString());
            }

            oos.writeObject(users);
            System.out.println("序列化成功！");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
```

反序列化

```java
    public static void main(String[] args) {
        try (
                ObjectInputStream ois = new ObjectInputStream(new FileInputStream("file-io-app\\src\\com\\showguan\\ObjectStreamDemo\\test1.txt"));
        ){
            List<User> users = (ArrayList<User>) ois.readObject();
            users.forEach( user -> {
                    System.out.print(user.getUserName() + " ");
                    System.out.print(user.getLoginName() + " ");
                    System.out.print(user.getAge() + " ");
                    System.out.print(user.getPassWord() + " ");
            });
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
```

## IO框架

**框架**

- 解决某类问题，编写的一套类、接口等，可以理解成一个半成品，大多框架都是第三方研发的
- **好处**：在框架的基础上开发，可以得到优秀的软件架构，并能提高开发效率

**IO框架**

- 封装了Java提供的对文件、数据进行操作的代码，对外提供了更简单的方式来对文件进行操作，对数据进行读写。

**框架的导入**：(Commons-IO)

1. 在项目中创建一个文件夹 ：lib
2. 将xxx.jar文件复制到lib文件夹
3. 在jar文件夹上点击右键，选择Add as Library 
4. 在类中导包使用

### 使用Commons-IO

```java
    public static void main(String[] args) throws IOException {
        // 复制文件
        FileUtils.copyFile(new File("D:\\JAVA\\Test\\t3.mp4"), new File("file-io-app\\src\\com\\showguan\\CommonsIODemo\\VideoTest.mp4"));
        
        // 复制目录及其内容
        FileUtils.copyDirectory(new File("D:\\JAVA\\test1"), new File("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test\\t"));
        
        // 删除目录及其内容
        FileUtils.deleteDirectory(new File("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test\\t"));
        
        // 读取文件内容为字符串
        String rs = FileUtils.readFileToString(new File("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test1.txt"));
        System.out.println(rs);
        
        // 向文件写入字符串数据，追加到文件末尾
        FileUtils.writeStringToFile(new File("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test.txt"), "data", true);
    }
```

Files也能做到同样的效果，但是功能不够强大。

```java
    public static void main(String[] args) throws IOException {
        // 复制文件
        Files.copy(Path.of("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test.txt"), Path.of("file-io-app\\src\\com\\showguan\\CommonsIODemo\\test2.txt"));
    }
```

