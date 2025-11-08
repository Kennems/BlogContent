---
title : 'Kotlin基础'
date : 2024-08-03T22:30:13+08:00
lastmod: 2024-08-03T22:20:13+08:00
description : "Kotlin学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Kotlin学习笔记"]
tags : ["Kotlin"]
# password : leetcode
---

# Kotlin基础

## `val` & `var`

- `const` 编译时常量只能在函数之外定义，因为编译时常量必须在编译时赋值， 而函数都是在运行时才调用， 函数内的变量也是在运行时赋值，编译时常量要在这些变量赋值前就已经存在。

## 函数参数

```kotlin
fun main(){
    println(doSomething(1, false))
    println(fix("Jack"))
    println(fix(age=10, name="Rose"))
}

private fun doSomething(age:Int, flag:Boolean):String{
    return "result"
}

fun fix(name:String, age:Int = 2){
    println(name + " " + age)
}
```

函数没有返回类型时默认返回`Unit`

## 匿名函数

- 定义时不取名字的函数，称之为匿名函数， 匿名函数通常整体传递给其他函数， 或者从其他函数返回。
- 匿名函数对Kotlin来说很重要，可以根据需要指定特殊规则，轻松定制标准库里面的内置函数。

```kotlin
    val blessingFunction:()->String
    blessingFunction = {
        val holiday = "New Year."
        "Happy $holiday" // 隐式返回：默认返回最后一句
    }

    println(blessingFunction())

    val blessingFunction:(String)->String = {
        name -> val holiday = "New Year."
        "$name, Happy $holiday"
    }

    println(blessingFunction("Jack"))
```

### it关键字

定义只有一个参数的匿名函数时， 可以使用it关键字来标识参数名。当需要传入两个值参， it关键字就不能用了

### 定义参数是函数的函数

```kotlin
fun main(){
    val getDiscountWords = { goodsName:String, hour:Int ->
        val currentYear = 2024
        "${currentYear}年， 双11${goodsName}促销倒计时：$hour 小时"
    }

    showOnBoard("卫生纸", getDiscountWords)

}

fun showOnBoard(goodsName:String, getDiscountWords:(String, Int) -> String){
    val hour = (1..24).random()
    println(getDiscountWords(goodsName, hour))

}
```

```kotlin
fun main() {
    showOnBoard("卫生纸") { goodsName: String, hour: Int ->
        val currentYear = 2024
        "${currentYear}年， 双11${goodsName}促销倒计时：$hour 小时"
    }
}

private fun showOnBoard(goodsName: String, getDiscountWords: (String, Int) -> String) {
    val hour = (1..24).random()
    println(getDiscountWords(goodsName, hour))
}
```

### 函数内联

添加`inline`， 避免多个lambda表达式的内存消耗

### 闭包

- 在`Kotlin`中，匿名函数能修改并引用定义在自己的作用域之外的变量， 匿名函数引用着定义自身的函数里的变量，**Kotlin的lambda就是闭包**。
- 能接收函数或者返回函数的函数又叫做**高级函数**， 高级函数广泛应用于函数式编程当中。

## 类型推断

```kotlin
    var str:String? = "strawberry" // 可空类型
    str = null
```

### 使用带let的安全调用

```kotlin
    var str: String? = "strawberry" // 可空类型
    str = str?.let { // let返回最后一行执行的结果
        if (it.isNotBlank()) {
            it.capitalize()
        } else {
            "strawberry"
        }
    }
    println(str)
```

### 使用`非空断言操作符`

- `!!`又称为感叹号操作符， 当变量值为null时， 会抛出`KotlinNullPointerException`

### 空合并操作符

![image-20240727210813033](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240727210813033.png)

### 字符串操作

#### **subString**

```kotlin
    val index = NAME.indexOf('\'')
    val str = NAME.substring(0 until index)
    println(str)
```

#### **split**

```kotlin
	const val NAMES = "Jack, jacky, jason"

	val (origin, dest, proxy) = NAMES.split(",")
    println(origin)
    println(dest)
    println(proxy)
```

#### replace

```kotlin
    val str1 = "The United states of American"
    val str2 = str1.replace(Regex("[aeiou]")) {
        when (it.value) {
            "a" -> "8"
            "e" -> "6"
            "i" -> "9"
            "o" -> "1"
            "u" -> "3"
            else -> it.value
        }
    }

    println(str1)
    println(str2)
```

#### 字符串比较

```kotlin
    val str1 = "Jason"
    val str2 = "jason".capitalize()
    println(str1 == str2) // 内容
    println(str1 === str2) // 是否是同一个引用
```

#### 字符串遍历

```kotlin
    "The people's Reputation of the Jones".forEach {
        print("$it ")
    }
```

### 数字类型

```kotlin
//    val number1:Int = "8.98".toInt()
    val number1: Int? = "8.98".toIntOrNull()
    println(number1)

    println(8.63413423.toInt())
    println(8.63413423.roundToInt())

    println("%.4f".format(8.63413423))
```

### apply

```kotlin
    val file1 = File("D://test//test.txt")
    file1.setReadable(true)
    file1.setWritable(true)
    file1.setExecutable(true)

    val file2 = File("D://test//test.txt").apply {
        setReadable(true)
        setWritable(true)
        setExecutable(true)
    }

```

### run

```kotlin
fun main() {
    val file = File("d://test//test.txt")
    val result = file.run {
        readText().contains("has")
        // 返回执行结果， 并且包含 file.readText() 这样的隐式调用
    }
    println(result)

    val result2 = "The quick brown fox jumps over the lazy dog"
        .run(::isLong)
        .run(::showMessage) // 链式调用， 将上一个返回的结果做为输入
        .run(::println)
}

fun isLong(name:String) = name.length >= 10

fun showMessage(isLong: Boolean):String{
    return if(isLong){
        "Name is not suitable"
    }else{
        "Name is suitable"
    }
}
```

### with

```kotlin
    val result1 = "The people of world".run {
        length >= 10
    }

    val result2 = with("The people of world") {
        length >= 10
    }

    val result3 = with("The people of world") {
        length >= 100
    }
    println(result1)
    println(result2)
    println(result3)
```

### also

```kotlin
    val fileConents:List<String>

    File("D:\\test\\test.txt")
        .also {
            println(it.name) // 把接收者作为值参传给lambda， 返回接收者对象
        }
        .also {
            fileConents = it.readLines()
            println(fileConents)
        }
```

### takeIf

```kotlin
    val result = File("D:\\test\\test.txt")
        .takeIf {
            it.exists() && it.canRead()
        }?.readText()
    println(result)
```

### takeUnless

```kotlin
    val result = File("D:\\test\\test.txt")
        .takeUnless {
            it.isHidden
        }?.readText()
    println(result)
```

## 集合类型

### `list`

```kotlin
    val list = listOf("Jason", "Jack", "Jacky")
    println(list.getOrElse(3){"what?"})
    println(list.getOrNull(3)?: "it is null")
```

### `mutableListOf`可变列表

```kotlin
    val mutableList  = mutableListOf("Java","Kotlin","C**")
    mutableList.add("Go")
    mutableList.remove("Kotlin")

    println(mutableList)

    val toMutableList = listOf("Java", "Kotlin", "C**").toMutableList()
    val toList = mutableListOf("Java", "Python", "ls").toList()
```

### mutator函数

```kotlin
    // mutator函数 
    mutableList += "Jimmy"
    println(mutableList)
    mutableList -= "Jimmy"
    println(mutableList)


    mutableList.removeIf{
        it.contains("Jack")
    }
    println(mutableList)
```

### 遍历

```kotlin
    val list = listOf("Java", "Kotlin", "C**", "Jack", "Jacky")

    for (s in list) {
        println(s)
    }

    list.forEach { println(it) }

    list.forEachIndexed{
        index, item -> println("$index $item")
    }
```

### Set & mutableSet

```kotlin
    val set = setOf("Jason", "Jack", "Jacky", "Jimmy")

    println(set.elementAt(2))
    println(set.elementAt(3))
```

```kotlin
    val mutableSet = mutableSetOf("Jason", "Java", "Kotlin")
    mutableSet += "Jimmy"
    mutableSet -= "Jason"
    println(mutableSet)
```

### 数组类型

Kotlin提供各种Array， 虽然是引用类型，但可以编译成Java基本数据类型。

![image-20240730134749156](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240730134749156.png)

```kotlin
    val intArrayOf = intArrayOf(10, 20, 30)
    val toIntArray = listOf(10, 20, 30).toIntArray()

    arrayOf(File("xxx"), File("yyy"))
```

### Map集合

```kotlin
    val map = mapOf("Jack" to 20, 1 to "bag")

    println(map["1"])
    println(map.get("Jack"))
    println(map.getOrElse("Rose") { "Unknown key" })
    println(map.getOrDefault("Rost", 0))

    map.forEach {
        println("${it.key} -> ${it.value}")
    }

    map.forEach { t, u ->
        println("$t -> $u")
    }
```

## 类

```kotlin
class Player{
    var name = "jack"
        get() = field.capitalize()
        set(value){
            field = value.trim()
        }
}

fun main() {
    var p = Player()
    p.name = "rose" //  setName
    println(p.name)
}
```

### 类的初始化

构造函数的使用

```kotlin
class Player (
    // 只用一次的变量用下划线开头
    _name:String,
    _age:Int,
    _isNormal:Boolean
){
    var name = _name
        get() = field.capitalize()
        set(value){
            field = value.trim()
        }

    var age = _age
        get() = field.absoluteValue
        set(value){
            field = value.absoluteValue
        }

    var isNormal = _isNormal

    override fun toString(): String {
        return "Player(isNormal=$isNormal, age=$age, name='$name')"
    }

}

fun main() {
    val p = Player("Jack", 10, false)
    p.name = "Rose"
    println(p.toString())
}
```

### 构造函数：声明时即初始化

```kotlin
class Player2 (
    // 只用一次的变量用下划线开头
    _name:String,
    var age:Int,
    var isNormal:Boolean
){
    var name = _name
        get() = field.capitalize()
        set(value){
            field = value.trim()
        }

    override fun toString(): String {
        return "Player2(age=$age, isNormal=$isNormal, name='$name')"
    }
}

fun main() {
    val p = Player("Jack", 10, false)
    p.name = "Rose"
    println(p.toString())
}
```

### 次构造函数

```kotlin
class Player2 (
    // 只用一次的变量用下划线开头
    _name:String,
    var age:Int,
    var isNormal:Boolean
){
    var name = _name
        get() = field.capitalize()
        set(value){
            field = value.trim()
        }

    override fun toString(): String {
        return "Player2(age=$age, isNormal=$isNormal, name='$name')"
    }

    // 次构造函数， 可以定义多个次构造函数来配置不同的参数组合
    constructor(name:String):this(name,0,false)

    constructor(name:String, age:Int):this(name,0,false){
        this.name = name.toUpperCase()
    }
}

fun main() {
    val p = Player("Jack", 10, false)
    p.name = "Rose"
    println(p.toString()) //Player(isNormal=false, age=10, name='Rose')

    val player2 = Player2("Rose")
    println(player2.toString()) //Player2(age=0, isNormal=false, name='Rose')

    val player3 = Player2("Rose", 10)
    println(player3.toString()) //Player2(age=0, isNormal=false, name='ROSE')
}
```

### 初始化块

```kotlin
    init{
        require(age > 10){
            "age must greater than 10!"
        }
        require(name.isNotBlank()){
            "name must not blank!"
        }
    }
```

### 初始化顺序

- 主构造函数里声明的属性
- 类级别的属性赋值
- `init`初始化块里的属性赋值和函数调用
- 次构造函数里的属性赋值和函数调用

### 延迟初始化 lazy

```kotlin
class Player5 (
    _name:String

){

    var name = _name
    val config by lazy { // 延迟初始化
        loadConfig()
    }
    private fun loadConfig():String{
        println("loading...")
        return "xxx"
    }
}

fun main() {
    val p = Player5("test")
    Thread.sleep(1000)
    println(p.config)
}
```

### 继承

- 类默认都是封闭的， 要让某个类开放继承，必须使用open关键字修改它。

```kotlin

open class Product(
    val name: String
) {

    fun description() = "Product : $name\n"
    open fun load() = "Nothing"
}

class LuxuryProduct : Product(
    "Luxury product name"
) {
    override fun load() = "Luxury product load"
    fun special() = "LuxuryProduct special function"
}

```

kotlin超类Any

```kotlin
    println(p is Any)
```

### 对象

#### 对象声明

```kotlin
open class Player{
    open fun load() = "load nothing..."
}

fun main() {
    val p = object : Player(){
        override fun load(): String {
            return "anonymous nothing..."
        }
    }
    println(p.load())
}
```

#### 伴生对象

```kotlin
open class ConfigMap {
    // 伴生对象
    companion object{
        private const val PATH = "path.txt"

        fun load() = File(PATH).readBytes()
    }
}

fun main() {
    // static
    // 不调用不会实例化，一旦调用则实例化，并且仅实例化一次
    ConfigMap.load()
}
```

#### 嵌套类

```kotlin
class Player2{
    // 内部类
    class Equipment(var name:String){
        fun show() = println("Equipment : ${name}")
    }

    fun battle(){

    }
}

fun main() {
    val p = Player2()
    p.battle()
    Player2.Equipment("Sharp knife").show()
}
```

#### 数据类

用data声明的类，会重写 `equals` 和 `hashCode` 方法

```kotlin
data class Coordinate(
    var x: Int = 10,
    var y: Int = 1
) {
    val isInBounds = x > 0 && y > 0
}

fun main() {
    println(Coordinate(1, 2).isInBounds)
    println(Coordinate(10, 20) == Coordinate(10, 20))
    println(Coordinate(10, 20) === Coordinate(10, 20))
}
```

#### copy

```kotlin
data class Student(
    var _name: String,
    val age: Int
) {
    var name = _name
    var score = 0
    private val hobby = "music"
    val subject: String

    init {
        println("initializing Student $name")
        subject = "math"
    }

    constructor(_name: String) : this(_name, 10) {
        score = 10
    }

    override fun toString(): String {
        return "Student(age=$age, name='$name', score=$score, hobby='$hobby', subject='$subject')"
    }
}

fun main() {
    val student = Student("John")
    
    val copy = student.copy("Rose")
    println(student)
    // Student(age=10, name='John', score=10, hobby='music', subject='math')
    println(copy)
    // Student(age=10, name='Rose', score=0, hobby='music', subject='math')
}
```

#### 解构声明

```kotlin
class PlayerScore(
    val experience:Int,
    val level:Int
){
    operator fun component1():Int = experience
    operator fun component2():Int = level
}

fun main() {
    // 数据类天然支持component1, component2
    val (x, y) = PlayerScore(10, 20)
    println("${x}, $y")
}
```

#### 运算符重载

- 如果要将内置运算符应用在自定义类身上， 必须重写运算符函数，告诉编译器该如何操作自定义类。

![image-20240730204325847](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240730204325847.png)

```kotlin
data class Coordinate2(
    var x: Int = 10,
    var y: Int = 1
) {
    val isInBounds = x > 0 && y > 0

    operator fun plus(coordinate: Coordinate2): Coordinate2 = Coordinate2(x + coordinate.x, y + coordinate.y)
}

fun main() {
    var c1 = Coordinate2(1, 2)
    val c2 = Coordinate2(3, 4)
    c1 += c2
    println(c1)
    // Coordinate2(x=4, y=6)
}
```

#### 枚举类

```kotlin
enum class Direction(){
    EAST,
    WEST,
    SOUTH,
    NORTH
}

fun main(){
    println(Direction.NORTH) //NORTH
    println(Direction.EAST is Direction) //true
}
```

#### 密封类

- 对于更复杂的`ADT`， 可以使用Kotlin的密封类（`sealed class`）来实现复杂的定义，密封类可以用来定义一个类似于枚举类的ADT

```kotlin
// 密封类
sealed class LicenseStatus2 {
    // object 单例对象，只有一个实例
    object UNQUALIFIED : LicenseStatus2()
    object LEARNING : LicenseStatus2()
    class QUALIFIED(val licenseId: String) : LicenseStatus2()
}

private class Driver2(var status: LicenseStatus2) {
    fun checkLicense(): String {
        return when (status) {
            is LicenseStatus2.UNQUALIFIED -> "没资格"
            is LicenseStatus2.LEARNING -> "在学"
            is LicenseStatus2.QUALIFIED -> "有资格, 驾驶证编号： ${(this.status as LicenseStatus2.QUALIFIED).licenseId}"
        }
    }
}

fun main() {
    println(Driver2(LicenseStatus2.QUALIFIED("100")).run {
        checkLicense()
    })
}
```

## 接口

```kotlin
interface Movable {
    var maxSpeed: Int
    var wheels: Int

    fun move(movable: Movable): String
}

class Car(
    _name: String,
    override var wheels: Int = 4
) : Movable {
    override var maxSpeed: Int
        get() = TODO("Not yet implemented")
        set(value) {
            1
        }

    override fun move(movable: Movable): String {
        TODO("Not yet implemented")
    }
}

fun main() {

}
```

### 抽象类

```kotlin
abstract class Gun(val range:Int){
    abstract fun pullTrigger():String
}


class AK47(val price:Int):Gun(range = 100){
    override fun pullTrigger(): String {
        return "Shot"
    }
}

fun main() {

}
```

### 定义泛型类

`T (type)`

```kotlin
class MagicBox<T>(item:T) {
    private var subject: T = item

}

class Boy(val name: String, val age: Int)

class Dog(val weight: Int)

fun main() {
    val box1 = MagicBox(Boy("Cute dog", 1))
    val box2 = MagicBox(Dog(1))

}
```

### 泛型约束类

```kotlin
class MagicBox<T:Human>(item: T) {
    var available = false
    var subject: T = item

    fun fetch(): T? {
        return subject.takeIf { available }
    }

    // 把元素进行修改
    // 魔盒里面是男孩，编程男人
    fun <R> fetch(subjectModFunction: (T) -> R): R? {
        return subjectModFunction(subject).takeIf { available }
    }
}
open class Human(val age:Int)

class Boy(val name: String, age: Int):Human(age)

class Man(private val name: String, age: Int):Human(age) {
    override fun toString(): String {
        return "Man(name='$name', age=$age)"
    }
}

class Dog(val weight: Int)

fun main() {
    val box1 = MagicBox(Boy("Cute Boy", 1))
//    val box2 = MagicBox(Dog(1))
//    box2.available = false
//    box2.fetch()?.run {
//        println("Your find it")
//    }

    box1.available = true
    box1.fetch()?.run {
        println("you find $name")
    }
    val man = box1.fetch {
        Man(it.name, it.age.plus(10))
    }
    println(man)
}
```

### vararg关键字与get函数

- `MagicBox`能存放任何类型的Human类型， 但一次只能一个。

```kotlin
class MagicBox<T : Human>(vararg item: T) {
    var available = false

    // itme是可变参数, 有多个
    // 泛型是可变参数， 必须写out
    var subject: Array<out T> = item

    fun fetch(index: Int): T? {
        return subject[index].takeIf { available }
    }

    // 把元素进行修改
    // 魔盒里面是男孩，编程男人
    fun <R> fetch(index: Int, subjectModFunction: (T) -> R): R? {
        return subjectModFunction(subject[index]).takeIf { available }
    }

    // 可变参数
    fun a(vararg a: Int) {

    }

    operator fun get(index: Int): T? = subject[index]
}

open class Human(val age: Int)

class Boy(val name: String, age: Int) : Human(age)

class Man(private val name: String, age: Int) : Human(age) {
    override fun toString(): String {
        return "Man(name='$name', age=$age)"
    }
}

class Dog(val weight: Int)

fun main() {
    val box1 = MagicBox(
        Boy("Cute Boy", 1),
        Boy("Kennem", 10)
    )

    box1.available = true;

    box1.fetch(1)?.run {
        println("Your find $name")
    }

    val man = box1.fetch(0) {
        Man(it.name, it.age.plus(10))
    }
}
```

### out

- **out（协变）**，如果泛型类只将泛型类**作为函数的返回（输出）**， 那么使用out， 可以称之为生产类/接口，因为它主要用来生产（produce）指定的泛型对象
  - **子类泛型的对象可以赋值给父类泛型的对象， 用out**

- **in（逆变）**， 如果泛型类只将泛型类型**作为函数的入参（输入）**，那么使用in，可以称之为消费者类/接口，因为它主要是用来消费（consume）指定的泛型对象。
  - **父类泛型对象可以赋值给子类泛型对象，用in**

- **invariant（不变）**，如果泛型类既将泛型类型作为函数参数，又将泛型类型作为函数的输出，那么既不用out也不用in。

```kotlin
//out
interface Production<out T> {
    fun product(): T
}

//in
interface Consumer<in T> {
    fun consume(item: T)
}

// 不变
interface ProductionConsumer<T> {
    fun produce(item: T)
    fun consume(item: T)
}

open class Food
open class FastFood : Food()
class Burger : FastFood()

class FoodStore : Production<Food> {
    override fun product(): Food {
        println("Produce food.")
        return Food()
    }
}

class FastFoodStore : Production<FastFood> {
    override fun product(): FastFood {
        println("Produce FastFood.")
        return FastFood()
    }
}

class BurgerStore : Production<Burger> {
    override fun product(): Burger {
        println("Produce FastFood.")
        return Burger()
    }
}

// 消费者
class Everybody : Consumer<Food> {
    override fun consume(item: Food) {
        println("Eat food.")
    }
}

class ModernPeople : Consumer<FastFood> {
    override fun consume(item: FastFood) {
        println("Eat fastFood.")
    }
}

class American : Consumer<Burger> {
    override fun consume(item: Burger) {
        println("Eat burger food.")
    }
}

fun main() {
    // 子类泛型的对象可以赋值给父类泛型的对象， 用out
    val production1: Production<Food> = FoodStore()
    val production2: Production<Food> = FastFoodStore()
    val production3: Production<Food> = BurgerStore()

    // 父类泛型对象可以赋值给子类泛型对象，用in
    val consumer1: Consumer<Burger> = Everybody()
    // 这里Everybody : Consumer<Food> 可以赋值为 Consumer<Burger>
    consumer1.consume(Burger())
    val consumer2: Consumer<Burger> = ModernPeople()
    val consumer3: Consumer<Burger> = American()
}

```

### reified

- reified关键字能够检查泛型参数类型

```kotlin
class MagicBox<T : Human>(vararg item: T) {
    // 随机产生一个对象，如果不是指定类型的对象，就通过backup函数生成一个指定类型的对象
//    fun <T> randomOrBackup(backup: () -> T): T {
//        val items = listOf(
//            Boy("Jack", 20),
//            Man("John", 20)
//        )
//        val random = items.shuffled().first()
//        return if(random is T){
//            random
//        }else{
//            backup()
//        }
//    }

    inline fun <reified T> randomOrBackup(backup: () -> T): T {
        val items = listOf(
            Boy("Jack", 20),
            Man("John", 20)
        )
        val random = items.shuffled().first()
        return if(random is T){
            println(random)
            random
        }else{
            backup()
        }
    }

}

open class Human(val age: Int)
class Boy(val name: String, age: Int) : Human(age){
    override fun toString(): String {
        return "Boy(name='$name')"
    }
}
class Man(private val name: String, age: Int) : Human(age) {
    override fun toString(): String {
        return "Man(name='$name', age=$age)"
    }
}

class Dog(val weight: Int)

fun main() {
    val box1 = MagicBox<Human>()
    // 由backup函数推断出T的类型
    val subject = box1.randomOrBackup {
        Boy("Jimmy", 29)
    }
    println(subject)
}
```

## 扩展函数

### 定义扩展函数

- 扩展可以在不直接修改类定义的情况下增加类功能， 扩展可以用于自定义类，也可以用于如`List`， `String`， 以及`Kotlin`标准库里的其他类。

```kotlin
fun String.addExt(amount:Int = 1) = this + "!".repeat(amount)

fun String.easyPrint():String{
    println(this)
    return this
}

fun main() {
    println("abc".addExt(10))
    //abc!!!!!!!!!!

    "abc".easyPrint()
    "abc".easyPrint().addExt(3).easyPrint()
}
```

### 泛型扩展函数

```kotlin
fun String.addExt(amount: Int = 1) = this + "!".repeat(amount)

fun <T> T.easyPrint(): T {
    println(this)
    return this
}

fun main() {
    println("abc".addExt(10))
    //abc!!!!!!!!!!

    "abc".easyPrint()
    "abc".easyPrint().addExt(3).easyPrint()

    3.14.easyPrint()
}
```

### 扩展属性

给`String`添加一个统计元音字母个数的属性

```kotlin
val String.numVowels
    get() = count{
        "aeiou".contains(it)
    }

private fun <T> T.easyPrint():T{
    println(this)
    return this
}

fun main() {
    println("The people's Republic of China".numVowels)
}
```

### 可空类扩展

```kotlin
fun String?.printWithDefault(default:String) = print(this ?: default)

fun main() {
//    val nullableString:String? = null
    val nullableString:String? = "null"
    nullableString.printWithDefault("xxx")
}
```

### infix关键字

```kotlin
infix fun String?.printWithDefault(default:String) = print(this ?: default)

fun main() {
    val nullableString:String? = "null"
    nullableString printWithDefault("xxx")
    
    // 实现效果
    "jack".to(18)
    "jack" to 10

}
```

### 扩展文件

![image-20240801123605824](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240801123605824.png)

```kotlin
fun <T> Iterable<T>.randomTake(): T = this.shuffled().first()
```

在其他文件中可以使用这个扩展文件内的函数，并且可以起别名

```kotlin
import com.showguan.extension.randomTake as randomizer

fun main() {
    val list = listOf("Jason", "Jack", "Tom")
    val set = list.toSet()

    println(list.randomizer())
}
```

### 带接收者的函数字面量

**apply 实现**

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

```kotlin
public inline fun File.apply(block: File.() -> Unit): File {
    block()
    return this
}

fun main() {
    val file = File("xx").apply {
        setReadable(true)
        setWritable(true)
    }

    // 详细实现过程
    fun File.ext(): Unit {
        setReadable(true)
    }
    val block = File::ext
    File("xx").apply(block)
}
```

### DSL（Domain-Specific Language，领域特定语言）

```kotlin
interface Element

class HTML {
    private val elements = mutableListOf<Element>()

    fun body(init: Body.() -> Unit) {
        val body = Body()
        body.init()
        elements.add(body)
    }

    override fun toString(): String {
        return elements.joinToString("")
    }
}

class TextElement(val tag: String, val text: String) : Element {
    override fun toString() = "<$tag>$text</$tag>"
}

class Body : Element {
    private val elements = mutableListOf<Element>()

    fun h1(text: String) {
        elements.add(TextElement("h1", text))
    }

    fun h2(text: String) {
        elements.add(TextElement("h2", text))
    }

    fun h3(text: String) {
        elements.add(TextElement("h2", text))
    }

    fun p(text: String) {
        elements.add(TextElement("p", text))
    }

    override fun toString(): String {
        return TextElement("body", elements.joinToString("")).toString()
//        return "<body>${elements.joinToString("")}</body>"
    }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}

fun main() {

    // 使用DSL创建HTML结构
//    val document = html {
//        body {
//            h1("Kotlin DSL")
//            p("This is a paragraph.")
//        }
//    }

    val document = html {
        body {
            h1("这里是一级标题")
            h2("这里是二级标题")
            p("这里是第一段")
            p("这里是第二段")
        }
    }

    println(document)
}
```

## 函数式编程

编程范式

一个函数式应用通常由三大类函数构成：**变换transform**, **过滤filter**, **合并combine**。每类函数都针对集合数据类型，目标是**产生一个最终结果**。

函数式编程范式支持的设计理念就是不可变数据的副本在链上的函数间传递

### map

```kotlin
fun main() {
    val animals = listOf("zebra", "pig", "tiger", "giraffe", "rat", "cat")
    val map = animals
        .map { animal -> "A baby $animal" }
        .map { baby -> "$baby, with the cutest little tail ever!" }

    var animalsLength = animals.map {
        it.length
    }
    println(animals)
    println(map)
    println(animalsLength)

}
```

### flatMap

```kotlin
fun main() {
    val list = listOf(listOf(1, 2, 3), listOf(4, 5, 6), listOf(7, 8, 9)).flatMap {
        it
    }
    println(list)
    //[1, 2, 3, 4, 5, 6, 7, 8, 9]
}
```

### filter

```kotlin
fun main() {

    val list = List(100) { Random.nextInt(0, 1001) }
    println(list)

    val primes = list.filter { number ->
        (2 until number).map { number % it }
            .none { it == 0 } // 没有一个结果是等于0的
    }
    println(primes)
}
```

### zip

合并函数来合并两个集合， 返回一个包含键值对的新集合

```kotlin
fun main() {
    val employees = listOf("Jack", "Jason", "Tommy")

    val shirtSize = listOf("large", "xl-large", "medium")

    val employeesSizes = employees.zip(shirtSize).toMap()
    println(employeesSizes)
}
```

### fold

接收一个初始累加器值，随后会根据匿名函数的结果更新

```kotlin
fun main() {
    val employees = listOf("Jack", "Jason", "Tommy")
    val shirtSize = listOf("large", "xl-large", "medium")

    val fold = listOf(1, 2, 34, 4).fold(0) { accumulator, number ->
        println("Accumulator value : " + accumulator)
        accumulator + (number * 3)
    }
    /**
     * Accumulator value : 0
     * Accumulator value : 3
     * Accumulator value : 9
     * Accumulator value : 111
     */
    println("Final value : " + fold)
    //Final value : 123
}
```

函数式编程的优点：

- 累加变量都是隐式定义的
- 函数运算结果会自动赋值给累加变量，降低了代码出错的机会
- 执行新任务的函数很容易添加到函数调用链上，因为他们都兼容`Iterable`类型

### 序列

Kotlin内有一个内置惰性集合类型叫序列（`Sequence`）， 序列不会索引排序它的内容，也不记录元素数目，在使用一个序列时，序列里的值可能有无限多，因为某个数据源能产生无限多个元素。

```kotlin
fun Int.isPrime():Boolean{
    (2 until this).map {
        if(this % it == 0){
            return false
        }
    }
    return true
}

fun main() {
    val filter = (2..10000).toList().filter {
        it.isPrime()
    }.take(1000)
    println(filter)

    val take = generateSequence(2) { value ->
        value + 1
    }.filter { it.isPrime() }.take(1000)
    println(take.toList())
}
```

### 互操作性与可空性

### Java文件

#### Jhava.java

```java
public class Jhava {
    public String utterGreeting(){
        return "HELLO!";
    }

    @Nullable
    public String determineFriendshipLevel(){
        return null;
    }
}
```

#### Hello.kt

```kotlin
fun main() {
    val adversary = Jhava()
    println((adversary.utterGreeting()))
    val level = adversary.determineFriendshipLevel()
    // 防止为空
    println(level?.toLowerCase())
}
```

### 类型映射

代码运行时，所有的映射类型都会重新映射回对应的Java类型

用kotlin获取java中变量的类型

**java**:

```java
    public Integer x = 100;

```

**kotlin**:

```kotlin
    println(adversary.x.javaClass)
    //class java.lang.Integer
```

### 属性访问

直接访问即可，实际上使用的还是get/set方法

#### **@JvmName**

通过 `@file:JvmName(name = "Hero")`来自定义需要引用的名称

**kotlin:**

```kotlin
@file:JvmName(name = "Hero") // 顶部

fun makeProclamation() = "Greeting, beast!"
```

**java：**

```java
System.out.println(Hero.makeProclamation());
```

#### @JvmField

**kotlin:**

```kotlin
class Spellbook {
    @JvmField
    val spells = listOf("Magic Ms. L", "Lay on Hans")
}
```

**Java:**

```java
        Spellbook spellbook = new Spellbook();
        System.out.println(spellbook);
        //com.showguan.Spellbook@4edde6e5
        for (String spell : spellbook.spells) {
            System.out.println(spell);
        }
        /*
        Magic Ms. L
        Lay on Hans
         */
```

#### @JvmOverloads

自动重载

```kotlin
@JvmOverloads
fun handOverFood(leftHand:String = "berries", rightHand:String = "beef"){
    println("Your right hand is ${rightHand}, left hand is $leftHand")
}
```

```java
        Hero.handOverFood("apple");
        Hero.handOverFood("apple", "banana");
```

#### @JvmStatic

```kotlin
class Spellbook {
    @JvmField
    val spells = listOf("Magic Ms. L", "Lay on Hans")

    companion object {
        @JvmField
        val MAX_SPELL_COUNT = 5
        @JvmStatic
        fun getSpellbookGreeting() = "I am the Great Grimoire"
    }
}
```

```java
//        int maxSpellCount = Spellbook.Companion.getMAX_SPELL_COUNT();
        //静态调用
        int maxSpellCount = Spellbook.MAX_SPELL_COUNT;
//        String spellbookGreeting = Spellbook.Companion.getSpellbookGreeting();
        //静态调用
        String spellbookGreeting = Spellbook.getSpellbookGreeting();
```

#### @Throws

kotlin中抛出异常

```kotlin
@Throws(IOException::class)
fun acceptApology(){
    throw IOException()
}
```

Java中处理异常

```java
        try {
            Hero.acceptApology();
        } catch (IOException e) {
            System.out.println("Caught!");
        }
```

### 函数类型操作

**kotlin:**

```kotlin
val translator = { utterance:String ->
    println(utterance.toLowerCase().capitalize())
}
```

**Java:**

```java
Function1<String, Unit> translator = Hero.getTranslator();
translator.invoke("true");
```

`Function1` 代表有1个参数， 同理`Function2`代表有2个参数。
