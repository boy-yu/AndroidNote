# 基本数据类型

## 整数类型

| 变量名           | 位数 | 范围             |
| ---------------- | :--- | ---------------- |
| Byte             | 8    | -128~127         |
| Short            | 16   | -32,768 ~ 32,767 |
| Int(默认)        | 32   | -2^31 ~ 2^31 - 1 |
| Long(后缀+大写L) | 64   | -2^63 ~ 2^63 - 1 |

常用的一些方法

```kotlin
/// 定义
val byteValue: Byte = 127
val shortValue: Short = 32767
val intValue: Int = 2147483647
val longValue: Long = 9223372036854775807L


// ---------- 设置上下限 ----------
val a: Int = 3;
/// 范围限制
var result = a.coerceIn(0, 10) // 现在输入范围在[0-10]
/// 限制上限
result = a.coerceAtMost(5) //不得高于5 [result,5]
/// 限制下限
result = a.coerceAtLeast(5) //下限不得低于5 [5,result]

// ---------- 设置类型转换 ----------
var a:Int = "123".toInt
// 但是可能会有异常情况所以建议使用
var a:Int? = "abc".toIntOrNull() // 如果报错就为空

// ---------- 比较大小 ----------
/// 比较大小 a = 3 时
var compare = a.compareTo(1) // 输出 1(大)
var compare = a.compareTo(3) // 输出 0(等于)
var compare = a.compareTo(5) // 输出 -1(小)
```

## 浮点类型

| 变量名                  | 位数 | 范围            |
| ----------------------- | ---- | --------------- |
| Float(单精度)(后缀+f/F) | 32位 | 6-7位有效数字   |
| Double(双精度) (默认)   | 64位 | 15-16位有效数字 |

常用的一些方法

```kotlin
// 浮点数类型
val floatValue: Float = 3.14F
val doubleValue: Double = 3.141592653589793

// ---------- 四舍五入 ----------
3.6.roundToInt()   // 4
3.4.roundToInt()   // 3

// ---------- 取整 ----------
3.9.toInt()  // 3
ceil(3.1)   // 4.0
floor(3.9)  // 3.0

// ---------- 截取小数点 ----------
// 由于不支持直接截取需要转换为String截取
val num = 3.1415926
val result = String.format("%.2f", num)
println(result)  // 3.14
```

## 字符/字符串

| 变量名 | 位数 |
| ------ | ---- |
| Char   | 16位 |
| String | -    |

常用的一些方法

```kotlin
// 字符类型
val charValue: Char = 'A'
// 字符串类型
val stringValue: String = "Hello, Kotlin!"

// ---------- 获取字符串核心信息 ----------

val s:String = "Hello"
s.length	// 5
s.get(1) // e
// 避免越界
s.getOrNull(5) // null
// 首次出现的索引
s.indexOf('l'/"ll") // 输出2
// 最后一次出现的索引
s.lastIndexOf('l'/"lo") // 输出3

// ---------- 获取字符串截取 ----------
"abcdef".substring(2) // 输出cdef
// 截取范围[start,end)
"abcdef".substring(1,4) // 输出bcd
// 截取前 n 个字符
"abcdef".take(3) // 输出 abc
// 截取后 n 个字符
"abcdef".takeLast(3) // 输出 def
// 舍弃前 n 个字符，返回剩余部分
"abcdef".drop(2) // 输出cdef
// 舍弃后 n 个字符，返回剩余部分
"abcdef".dropLast(2) // 输出abcd

// ---------- 字符串替换 ----------
"abac".replace('a', 'x') // 输出 xbxc
"abac".replaceFirst('a', 'x') // 输出 xbac
// 替换范围[start,end)
"abcdef".replaceRange(1, 4, "xyz") // 输出 axyzef

// ---------- 字符串分割 ----------
"a,b,c".split(',')、"a;b,c".split(';', ',') //输出 [a, b, c]

// ---------- 字符串大小写转换 ----------
"hello".uppercase() // 输出 HELLO
"HELLO".lowercase() // 输出 hello

//  ---------- 字符串去空格/空白字符 ----------
" abc \t\n".trim() // 输出 abc
" abc ".trimStart() // 输出 "abc "
" abc ".trimEnd() // 输出" abc"

//  ---------- 字符串字符串判断与校验 ----------
"".isEmpty()/" ".isEmpty() // 输出 true / false
// 判断是否为空白字符
"".isBlank()/" ".isBlank() // 输出true / true
// 判断指定开头
"https://www.baidu.com".startsWith("https") // true
// 判断指定结尾
"test.txt".endsWith(".txt") //true

/// ---------- 判断字符/字符串是否相同 ----------
"abc" == "abc" // 输出为true
// 忽略大小写
"Admin".equals("admin",true) // 输出为true
/// 比较引用
val s1 = 'abc' 
val s2 = 'abc' // 常量池复用，引用相同
val s3 = String(charArrayOf('a','b','c')) // 新对象，引用不同
s1 === s2 // 输出为true
s1 === s3 // 输出为false
```

## 布尔类型

```kotlin
val b:Boolean = true
```

## 集合类型

### 数组 Array

```kotlin
/// 创建并初始化
val strArray: Array<String> = arrayOf("a", "b", "c")

/// 原始类型（性能更优）
val intArray: IntArray = intArrayOf(1, 2, 3, 4)
val doubleArray: DoubleArray = doubleArrayOf(1.1, 2.2)
val floatArray: FloatArray = floatArrayOf(1.1f, 2.2f)
val longArray: LongArray = longArrayOf(1, 2, 3, 4)
val shortArray: ShortArray = shortArrayOf(1, 2, 3, 4)
val byteArray: ByteArray = byteArrayOf(1, 2, 3, 4)
val booleanArray: BooleanArray = booleanArrayOf(true, false)
val charArray: CharArray = charArrayOf('a', 'b', 'c')

/// ---------- 常用操作 ----------
intArray.forEach { println(it) } // 打印 1、2、3、4
/// 查找元素索引
intArray.indexOf(3) // 找到返回位置 未找到返回-1
/// 过滤元素
intArray.filter { it % 2 == 0 } // 返回 [2,4]
/// 排序
[2,4,1,3].sortedArray() // 返回 [1,2,3,4] 原数组不会变
/// 安全访问
intArray.getOrNull(10) // 返回 null
```

> `原始类型`不会进行拆箱和装箱,内存占用小,访问速度更快 
> `装箱拆箱`:暂时略过... 涉及JVM后续深入

### Map

Map 是 **键值对（Key-Value）** 容器，核心价值是「通过键快速查询值」（底层哈希表，查询效率 O (1)），键（Key）唯一，值（Value）可重复。

分为「不可变 Map」`Map<T>` 和「可变 Map」`MutableMap<T>`

| 实现类          | 特点                           | 适用场景                       |
| --------------- | ------------------------------ | ------------------------------ |
| HashMap（默认） | 无序、查询快（哈希表）、键唯一 | 日常键值映射（如配置项、缓存） |
| LinkedHashMap   | 有序（保留插入顺序）、查询快   | 需有序的键值映射（如历史记录） |
| TreeMap         | 键自然排序                     | 需按键排序的映射（如成绩排名） |

> TreeMap 和 LinkedHashMap 占时不做了解知道有就好了

```kotlin
// 1. 不可变 Map（默认，推荐优先使用）只读
val immutableMap: Map<String, Int> = mapOf(
    "a" to 1,
    "b" to 2,
    "c" to 3
)
immutableMap["a"] = 1 //报错
 immutableMap.put("a", 1) //报错

// 2. 可变 Map（需修改键值对时用）
val mutableMap: MutableMap<String, Int> = mutableMapOf(
    "apple" to 5,
    "banana" to 3
)
mutableMap["apple"] = 1

/// ---------- 常用操作 ----------
mutableMap["apple"] = 1
val n = mutableMap["a"] // 可能为空
val age = mutableMap.getOrDefault("age", 0) /// 如果为空赋值为0
mutableMap.putIfAbsent("orange", 2) /// 如果没有就创建
mutableMap.remove("apple")

/// ---------- 获取集合元素 ----------
val newMap = mutableMap.map { (key, value) ->
    println("$key: $value")
}
/// 返回值 [apple: 5,banana: 3]


/// 筛选
val new = mutableMap.filter { (key, value) ->
    key.startsWith("a") && value > 2
}
new.map {
    (key, v)->
    println("$key: $v")
}
/// 输出 apple: 5
```

### Set

Set 是 **无序、不可重复元素** 的容器，核心价值是「去重」和「快速判断元素是否存在」（底层哈希表实现，查询效率 O (1)）。

分为「不可变 Set」（`Set<T>`）和「可变 Set」（`MutableSet<T>`）



```kotlin
// 1. 不可变 Set（默认，推荐优先使用）
val immutableSet: Set<String> = setOf("a", "b", "c", "a")
// 2. 可变 Set
val mutableSet: MutableSet<Int> = mutableSetOf(1, 2, 3)

/// ---------- 常用操作 ----------
mutableSet.add(4)
mutableSet.addAll(listOf(5,6))

mutableSet.remove(3)
mutableSet.removeAll(listOf(5,6))

mutableSet.clear()

/// 判断是否包含
mutableSet.contains(2) // 返回true

/// ---------- 通用操作 ----------
immutableSet.size // 返回3

immutableSet.forEach { println(it) }

immutableSet.toList() // 返回 [a,b,c,a]

/// 交集（共同元素）
setOf(1,2,3).intersect(setOf(3,4,5)) // 返回 [3]

/// 并集（合并去重）
setOf(1,2,3).union(setOf(3,4,5))  // 返回 [1,2,3,4,5]

/// 差集（A 有 B 无）
setOf(1,2,3).subtract(setOf(3,4,5)) // 返回 [1,2]
```



## 可空类型

```kotlin
// 类型定义
var age:String? = 23
```

这就是定义一个可空类型,声明方式就是<数据类型>+`?`

使用时可以通过`?.`的方式避免出现空指针异常,安全使用
```kotlin
//如
val a:Int? = age?.toInt() // 这个可能返回为空

/// 不推(强制使用)
val b:Int? = age!!.toInt() //这个就有了出现空指针
```

---

# 条件控制

## if else

```kotlin
/// 基础写法
if(a>b){...}else if(a>c){...}else{...}

/// 简洁写法
val c = if (a>b) a else b
/// 三元
val c = a ?: b

/// 区间
if(x in 1..8) println("x 在区间内")
```

## when

```kotlin
var x = 0
when (x) {
    0, 1 -> println("x=0 / x=1")
    else -> println("otherwise")
}
// x=0 / x=1

when (x) {
    1 -> println("x == 1")
    2 -> println("x == 2")
    else -> { 
        println("x 不是 1")
        println("x 不是 2")
    }
}
// x 不是 也不是 2

when (x) {
    in 0..10 -> println("x 在该区间范围内")
    else -> println("x 不在该区间范围内")
} 
// 输出 x 在该区间范围内
```

# 循环

## for

```kotlin
val chatArr = charArrayOf('a', 'b', 'c', 'd')

for (i in chatArr) print(i)

chatArr.forEach { i ->
    print(i)
}

for ((index, value) in chatArr.withIndex()) {
    print("the element at $index is $value  \"")
}

/// 正序打印[1,4]
for (i in 1..4) print(i) // 结果 1234

/// 倒叙打印[4,1]
for (i in 4 downTo 1) print(i) // 结果 4321

/// 步进打印(step 可以理解为 间隔 x-1个打印)
for (i in 1..10 step 2) print(i) // 1,3

/// 直到打印 [1,4)
for (i in 1 until 4) print(i) // 1,2,3
```

## While

> 先判断后循环

```kotlin
var num = 0
while (num > 0) print(num--) // 无返回值
```

## do...while

> 先循环后判断

```kotlin
var num = 0
do print(num--) while (num >= 0) // 返回 0
```

## 跳过和跳出循环

```kotlin
for (i in 1..5) {
    if (i == 2) continue // 当前循环跳过
    print(i)
    if (i == 3) break // 跳出当前循环
}
/// 返回值 1,3
```

## 标签

```kotlin
loop@ for (i in 1..5) {
    println(i)
    for (j in 1..5) {
        print(j)
        if (j == 2) continue@loop
    }
}
/// 输出[1:1,2 | 2:1,2 | 3:1,2 ...]

/// 如果是跳出当前循环
loop@ for (i in 1..5) {
    println(i)
    for (j in 1..5) {
        print(j)
        if (j == 2) break@loop
    }
}
/// 输出[1:1,2]
```

# 类和对象

在kotlin中类可以包含以下东西:`构造函数`,`初始化代码块`,`函数`,`属性`,`内部类`,`对象声明`

我们将从这6个地方开始入手

## 类的属性

其实和Java中一样,只不过需要用到`var`和`val`来声明这个属性是否可变

```kotlin
class Student() {
    val id: Int = 0
    var age: Int = 0
    var name: String = ""
}
```

此时我们就成功定义了一个学生类,接下来我们使用它.

```kotlin
/// 不用添加new关键字 Kotlin省去了
val stu = Student() // 创建对象
// 访问属性
val id = stu.id
// 修改属性
stu.age = 18
val age = stu.age
```

下面我们可以给这些属性添加`get`和`set`

```kotlin
var age: Int = 0
        get() = field
        set(value) {
            field = if (value > 0) value else 0
        }
```

> field 在那个属性下面就指代的是谁

其实我们发现,就算我们没有写`get`和`set`我们好像也可以访问到属性,和改变属性的值.这是因为kotlin默认帮我们设置了`get`和`set`,所以我们在初始化的时候必须要赋值,或者类型为可空类型.如果类型是`val`那么就不会有`set`只会有get.

## 构造函数

### 主构造函数

其实我们可以发现,好像类里面不光有把属性写在`{}`花括号里面的还有写在`()`括号内部的,其实这里面不是函数的属性了,他属于是类的构造函数.

```kotlin
class Student(id: Int, age: Int, name: String) {
  	/// init外部不可访问,创建时调用
    init {
        println("Student $id $age $name")
    }
}
```

其实这样写我们发现,很麻烦,因为在类里面我们还需要有属性来承接传递进来的值,但是我们只需要有一个小改变就会然代码不一样:

```kotlin
class Student(val id: Int, var age: Int, var name: String) {
    init {
        println("Student $id $age $name")
    }

    fun printAge() {
        println("Student $name $age")
    }
}

/// 使用
// 创建对象
val stu = Student(0, 1, "w")
stu.age = 20
stu.printAge() // 返回结果为 Student w 20
```

这样我们这个类,既有`构造参数`也可以在外部访问和修改类的属性了

> 如果我们要修改类的`可见性`或加了注解`constructor`就必须写出来,因为Kotlin帮我做了处理只不过没有显示出来而且,但是如果我们添加了修饰符,顺序就会出问题,语法就会混乱所以只有我们自己添加
> 时序如下:
> [注解] [可见性修饰符] constructor (...)

### 次构造函数

有主构造函数,必然就会有次构造函数.有时候我们发现有的类可以传递一个参数或两个参数,或者传递的参数都不一样,这就是用的次构造函数,下面我们来定义一个次构造函数

```kotlin
class Student(val id: Int) {
    var age: Int = 0
    var name: String = ""

    constructor(id: Int, age: Int, name: String) : this(id) {
        this.name = name
        this.age = age
    }

    init {
        println("Student $id")
    }

    fun printAge() {
        println("Student $id $age")
    }
}
```

我们在类的里面添加了一个`constructor`构造函数关键字,并且加了一些构造参数,但是次构造函数里面就不能添加`val`或`var`参数了,使用的时候我们就可以有两种选择:

```kotlin
var stu1 = Student(123)
var stu2 = Student(456,20,"W")
```

## 抽象类

**抽象**是面向对象编程的特征之一，类本身，或类中的部分成员，都可以声明为`abstract`的。抽象成员在类中不存在具体的实现。

> 注意：无需对抽象类或抽象成员标注open注解。

抽象类的实现方式

```kotlin
abstract class Animal {
    /// 必须重写
    abstract fun eat()

    /// 可选
    open fun sleep() {
        println("sleeping")
    }
}

class Dog : Animal() {
    override fun eat() {
        println("eating")
    }

    override fun sleep() {
        super.sleep() // 调用父类的方法
        println("Dog sleeping")
    }
}

/// 调用
val dog = Dog()
dog.eat()
dog.sleep()
```

## 嵌套类

我们可以把类嵌套在其他类中，看以下实例：

```kotlin
class Outer {                  // 外部类
    private val bar: Int = 1
    class Nested {             // 嵌套类
        fun foo() = 2
    }
}

/// 使用
val demo = Outer.Nested().foo()
println(demo) // 输出结果 2
```

我们发现,在Nested里面无法直接使用Outer里面的东西,下面我们就要引出我们另外一个**内部类**.

## 内部类

内部类使用 `inner` 关键字来表示。

内部类会带有一个对外部类的对象的**引用**，所以内部类可以访问外部类成员属性和成员函数。

```kotlin
class Outer {
    private val bar: Int = 1
    var v = "成员属性"
    /**嵌套内部类**/
    inner class Inner {
        fun foo() = bar  // 访问外部类成员
        fun innerTest() {
            var o = this@Outer //获取外部类的成员变量
            println("内部类可以引用外部类的成员，例如：" + o.v)
        }
    }
}

/// 使用
val demo = Outer().Inner().foo()
println(demo) //   1
val demo2 = Outer().Inner().innerTest()
println(demo2) // 内部类可以引用外部类的成员，例如：成员属性
```

>为了消除歧义，要访问来自外部作用域的 this，我们使用this@label，其中 @label 是一个 代指 this 来源的标签。

## 匿名内部类

使用**对象表达式**来创建匿名内部类：

```kotlin
class Test {
    var v = "成员属性"

    fun setInterFace(test: TestInterFace) {
        test.test()
    }
}

/// 实现
val test = Test()
/**
 * 采用对象表达式来创建接口对象，即匿名内部类的实例。
 */
test.setInterFace(object : TestInterFace {
    override fun test() {
        println("对象表达式创建匿名内部类的实例")
    }
})

/// 但是尽可能承接一下这个内部类(避免内存泄漏)

val testInterFace = object : TestInterFace {
    override fun test() {
        println("实现接口")
    }
}
/// 再放进去
test.setInterFace(testInterFace)
```

>编译器会自动把 lambda 转成匿名内部类，但不需要写 `object : ... {}`
>
>

## 类的修饰符

类的修饰符包括**类属性修饰符**和**访问权限修饰符**:

- 类属性修饰符

  ```kotlin
  abstract    // 抽象类  
  final       // 类不可继承，默认属性
  enum        // 枚举类
  open        // 类可继承，类默认是final的
  annotation  // 注解类
  ```

- 访问权限修饰符

  ```kotlin
  private    // 仅在同一个文件中可见
  protected  // 同一个文件中或子类可见
  public     // 所有调用的地方都可见
  internal   // 同一个模块中可见
  ```

---

# 继承

Kotlin 中所有类都继承该 `Any` 类，它是所有类的超类，对于没有超类型声明的类是默认超类：

```kotlin
class Example // 从 Any 隐式继承
```

Any 默认提供了三个函数：

```kotlin
/// 比较两个对象是否“内容相等”
equals(other: Any?): Boolean

/** 返回对象的哈希值。
 * 与equals()强关联
 * 如果两个对象equals为true，它们的 hashCode() 必须相同
 */
hashCode(): Int

/// 返回对象的字符串表示
toString()
```

> 注意：Any 不是 java.lang.Object。

如果一个类要被继承，可以使用`open`关键字进行修饰。

```kotlin
open class Base(p: Int) // 定义基类

class Derived(p: Int) : Base(p)
```

## 构造函数

### 子类有主构造函数

如果子类有主构造函数， 则基类必须在主构造函数中**立即初始化**。

```kotlin
open class Person(var name: String, var age: Int)

class Student(name: String, age: Int, var no: String, var score: Int) : Person(name, age)

/// 实现
val s = Student("jeremy", 18, "2019001", 100)
println("学生名:${s.name}") /// 输出 学生名:jeremy
println("年龄:${s.age}") /// 输出 年龄:18
println("学生号:${s.no}") /// 输出 学生号:2019001
println("成绩:${s.score}") /// 输出 成绩:100
```

### 子类没有主构造函数

如果子类没有主构造函数，则必须在每一个二级构造函数中用`super`关键字初始化基类，或者在代理另一个构造函数。初始化基类时，可以调用基类的不同构造方法。

```kotlin
/**用户基类**/
open class Person(name:String){
    /**次级构造函数**/
    constructor(name:String,age:Int):this(name){
        //初始化
        println("-------基类次级构造函数---------")
    }
}

/**子类继承 Person 类**/
class Student:Person{

    /**次级构造函数**/
    constructor(name:String,age:Int,no:String,score:Int):super(name,age){
        println("-------继承类次级构造函数---------")
        println("学生名： ${name}")
        println("年龄： ${age}")
        println("学生号： ${no}")
        println("成绩： ${score}")
    }
}

fun main(args: Array<String>) {
    var s =  Student("Runoob", 18, "S12345", 89)
}

/// 输出结果
-------基类次级构造函数---------
-------继承类次级构造函数---------
学生名： Runoob
年龄： 18
学生号： S12345
成绩： 89
```

## 方法重写

在基类中，使用`fun`声明函数时，此函数默认为`final`修饰，不能被子类重写。如果允许子类重写该函数，那么就要手动添加`open`修饰它, 子类重写方法使用`override`关键词：

```kotlin
/**用户基类**/
open class Person{
    open fun study(){       // 允许子类重写
        println("我毕业了")
    }
}

/**子类继承 Person 类**/
class Student : Person() {

    override fun study(){    // 重写方法
        println("我在读大学")
    }
}

val s =  Student()
s.study(); // 我在读大学
```

如果有多个相同的方法（继承或者实现自其他类，如A、B类），子类如果在重写时需要实现父类中的方法,需要指代使用那个父类的方法,不然只写`super.xx()`会报错,不知道调用那个

```kotlin
open class A {
    open fun f () { println("A") }
    fun a() { println("a") }
}

interface B {
    fun f() { println("B") } //接口的成员变量默认是 open 的
    fun b() { println("b") }
}

class C() : A(), B {
    override fun f() {
        //
//        super<A>.f()//调用 A.f()
//        super<B>.f()//调用 B.f()
        super.f()// 报错不知道用哪个
        println("C")
    }
}

val c =  C()
c.f(); // 打印 C
```

## 属性重写

属性重写使用`override`关键字，属性必须具有兼容类型，每一个声明的属性都可以通过初始化程序或者`getter`方法被重写：

```kotlin
open class Foo {
    open val x: Int get { …… }
}

class Bar1 : Foo() {
    override val x: Int = ……
}
```

你可以用一个`var`属性重写一个`val`属性，但是**反过来不行**。因为`val`属性本身定义了`getter`方法，重写为`var`属性会在衍生类中额外声明一个`setter`方法

你可以在主构造函数中使用`override`关键字作为属性声明的一部分:

```kotlin
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0
}
```

> 注意:
> 子类继承父类时，**不能**有跟**父类同名**的变量，除非父类中该变量为`private`，或者父类中该变量为`open` 并且子类用`override`关键字重写

```kotlin
open class Person(var name: String, var age: Int) {    
    open var sex: String = "unknow"    
    init {        
        println("基类初始化")    
    }
}
// 子类的主构造方法的 name 前边也加了 var，这是不允许的，会报'name' hides member of supertype and needs 'override' modifier
class Student(var name: String, age: Int, var no: String, var score: Int) : Person(name, age) {
    override var sex: String = "male"
}
```

如上代码片段中，子类`Student`主构造方法的第一个字段`name`前边加`var`关键字会报错。
解决方法:1.删除var 2.将`Person`里面的`name`修改为`open`,然后使用`override`重写.

# 接口

## 实现接口

使用`interface`关键字定义接口，允许方法有默认实现：

```kotlin
interface MyInterface {
    // 未实现(必须重写)
    fun bar()

    // 已实现(可不重写)
    fun foo() {
        println("foo")
    }
}

class Child : MyInterface {
    // 必须的方法体
    override fun bar() {
        println("bar")
    }
}

val child = Child()
child.bar() /// 打印 bar
child.foo() /// 打印 foo
```

## 接口中的属性

接口中的属性只能是**抽象**的，**不允许**初始化值，接口**不会保存**属性值，实现接口时，必须重写属性

```kotlin
interface MyInterface{
    var name:String //name 属性, 抽象的
}

class Child:MyInterface{
    override var name: String = "jeremy" //重写属性
}

val child = Child()
println(child.name) /// 打印 jeremy
```

## 函数重写

实现多个接口时，可能会遇到同一方法继承多个实现的问题。例如:

```kotlin
interface A {
    fun foo() { // 已实现
        println("foo A")
    }
    
    fun bar() // 未实现，没有方法体，是抽象的
}

interface B {
    fun foo() { // 已实现
        println("foo B")
    }

    fun bar() { // 已实现
        println("bar B")
    }
}

class C : A, B {
    override fun foo() {
        /// 两个接口都实现了方法 那就需要指定哪一个接口的foo方法
        super<A>.foo()
        super<B>.foo()
    }

    override fun bar() {
        /// 只有一个接口都实现了方法 那就需要无需指定
        super.bar()
        println("bar C")
    }
}

class D : A, B {
    override fun foo() {
        println("foo D")
    }
    
    override fun bar() {
        println("bar D")
    }
}

/// 实现
val c = C()
val d = D()

c.foo(); // foo A B
c.bar(); // bar A B
d.foo(); // foo D
d.bar(); // bar D
```



