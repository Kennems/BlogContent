---
title : 'Java20天速成——进阶课程(1)'
date : 2024-04-15T22:30:13+08:00
lastmod: 2024-04-15T22:20:13+08:00
description : "Java学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Java学习笔记"]
tags : ["Java"]
# password : leetcode
---


## 进阶课程(1)

## OOP

### static

- 静态， 可以修饰成员变量，成员方法
- 成员变量按照有无`static`修饰，分为两种
  - 类变量 ： 有`static`修饰，属于类，**在计算机里只有一份**，会**被类的全部对象共享**
  - 实例变量（对象的变量）：无`static`修饰，**属于每个对象** 

```java
        // 类变量推荐赋值方式
        Student.name = "Java";
        // 不推荐赋值方式
        Student s1 = new Student();
        s1.name = "True Java";
        Student s2 = new Student();
        s2.name = "False Java";
        System.out.println(s2.name); //False Java
        System.out.println(Student.name); //False Java

		// 成员变量
        s1.age = 25;
        s2.age = 15;
        System.out.println(s1.age); //25
        System.out.println(s2.age); //15
```

```java
 //User类
	public static int number;
    public User(){
        number+=1; //类中访问自己的变量可以不写 User().
    }

//Test类
    public static void main(String[] args) {
        User u1 = new User();
        User u2 = new User();
        User u3 = new User();
        System.out.println(User.number); //3
    }
```

- **类方法**：有`static`修饰的成员方法，属于**类**。
- **实例方法**：无`static`修饰的成员方法，属于**对象**

```java
//Student
	double score;

    public static void printHelloWorld(){
        System.out.println("Hello World");
        System.out.println("Hello World");
    }
    public void printPass(){
        System.out.println("成绩：" + (score >= 60 ? "及格" : "不及格"));
    }
//Test
        Student.printHelloWorld();//Hello World Hello World
        Student s = new Student();
        s.score = 100;

        s.printPass(); //成绩：及格
```

### 类方法的常见应用案例

- 类方法最常见的应用场景是做工具类

**工具类**：工具类中的方法都是一些类方法，每个方法都是用来完成一个功能的，工具类是给开发人员共同使用的

可以提高代码复用性：调用方便，提高了开发效率

使用类方法、实例方法时的几点注意事项

- 类方法中可以直接访问**类的成员**，不可以直接访问**实例成员**
- 实例方法中既可以直接访问类成员，也可以直接访问实例成员
- 实例方法中可以出现`this`关键字，**类方法中不可以出现`this`关键字**

### 代码块

- **静态代码块**
  - 格式 ： `static{ }`
  - 特点：**类加载时自动执行，由于类只会加载一次，所以静态代码块只会执行一次**
  - 作用：完成类的初始化，例如：对类变量的初始化赋值
- **实例代码块：**
  - 格式：`{ }`
  - 特点：**每次创建对象时**，**执行实例代码块**，**并在构造器前执行**
  - 作用：和构造器一样，都是用来完成对象的初始化的，例如：对实例变量进行初始化赋值

### 单例设计模式

**设计模式（Design pattern）** 就是解决问题的最优解。

单例设计模式：**确保一个类只有一个对象**

**饿汉式单例类**

- 把类的构造器**私有**
- 定义一个类变量记为类的一个对象
- 定义一个类方法，返回对象

```java
public class A {
    private static A a = new A();
    private A(){

    }
    public static A getObject(){
        return a;
    }
}
```

### 继承

继承的好处：减少重复代码的编写

```java
public class people {
    private String name;
    public String getName(){
        return this.name;
    }
    public void setName(String name){
        this.name = name;
    }
}

public class teacher extends people{
    private String skill;

    public String getSkill() {
        return skill;
    }

    public void setSkill(String skill) {
        this.skill = skill;
    }

    public void printInfo(){
        System.out.println(getName() + "具备的技能 ： " + skill);
    }
}

public class Test {
    public static void main(String[] args) {
        teacher t1 = new teacher();
        t1.setName("Kennem");
        t1.setSkill("Java");
        System.out.println(t1.getName()); //Kennem
        System.out.println(t1.getSkill()); //Java

        t1.printInfo(); //Kennem具备的技能 ： Java
    }
}
```

继承的注意事项：

1. 权限修饰符
2. 单继承，`Object`类
3. 方法重写
4. 子类中访问其他成员的特点
5. 子类构造器的特点
6. 注意事项的小结

|   修饰符    | 本类中 | 同一个包下的其他类继承 | 任意包下的子类继承 | 任意包下的任何类继承 |
| :---------: | :----: | :--------------------: | :----------------: | :------------------: |
|  `private`  |   ✓    |                        |                    |                      |
|   `缺省`    |   ✓    |           ✓            |                    |                      |
| `protected` |   ✓    |           ✓            |         ✓          |                      |
|  `public`   |   ✓    |           ✓            |         ✓          |          ✓           |

**注意**：用`protected`修饰，只能在子类中访问，而不是子类的对象访问

#### 单继承

`Java`不支持多继承，支持多层继承

#### `Object`类

`Object`类是任何类的父类。

#### 方法重写

重写注意事项

- 使用`Override`注解

  - ```java
        @Override
        public void print(){
            System.out.println("88888888");
        }
        @Override
        public void print2(){
            System.out.println("8");
        }
    ```

- 子类复写父类方法时，**访问权限必须大于或者等于父类该方法的权限**（`publc -> protected -> 缺省`）

- 重写的方法返回值类型，必须与被重写方法的**返回值类型一致**，**或者范围更小**

- **私有**方法、**静态**方法**不能被重写**

声明不变，重新实现

**toString()方法重写**

```java
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
```

子列构造器的特点：

- 子类的全部构造器，都会**先调用父类的构造器**，再执行自己的构造器。
- 子类构造器是符合实现调用父类构造器的：
  - 默认情况下，子类全部构造器的第一行**都是`super()`（默认都有）**，它会调用父类的**无参数构造器。**
  - 如果父类没有无参数构造器，则我们必须再子类构造器的第一行手写`super(...)`， 指定去调用父类的有参数构造器。

```java
class Student extends People{
    private String skill;
    public Student(String name, int age, String skill){
        super(name, age); //调用父类的有参构造器 父类中name和age构造器
        this.skill = skill;
    }

    public String getSkill() {
        return skill;
    }

    public void setSkill(String skill) {
        this.skill = skill;
    }
}
```

**补充知识**：`this(...)`调用兄弟构造器

- 任意类的构造器中，是可以通过`this(...)`去调用该类的其他构造器的。
- `this(...)` 和 `super(...)` 使用时的注意事项
  - `this(...)`、`super(...)`都只能放在构造器的第一行，因此，有了`this(...)`就不能写`super(...)`了，反之亦然

```java
    public Boy(String name, int age) {
        this(name, age, "黑马程序员");
    }
    public Boy(String name, int age, String schoolName) {
        this.name = name;
        this.age = age;
        this.schoolName = schoolName;
    }
```

### 多态

多态是在`继承`/`实现`情况下的一种现象，表现为：对象多态，行为多态。

- 在多态形式下，右边对象是解耦合的，更偏于扩展和维护。

```java
public class People {
    public String name = "我是人";
    public void run(){
        System.out.println("人可以跑");
    }
}
public class Student extends People{
    public String name = "我是学生";
    @Override
    public void run() {
        System.out.println("学生跑得很快！");
    }
    public void test(){
        System.out.println("学生要考试...");
    }
}
public class Teacher extends People{
    public String name = "我是老师";
    @Override
    public void run() {
        System.out.println("老师跑的气喘吁吁！");
    }
}

public class Test {
    public static void main(String[] args) {
        People p = new People();
        System.out.println(p.name);
        People p1 = new Student();
        System.out.println(p1.name);
        p1.run(); //编译看父类， 运行看子类
        People p2 = new Teacher();
        System.out.println(p2.name);
        p2.run();
    }
}
```

```java
        if(p instanceof Student){
            Student s = (Student) p;
            s.test();
        }else if(p instanceof Teacher){
            Teacher t = (Teacher) p;
            t.work();
        }
```

```java
public class People {
    public String name = "我是人";
    public void run(){
        System.out.println("人可以跑");
    }
}
public class Student extends People {
    public String name = "我是学生";
    @Override
    public void run() {
        System.out.println("学生跑得很快！");
    }
    public void test(){
        System.out.println("学生要考试");
    }
}
public class Teacher extends People {
    public String name = "我是老师";
    @Override
    public void run() {
        System.out.println("老师跑的气喘吁吁！");
    }
    public void work(){
        System.out.println("老师要上班");
    }
}
```

#### final

- `final`关键字是最终的意思，可以修饰（类， 方法， 变量）
- **修饰类**：该类被成为最终类，特点是**不能被继承了**
- **修饰方法**：该方法被称为最终方法，特点是**不能被重写了**
- **修饰变量**：该变量**只能被赋值一次**

```java
public class Test {

    // 3.常量，建议名称全部大写，多个单词下划线连接
    public static final String CURRENT_NAME = "Kennem";
    public static void main(String[] args) {
        //
        final double pi = 3.14;
        //pi = 3.1;  报错，final修饰的变量不能修改
        final int[] arr = {11, 22, 33};
        arr[1] = 13; // 4.final修饰的地址不能变，但地址内的内容可以变


    }
}
final class A{ // 1.final 修饰类，类不能被继承了

}
// 2.final 修饰方法，方法不能被重写了
class C{
    public final void test(){

    }
}

class D extends C{

}
```

##### 常量

- 使用了`static final`修饰的成员变量被称为**常量**

- 作用：通常用于记录系统的配置信息

**使用常量记录系统配置信息的优势，执行原理**

- 代码可读性更好，可维护性也更好
- 程序编译后，常量会被"**宏替换**"；出现常量的地方全部会被替换成其记住的字面量，这样可以保证使用常量和直接用字面量的性能是一样的。

### 抽象类

```java
public abstract class A {
    private String name;
    public static String schoolName;
    public abstract void run();
}

public class B extends A{
    @Override
    public void run() {
        System.out.println("run");
    }
}
```

**抽象类的好处：**

- 父类知道每个子类都要做某个行为，但每个子类要做的情况不一样，父类就定义成抽象方法，交给子类去重写实现，我们设计这样的抽象类，就是为了更好的支持多态。

抽象类可以制作**模板类**

#### 模板类

```java
public abstract class People {
    public abstract String write();
    public final void WriteOn(){
        System.out.println("\t\t今天是2023/04/14");
        System.out.println("\t身份:");
        System.out.println(write());
        System.out.println("以上就是我发表的内容");
        System.out.println("\t\t感谢大家！");
    }
}
public class Teacher extends People {
    @Override
    public String write() { //只用重写每个类中需要不同实现的方法即可
        return "我是老师，这里是老师类";
    }
}
public class Student extends People {
    @Override
    public String write() {
        return "我是学生，这里是学生类";
    }
}
public class Test {
    public static void main(String[] args) {
        Teacher t = new Teacher();
        t.WriteOn();

        Student s = new Student();
        s.WriteOn();
    }
}
```

建议使用`final`关键字修饰模板类

- 模板方法是给对象直接使用的不能被修改
- 一旦子类重写了模板方法，模板方法就失效了

### 接口

- Java提供了一个关键字`interface`

```java
public class Test {
    public static void main(String[] args) {
        Driver d = new A();
        d.drive();
        
    }
}

class A extends Student implements Driver, Singer{

    @Override
    public void drive() {

    }

    @Override
    public void sing() {

    }
}


class Student{

}

interface Driver{
    void drive();
}

interface Singer{
    void sing();
}
```

**接口的好处**：

- 可以解决类单继承的问题，通过接口， 可以让一个类有一个继承父类的同时，**还可以去找多个接口去拓展自己的功能**
- 因为通过接口可以显性的知道你是谁
- 一个类可以实现多个接口，同样，一个接口也可以被多个类实现，这样的好处是我们的程序可以面向接口编程了，这样程序员就可以很方便的灵活切换各种业务实现了。

**接口的综合案例:**

使用接口`(StudentOperator)`之后，在不同的继承接口的类`(StudentOperatorImpl1, StudentOperatorImpl2)`里面编写不同的程序，用接口对象实现操作时，仅需要改变不同的实现类即可。

#### 接口的其他方法

```java
public class Student {
    private String name;
    private char sex;
    private int score;

    public Student() {
    }
}
public interface StudentOperator {
    void printInfo(ArrayList<Student> students);
    void printAverageScore(ArrayList<Student> students);
}
public class StudentOperatorImpl1 implements StudentOperator{

    @Override
    public void printInfo(ArrayList<Student> students) {
        System.out.println("==全班全部信息如下==");
        for (int i = 0; i < students.size(); i++) {
            Student s = students.get(i);
            System.out.println("姓名为 ： " + s.getName() + ", 性别为 ： " + s.getSex() + ", 得分为 ： " + s.getScore());
        }
        System.out.println("-------------------");
    }

    @Override
    public void printAverageScore(ArrayList<Student> students) {
        double total = 0;
        for (int i = 0; i < students.size(); i++) {
            Student s = students.get(i);
            total += s.getScore();
        }
        System.out.println("班级平均分为 ： " + (total/(students.size())));

    }
}
public class StudentOperatorImpl2 implements StudentOperator{

    @Override
    public void printInfo(ArrayList<Student> students) {
        System.out.println("==全班全部信息如下==");
        int count1 = 0;
        for (int i = 0; i < students.size(); i++) {
            Student s = students.get(i);
            System.out.println("姓名为 ： " + s.getName() + ", 性别为 ： " + s.getSex() + ", 得分为 ： " + s.getScore());
            if(s.getSex()=='男'){
                count1+=1;
            }
        }
        System.out.println("男生人数为 ： " + count1 + ", 女生人数为 ： " + (students.size()-count1));
        System.out.println("班级总人数为 : " + (students.size()));
        System.out.println("-------------------");
    }

    @Override
    public void printAverageScore(ArrayList<Student> students) {
        double total = 0;
        double max = students.get(0).getScore();
        double min = students.get(0).getScore();
        for (int i = 0; i < students.size(); i++) {
            Student s = students.get(i);
            total += s.getScore();
            if(s.getScore()>max){
                max = s.getScore();
            }
            else if(s.getScore()<min){
                min = s.getScore();
            }
        }
        System.out.println("学生的最高分为 ： " + max + ", 学生的最低分为" + min);
        System.out.println("去除最低分和最高分后，班级平均分为 ： " + (total/(students.size())));
    }
}
public class ClassManagement {
    private ArrayList<Student> students = new ArrayList<>();
    StudentOperator so = new StudentOperatorImpl2();

    public ClassManagement() {
        students.add(new Student("小明", '男', 99));
        students.add(new Student("小黄", '女', 100));
        students.add(new Student("小白", '男', 94));
        students.add(new Student("小亮", '女', 90));

    }

    public void printInfo(){
        so.printInfo(students);
    }
    public void printScore(){
        so.printAverageScore(students);
    }
}
public class Test {
    public static void main(String[] args) {
        ClassManagement cm = new ClassManagement();
        cm.printInfo();
        cm.printScore();
    }
}
```

**接口的其他方法：**

```java
    /** 1. 默认方法：必须使用 default 修饰，默认会被 public 修饰
     *
     */
    default void test1(){
        System.out.println("===默认方法===");
        test2();
    }
    /** 2. 私有方法：必须使用 private 修饰，（JDK9开始支持）
     * 实例方法对象的方法
     */
    private void test2(){
        System.out.println("===私有方法===");
    }

    /**
     * 3. 静态方法，必须使用 static 修饰，默认会被 public 修饰
     */
    public static void test3(){
        System.out.println("===静态方法===");
    }
```

#### 接口的多继承

一个接口可以同时继承多个接口

1. 一个接口继承多个接口，如果多个接口中存在方法名冲突，则此时不支持多继承

   - ```java
     interface I{
         void test1();
     }
     interface J{
         void test1();
     }
     interface C implements I, J{ //报错
     
     
     }
     ```

2. 一个类实现多个接口，如果多个接口存在方法名冲突，则此时不支持多实现

   - ```java
     interface I{
         void test1();
     }
     interface J{
         void test1();
     }
     class C implements I, J{ //报错
         
     }
     ```

3. 一个类继承了父类，又实现了接口，父类中和接口中有同名的默认方法，实现类会优先使用父类的

   - ```java
     class Fu{
         public void run(){
             System.out.println("===父类的run方法执行了");
         }
     }
     interface IT{
         default void run(){
             System.out.println("===接口IT中的run方法执行了");
         }
     }
     
     class Zi extends Fu implements IT{
     
     }
     public class Demo2 {
         public static void main(String[] args) {
             Zi zi = new Zi();
             zi.run(); //===父类的run方法执行了
         }
     }
     ```

4. 一个类实现多个接口，多个接口中存在同名的默认方法，可以不冲突， 这个类重写该方法即可

   - ```java
     interface I{
         void test1();
     }
     interface J{
         void test1();
     }
     //interface C implements I, J{ //报错
     //
     //}
     class C implements I, J{
         @Override
         public void test1() {
     
         } //重写后不会报错
     
     }
     ```

### 内部类

#### 成员内部类

```java
public class Outer {
    private String outerName;
    private int age=88;
    public static int outerNumber;
    public class Inner{
        private String name;
        public static int number; // JDK16才开始支持
        private int age=99;
        public void test(){

        }
        public void test2(){
            System.out.println(outerName);
            System.out.println(outerNumber);

            int age = 66;
            System.out.println(age);
            System.out.println(this.age);
            System.out.println(Outer.this.age);
        }

        public Inner() {
            System.out.println("Inner has been created.");
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public static int getNumber() {
            return number;
        }

        public static void setNumber(int number) {
            Inner.number = number;
        }
    }
}
    public static void main(String[] args) {
        Outer.Inner in = new Outer().new Inner();
        in.test2();
        /**
         * Inner has been created.
         * null
         * 0
         * 66
         * 99
         * 88
         */
    }
```

#### 静态内部类

可以直接访问外部类的**静态变量**，而不能访问外部类的**实例变量**

```java
public class StaticInner {
    private String name; // 实例变量，只能由外部对象来访问
    public static int age; // 静态变量，可以由类名直接访问

    // 静态内部类
    public static class Inner {
        // 在静态内部类中访问外部类的静态变量是合法的
        public void test() {
            System.out.println(age);
            // 无法在静态内部类中直接访问外部类的实例变量，会导致编译错误
            //System.out.println(name);
        }
    }
}

```

**局部内部类**：定义在方法，代码块，构造器中。

#### 匿名内部类

创建一个类的实例的一种方式，同时**定义**并**实现**该类。

```java
public class Demo1 {
    public static void main(String[] args) {
        Animal cat = new Animal(){ //cat类可以不用单另新创建，实现的同时定义该类即可
            @Override
            public void cry() {
                System.out.println("猫喵喵喵的叫~~");
            }
        };
        cat.cry();
    }
}

// 用抽象方法实现匿名内部类
abstract class Animal{
    public abstract void cry();
}
```

```java
public class Demo2 {
    public static void main(String[] args) {
        go(new Swimming() {
            @Override
            public void swim() {
                System.out.println("狗游的很快！");
            }
        });
    }
    public static void go(Swimming s){
        System.out.println("===GO====");
        s.swim();
    }
}

// 用接口实现匿名内部类
interface Swimming{
    void swim();
}
```

```java
public class Demo3 {
    public static void main(String[] args) {
        JFrame win = new JFrame("登录界面");
        JPanel panel = new JPanel();
        win.add(panel);
        JButton btn = new JButton("登录");
        panel.add(btn);


        //匿名内部类的实际使用场景
        btn.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                JOptionPane.showConfirmDialog(win, "你点击了登录按钮");
            }
        });

        win.setSize(400, 800);
        win.setLocationRelativeTo(null);
        win.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
        win.setVisible(true);
    }
}
```

### 枚举

枚举是一种特殊的类

- 枚举类的第一行，只能写一些合法的标识符（名称）， 多个名称用逗号隔开
- 这些名称，**本质是常量**，每个常量都会记住枚举类的一个对象

```java 
public enum A {
    X, Y, Z;
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

public class Test  {
    public static void main(String[] args) {
        A a1 = A.X;
        System.out.println(a1); //X

        A a2 = A.Y;
        A[] as = A.values();
        System.out.println(as); //[Lcom.showguan.Enum.A;@3b07d329
        A a3 = A.valueOf("Z");
        System.out.println(a3); //Z
        System.out.println(a3.ordinal()); //2 次序
    }
}
```

**枚举类的特点**：

- 枚举类的第一行只能罗列一些名称，这些名称都是常量，并且每个常量记住的都是枚举类的一个对象
- 枚举都是最终类，**不可以被继承**
- 枚举类中，从第二行开始，可以定义类的其他各种成员
- 编译器为枚举类新增了几个方法，并且枚举类都是继承 ：`java.lang.Enum`类的，从`enum`类也会继承到一些方法 

 枚举的实际作用：

```java
public enum Constant2 {
    BOY, GIRL;

}
    public static void check(Constant2 sex) {
        switch (sex) {
            case BOY:
                System.out.println("你是个男孩！");
                break;
            case GIRL:
                System.out.println("你是个女孩！");
                break;
        }
    }
    
    public static void main(String[] args) {
        check(Constant2.BOY);
    } 
```

### 泛型

- 定义类，接口，方法时，同时声明一个或者多个类型变量（如`<E>`)，成为泛型类，泛型接口，泛型方法，它们统称为泛型。

```java
public class Test {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("Java1");
        list.add("Java2");
        list.add("Java3");
        list.add(new Cat());
        for (int i = 0; i < list.size(); i++) {
//            Object o = list.get(i);
//            String s = (String) list.get(i); //报错 Cat()对象时Object类型，不能转换成String类型
//            System.out.println(s);
        }
        ArrayList<String> list2 = new ArrayList(); //泛型为String
        list2.add("Java1");
        list2.add("Java2");
        list2.add("Java3");
       // list2.add(new Cat()); //在编译阶段就限制添加对象的类型
        ArrayList<Cat> list3 = new ArrayList(); //泛型为Cat()
    }
}
```

拓展:

```java
public class MyArrayList<E, T>  //可以多个类型
public class MyArrayList<E, T extends Animal>  //指定类型需要继承某个类
```

#### 泛型接口

```java
public interface Data<E>{
    void get(E e);

    ArrayList<E> getName(String name);
}
public class TeacherData implements Data<Teacher>{ //指定泛型类型后，重写的方法都会自动换成该类型
    @Override
    public void get(Teacher teacher) {

    }

    @Override
    public ArrayList<Teacher> getName(String name) {
        return null;
    }
}
```

使用通配符 `?`

```java
    public static void main(String[] args) {
        System.out.println(test("Java"));
        Dog d  = test(new Dog());


        ArrayList<Car> cars = new ArrayList<>();
        cars.add(new Car());
        cars.add(new Car());
        cars.add(new Car());
        go(cars);

        ArrayList<BMW> bmws = new ArrayList<>();
        bmws.add(new BMW());
        bmws.add(new BMW());
        bmws.add(new BMW());
        bmws.add(new BMW());
        go(bmws);

        ArrayList<BENZ> benzs = new ArrayList<>();
        benzs.add(new BENZ());
        benzs.add(new BENZ());
        benzs.add(new BENZ());
        go(benzs);
    }
    public static <T> T test(T t){
        return t;
    }
    public static void go(ArrayList<? extends Car> cars){

    }
}
```

## API

### `toString()`方法和`equals()`

```java
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && Objects.equals(name, student.name);
    }
```

### `clone()`

```java
    public static void main(String[] args) throws CloneNotSupportedException {
        Student s1 = new Student("Kennem", 21, new double[]{0.0});
        System.out.println(s1.toString());
        System.out.println(s1.getName());
        System.out.println(s1.getAge());
        System.out.println(s1);
        System.out.println(s1.getClass());
        System.out.println(s1.getScores());
        /**
         * Student{name='Kennem', age=21}
         * Kennem
         * 21
         * Student{name='Kennem', age=21}
         * class com.showguan.APIObject.Student
         * [D@27d6c5e0
         */

        Student s2 = new Student("Kennem", 21, new double[]{0.0});
        System.out.println(s1.equals(s2)); //true

        Student s3 = (Student) s1.clone();
        System.out.println(s3.getName());
        System.out.println(s3.getAge());
        System.out.println(s3);
        System.out.println(s3.getClass());
        System.out.println(s3.getScores());
        /**
         * Kennem
         * 21
         * Student{name='Kennem', age=21}
         * class com.showguan.APIObject.Student
         * [D@4f3f5b24
         */

    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Student tmp = (Student) super.clone();
        tmp.scores = tmp.scores.clone();
        return tmp;
    }
```

`Objects.`

```java
    public static void main(String[] args) {
        String s1 = "Java";
        String s2 = null;
        System.out.println(s1.equals(s2));
//        System.out.println(s2.equals(s1)); //空指针异常
        System.out.println(Objects.equals(s1, s2)); //防止空指针异常

        System.out.println(Objects.isNull(s1)); //false
        System.out.println(Objects.nonNull(s1)); //true
        System.out.println(Objects.isNull(s2)); //true
        System.out.println(Objects.nonNull(s2)); //false
    }
```

### 包装类

```java
    public static void main(String[] args) {
        Integer a = Integer.valueOf(12);
        Integer b = 12;
        int c = b;

        ArrayList<Integer> arr = new ArrayList<>();
        arr.add(12);
        arr.add(10);
        arr.get(1);


        Integer d = 25;
        String s1 = Integer.toString(d);
        System.out.println(s1);

        String s2 = d.toString();

        String s3 = d + "";
        System.out.println(s2);
        System.out.println(s3);


        String s4 = "29";
//        int age = Integer.parseInt(s4);
        int age = Integer.valueOf(s4);
        System.out.println(age);

        String s5 = "99.9";
//        double score = Double.parseDouble(s5);
        double score = Double.valueOf(s5);
        System.out.println(score+0.1);
    }
```

### `StringBuilder`

适合多次拼接，修改

```java
    public static void main(String[] args) {
        StringBuilder s = new StringBuilder();
        s.append("Java");
        s.append("666").append("888").append("999");
        System.out.println(s);
        s.reverse();
        System.out.println(s);
        System.out.println(s.length());
        String ss = s.toString();
        System.out.println(ss);
        /**
         * Java666888999
         * 999888666avaJ
         * 13
         * 999888666avaJ
         */
    }
```

`StringBuffer`方法和`StringBuilder`类似，但是是线程安全的，而`StringBuilder`是线程不安全的

### `StringJoiner`

```java
        StringJoiner sj = new StringJoiner(",", "[", "]");
        sj.add("1");
        sj.add("2");
        sj.add("3");
        sj.add("4");
        System.out.println(sj);
```

### `System`

```java
public class Main {
    public static void main(String[] args) {
        // 主函数入口

        // 以下代码块被注释掉，使用 System.exit(0) 可以立即终止程序的执行
//        System.exit(0);

        // 打印分隔线
        System.out.println("----------------");

        // 以下代码块被注释掉，用于打印当前时间的毫秒表示
//        System.out.println(System.currentTimeMillis());

        // 记录程序开始时间
        long t1 = System.currentTimeMillis();

        // 初始化一个空字符串
        String s = "";

        // 循环将字符串"abc"追加到字符串s中，重复200000次
        for (int i = 0; i < 200000; i++) {
            s += "abc";
        }

        // 记录程序结束时间
        long t2 = System.currentTimeMillis();

        // 计算程序执行时间并将结果以秒为单位打印出来
        System.out.println((t2 - t1) / 1000.0 + "s"); //4.656s
    }
}
```

### `BigDecimal`

```java
public class Main {
    public static void main(String[] args) {
        // 主函数入口

        // 使用双精度浮点数进行运算
        double a = 0.1;
        double b = 0.2;
        double c = a + b; // 直接相加
        System.out.println(c); // 输出结果

        // 使用 BigDecimal 类进行精确运算
        BigDecimal a1 = BigDecimal.valueOf(a); // 将双精度浮点数转换为 BigDecimal 类型
        BigDecimal b1 = BigDecimal.valueOf(b);
        
        // 使用 BigDecimal 的 multiply 方法进行乘法运算
        BigDecimal c1 = a1.multiply(b1); 
        System.out.println(c1); // 输出结果

        // 使用 BigDecimal 的 add 方法进行加法运算
        BigDecimal c2 = a1.add(b1);
        System.out.println(c2); // 输出结果

        // 使用 BigDecimal 的 subtract 方法进行减法运算
        BigDecimal c3 = a1.subtract(b1);
        System.out.println(c3); // 输出结果

        // 使用 BigDecimal 的 divide 方法进行除法运算
        BigDecimal c4 = a1.divide(b1); 
        System.out.println(c4); // 输出结果

        // 使用 BigDecimal 进行除法运算，并设置精确度和舍入模式
        BigDecimal i = BigDecimal.valueOf(0.1);
        BigDecimal j = BigDecimal.valueOf(0.3);
        BigDecimal k = i.divide(j, 2, RoundingMode.HALF_UP); // 设置精确度为2位小数，采用四舍五入模式
        System.out.println(k); // 输出结果，0.33
    }
}
```

### `Date`

`parse`方法和`format`方法

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateFormatExample {
    public static void main(String[] args) {
        // 创建一个日期对象
        Date d = new Date();

        // 定义日期格式
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss EEE a");

        // 格式化当前日期并输出
        String rs = sdf.format(d);
        System.out.println("当前日期格式化输出: " + rs);

        // 再次格式化当前日期并输出（可以省略此步骤，仅为展示）
        String rs2 = sdf.format(d);
        System.out.println("再次格式化当前日期: " + rs2);

        // 将字符串解析为日期对象
        String dateStr = "2022-12-12 12:12:11";
        SimpleDateFormat sdf2 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        try {
            Date d2 = sdf2.parse(dateStr);
            System.out.println("解析后的日期: " + d2);
        } catch (ParseException e) {
            e.printStackTrace();
        }
    }
}

```

```java
    public static void main(String[] args) throws ParseException {
        String start = "2024年12月12日 00:00:00";
        String end = "2024年12月12日 00:10:00";
        String xj = "2024年12月12日 00:01:18";
        String xp = "2024年12月12日 00:10:57";
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
        Date startDt = sdf.parse(start);
        Date endDt = sdf.parse(end);
        Date xjDt = sdf.parse(xj);
        Date xpDt = sdf.parse(xp);

        long startTime = startDt.getTime();
        long endTime = endDt.getTime();
        long xjTime = xjDt.getTime();
        long xpTime = xpDt.getTime();


        if(xjTime>=startTime && xjTime<=endTime){
            System.out.println("小贾你秒杀成功了");
        }else{
            System.out.println("小贾你秒杀失败了");
        }

        if(xpTime>=startTime && xpTime<=endTime){
            System.out.println("小皮你秒杀成功了");
        }else{
            System.out.println("小皮你秒杀失败了");
        }
    }
```

### `Calendar`

```java
public class Main {
    public static void main(String[] args) {
        // 获取当前日期和时间的 Calendar 对象
        Calendar now = Calendar.getInstance();
        System.out.println("当前日期和时间：" + now);

        // 获取当前年份
        int year = now.get(Calendar.YEAR);
        System.out.println("当前年份：" + year);

        // 获取一年中的第几天
        int days = now.get(Calendar.DAY_OF_YEAR);
        System.out.println("一年中的第几天：" + days);

        // 获取Date对象
        Date d = now.getTime();
        System.out.println("Date对象：" + d);

        // 获取时间戳
        long time = now.getTimeInMillis();
        System.out.println("时间戳：" + time);

        // 设置月份为4月，日期为4月1日
        now.set(Calendar.MONTH, Calendar.APRIL);
        now.set(Calendar.DAY_OF_MONTH, 1);
        System.out.println("设置后的日期：" + now.getTime());

        // 获取设置后的日期
        Date d1 = now.getTime();
        System.out.println("设置后的Date对象：" + d1);

        // 增加105天
        now.add(Calendar.DAY_OF_YEAR, 105);
        Date d2 = now.getTime();
        System.out.println("增加105天后的日期：" + d2);
    }
}
```

`JDK`之后新增的时间

- 设计更合理，功能丰富，使用更方便
- 都是不可变对象，修改后会返回新的实践对象，不会丢失最开始的时间
- 线程安全
- 能精确到毫秒、纳秒

###  `LocalDate`， `LocalTime`, `LocalDateTime`

#### `LocalDate`

```java
    public static void main(String[] args) {
        // 获取当前日期
        LocalDate ld = LocalDate.now();
        System.out.println(ld);

        // 获取日期的年、月、日、年中的第几天、星期几等信息
        int year = ld.getYear();
        int month = ld.getMonthValue();
        int day = ld.getDayOfMonth();
        int dayOfYear = ld.getDayOfYear();
        int dayOfWeek = ld.getDayOfWeek().getValue();
        
        System.out.println(year);
        System.out.println(month);
        System.out.println(day);
        System.out.println(dayOfYear);
        System.out.println(dayOfWeek);

        // 使用 with 方法设置日期的年、月
        LocalDate ld2 = ld.withYear(2077);
        LocalDate ld3 = ld.withMonth(11);
        System.out.println(ld2);
        System.out.println(ld3);

        // 使用 plus 方法增加日期的天数、月数
        LocalDate ld4 = ld.plusDays(2);
        LocalDate ld5 = ld.plusMonths(3);

        // 使用 minus 方法减少日期的天数、月数
        LocalDate ld6 = ld.minusDays(10);
        LocalDate ld7 = ld.minusMonths(2);

        // 使用 of 方法创建指定日期的 LocalDate 对象
        LocalDate ld8 = LocalDate.of(2077, 12, 12);

        // 使用 equals、isAfter、isBefore 方法比较日期
        System.out.println(ld5.equals(ld6));
        System.out.println(ld5.isAfter(ld6));
        System.out.println(ld5.isBefore(ld6));
    }
```

#### `LocalTime`

```java
    public static void main(String[] args) {

        // 0、获取本地时间对象
        LocalTime lt = LocalTime.now(); // 获取当前时间
        System.out.println(lt);

        // 1、获取时间中的信息
        int hour = lt.getHour(); // 获取小时
        int minute = lt.getMinute(); // 获取分钟
        int second = lt.getSecond(); // 获取秒
        int nano = lt.getNano(); // 获取纳秒

        // 2、修改时间：withHour、withMinute、withSecond、withNano
        LocalTime lt3 = lt.withHour(10); // 设置小时为10
        LocalTime lt4 = lt.withMinute(10); // 设置分钟为10
        LocalTime lt5 = lt.withSecond(10); // 设置秒为10
        LocalTime lt6 = lt.withNano(10); // 设置纳秒为10

        // 3、加多少：plusHours、plusMinutes、plusSeconds、plusNanos
        LocalTime lt7 = lt.plusHours(10); // 增加10小时
        LocalTime lt8 = lt.plusMinutes(10); // 增加10分钟
        LocalTime lt9 = lt.plusSeconds(10); // 增加10秒
        LocalTime lt10 = lt.plusNanos(10); // 增加10纳秒

        // 4、减多少：minusHours、minusMinutes、minusSeconds、minusNanos
        LocalTime lt11 = lt.minusHours(10); // 减少10小时
        LocalTime lt12 = lt.minusMinutes(10); // 减少10分钟
        LocalTime lt13 = lt.minusSeconds(10); // 减少10秒
        LocalTime lt14 = lt.minusNanos(10); // 减少10纳秒

        // 5、获取指定时间的LocalTime对象：
        // public static LocalTime of(int hour, int minute, int second)
        LocalTime lt15 = LocalTime.of(12, 12, 12); // 创建指定时间的LocalTime对象
        LocalTime lt16 = LocalTime.of(12, 12, 12); // 创建指定时间的LocalTime对象

        // 6、判断2个时间对象，是否相等，在前还是在后： equals isBefore isAfter
        System.out.println(lt15.equals(lt16)); // 判断两个时间是否相等
        System.out.println(lt15.isAfter(lt)); // 判断lt15是否在lt之后
        System.out.println(lt15.isBefore(lt)); // 判断lt15是否在lt之前
    }
```

#### `LocalDateTime`

```java
    public static void main(String[] args) {
        // 获取当前日期和时间
        LocalDateTime ldt = LocalDateTime.now();
        System.out.println("当前日期和时间：" + ldt);

        // 获取日期和时间中的信息
        int year = ldt.getYear(); // 年份
        int month = ldt.getMonthValue(); // 月份
        int day = ldt.getDayOfMonth(); // 日
        int hour = ldt.getHour(); // 小时
        int minute = ldt.getMinute(); // 分钟
        int second = ldt.getSecond(); // 秒
        int nano = ldt.getNano(); // 纳秒

        System.out.println("年份：" + year);
        System.out.println("月份：" + month);
        System.out.println("日：" + day);
        System.out.println("小时：" + hour);
        System.out.println("分钟：" + minute);
        System.out.println("秒：" + second);
        System.out.println("纳秒：" + nano);

        // 设置日期和时间
        LocalDateTime ldt2 = ldt.withYear(2025)
                .withMonth(6)
                .withDayOfMonth(15)
                .withHour(18)
                .withMinute(30)
                .withSecond(0)
                .withNano(0);
        System.out.println("修改后的日期和时间：" + ldt2);

        // 增加和减少日期和时间
        LocalDateTime ldt3 = ldt.plusDays(10)
                .plusHours(2)
                .minusMinutes(15);
        System.out.println("增加和减少后的日期和时间：" + ldt3);

        // 创建指定日期和时间的 LocalDateTime 对象
        LocalDateTime ldt4 = LocalDateTime.of(2030, 12, 31, 23, 59, 59);
        System.out.println("指定日期和时间：" + ldt4);

        // 判断两个 LocalDateTime 对象的关系
        System.out.println("当前日期和时间是否在指定日期和时间之后：" + ldt.isAfter(ldt4));
        System.out.println("当前日期和时间是否在指定日期和时间之前：" + ldt.isBefore(ldt4));
    }
```

### `Arrays`

对于自定义对象的排序：

```java
    @Override
    public int compareTo(Student o) {
//        if(this.age > o.age){ //左边大，正整数
//            return 1;
//        }
//        else if(this.age < o.age){ //右边大， 负整数
//            return -1;
//        }
//        return 0; //相等 0
        return o.age-this.age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", height=" + height +
                ", age=" + age +
                '}';
    }
    
		Student[] students = new Student[4];
        students[0] = new Student("Kenn", 180, 21);
        students[1] = new Student("ken", 181, 22);
        students[2] = new Student("ken", 189, 22);
        students[3] = new Student("k", 168, 20);
        Arrays.sort(students);
        System.out.println(Arrays.toString(students));


        Arrays.sort(students, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return Integer.compare(o1.getAge(), o2.getAge());
            }
        });
```

## `Lambda`表达式

**`Lambda`表达式只能简化函数式接口的匿名内部类！**

函数式接口：

- **有且仅有一个抽象方法的接口**
- 注意：将来见到的大部分函数式接口，上面都可能会有一个`@FunctionalInterface`的注解，有该注解的接口必定是函数式接口

```java
//        Arrays.sort(students, new Comparator<Student>() {
//            @Override
//            public int compare(Student o1, Student o2) {
//                return Integer.compare(o1.getAge(), o2.getAge());
//            }
//        });
//        Arrays.sort(students, (Student o1, Student o2) ->{
//                return Double.compare(o1.getHeight(), o2.getHeight());
//        });
        Arrays.sort(students, (o1,  o2) -> {
            return Double.compare(o1.getHeight(), o2.getHeight());
        });
        Arrays.sort(students, (o1,  o2) -> Double.compare(o1.getHeight(), o2.getHeight()));
```

```java
//        Arrays.setAll(prices, new IntToDoubleFunction() {
//            @Override
//            public double applyAsDouble(int value) {
//                return prices[value]*0.8;
//            }
//        });
//        Arrays.setAll(prices, (int value) -> {
//                return prices[value]*0.8;
//        });
//        Arrays.setAll(prices,  value -> {
//            return prices[value]*0.8;
//        });
        Arrays.setAll(prices,  value -> prices[value]*0.8);
```

## 方法引用
方法引用是Java 8引入的一种简洁语法，允许我们使用现有的方法定义来创建Lambda表达式。这种方式使得代码更简洁、易读。方法引用可以看作是`Lambda`表达式的简化形式，主要用于增强代码的可读性和可维护性。

### 静态方法的引用

`静态方法引用`用于引用类的静态方法。在这个例子中，我们引用了`CompareByAge`类的静态方法`comparByAge`。

```java
// CompareByAge.java
public class CompareByAge {
    public static int comparByAge(Student o1, Student o2) {
        return o1.getAge() - o2.getAge();
    }
}

// Test.java
Student[] students = ... // 假设已经初始化的学生数组

// 使用Lambda表达式排序
Arrays.sort(students, (o1, o2) -> CompareByAge.comparByAge(o1, o2));

// 使用方法引用排序，与上面的作用相同
Arrays.sort(students, CompareByAge::comparByAge);
```

**说明**：通过使用`CompareByAge::comparByAge`方法引用，我们可以简化Lambda表达式。

### 实例方法引用

实例方法引用用于引用某个特定对象的实例方法。在这个例子中，我们引用了`CompareByHeight`类的实例方法`comparebyHeight`。

```java
public class CompareByHeight {
    public int comparebyHeight(Student o1, Student o2) {
        return (int) (o2.getHeight() - o1.getHeight());
    }
}

// Test.java
CompareByHeight cmpH = new CompareByHeight();
Student[] students = ... // 假设已经初始化的学生数组

// 使用Lambda表达式排序
Arrays.sort(students, (o1, o2) -> cmpH.comparebyHeight(o1, o2));

// 使用方法引用排序，与上面的作用相同
Arrays.sort(students, cmpH::comparebyHeight);
```

**说明**：通过使用`cmpH::comparebyHeight`方法引用，我们可以简化Lambda表达式。

### 特定类型的方法引用

特定类型的方法引用用于引用某个特定类型的实例方法。在这个例子中，我们引用了`String`类的实例方法`compareToIgnoreCase`。

```java
String[] names = ... // 假设已经初始化的字符串数组

// 使用Lambda表达式排序
Arrays.sort(names, (o1, o2) -> o1.compareToIgnoreCase(o2));

// 使用方法引用排序，与上面的作用相同
Arrays.sort(names, String::compareToIgnoreCase);
```

**说明**：通过使用`String::compareToIgnoreCase`方法引用，我们可以简化Lambda表达式。

### 构造器引用

构造器引用用于引用类的构造方法。在这个例子中，我们引用了`Car`类的构造方法。

```java
public class Car {
    private String name;
    private double price;

    public Car(String name, double price) {
        this.name = name;
        this.price = price;
    }

    // 省略getter和setter方法
}

@FunctionalInterface
interface CreateCar {
    Car create(String name, double price);
}

// Test.java
// 使用匿名内部类
CreateCar cc1 = new CreateCar() {
    @Override
    public Car create(String name, double price) {
        return new Car(name, price);
    }
};

// 使用Lambda表达式
CreateCar cc2 = (name, price) -> new Car(name, price);

// 使用构造器引用
CreateCar cc3 = Car::new;
```

**说明**：通过使用`Car::new`构造器引用，我们可以简化Lambda表达式。

### 总结

方法引用和构造器引用使得代码更简洁易读。以下是四种常见的引用方式：

1. **静态方法引用**：`ClassName::staticMethodName`
2. **实例方法引用**：`instance::instanceMethodName`
3. **特定类型的方法引用**：`ClassName::instanceMethodName`
4. **构造器引用**：`ClassName::new`

引用方式减少了样板代码，增强了代码的可读性和可维护性。


## 综合案例

### Hospital
