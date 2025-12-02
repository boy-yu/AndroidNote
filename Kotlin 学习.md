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
"abcdef".drop(2) // 输出abcd

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

