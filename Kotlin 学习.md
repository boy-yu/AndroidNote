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

### List

List 是长度可变与不可变两套体系（只读 vs 可变），但本质是更高级的集合接口。

```kotlin
val list = listOf(1, 2, 3)
// list[0] = 10  // ❌ cannot set
// list.add(4)    // ❌ cannot add

val mList = mutableListOf(1, 2, 3)
mList.add(4)     // ✔ 可以扩容
mList[0] = 100   // ✔ 可以修改


/// ----------  遍历集合 ----------
list.forEach { println(it) }
// 下标 + 值
list.forEachIndexed { index,value ->
    println("index:$index,value:$value")
}
for (item in list) {
    println(item)
}

/// ----------  查找元素 ----------
/// 找到对应的元素
val item = list.find { it == 10 }

/// 找到最后一个匹配
val last = list.findLast { it > 5 }

/// 查找满足条件的索引
val index = list.indexOfFirst { it.id == 1 } //没有就返回 -1

/// 元素是否在list里
list.contains(3)
3 in list


/// ---------- List 转换/映射操作 ----------
list.map { Pair(it.id, it.name) }

list.associate { it.id to it.name } /// 创建一个只有key-value的Map

list.associateBy { it.id } /// 创建一个指定key value为it的map

list.filter { it % 2 == 0 } /// 筛选是的

list.filterNot { it % 2 == 0 } /// 筛选不是的

list.take(2)       // 获取前两个
list.takeLast(2)   // 获取后两个

list.drop(1)       // 舍弃前1个
list.dropLast(1)   // 舍弃后1个


/// ---------- List 排序 ----------
list.sort() // 返回Unit
list.sorted() // 升序 返回List 先判断是否是集合
list.sortedDescending() // 降序
listString.sortedBy { it.length } // 根据条件排序
```



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
/// 二元
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

/// 取下标
for((index,char) in charArr){
   println("下标：$index，元素：$char") // 输出：下标0→a，下标1→b，下标2→c
}
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
interface TestInterFace {
    fun test()
}

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

## 装饰器

当你想给某个对象增加功能时，如果用继承，会导致类爆炸、组合不灵活、扩展成本非常高。

装饰器解决的，就是：**动态扩展对象功能，而不修改原类、不增加复杂继承体系。**

```kotlin
open class Coffee {
    open var cost: Int = 0
    open fun getCost(): Int {
        return cost
    }
}

class SimpleCoffee : Coffee() {
    override var cost: Int = 10
}

open class CoffeeDecorator(val coffee: Coffee) : Coffee() {
    override var cost: Int = coffee.cost
    override fun getCost(): Int {
        return coffee.cost
    }
}

/// 添加牛奶
class MilkDecorator(coffee: Coffee) : CoffeeDecorator(coffee) {
    override var cost: Int = coffee.cost + 3
    override fun getCost(): Int {
        return coffee.cost + 3
    }
}

/// 添加糖
class SugarDecorator(coffee: Coffee) : CoffeeDecorator(coffee) {
    override var cost: Int = coffee.cost + 3
    override fun getCost(): Int {
        return coffee.cost + 2
    }
}

var coffee: Coffee = SimpleCoffee()
coffee = MilkDecorator(coffee)
coffee = SugarDecorator(coffee)
/// 使用属性
println("Coffee cost: ${coffee.cost}") // Coffee cost:15
/// 使用方法
println("Coffee cost: ${coffee.getCost()}") // Coffee cost:15
```

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

## 接口和抽象的区别

| 对比点   | 抽象类     | 接口             |
| -------- | ---------- | ---------------- |
| 是否是类 | ✔ 是       | ✘ 不是类         |
| 构造函数 | ✔ 有       | ✘ 无             |
| 字段     | ✔ 真实字段 | ✘ 不能有真实字段 |
| 默认实现 | ✔ 支持     | ✔ 支持           |
| 多继承   | ✘ 只能一个 | ✔ 可以多个       |
| 关系     | 强 is-a    | 弱 can-do        |
| 适用场景 | 模板、基类 | 能力、规范       |

### 抽象

在抽象类里面,如果已经实现了某个函数,这个函数无法抽象,子类也无法重写已实现的函数

```kotlin
abstract class Animal(val name: String) {
    abstract fun makeSound()

    /// 如果已经实现了 无法抽象
    fun run() {
        println("$name is running")
    }
}


class Dog(name: String) : Animal(name) {
    override fun makeSound() {
        println("$name is barking")
    }
    // 无法重写
    override fun run() {
        println("$name is barking")
    }
}
```

### 接口

在接口里面,无论内部函数是否实现了函数,都可以进行重写,并且可以调用接口的函数

```kotlin
interface Flyable {
    fun fly()  // 抽象方法

    fun land() {
        println("Landing...")
    }
}

class Bird : Flyable {
    override fun fly() = println("Bird flying")
    override fun land() {
        super.land() /// 调用接口的 land() 方法
        println("Bird landing")
    }
}
```

## 接口与抽象类

当一个接口需要多个实现的时候,我们就可以这样写

```kotlin
/// 行为规范
interface Action {
    fun run()
    fun fly()
    fun swim()
}

/// 提供默认实现，减少子类负担
abstract class Animal : Action {
    ///可以只重写一个 但是必须要有一个
    override fun run() {}    // 默认空实现
    override fun fly() {}
    override fun swim() {}
}

/// 子类按需实现自己真实的能力
class Bird : Animal() {
    override fun fly() = println("Bird fly")
}

class Dog : Animal() {
    override fun run() = println("Dog run")
}

class Fish : Animal() {
    override fun swim() = println("Fish swim")
}

```

---

# 拓展

Kotlin 可以对一个类的属性和方法进行扩展，且不需要继承或使用 `Decorator(装饰器)` 模式

扩展是一种静态行为，对被扩展的类代码本身不会造成任何影响。

## 扩展函数

扩展函数可以在已有类中添加新的方法，不会对原类做修改，扩展函数定义形式：

```kotlin
fun [接收者].[函数名]([参数]){
  ...
}
```

以下实例扩展 User 类 ：

```kotlin
class User(var name:String)

/**扩展函数**/
fun User.Print(){
    print("用户名 $name")
}

val user = User("jeremy")
user.Print() // 用户名 jeremy
```

下面代码为 MutableList 添加一个swap 函数：

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] //  this 对应该列表
    this[index1] = this[index2]
    this[index2] = tmp
}

val l = mutableListOf(1, 2, 3)
// 位置 0 和 2 的值做了互换
l.swap(0, 2) // 'swap()' 函数内的 'this' 将指向 'l' 的值

println(l.toString()) // [3, 2, 1]
```

`this`关键字指代接收者对象(receiver object)(也就是调用扩展函数时, 在**点号之前指定的对象实例**)。

## 扩展函数是静态解析的

扩展函数是**静态解析**的，并不是接收者类型的虚拟成员，在调用扩展函数时，具体被调用的的是哪一个函数，由调用函数的对象表达式来决定的，而不是动态的类型决定的:

```kotlin
open class C

class D: C()

fun C.foo() = "c"   // C扩展函数 foo

fun D.foo() = "d"   // D扩展函数 foo

 // 指定传入类型是 C 类
fun printFoo(c: C) {
    println(c.foo()) 
}

printFoo(D()) // 输出 c
```

> 若扩展函数和成员函数一致，则使用该函数时，会优先使用成员函数。

```kotlin
class C {
    fun foo() { println("成员函数") }
}

/// 这里编译器会提示
fun C.foo() { println("扩展函数") }

val c = C()
c.foo() /// 成员函数
```

## 扩展一个空对象

在扩展函数内,可以通过`this`来判断接收者是否为空,这样即使接收者为空,也可以调用扩展函数。例如:

```kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // 空检测之后，“this”会自动转换为非空类型，所以下面的 toString()
    // 解析为 Any 类的成员函数
    return toString()
}

val t = null
println(t.toString()) /// 输出 null
```

除了函数，Kotlin 也支持属性对属性进行扩展:

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

扩展属性允许定义在类或者kotlin文件中，不允许定义在函数中。初始化属性因为属性没有后端字段（backing field），所以不允许被初始化，只能由显式提供的 getter/setter 定义。

```kotlin
val Foo.bar = 1 // 错误：扩展属性不能有初始化器
```

## 扩展声明为成员

在一个类内部你可以为另一个类声明扩展。

在这个扩展中，有个多个隐含的接受者，其中扩展方法定义所在类的实例称为分发接受者，而扩展方法的目标类型的实例称为扩展接受者。

```kotlin
class D {
    fun bar() { println("D bar") }
}

class C {
    fun baz() { println("C baz") }

    /// 声明D的拓展函数
    fun D.foo() {
        bar()   // 调用 D.bar
        baz()   // 调用 C.baz
    }

    fun caller(d: D) {
        d.foo()   // 调用扩展函数
    }
}

val c = C()
val d = D()
c.caller(d) // D bar C bar
```

在 C 类内，创建了 D 类的扩展。此时，C 被成为分发接受者，而 D 为扩展接受者。从上例中，可以清楚的看到，在扩展函数中，可以调用派发接收者的成员函数。

假如在调用某一个函数，而该函数在分发接受者和扩展接受者均存在，则以扩展接收者优先，要引用分发接收者的成员你可以使用限定的`this`语法。

```kotlin
class D {
    fun bar() { println("D bar") }
}

class C {
    fun bar() { println("C bar") }  // 与 D 类 的 bar 同名

    fun D.foo() {
        bar()         // 调用 D.bar()，扩展接收者优先
        this@C.bar()  // 调用 C.bar()
    }
    
    fun caller(d: D) {
        d.foo()   // 调用扩展函数
    }
}

val c = C()
val d = D()
c.caller(d) // D bar C bar
```

以成员的形式定义的扩展函数, 可以声明为`open`, 而且可以在子类中覆盖. 也就是说, 在这类扩展函数的派 发过程中, 针对分发接受者是虚拟的(virtual), 但针对扩展接受者仍然是静态的。

```kotlin
open class A

class B : A()

open class C {
    open fun A.foo() {
        println("A.foo in C")
    }

    open fun B.foo() {
        println("B.foo in C")
    }

    fun caller(a: A) {
        a.foo()   // 调用扩展函数
    }
}

class D : C() {
    override fun A.foo() {
        println("A.foo in D")
    }

    override fun B.foo() {
        println("B.foo in D")
    }
}

C().caller(A())   // 输出 "A.foo in C"
D().caller(A())  // 输出 "A.foo in D" —— 分发接收者虚拟解析
C().caller(B())  // 输出 "A.foo in C" —— 扩展接收者静态解析
```

---

# 数据类

Kotlin 可以创建一个只包含数据的类，关键字为 `data`:

```kotlin
data class [类名] (构造函数) { 函数体 }
```

编译器会自动的从**主构造函数**中根据所有声明的属性提取以下函数：

- `equals()`/`hashCode()`
- `toString()` 格式如 "User(name=John, age=42)"
- `componentN() functions` 对应于属性，按声明顺序排列
- `copy()`函数

如果这些函数在类中已经被明确定义了，或者从超类中继承而来，就不再会生成。

为了保证生成代码的一致性以及有意义，数据类需要满足以下条件：

- 主构造函数至少包含一个参数。
- 所有的主构造函数的参数必须标识为`val` 或者 `var` ;
- 数据类不可以声明为 `abstract`, `open`, `sealed` 或者 `inner`;
- 数据类不能继承其他类 (但是可以实现接口)。

```kotlin
data class User(var name: String, var age: Int)

val oldUser = User("jeremy", 18)
val newUser = oldUser.copy(age = 20)

/// 解构声明
val (name, age) = newUser

println("oldUser:$oldUser") // oldUser:User(name=jeremy, age=18)
print("newUser[name:$name, age:$age]") // newUser[name:jeremy, age:20]
```

# 密封类

使用 **sealed** 修饰类，密封类可以有子类，但是所有的子类都必须要内嵌在密封类中。

密封类用来表示**受限的类**继承结构：当一个值为有限几种的类型, 而不能有任何其他类型时。在某种意义上，他们是枚举类的扩展：枚举类型的值集合 也是受限的，但每个枚举常量只存在一个实例，而密封类 的一个子类可以有可包含状态的多个实例。

`sealed` 不能修饰 `interface` ,`abstract` class(会报 warning,但是不会出现编译错误)

> 密封类是一种受限制的继承体系，所有子类必须在同一个文件中定义，使得类型是“**可穷举**”的。

```kotlin
/// 密封一个网络回调
sealed class Result

/// 回调成功
data class Success(val data: String) : Result()
/// 回调失败
data class Error(val message: String) : Result()
/// 加载中...
object Loading : Result()
```

密封类的子类可以是：

```kotlin
data class
class
object(单例)
```

> 所有子类必须放在 **同一个文件里**
> 这和 Java 不同，Java 的继承是开放的。

```kotlin
when (result) {
    is Success -> ...
    is Error -> ...
    Loading -> ...
    // 不需要 else !
}
```

>如果漏写某个状态 → 编译器直接报错！
> **这就是 sealed 最大价值：类型安全。**

---

# 泛型

## 泛型声明

泛型，即 "参数化类型"，将类型参数化，可以用在类，接口，方法上。

与 Java 一样，Kotlin 也提供泛型，为类型安全提供保证，消除类型强转的烦恼。

声明一个泛型类:

```kotlin
class Box<T>(t: T) {
    var value: T = t
}
```

创建类的实例时我们需要指定类型参数:

```kotlin
val box:Box<Int> = Box<Int>(1)
/// 也可以使用Kotlin的类型推导
var box = Box(1)
```

以下实例向泛型类 Box 传入整型数据和字符串：

```kotlin
val boxInt = Box(10)
val boxString = Box("Jeremy")

println(boxInt.value) /// 10
println(boxString.value) /// Jeremy
```

在调用泛型函数时，如果可以推断出类型参数，可以省略泛型参数。

以下实例创建了泛型函数 doPrintln，函数根据传入的不同类型做相应处理：

```kotlin
fun <T> doPrintln(content: T) {
    when (content) {
        is Int -> println("整型数字为 $content")
        is String -> println("字符串转换为大写:${content.uppercase()}")
        else -> println("T 不是整型，也不是字符串")
    }
}

val age = 23
val name = "Jeremy"
val bool = true

doPrintln(age) /// 整型数字为 23
doPrintln(name) /// 字符串转换为大写:JEYEMY
doPrintln(bool) /// T 不是整型，也不是字符串
```

## 泛型约束

### 单约束

泛型允许类或函数接收任意类型，但有些时候你 **必须限制 `T` 的类型** 才能调用某些方法或属性。

```kotlin
fun <T> max(a: T, b: T): T {
    return if (a > b) a else b   // ❌ 错误：不能比较未知类型
}
```

Kotlin 默认上界是 `Any?`：

```kotlin
/// Kotlin 默认的泛型类型
fun <T : Any?> foo(v: T) {}

/// 如果我们需要约束类型那么可以这样
fun <T : Number> foo(a: T, b: T) {
    println(a.toDouble() / 2)
}
```

### 多约束

对于多个上界约束条件，可以用 where 子句：

```kotlin
fun <T> foo(a: T, b: T) where T : Number, T : Comparable<T> {
    println(a > b)
}
```

> 这里`Number`代表是数字,`Comparable`代表是可比较大小的意思

## 型变

型变有两种:

- `out`:生产者(Producer),代表这个泛型类型是一个 **只输出、不输入** 的类型
- `in`:消费者(Consumer),代表这个泛型类型是一个 **只输入、不输出** 的类型

为什么要这样写,因为**安全**,假设有一个String类型的列表,赋值给一个Any类型,后面往这个列表里面添加数字,就会导致报错

``` kotlin
fun <T : MutableList<String>> list(list: T) {
    fun foo(list: T) {
        println(list)
    }
}

val oldList = list(mutableListOf("12", "23", "34"))
val newList: MutableList<Any> = oldList; // 如果运行
newList.add(1) /// ❌ 往 String 列表写入 Int
```

两个的使用:

```kotlin
/// out
fun copy(from: Array<out Any>, to: Array<Any>) {
    // 从 from 读
    // 往 to 写
}

/// in
fun fill(target: Array<in String>, value: String)
```

## 星号投射

有些时候, 你可能想表示你并不知道类型参数的任何信息, 但是仍然希望能够安全地使用它. 这里所谓"安全地使用"是指, 对泛型类型定义一个类型投射, 要求这个泛型类型的所有的实体实例, 都是这个投射的子类型。

对于这个问题, Kotlin 提供了一种语法, 称为 星号投射(star-projection):

- 假如类型定义为 Foo<out T\> , 其中 T 是一个协变的类型参数, 上界(upper bound)为 TUpper ,Foo<\*> 等价于 Foo<out TUpper\> . 它表示, 当 T 未知时, 你可以安全地从 Foo<\*> 中 读取TUpper 类型的值.
- 假如类型定义为 Foo<in T\> , 其中 T 是一个反向协变的类型参数, Foo<\*> 等价于 Foo<inNothing\> . 它表示, 当 T 未知时, 你不能安全地向 Foo<\*> 写入 任何东西.
- 假如类型定义为 Foo<T\> , 其中 T 是一个协变的类型参数, 上界(upper bound)为 TUpper , 对于读取值的场合, Foo<*> 等价于 Foo<out TUpper\> , 对于写入值的场合, 等价于 Foo<in Nothing\> .

如果一个泛型类型中存在多个类型参数, 那么每个类型参数都可以单独的投射. 比如, 如果类型定义为interface Function<in T, out U> , 那么可以出现以下几种星号投射:

1. Function<*, String> , 代表 Function<in Nothing, String> ;
2. Function<Int, *> , 代表 Function<Int, out Any?> ;
3. Function<*,\* > , 代表 Function<in Nothing, out Any?> .

注意: 星号投射与 Java 的原生类型(raw type)非常类似, 但可以安全使用

---

# 枚举

枚举类最基本的用法是实现一个类型安全的枚举。

枚举常量用逗号分隔,每个枚举常量都是一个对象。

```kotlin
enum class Color{
    RED,BLACK,BLUE,GREEN,WHITE
}
```

## 枚举初始化

每一个枚举都是枚举类的实例，它们可以被初始化：

```kotlin
enum class Color(val rgp: Int) {
    RED(0xFF0000), GREEN(0x00FF00), BLUE(0x0000FF);

    fun isBLUE() = this == Color.BLUE
}

val red = Color.RED.rgb
val isBlue = Color.GREEN.isBLUE()

println("颜色:$red") // 输出: 颜色:0xFF0000
println("是否是蓝色:$isBlue") // 输出: 是否是蓝色:false
```

枚举类可以实现接口（但不能继承类）

```kotlin
interface Printable {
    fun print()
}

enum class Mode : Printable {
    AUTO {
        override fun print() = println("Auto Mode")
    },
    MANUAL {
        override fun print() = println("Manual Mode")
    }
}

val auto = Mode.AUTO
val manual = Mode.MANUAL

println(auto.print()) // 输出: Auto Mode
println(manual.print()) // 输出: Manual Mode
```

枚举还支持以声明自己的匿名类及相应的方法、以及覆盖基类的方法。如：

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}

println(ProtocolState.WAITING.signal()) /// TALKING
println(ProtocolState.TALKING.signal()) /// WAITING
```

枚举的重要属性与方法

| 属性 / 方法     | 说明                      |
| --------------- | ------------------------- |
| `name`          | 枚举项名称（字符串）      |
| `ordinal`       | 枚举项的顺序（从 0 开始） |
| `entries`       | 获取所有枚举值数组        |
| `valueOf(name)` | 获取对应名称的枚举项      |

```kotlin
enum class Color(val rgp: Int) {
    RED(0xFF0000), GREEN(0x00FF00), BLUE(0x0000FF)
}

val c = Color.RED
println(c.name) /// RED
println(c.ordinal) /// 0
Color.entries.forEach { print("$it ") } /// RED GREEN BLUE
println(Color.valueOf(c.name)) /// RED
```

---

# 对象表达式和对象声明

## 对象表达式

即时创建一个对象（匿名对象），使用范围 = 创建点。

```kotlin
val obj = object {
    val x = 10
    fun hi() = println("hello")
}
```

或用来实现接口 / 继承类（匿名内部类）：

```kotlin
val listener = object : View.OnClickListener {
    override fun onClick(v: View?) {
        println("click")
    }
}
```

### 特点

- 每次执行都会创建一个新对象
- 是**匿名类**（没有类名）
- 是“即时创建”的对象（运行时 new）
- 作用域仅在表达式链内

## 对象声明(单例)

> 定义一个全局单例(Singleton)

```kotlin
object AppConfig {
    val version = "1.0"
    fun init() { println("Init") }
}

AppConfig.init() /// Init
```

### 特点

- **天然单例（线程安全）**
- 全局只有一个实例
- 在第一次访问时 lazy 初始化（惰性加载）
- 是一个有名字的单例类，作用域可以是任意（文件、类内等）

## 对象表达式和对象申明的区别

| 特性            | **对象表达式**（object expression） | **对象声明**（object declaration） |
| --------------- | ----------------------------------- | ---------------------------------- |
| 是否匿名        | ✔ 匿名                              | ❌ 有名字                           |
| 是否单例        | ❌ 不是，每次 new 一个               | ✔ 永远单例                         |
| 初始化时机      | 执行到表达式立即创建                | 第一次访问时创建（lazy）           |
| 用途            | 临时对象、接口实现、匿名类          | 全局单例、Manager、Utils           |
| 类似            | Java 的匿名内部类                   | Java 的单例模式                    |
| 能否继承类/接口 | ✔ 能（匿名内部类）                  | ✔ 能（全局对象）                   |

> object {} 是创建对象 | object X 是创建单例

## 伴生对象(static)

Kotlin 的 `companion object` 也是对象声明的一种：

```kotlin
class User {
    companion object {
        const val DEFAULT_NAME = "Guest"
        fun create() = User()
    }
}


/// 使用

User.DEFAULT_NAME
User.create()
```

> 特点:
>       1.类似 Java 的 static
>       2.属于对象声明的一种变体
>
> 
>
> 

---

# 委托

委托就是把一个类或属性的某些功能，转交给另一个对象来实现。

## 类委托

类的委托即一个类中定义的方法实际是调用另一个类的对象的方法来实现的。

以下实例中派生类 Derived 继承了接口 Base 所有方法，并且委托一个传入的 Base 类的对象来执行这些方法。

```kotlin
interface Printer {
    fun print()

    fun play()
}

class APrinter : Printer {
    override fun print() {
        println("A Printing...")
    }

    override fun play() {
        println("A Playing...")
    }
}

class BPrinter(printer: Printer) : Printer by printer {
    override fun play() {
        println("B Playing...")
    }
}

val p = BPrinter(APrinter())
p.print() /// A Printing...
p.play()  /// B Playing...
```

`Printer by printer` 的意思是：Printer 的所有方法都可以由 printer 对象来实现,除非自己实现了.

## 属性委托

属性委托指的是一个类的某个属性值不是在类中直接进行定义，而是将其托付给一个代理类，从而实现对该类的属性统一管理。

属性委托语法格式：

```kotlin
val/var <属性名>: <类型> by <表达式>
```

`by` 关键字之后的表达式就是委托, 属性的 `get()` 方法*(以及`set()` 方法)*将被委托给这个对象的 `getValue()` 和 `setValue()` 方法。属性委托不必实现任何接口, 但必须提供 `getValue()` 函数(对于 `var` 属性,还需要 `setValue()` 函数)。

```kotlin
class MyDelegate {
    private var value: String = ""

    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return value
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        this.value = value.uppercase()
    }
}

var name: String by MyDelegate()
name = "jeremy"
println(name)

/// 打印值
Setting value for name
Getting value for name
JEREMY
```

## Kotlin 内置属性委托

### lazy 延迟初始化

第一次调用 get() 会执行已传递给 lazy() 的 lamda 表达式并记录结果,后续调用 get() 只是返回记录的结果。

```kotlin
// lazy 判断变量是否有值,如果没有值执行表达式,有就不执行
val lazyValue: String by lazy {
    println("computed!") 
    "hello"
}

fun main(args: Array<String>) {
    println(lazyValue)   // 第一次执行，执行两次输出表达式
    println(lazyValue)   // 第二次执行，只输出返回值 "hello"
}
```

### Observable 可观察属性

observable 可以用于实现观察者模式。

Delegates.observable() 函数接受两个参数: 第一个是初始化值, 第二个是属性值变化事件的响应器(handler)。

在属性赋值后会执行事件的响应器(handler)，它有三个参数：被赋值的属性、旧值和新值：

```kotlin
class User {
    var name: String by Delegates.observable("初始值") {
        prop, old, new ->
        println("旧值：$old -> 新值：$new")
    }
}

fun main(args: Array<String>) {
    val user = User()
    user.name = "第一次赋值"
    user.name = "第二次赋值"
}

/// 打印
旧值：初始值 -> 新值：第一次赋值
旧值：第一次赋值 -> 新值：第二次赋值
```

### vetoable 可拦截属性变化

当`vetoable`函数返回为false时,不允许改变值

``` kotlin
var age: Int by Delegates.vetoable(0) { _, old, new ->
    new >= 18
}
println(age) // 打印初始值 0
age = 17
println(age) // 不符合要求不赋值 0
age = 18
println(age) // 符合要求 赋值 18
```

---

# 函数

## 内联函数

### inline

作用：把函数内容“复制”到调用处

不再真正调用函数，也不会创建 lambda 对象。
**优点**

- 性能更好(少一次函数调用)
- lambda不在创建对象(节省内存)
- 允许lambda做"**非局部返回**"

```kotlin
inline fun doSomething(block: () -> Unit) {
    println("Start")
    block()
    println("End")
}

doSomething {
    println("Inside")
}

/// 返回值
Start
Inside
End
```

### noinline

如果某些 lambda 需要当“对象”使用（如传递、保存），就不能内联。

否则会报错，必须标 noinline。

```kotlin
// 非内联函数：接收普通 lambda
fun normalFunction(task: () -> Unit) {
    println("执行非内联函数的任务")
    task()
}

// inline 函数，其中一个 lambda 用 noinline
inline fun mixInline(
    inlineAction: () -> Unit,
    noinline noInlineAction: () -> Unit
) {
    // 内联 lambda 只能即时执行
    inlineAction()

    // noinline lambda 可以传给非内联函数
    normalFunction(noInlineAction)
}

fun main() {
    mixInline(
        inlineAction = { println("内联逻辑执行") },
        noInlineAction = { println("noinline 逻辑被传给非内联函数执行") }
    )
}

/// 输出结果
内联逻辑执行
执行非内联函数的任务
noinline 逻辑被传给非内联函数执行
```

### crossinline

因为 inline 会把 lambda 代码展开到调用处，因此你可以这么写：

```kotlin
inline fun test(block: () -> Unit) {
    println("A")
    block()  // 允许 return 跳出外层函数
    println("B")
}

test {
    println("C")
    return
    println("D")
}
println("E")

/// 返回值 A C
```

期望 return 的只是 lambda，而不是外层函数,如果你想禁止这种行为，就用`crossinline`。

```kotlin
inline fun test(crossinline block: () -> Unit) {
    println("A")
    block()  // 允许 return 跳出外层函数
    println("B")
}

test {
    println("C")
    return // 报错了不允许return
    println("D")
}
println("E")
```

### 三者的关系

| 关键字          | 是否内联 lambda | 是否允许非局部return | 用途                        |
| --------------- | --------------- | -------------------- | --------------------------- |
| **inline**      | ✔ 是            | ✔ 是                 | 高性能、高阶函数            |
| **noinline**    | ✘ 否            | ✘ 否                 | 需要保存/传递 lambda 时     |
| **crossinline** | ✔ 是            | ✘ 否                 | lambda 不能 return 出外层时 |

---

## 标准函数

Kotlin 标准函数主要有：

| 函数名  | 作用                         | 常用场景            |
| ------- | ---------------------------- | ------------------- |
| `let`   | 处理作用域、链式调用、空安全 | 可空对象操作        |
| `run`   | 以对象为上下文，返回最后一行 | 初始化、计算结果    |
| `also`  | 以对象为 it，返回对象本身    | 调试、链式副作用    |
| `apply` | 配置对象，返回对象本身       | 构建对象（Builder） |
| `with`  | 和 run 很像，但不是扩展函数  | 配置对象            |

### let

处理可空对象／链式调用,返回 lambda 最后一行的结果.

```kotlin
val user = User("jeremy", 18)
val length = user.name.let {
    it.length
    println("名字长度${it.length}")
    it.uppercase()
}
println("用户名:${user.name}")
println("返回结果:$length")

/// 返回结果
名字长度6
用户名jeremy
返回结果JEREMY
```

### run

在上下文中执行代码并返回结果

和 let 类似，但 **this** 代表对象

``` kotlin
class User {
    var name: String? = null
    var age: Int? = null
}

val user = User().run {
    name = "Jeremy"
    age = 18
    this  // 如果你想返回对象，可以这样写
}
println("[name: ${user.name}, age: ${user.age}]")

/// 返回结果
[name: Jeremy, age: 18]
```

### also

用 it 做副作用操作，不改变链式返回值

返回对象本身

``` kotlin
val list = mutableListOf(1, 2, 3)
    .also { it.add(4) }
    .also { println("最终内容：$it") }

print(list.toString())

/// 打印结果
最终内容：[1, 2, 3, 4]
[1, 2, 3, 4]
```

### apply

**返回对象本身**, 使用 this 操作对象

```kotlin
class User {
    var name: String? = null
    var age: Int? = null
}

val user = User().apply {
    this.name = "Jeremy" //this 可不要
    age = 18
}
println("[name: ${user.name}, age: ${user.age}]")

///  打印
[name: Jeremy, age: 18]
```

> 类似于run 结尾加this 这个自动添加了 不需要手动添加

### with

结构化写法，对象作为参数,功能与 run 类似，但写法不同

```kotlin
val user = User().apply {
    name = "Jeremy"
    age = 18
}

val result = with(user) {
    println(name)
    println(age)
    age + 5  // 返回值
}
```

### 区别

| 函数名    | 对象名 | 返回值          | 常用                 |
| --------- | ------ | --------------- | -------------------- |
| **let**   | `it`   | lambda 最后一行 | 可空、安全链式       |
| **run**   | `this` | lambda 最后一行 | 计算结果、上下文操作 |
| **apply** | `this` | 对象本身        | 配置对象             |
| **also**  | `it`   | 对象本身        | 副作用／调试         |
| **with**  | `this` | lambda 最后一行 | 多个操作但不需要链式 |

```kotlin
data class User(var name: String, var age: Int)


fun main() {
    val user = User("Jeremy", 20)

    // let：使用 it，返回表达式
    val length = user.name.let {
        it.length
    }
    println("length:$length") // length:6

    // run：使用 this，返回表达式
    val result = user.run {
        age + 1
    }

    println("result:$result") // result:21

    // apply：配置 this，返回 user
    val newUser = user.apply {
        age = 22
    }

    println("newUser:${newUser}") // newUser:User(name=Jeremy, age=22)

    // also：使用 it 做副作用，返回 user
    val debug = user.also {
        println("User is $it") // User is User(name=Jeremy, age=22)
    }

    // with：不是扩展函数，返回表达式
    val ageAfter = with(user) {
        age + 5
    }
    println("ageAfter:$ageAfter") // ageAfter:27

}
```

# 协程

Kotlin 协程的核心使用方式围绕「协程构建器」展开，不同构建器对应不同的「返回值、线程阻塞行为、异常处理策略」，适配从 “简单异步执行” 到 “并发任务调度” 的全场景。

**核心前置概念**

协程的执行依赖 3 个核心要素：

1. **作用域（CoroutineScope）**
   管控协程生命周期(如 Android 的 `viewModelScope`/`lifecycleScope`、通用的 `CoroutineScope()`)；

2. **构建器（Builder）**
   启动协程的入口(如 `launch`/`async`);

3. **调度器（Dispatcher）**

   指定协程执行的线程(`Dispatchers.Main`/`IO`/`Default`);

## 5类核心使用方式

### launch

无返回值的 “火忘式” 异步执行

```kotlin
// 通用作用域（需手动管理取消）
val scope = CoroutineScope(Dispatchers.Main)
// Android 生命周期绑定作用域（自动取消）
// val scope = lifecycleScope / viewModelScope

val job = scope.launch(Dispatchers.IO) {
    // 执行无返回值的异步任务（如文件写入、日志上报）
    writeFile("data.txt", "content")
}

// 可选：手动取消协程（作用域绑定的协程无需手动取消）
// job.cancel()
```

**核心区别**

- 无返回值（返回 `Job` 对象，用于管控协程生命周期：取消 / 等待）
- 不阻塞调用线程，仅在指定调度器异步执行
- 属于 “火忘式”（fire-and-forget），执行结果无需回调

**适用场景**

- 执行无需返回结果的异步任务（如 UI 状态更新、日志上报、数据缓存、网络请求回调处理）
- Android 中与生命周期绑定（`lifecycleScope.launch` 自动在页面销毁时取消，避免内存泄漏）

### async/await

有返回值的并发执行(与Flutter一样,仅多了一个切换线程的操作)

```kotlin
scope.launch(Dispatchers.Main) {
    // 1. 启动多个异步任务（并发执行）
    val deferred1 = async(Dispatchers.IO) { fetchUserInfo() } // 返回 Deferred<User>
    val deferred2 = async(Dispatchers.IO) { fetchUserOrders() } // 返回 Deferred<List<Order>>

    // 2. 等待结果（挂起当前协程，不阻塞线程）
    val user = deferred1.await()
    val orders = deferred2.await()

    // 3. 切回主线程更新UI
    updateUI(user, orders)
}
```

**核心区别**

- 有返回值：返回 `Deferred<T>`，通过 `await()` 获取结果;
- 并发特性：多个 `async` 可并行执行(区别于串行的 `withContext`);
- `await()` 会挂起当前协程（而非阻塞线程），直到任务完成;
- 即使不调用 `await()`，`async` 启动的协程也会执行(仅拿不到结果);

**适用场景**

- 需要获取异步任务返回结果(如接口请求、数据库查询);
- 多任务并发执行(如同时请求 “用户信息 + 订单列表”，减少总耗时);
- 注意：**避免单个 `async` 配合 `await()` 使用**(不如 `withContext` 简洁);

### runBlocking

阻塞当前线程的协程（仅测试 / 主函数）

```kotlin
// 阻塞主线程，直到协程执行完毕
fun main() = runBlocking {
    println("主线程开始")
    launch { delay(1000); println("协程执行完成") }
    println("主线程等待协程...")
}
// 输出：主线程开始 → 主线程等待协程... → 协程执行完成
```

**核心区别**

- 阻塞调用线程（如主线程），直到协程体执行完毕；
- 本质是 “桥接同步代码和协程”，不属于真正的异步；
- 返回值为协程体的最后一行结果 (`runBlocking<T>`);

**适用场景**

- 仅用于 **测试代码**（单元测试中模拟协程执行）或 **main 函数**（程序入口）
- ❌ 绝对禁止在 Android UI 线程使用（会导致 ANR）

### withContext

调度器切换 + 有返回值（无新协程）

```kotlin
scope.launch(Dispatchers.Main) {
    // 切到 IO 线程执行耗时操作（挂起当前协程，不创建新协程）
    val data = withContext(Dispatchers.IO) {
        readFile("data.txt") // 返回 String
    }
    // 自动切回主线程，更新UI
    tvContent.text = data
}
```

**核心区别**

- 有返回值，无新协程：复用当前协程，仅切换调度器 (区别于 `async` 会创建新协程);
- 挂起当前协程（不阻塞线程），执行完自动切回原调度器;
- 替代 `launch + async + await` 的极简写法(单个有返回值任务)

**适用场景**

- 单任务线程切换 (如 UI 线程 → IO 线程读文件 / 请求接口 → 切回 UI 线程);
- 替代 `async { ... }.await()` (更简洁，无多余协程创建);
- 强制指定任务的执行线程(如 CPU 密集型任务用 `Dispatchers.Default`)

### coroutineScope/supervisorScope

两者都是 “挂起函数”，用于在协程内创建子作用域，等待所有子协程完成后才继续执行，核心区别是**异常处理策略**。

#### coroutineScope

子协程异常 “一错全错”

```kotlin
scope.launch {
    try {
        val result = coroutineScope {
            // 子协程1
            launch { fetchData1() }
            // 子协程2（若抛异常，所有子协程+当前协程都会取消）
            val data2 = async { fetchData2() } 
            data2.await()
        }
    } catch (e: Exception) {
        // 捕获任意子协程的异常
        handleError(e)
    }
}
```

#### supervisorScope

子协程异常 “互不影响”

```kotlin
scope.launch {
    supervisorScope {
        // 子协程1抛异常，不影响子协程2
        launch { fetchData1() } 
        launch { fetchData2() }
    }
    // 所有子协程执行完（即使部分失败），才会执行这里
    println("所有子任务执行完毕")
}
```

**核心区别**

| 特性     | coroutineScope                        | supervisorScope                        |
| -------- | ------------------------------------- | -------------------------------------- |
| 异常传播 | 子协程异常会取消所有子协程 + 当前协程 | 子协程异常仅取消自身，不影响其他子协程 |
| 适用场景 | 子任务 “一荣俱荣，一损俱损”           | 子任务独立（失败不影响其他）           |

**适用场景**

- `coroutineScope`：多任务依赖 (如 “登录 + 拉取用户信息”，登录失败则拉取任务无意义)
- `supervisorScope`：多任务独立 (如列表多个接口请求、页面多个模块初始化，一个失败不影响其他)；
- 替代 `runBlocking`（非阻塞，仅挂起），用于协程内管控子任务。

## 调度器（协程的线程策略）

所有构建器都可指定 `Dispatcher`，决定协程执行的线程

| 调度器                 | 适用场景                          | 线程特性                      |
| ---------------------- | --------------------------------- | ----------------------------- |
| Dispatchers.Main       | Android UI 操作（更新控件、弹窗） | 主线程（单线程）              |
| Dispatchers.IO         | IO 密集型（网络、文件、数据库）   | 线程池（按需创建，最多 64）   |
| Dispatchers.Default    | CPU 密集型（计算、排序、解析）    | 线程池（核心数 = CPU 核心数） |
| Dispatchers.Unconfined | 无固定线程（少用）                | 随挂起点切换线程              |

## 核心区别对比表

| 构建器          | 返回值      | 是否阻塞线程 | 是否挂起   | 异常传播       | 核心场景                          |
| --------------- | ----------- | ------------ | ---------- | -------------- | --------------------------------- |
| launch          | Job         | 否           | 否         | 传播到父协程   | 无返回值异步任务（UI 更新、日志） |
| async/await     | Deferred<T> | 否           | await 挂起 | 传播到父协程   | 多任务并发、有返回值异步任务      |
| runBlocking     | T           | 是           | 否         | 传播到调用线程 | 测试 / 主函数（桥接同步代码）     |
| withContext     | T           | 否           | 是         | 传播到父协程   | 单任务线程切换、有返回值          |
| coroutineScope  | T           | 否           | 是         | 子协程一错全错 | 子任务依赖、统一异常处理          |
| supervisorScope | T           | 否           | 是         | 子协程互不影响 | 子任务独立、部分失败不影响整体    |

