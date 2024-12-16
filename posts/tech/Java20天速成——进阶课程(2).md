---
title : 'Java20天速成——进阶课程(2)'
date : 2024-04-16T22:30:13+08:00
lastmod: 2024-04-15T22:20:13+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---

# Java20天速成——进阶课程(2)

## 正则表达式

| 符号  |         含义          |           举例           |
| :---: | :-------------------: | :---------------------: |
| `[]`  |    匹配方括号内的任一字符    |         `[abc]`          |
|  `^`  |      匹配除指定字符外的任意字符      |        `[^abc]`         |
| `&&`  |    匹配两个字符集的交集，需作为整体出现    |     `[a-z&&m-p]`        |
|  `.`  |      匹配除换行符外的任意字符      |    `a.b` （匹配 "a" 后接任意字符，再接 "b"）   |
|  `\`  |      转义字符，用于匹配特殊字符     |         `\\d`           |
| `\\d` |      匹配数字 0-9      |         `\\d+`          |
| `\\D` |     匹配非数字字符      |         `\\D+`          |
| `\\s` |      匹配空白字符      |        `\s匹配空格、制表符、换行符等等`     |
| `\\S` |    匹配非空白字符     |        `\\S+` （匹配非空白字符序列）      |
| `\\w` |     匹配单词字符      |     `[a-zA-Z_0-9]`       |
| `\\W` |     匹配非单词字符     |        `[^\w]`          |
|  `*`  | 匹配前面的元素零次或多次，即可有可无      |      `a*b` （匹配 "b"、"ab"、"aab"、等等）    |
|  `?`  | 匹配前面的元素零次或一次，即可有可无      |       `colou?r` （匹配 "color" 或 "colour"）    |
| `()`  |        分组，将其中的字符视为一个单元        |       `a(bc)+`        |
| `\f`  |      匹配换页符       |         `\f`           |

```markdown
|  `|`  |       或，用于匹配多个模式中的任意一个      |        `ab\|AB`        |
```

```java
public static void method1() {
    // 给定的字符串包含了电话号码和邮箱地址的信息
    String data = " 来黑马程序员学习Java，\n" +
            "        电话：1866668888，18699997777\n" +
            "        或者联系邮箱：boniu@itcast.cn，\n" +
            "        座机电话：01036517895，010-98951256\n" +
            "        邮箱：bozai@itcast.cn，\n" +
            "        邮箱：dlei0009@163.com，\n" +
            "        热线电话：400-618-9090 ，400-618-4000，4006184000，4006189090";
    
    // 定义正则表达式来匹配电话号码和邮箱地址
    String regex = "(1[3-9]\\d{9})|"
                 + "(0\\d{2,9}-?\\d{7,18})|"  // 匹配固定电话号码，包括区号和分机号
                 + "(\\w{2,}@\\w{2,20}(\\.\\w{2,10}){1,2})|"  // 匹配邮箱地址
                 + "(400-?\\d{3,7}-?\\d{3,7})";  // 匹配400电话号码，包括可能的分机号
    
    // 编译正则表达式
    Pattern pattern = Pattern.compile(regex);
    
    // 创建匹配器对象
    Matcher matcher = pattern.matcher(data);
    
    // 循环查找匹配项并打印
    while(matcher.find()){
        String res = matcher.group();  // 获取匹配到的字符串
        System.out.println(res);
    }
}
```

```java
    String s1 = "古力娜扎ai8888迪丽热巴999aa5566马尔扎哈fbbfsfs42425卡尔扎巴";
    System.out.println(s1.replaceAll("\\w+", "-"));
    String s2 = "我我我喜欢编编编编编编编编编编编编程程程";
    System.out.println(s2.replaceAll("(.)\\1+", "$1"));
    String s3 = "古力娜扎ai8888迪丽热巴999aa5566马尔扎哈fbbfsfs42425卡尔扎巴";
    String[] names = s3.split("\\w+");
    System.out.println(Arrays.toString(names));
```

## 异常

**Error**： 代表的系统级别错误（属于严重问题）， 也就是说系统一旦出现问题，sun公司会把这些问题封装成`Error`对象给出了，即`Error`是Sun公司自己用的，而不是给程序员用的

**Exception**：异常，它代表的才是程序可能出现的错误，所以通常会用`Exception`以及它的子类来封装程序出现的错误

- **运行时异常**：`RuntimeException`及其子类，编译阶段不会出现错误提醒，运行时出现的异常（数组索引越界异常）
- **编译时异常**：编译阶段就会出现的错误提醒。（日期解析异常）

### 自定义异常

#### 运行时异常

```java
package com.showguan.Exception;

// 自定义运行时异常类，用于表示年龄不合法的情况
public class AgeIllegalRuntimeException extends RuntimeException {
    // 默认构造函数
    public AgeIllegalRuntimeException() {

    }

    // 带有消息参数的构造函数，用于传递异常信息
    public AgeIllegalRuntimeException(String message) {
        super(message);
    }
}

public class Main {
    // 保存年龄的方法，如果年龄不合法则抛出自定义的运行时异常
    public static void saveAge(int age) {
        if (age > 0 && age < 150) {
            System.out.println("年龄合法");
        } else {
            throw new AgeIllegalRuntimeException("/age is illegal, your age is " + age);
        }
    }

    public static void main(String[] args) {
        try {
            // 调用保存年龄的方法，传入一个不合法的年龄值
            saveAge(-1);
            System.out.println("底层执行成功了");
        } catch (Exception e) {
            // 捕获自定义的异常并打印异常信息
            e.printStackTrace();
            System.out.println("底层出现了异常");
        }
    }
}
```

#### 编译时异常

```java
package com.showguan.Exception;

// 自定义异常类，继承自Exception类，用于表示年龄不合法的情况
public class AgeIllegalException extends Exception {
    // 默认构造函数
    public AgeIllegalException() {

    }

    // 带有消息参数的构造函数，用于传递异常信息
    public AgeIllegalException(String message) {
        super(message);
    }
}

public class Main {
    // 保存年龄的方法，如果年龄不合法则抛出自定义的受检查异常
    public static void saveAge2(int age) throws AgeIllegalException {
        if (age > 0 && age < 150) {
            System.out.println("年龄合法");
        } else {
            throw new AgeIllegalException("/age is illegal, your age is " + age);
        }
    }

    public static void main(String[] args) {
        try {
            // 调用保存年龄的方法，传入一个合法的年龄值
            saveAge2(1);
            System.out.println("底层执行成功了");
        } catch (Exception e) {
            // 捕获自定义的异常并打印异常信息
            e.printStackTrace();
            System.out.println("底层出现了异常");
        }
    }
}
```

```java
public class Demo2 {
    public static void main(String[] args) {
        try {
            // 调用test1方法，该方法声明了可能抛出ParseException和FileNotFoundException异常
            test1();
        } catch (ParseException e) {
            // 捕获ParseException异常，并输出错误提示信息
            System.out.println("日期格式错误，请检查日期格式");
            // 抛出一个新的RuntimeException异常，将原始异常作为其cause
            throw new RuntimeException(e);
        } catch (FileNotFoundException e) {
            // 捕获FileNotFoundException异常，并输出错误提示信息
            System.out.println("指定文件夹内没有该文件，请检查文件路径");
            // 抛出一个新的RuntimeException异常，将原始异常作为其cause
            throw new RuntimeException(e);
        }
    }
    
    // test1方法声明了可能抛出ParseException和FileNotFoundException异常
    public static void test1() throws ParseException, FileNotFoundException {
        // 创建SimpleDateFormat对象，用于解析日期字符串
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        // 解析日期字符串，如果日期格式不正确会抛出ParseException异常
        Date d = sdf.parse("2077-09-23 10:34:22");
        System.out.println(d);
        // 调用test2方法，该方法声明了可能抛出FileNotFoundException异常
        test2();
    }
    
    // test2方法声明了可能抛出FileNotFoundException异常
    public static void test2() throws FileNotFoundException {
        // 创建FileInputStream对象，如果指定文件不存在会抛出FileNotFoundException异常
        InputStream is = new FileInputStream("D:/Java.txt");
    }
}

```

```java
package com.showguan.Exception;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Demo3 {
    public static void main(String[] args) {
        try {
            // 调用test1方法，该方法声明了可能抛出Exception异常
            test1();
        } catch (Exception e) {
            // 捕获Exception异常，并输出错误提示信息
            System.out.println("操作有误");
            // 抛出一个新的RuntimeException异常，将原始异常作为其cause
            throw new RuntimeException(e);
        }
    }
    
    // test1方法声明了可能抛出Exception异常
    public static void test1() throws Exception {
        // 创建SimpleDateFormat对象，用于解析日期字符串
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        // 解析日期字符串，如果日期格式不正确会抛出ParseException异常
        Date d = sdf.parse("2077-09-23 10:34:22");
        System.out.println(d);
        // 调用test2方法，该方法声明了可能抛出Exception异常
        test2();
    }
    
    // test2方法声明了可能抛出Exception异常
    public static void test2() throws Exception {
        // 创建FileInputStream对象，如果指定文件不存在会抛出FileNotFoundException异常
        InputStream is = new FileInputStream("D:/Java.txt");
    }
}

```

## 集合

`Collection`集合特点：

- `List`系列集合：添加的元素是有序、可重复、有索引的。
  - `ArrayList`, `LinkedList`：有序、可重复、有索引

- `Set`系列集合：添加的元素是无序、不重复、无索引
  - `HashSet`: 无序、不重复、无索引
  - `LinkedHashSet`: **有序**、不重复、无索引
  - `TreeSet` : **按照大小默认升序排列**、不重复、无索引

### `Collection`

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;

public class Main {
    public static void main(String[] args) {
        // 创建一个ArrayList对象，并向其中添加元素
        Collection<String> c = new ArrayList<>();
        c.add("java1");
        c.add("java2");
        c.add("java3");
        c.add("java1"); // 可以添加重复元素
        System.out.println(c); // 输出集合中的所有元素

        // 清空集合中的所有元素
//        c.clear();
        System.out.println(c);

        // 判断集合是否为空
        System.out.println(c.isEmpty());

        // 获取集合中元素的个数
        System.out.println(c.size());

        // 判断集合是否包含指定元素
        System.out.println(c.contains("java1"));

        // 移除集合中的指定元素
        c.remove("java1");
        System.out.println(c);

        // 将集合转换为数组
        Object[] arr = c.toArray();
        System.out.println(Arrays.toString(arr));

        // 将集合转换为指定类型的数组
        String[] arr2 = c.toArray(new String[c.size()]);
        System.out.println(Arrays.toString(arr2));

        // 创建另一个集合，并将其元素全部添加到当前集合中
        Collection<String> c2 = new ArrayList<>();
        c2.add("Java8");
        c2.add("java9");
        c.addAll(c2);
        System.out.println(c);
    }
}
```

#### 迭代器

```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        // 创建一个 ArrayList 集合对象
        Collection<String> c = new ArrayList<>();

        // 向集合中添加元素
        c.add("Kennem");
        c.add("999");
        c.add("show");

        // 使用 forEach 方法遍历集合，传统方式
        c.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });

        // 使用 Lambda 表达式遍历集合，Lambda 参数类型推断
        c.forEach((String s) -> {
            System.out.println(s);
        });

        // 使用 Lambda 表达式遍历集合，简化参数类型
        c.forEach((String s) -> System.out.println(s));

        // 使用方法引用遍历集合，等同于 Lambda 表达式 System.out::println
        c.forEach( System.out::println ) ;
    }
}
```

```java

public class Main {
    public static void main(String[] args) {
        // 创建一个 Movie 类型的集合对象
        Collection<Movie> movies = new ArrayList<>();

        // 向集合中添加 Movie 对象
        movies.add(new Movie("《阿甘正传》", 9.5, "汤姆"));

        // 打印集合内容
        System.out.println(movies);

        // 使用增强型 for 循环遍历集合中的 Movie 对象
        for (Movie movie : movies) {
            // 获取电影名称并打印
            System.out.println(movie.getName());
            // 获取电影评分并打印
            System.out.println(movie.getScore());
            // 获取主演名称并打印
            System.out.println(movie.getActor());
        }
    }
}
```

### `List`()

#### `ArrayList`()

- **添加元素**：`add(E e)` 和 `add(int index, E element)`

- **删除元素**：`remove(int index)` 和 `remove(Object o)`

- **获取元素**：`get(int index)`

- **修改元素**：`set(int index, E element)`

- **检查元素是否存在**：`contains(Object o)`

- **获取列表大小**：`size()`

- **获取元素位置**：`indexOf(Object o)` 和 `lastIndexOf(Object o)`

- **遍历列表**：使用 `for-each` 循环和 `Iterator`

- **转换为数组**：`toArray()`

- **获取子列表**：`subList(int fromIndex, int toIndex)`

- **清空列表**：`clear()`

- **判断列表是否为空**：`isEmpty()`

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        // 创建一个 ArrayList 对象 ls
        List<String> ls = new ArrayList<>();

        // 向 ls 中添加元素
        ls.add("Kennem");
        ls.add("K");
        ls.add("至尊宝");
        ls.add("牛夫人");

        // 打印 ls 的内容
        System.out.println("Initial list: " + ls);

        // 在索引位置 2 插入元素 "紫霞仙子"
        ls.add(2, "紫霞仙子");
        System.out.println("After adding '紫霞仙子' at index 2: " + ls);

        // 移除索引位置 2 处的元素并打印移除的元素
        System.out.println("Removed element: " + ls.remove(2));
        System.out.println("After removal: " + ls);

        // 获取索引位置 2 处的元素并打印
        System.out.println("Element at index 2: " + ls.get(2));

        // 将索引位置 1 处的元素修改为 "牛魔王"，并打印被替换掉的元素
        System.out.println("Replaced element: " + ls.set(1, "牛魔王"));
        System.out.println("After setting '牛魔王' at index 1: " + ls);

        // 检查列表中是否包含某个元素
        System.out.println("Contains '至尊宝': " + ls.contains("至尊宝"));
        System.out.println("Contains '紫霞仙子': " + ls.contains("紫霞仙子"));

        // 获取列表大小
        System.out.println("List size: " + ls.size());

        // 获取某个元素的索引
        System.out.println("Index of '牛魔王': " + ls.indexOf("牛魔王"));
        System.out.println("Index of '至尊宝': " + ls.indexOf("至尊宝"));

        // 遍历列表
        System.out.println("List elements:");
        for (String element : ls) {
            System.out.println(element);
        }

        // 转换为数组
        Object[] array = ls.toArray();
        System.out.println("Array elements:");
        for (Object obj : array) {
            System.out.println(obj);
        }

        // 获取子列表
        List<String> subList = ls.subList(1, 3);
        System.out.println("Sublist (from index 1 to 3): " + subList);

        // 使用迭代器遍历列表
        System.out.println("List elements using iterator:");
        for (var iterator = ls.iterator(); iterator.hasNext();) {
            System.out.println(iterator.next());
        }

        // 清空列表
        ls.clear();
        System.out.println("Is list empty after clear? " + ls.isEmpty());
    }
}

```

#### `LinkedList()`

- **添加元素**：`add(E e)` 和 `add(int index, E element)`

- **删除元素**：`remove(int index)` 和 `remove(Object o)`

- **获取元素**：`get(int index)`

- **修改元素**：`set(int index, E element)`

- **检查元素是否存在**：`contains(Object o)`

- **获取列表大小**：`size()`

- **获取元素位置**：`indexOf(Object o)` 和 `lastIndexOf(Object o)`

- **遍历列表**：使用 `for-each` 循环和 `Iterator`

- **转换为数组**：`toArray()`

- **获取子列表**：`subList(int fromIndex, int toIndex)`

- **清空列表**：`clear()`

- **判断列表是否为空**：`isEmpty()`

- **特有方法**：`addFirst(E e)`，`addLast(E e)`，`getFirst()`，`getLast()`，`removeFirst()`，`removeLast()`

```java
import java.util.LinkedList;
import java.util.Iterator;

public class LinkedListExample {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();

        // 添加元素
        list.add("element1");
        list.add("element2");
        list.add(1, "element1.5");
        list.addFirst("firstElement");
        list.addLast("lastElement");

        // 访问元素
        System.out.println("First element: " + list.getFirst());
        System.out.println("Last element: " + list.getLast());
        System.out.println("Element at index 1: " + list.get(1));

        // 删除元素
        list.remove(1);
        list.remove("element1");
        list.removeFirst();
        list.removeLast();

        // 替换元素
        list.set(1, "newElement");

        // 检查元素是否存在
        System.out.println("Contains 'element2': " + list.contains("element2"));

        // 获取列表大小
        System.out.println("List size: " + list.size());

        // 遍历列表
        System.out.println("Elements in the list:");
        for (String element : list) {
            System.out.println(element);
        }

        // 转换为数组
        Object[] array = list.toArray();
        System.out.println("Array: ");
        for (Object obj : array) {
            System.out.println(obj);
        }

        // 获取子列表
        List<String> subList = list.subList(0, 1);
        System.out.println("Sublist: " + subList);

        // 清空列表
        list.clear();
        System.out.println("Is list empty? " + list.isEmpty());
    }
}

```

##### `LinkedList()`特有方法

```java
import java.util.LinkedList;

public class LinkedListExample {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();

        // 添加元素到开头和末尾
        linkedList.addFirst("firstElement");
        linkedList.addLast("lastElement");
        System.out.println("After adding elements to first and last: " + linkedList);

        // 获取第一个和最后一个元素
        System.out.println("First element: " + linkedList.getFirst());
        System.out.println("Last element: " + linkedList.getLast());

        // 删除第一个和最后一个元素
        System.out.println("Removed first element: " + linkedList.removeFirst());
        System.out.println("Removed last element: " + linkedList.removeLast());
        System.out.println("After removing first and last elements: " + linkedList);

        // 栈操作
        linkedList.push("stackElement");
        System.out.println("After push: " + linkedList);
        System.out.println("Popped element: " + linkedList.pop());
        System.out.println("After pop: " + linkedList);

        // 添加元素到队列两端
        linkedList.offerFirst("offerFirstElement");
        linkedList.offerLast("offerLastElement");
        System.out.println("After offering elements: " + linkedList);

        // 获取并移除队列两端的元素
        System.out.println("Polled first element: " + linkedList.pollFirst());
        System.out.println("Polled last element: " + linkedList.pollLast());
        System.out.println("After polling elements: " + linkedList);
    }
}
```

##### `ArrayList()` 和 `LinkedList()` 区别

- **`ArrayList`** 和 **`LinkedList`** 都实现了 `List` 接口，因此有许多相同的方法。

- **`LinkedList`** 还实现了 `Deque` 接口，因此有许多特有的方法，这些方法在 `ArrayList` 中不存在。

- **`ArrayList`** 在随机访问性能上优于 `LinkedList`，而 **LinkedList** 在频繁插入和删除操作时表现更优。

### `Set()`

#### `HashSet()`

哈希值

- 就是一个int类型的数值，Java中每个对象都有一个哈希值
- Java中的所有对象，都可以调用`Object`类提供的`hashCode`方法，返回该对象自己的哈希值

哈希表

`JDK 8`之前， 基于数组+链表

1. 创建一个默认长度为`16`的数组，默认加载因子为`0.75`， 数组名为`table`
2. 使用元素的哈希值对数组长度求余计算出应存入的位置
3. 判断当前位置是否为`null`， 如果是`null`直接存入
4. 如果不为null， 表示有元素，则调用`equals`方法比较
   - 若相等则不存入，否则存入
   - `JDK 8`之前，新元素存入数组，占老元素位置，老元素挂下面
   - `JDK 8`开始之后， 新元素直接挂在老元素下面

JDK 8开始之后，哈希表中引入了红黑树，进一步提高了操作数据的性能

**树**：

二叉树中，任意节点的度`<=2`

- 度：每一个节点的子节点数量
- 树高：树的总层数
- 根节点：最顶层的节点

二叉查找树（二叉排序树）

**规则**：小的存左边，大的存右边，一样的不存

平衡二叉树：左右子树的高度差不超过`1`

**红黑树**：可以自平衡的二叉树, 是一种增删改查性能都相对较好的结构 

从`JDK8`开始，当链表的长度超过`8`，且数组长度`>=64`时，自动将链表转成红黑树

```java
public static void main(String[] args) {
    // 创建一个 HashSet 对象，用于存储整数数据

    Set<Integer> set = new HashSet<>();

    // 往 HashSet 中添加整数元素
    // HashSet 会自动去重，不会存储重复的元素

    set.add(999); // 添加整数 999
    set.add(999); // 添加重复的整数 999，但由于 HashSet 不允许重复元素，此次添加无效
    set.add(777); // 添加整数 777

    // 打印 HashSet 中的元素
    // 输出结果：[777, 999]
    // HashSet 中的元素无序，并且已经去重了，只保留了一个 999
    System.out.println(set);
}

```

#### `LinkedHashSet`

```java
public static void main(String[] args) {
    // 创建一个 LinkedHashSet 对象，用于存储整数数据，并保持元素的插入顺序

    Set<Integer> set = new LinkedHashSet<>();

    // 往 LinkedHashSet 中添加整数元素
    // LinkedHashSet 会保持元素的插入顺序，并且会自动去重，不会存储重复的元素
    // 添加顺序：999, 000, 777, 888
    // 由于 LinkedHashSet 会保持插入顺序，因此打印时也会按照添加顺序输出

    set.add(999);
    set.add(000); // 注意：整数 000 在打印时会自动去掉前导零，变成 0
    set.add(777);
    set.add(888);
    set.add(999); // 重复元素不会被添加进 LinkedHashSet

    // 打印 LinkedHashSet 中的元素
    // 输出结果：[999, 0, 777, 888]
    // LinkedHashSet 中的元素按照插入顺序排列，并且去重了
    System.out.println(set);
}

```

### `TreeSet()`

基于红黑树实现排序，

```java
public static void main(String[] args) {
    // 创建一个 TreeSet 对象，用于存储整数数据，并按照自然顺序进行排序

    Set<Integer> set = new TreeSet<>();

    // 往 TreeSet 中添加整数元素
    // TreeSet 会自动去重，不会存储重复的元素
    // 添加顺序：999, 000, 777, 888
    // 由于 TreeSet 是有序的，它会根据自然顺序（从小到大）对元素进行排序

    set.add(999);
    set.add(000); // 注意：整数 000 在打印时会自动去掉前导零，变成 0
    set.add(777);
    set.add(888);
    set.add(999); // 重复元素不会被添加进 TreeSet

    // 打印 TreeSet 中的元素
    // 输出结果：[0, 777, 888, 999]
    // TreeSet 中的元素已经按照自然顺序排好序，并且去重了
    System.out.println(set);
}
```

对于自定义对象，确保存储的值相同就重复，重写hashCode方法

```java
    @Override
    public int hashCode() {
        return Objects.hash(name, age, height);//只要内容一样，哈希值就相同
    }
```

#### **自定义排序规则：**

- `TreeSet`集合存储自定义类型的对象时， 必须指定排序规则，支持如下两种方式来指定比较规则

方式一

- 让自定义的类（如学生类）实现`Comparable`接口，重写里面的`compareTo`方法来执行比较规则

```java
    @Override
    public int compareTo(Student o) {
        return this.age - o.age;
    }
```

方式二

- 通过调用`TreeSet`集合有参数构造器，可以设置`Comparator`对象（比较器对象，用于指定比较规则。）

```java
        // 就近选择自己自带的比较器进行比较
//        Set<Student> students = new TreeSet<>(new Comparator<Student>() {
//            @Override
//            public int compare(Student o1, Student o2) {
//                return Double.compare(o1.getHeight(), o2.getHeight());
//            }
//        });
        Set<Student> students = new TreeSet<>( (o1, o2) -> Double.compare(o1.getHeight(), o2.getHeight()));
```

### 集合的并发修改异常

```java
public static void main(String[] args) {
    // 创建一个字符串列表
    List<String> list = new ArrayList<>();
    list.add("小李子");
    list.add("利玛窦");
    list.add("胡风云");
    list.add("胡面上");
    list.add("胡椒粉");
    list.add("kennem");
    list.add("百事快");
    System.out.println(list);

    // 使用迭代器遍历列表并移除包含特定字符的元素
    Iterator<String> it = list.iterator();
    while (it.hasNext()) {
        String name = it.next();
        if (name.contains("胡")) {
            it.remove(); // 解决集合的并发修改异常
        }
    }
    System.out.println(list);

    // 使用普通for循环遍历列表并移除包含特定字符的元素
    // 通过维护索引，避免并发修改异常
    /*
    for (int i = 0; i < list.size(); i++) {
        String name = list.get(i);
        if (name.contains("胡")) {
            list.remove(name);
            i--; // 解决集合的并发修改异常
        }
    }
    System.out.println(list);
    */

    // 使用增强for循环遍历列表并移除包含特定字符的元素
    // 不推荐，会导致ConcurrentModificationException异常
    /*
    for (String s : list) {
        if (s.contains("胡")) {
            list.remove(s);
        }
    }
    System.out.println(list);
    */
}
```

## 可变参数

```java
public static void main(String[] args) {
    // 调用test方法，传入单个整数参数
    test(1);
    // 调用test方法，传入单个整数参数
    test(10);
    // 调用test方法，传入多个整数参数，其中最后一个参数为可变参数
    test(100, 10, 0);
    // 调用test方法，传入一个整数参数和一个整数数组参数
    test(1, new int[] {1, 2, 4, 4, 3, 3, 3, 3, 4, 3});
}

// 1. 一个形参列表中，只能有一个可变参数
// 2. 可变参数必须放在形参列表的最后面
public static void test(int age, int...nums){
    // 打印可变参数数组
    System.out.println(Arrays.toString(nums));
    // 打印可变参数数组的长度
    System.out.println(nums.length);
    // 打印分隔线
    System.out.println("----------");
}
```

## `Collections`

```java
public static void main(String[] args) {
    // 创建一个字符串列表
    List<String> names = new ArrayList<>();
    // 向列表中添加元素
    Collections.addAll(names, "Kennem", "张一","李三","王六");
    // 打印列表内容
    System.out.println(names);

    // 打乱列表中元素的顺序
    Collections.shuffle(names);
    // 打印打乱后的列表内容
    System.out.println(names);

    // 创建一个整数列表
    List<Integer> list = new ArrayList<>();
    // 向列表中添加元素
    list.add(3);
    list.add(4);
    list.add(1);

    // 对列表中的元素进行排序
    Collections.sort(list);
    // 打印排序后的列表内容
    System.out.println(list);

    // 创建一个学生对象列表
    List<Student> students = new ArrayList<>();
    // 创建学生对象并添加到列表中
    Student s1 = new Student("至尊宝", 21, 181.9);
    Student s2 = new Student("蜘蛛精", 21, 188);
    Student s3 = new Student("牛魔王", 20, 165.3);
    Student s4 = new Student("蝎子精", 20, 165.3);
    Student s5 = new Student("葫芦娃", 20, 165.3);
    Student s6 = new Student("至尊宝", 21, 181);
    students.add(s1);
    students.add(s2);
    students.add(s3);
    students.add(s4);
    students.add(s5);
    students.add(s6);
    // 打印学生列表内容
    System.out.println(students);

    // 使用Lambda表达式对学生列表按身高进行排序
    Collections.sort(students, (o1, o2) -> Double.compare(o1.getHeight(), o2.getHeight()));
    // 打印按身高排序后的学生列表内容
    System.out.println(students);
}
```

## `Map`

- `HashMap` ：无序、不重复、无索引；（用的最多）
- `LinkedHashMap`：由键决定的特点：**有序**，不重复，无索引
- `TreeMap`:**按照大小默认升序排序**，不重复，无索引

```java
public static void main(String[] args) {
    // 创建一个HashMap对象
    Map<String, Integer> map = new HashMap<>();
    // 向map中添加键值对
    map.put("手表", 100);
    map.put("手机", 20);
    map.put("Java", 120);
    map.put(null, null); // 允许键和值为null
    // 打印map的内容
    System.out.println(map);

    // 获取map的大小
    System.out.println(map.size());

    // 清空map
    // map.clear();

    // 打印清空后的map
    System.out.println(map);

    // 判断map是否为空
    System.out.println(map.isEmpty());

    // 获取指定键对应的值
    System.out.println(map.get("手机"));

    // 获取一个不存在的键对应的值
    System.out.println(map.get("666"));

    // 移除指定键对应的键值对，并返回对应的值
    System.out.println(map.remove("手机"));

    // 判断map中是否包含指定的键
    System.out.println(map.containsKey("手机"));
    System.out.println(map.containsKey("手机"));

    // 获取map中所有的键，并存储到Set集合中
    Set<String> keys = map.keySet();
    System.out.println(keys);

    // 获取map中所有的值，并存储到Collection集合中
    Collection<Integer> values = map.values();
    System.out.println(values);

    // 创建另一个HashMap对象
    Map<String, Integer> map1 = new HashMap<>();
    map1.put("Java1", 100);
    map1.put("Java2", 200);
    map1.put("Java3", 300);
    
    // 创建另一个HashMap对象
    Map<String, Integer> map2 = new HashMap<>();
    map2.put("Java3", 400);
    map2.put("Java4", 500);

    // 打印map1和map2的内容
    System.out.println(map1);
    System.out.println(map2);

    // 将map2中的键值对全部添加到map1中
    map1.putAll(map2);
    // 打印合并后的map1
    System.out.println(map1);   
}
```

### 遍历`Map`

```java
public class Main {
    public static void main(String[] args) {
        // 创建一个HashMap对象，键为String类型，值为Double类型
        Map<String, Double> map = new HashMap<>();
        
        // 向map中添加键值对
        map.put("蜘蛛精", 167.2); // 如果键已存在，则覆盖原有值
        map.put("蜘蛛精", 169.2);
        map.put("至尊宝", 164.2);
        map.put("牛魔王", 187.2);

        // 获取map中所有的键，并存储在Set集合中
        Set<String> keys = map.keySet();
        
        // 打印所有的键
        System.out.println(keys);
        
        // 遍历Set集合，获取每个键对应的值，并打印键值对
        for (String key : keys) {
            double value = map.get(key);
            System.out.println(key + "  " + map.get(key));
        }
    }
}
```

### `entry`遍历方式

```java
        // 获取map中所有的键值对，并存储在Set集合中
        Set<Map.Entry<String, Double>> entries = map.entrySet();
        
        // 遍历Set集合，获取每个键值对，并打印键值对
        for (Map.Entry<String, Double> entry : entries) {
            String key = entry.getKey();
            double value = entry.getValue();
            System.out.println(key + "----->" + value);
        }
```

`forEach`方式遍历

```java
        // 使用forEach方法遍历map，打印每个键值对
        map.forEach((k, v) -> {
            System.out.println(k + "--->" + v);
        });
```

### `LinkedHashMap`

类似`LinkedHashSet(),` 而且`LinkedHashSet()`底层使用的就是`linkedHashMap()`

```java
    public static void main(String[] args) {
        // 创建一个LinkedHashMap对象，键为Student类型，值为Integer类型
        Map<Student, Integer> lmap = new LinkedHashMap<>();
        
        // 向lmap中添加键值对，其中键为Student对象，值为Integer对象
        // 注意：Student类需要正确实现equals()和hashCode()方法以确保对象的唯一性
        lmap.put(new Student("Ken", 25, 150), 100);
        lmap.put(new Student("Mary", 23, 182), 123300);
        lmap.put(new Student("Bob", 21, 1180), 12300);
        lmap.put(new Student("Paul", 22, 130), 12);
        lmap.put(new Student("Sam", 15, 280), 120);
        lmap.put(new Student("Ken", 25, 150), 10); // 注意：与第一个Ken对象相同，但是值不同

        // 使用forEach方法遍历lmap，打印每个键值对
        lmap.forEach((k, v) -> {
            System.out.println(k + " " + v);
        });
    }
```

重写`equals`方法可以去除重复值

```java
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && Double.compare(height, student.height) == 0 && Objects.equals(name, student.name);
    }
```

### `TreeMap`

比较规则用重写`Comparable`接口，或者重写`Comparator`方法

```java
    public static void main(String[] args) {
        // 创建一个TreeMap对象，键为Student类型，值为Integer类型
        // 在构造TreeMap对象时，传入一个Comparator对象，用于按照学生身高进行排序
        Map<Student, Integer> tmap = new TreeMap<>((o1, o2) -> Double.compare(o1.getHeight(), o2.getHeight()));

        // 向tmap中添加键值对，其中键为Student对象，值为Integer对象
        // 注意：Student类需要正确实现equals()和hashCode()方法以确保对象的唯一性
        tmap.put(new Student("Ken", 25, 150), 100);
        tmap.put(new Student("Mary", 23, 182), 123300);
        tmap.put(new Student("Bob", 21, 1180), 12300);
        tmap.put(new Student("Paul", 22, 130), 12);
        tmap.put(new Student("Sam", 15, 280), 120);
        tmap.put(new Student("Ken", 25, 150), 10); // 与第一个Ken对象相同，但是值不同

        // 使用forEach方法遍历tmap，打印每个键值对
        tmap.forEach((k, v) -> {
            System.out.println(k + " " + v);
        });
    }
```

重写`Comparable`中的`compareTo`方法

```java
    @Override
    public int compareTo(Student o) {
        return this.age - o.age;
    }
```

### 集合的嵌套

```java
    public static void main(String[] args) {
        // 创建一个HashMap对象，键为String类型，值为List<String>类型
        Map<String, List<String>> map = new HashMap<>();
        
        // 创建第一个城市列表，存储江苏省的城市信息
        List<String> cities1 = new ArrayList<>();
        // 使用Collections.addAll方法向cities1中添加多个元素
        Collections.addAll(cities1, "南京市", "扬州市", "苏州市", "无锡市", "常州市");
        // 将键值对添加到map中，键为"江苏省"，值为cities1列表
        map.put("江苏省", cities1);

        // 创建第二个城市列表，存储湖北省的城市信息
        List<String> cities2 = new ArrayList<>();
        Collections.addAll(cities2, "武汉市", "孝感市", "十堰市", "宜昌市", "鄂州市");
        map.put("湖北省", cities2);

        // 打印整个map
        System.out.println(map);

        // 打印湖北省对应的城市列表
        System.out.println(map.get("湖北省"));
        
        // 使用增强for循环遍历第二个城市列表，打印其中的城市信息
        for (String s : cities2) {
            System.out.println(s);
        }

        // 使用forEach方法遍历map，打印每个省份及其对应的城市列表
        map.forEach((k, v) -> System.out.println(k + "--->" + v));
    }
```

## `Stream`

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.stream.Collectors;

public class Main {
    public static void main(String[] args) {
        // 创建一个字符串列表
        List<String> names = new ArrayList<>();
        // 向列表中添加多个元素
        Collections.addAll(names, "张某", "张五一", "张无忌", "张三丰", "Kennem");
        // 打印列表中的所有元素
        System.out.println(names);

        // 创建一个新的列表，用于存储以"张"开头且长度为3的字符串
        List<String> list = new ArrayList<>();
        // 使用增强for循环遍历原始列表
        for (String name : names) {
            // 如果字符串以"张"开头且长度为3，则添加到新列表中
            if (name.startsWith("张") && name.length() == 3) {
                list.add(name);
            }
        }
        // 打印新列表中的元素
        System.out.println(list);

        // 使用流进行筛选，获取以"张"开头且长度为3的字符串，然后收集到新的列表中
        List<String> list2 = names.stream()
                .filter(s -> s.startsWith("张")) // 筛选以"张"开头的字符串
                .filter(a -> a.length() == 3) // 筛选长度为3的字符串
                .collect(Collectors.toList()); // 收集到新的列表中
        // 打印新列表中的元素
        System.out.println(list2);
    }
}
```

### `ArrayList`, `HashSet`, `Map`的`Stream`流

```java
    public static void main(String[] args) {
        // 创建一个字符串列表
        List<String> names = new ArrayList<>();
        // 向列表中添加多个元素
        Collections.addAll(names, "张三丰", "张某人", "张某某", "张某", "张某某人");

        // 创建一个流，并对其中包含"某"的元素进行筛选并打印
        Stream<String> stream1 = names.stream();
        stream1.filter(s -> s.contains("某")).forEach(System.out::println);

        // 创建一个HashSet集合，并将多个元素添加到其中
        Set<String> set = new HashSet<>();
        Collections.addAll(set, "张三丰", "张某人", "张某某", "张某", "张某某人");

        // 创建一个流，并对其中包含"某"的元素进行筛选并打印
        Stream<String> stream2 = set.stream();
        stream2.filter(s -> s.contains("某")).forEach(System.out::println);

        // 创建一个HashMap对象，并向其中添加键值对
        Map<String, Double> map = new HashMap<>();
        map.put("张某", 165.3);
        map.put("Kennem", 181.2);
        map.put("Karl", 180.1);

        // 获取map中所有值的集合
        Collection<Double> values = map.values();

        // 创建一个流，并对其中包含"某"的键值对进行筛选并打印
        Set<Map.Entry<String, Double>> entries = map.entrySet();
        Stream<Map.Entry<String, Double>> stream3 = entries.stream();
        stream3.filter(e -> e.getKey().contains("某"))
                .forEach(e -> System.out.println(e.getKey() + "-->" + e.getValue()));

        // 创建一个字符串数组，并将多个元素添加到其中
        String[] names2 = {"张某人", "东方不败"};

        // 使用Arrays.stream方法创建一个流，并对其中的元素进行打印
        Stream<String> stream4 = Arrays.stream(names2);
        stream4.forEach(System.out::println);

        // 使用Stream.of方法创建一个流，并对其中的元素进行打印
        Stream<String> names21 = Stream.of(names2);
        names21.forEach(System.out::println);
    }
```

### 排序以及筛选

```java
    public static void main(String[] args) {
        // 创建一个Double类型的列表，存储考试成绩
        List<Double> scores = new ArrayList<>();
        Collections.addAll(scores, 88.5, 89.5, 90.0, 12.0, 1.0);

        // 使用流对考试成绩进行筛选，只打印及格的成绩
        scores.stream().filter(s -> s >= 60)
                .forEach(System.out::println);
        System.out.println("---------------");

        // 创建一个Student对象的列表，存储学生信息
        List<Student> students = new ArrayList<>();
        // 创建多个Student对象，并添加到列表中
        Student s1 = new Student("至尊宝", 21, 181.2);
        Student s2 = new Student("蜘蛛精", 21, 181.3);
        Student s3 = new Student("牛魔王", 20, 165.3);
        Student s4 = new Student("蝎子精", 20, 165.2);
        Student s5 = new Student("蜘蛛精", 22, 181.3);
        Collections.addAll(students, s1, s2, s3, s4, s5);

        // 使用流对学生信息进行筛选和排序，并打印结果
        students.stream().filter(s -> s.getAge() >= 10 && s.getAge() <= 30)
                .sorted((o1, o2) -> o2.getAge() - o1.getAge())
                .forEach(System.out::println);

        System.out.println("--------------");

        // 使用流对学生信息按身高进行排序，并打印身高最高的前三名学生
        students.stream().sorted(((o1, o2) -> Double.compare(o2.getHeight(), o1.getHeight())))
                .limit(3)
                .forEach(System.out::println);
        System.out.println("---------------");

        // 使用流对学生信息按身高进行排序，并打印身高最低的两名学生
        students.stream().sorted(((o1, o2) -> Double.compare(o2.getHeight(), o1.getHeight())))
                .skip(students.size() - 2)
                .forEach(System.out::println);

        System.out.println("-----------------");

        // 使用流对学生信息进行筛选，找出身高大于168的学生，并打印他们的姓名（去重）
        students.stream()
                .filter(s -> s.getHeight() > 168)
                .map(Student::getName)
                .distinct()
                .forEach(System.out::println);

        // 使用Stream.concat方法将两个流合并，然后打印合并后的结果
        Stream<Student> s11 = Stream.of(s1, s2);
        Stream<Student> s12 = Stream.of(s3, s4);
        Stream<Student> concat = Stream.concat(s11, s12);
        concat.forEach(System.out::println);
    }
```

### `Stream`终结方法

```java
    public static void main(String[] args) {
        // 创建学生列表
        List<Student> students = new ArrayList<>();
        // 创建学生对象并添加到列表中
        Student s1 = new Student("至尊宝", 21, 181.2);
        Student s2 = new Student("蜘蛛精", 21, 181.3);
        Student s3 = new Student("牛魔王", 20, 165.3);
        Student s4 = new Student("蝎子精", 20, 165.2);
        Student s5 = new Student("蜘蛛精", 21, 181.3);
        Collections.addAll(students, s1, s2, s3, s4, s5);

        // 使用流过滤身高大于168的学生，并将结果收集到列表中
        List<Student> res1 = students.stream()
                .filter(s -> s.getHeight() > 168)
                .collect(Collectors.toList());
        System.out.println(res1);

        // 使用流过滤身高大于168的学生，并将结果收集到集合中
        Set<Student> res2 = students.stream()
                .filter(s -> s.getHeight() > 168)
                .collect(Collectors.toSet());
        System.out.println(res2);

        // 使用流过滤身高大于168的学生，去除重复的学生对象，并将姓名和身高映射到Map中
        Map<String, Double> res3 =
                students.stream()
                        .filter(s -> s.getHeight() > 168)
                        .distinct()
                        .collect(Collectors.toMap(a -> a.getName(), a -> a.getHeight()));
        System.out.println(res3);

        // 使用流过滤身高大于168的学生，去除重复的学生对象，并将结果转换为对象数组
        Object[] res4 =
                students.stream()
                        .filter(s -> s.getHeight() > 168)
                        .distinct()
                        .toArray();
        System.out.println(Arrays.toString(res4));

        // 使用流过滤身高大于168的学生，去除重复的学生对象，并将结果转换为指定长度的学生数组
        Student[] res5 =
                students.stream()
                        .filter(s -> s.getHeight() > 168)
                        .distinct()
                        .toArray(len -> new Student[len]);
        System.out.println(Arrays.toString(res5));
    }
```



```
总结程序的知识点，并将知识点写到程序注释中去
```

