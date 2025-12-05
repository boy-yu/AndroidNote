# 基本数据类型

## 整数类型

| 类型      | 位数 | 范围             |
| --------- | ---- | ---------------- |
| byte      | 8    | -128~127         |
| short     | 16   | -32,768 ~ 32,767 |
| int(默认) | 32   | -2^31 ~ 2^31 - 1 |
| long      | 64   | -2^63 ~ 2^63 - 1 |

常用的一些方法

```Java
// 定义
byte byteValue = 127;
short shortValue = 32767;
int intValue = 2147483647;
long longValue = 9223372036854775807L; // 注意后缀L


// ---------- 设置上下限 ----------
int a = 3;
// 范围限制
int result = Math.max(0, Math.min(a, 10)); // 输入范围在[0-10]
// 限制上限
result = Math.min(a, 5); // 不得高于5
// 限制下限
result = Math.max(a, 5); // 下限不得低于5


// ---------- 类型转换 ----------
int num1 = Integer.parseInt("123"); // 可能抛NumberFormatException
// 安全转换（需手动处理异常）
Integer num2 = null;
try {
    num2 = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    // 处理异常，num2保持null
}


// ---------- 比较大小 ----------
// a = 3 时
int compare1 = Integer.compare(a, 1); // 输出 1（大）
int compare2 = Integer.compare(a, 3); // 输出 0（等于）
int compare3 = Integer.compare(a, 5); // 输出 -1（小）
```

## 浮点类型

| 类型         | 位数 | 范围            |
| ------------ | ---- | --------------- |
| float        | 32位 | 6-7位有效数字   |
| double(默认) | 64位 | 15-16位有效数字 |

常用的一些方法

```Java
// 浮点数类型
float floatValue = 3.14F; // 注意后缀F
double doubleValue = 3.141592653589793;


// ---------- 四舍五入 ----------
long rounded1 = Math.round(3.6); // 4
long rounded2 = Math.round(3.4); // 3


// ---------- 取整 ----------
int intValue1 = (int) 3.9; // 3（直接截断）
double ceilValue = Math.ceil(3.1); // 4.0
double floorValue = Math.floor(3.9); // 3.0


// ---------- 截取小数点 ----------
double num = 3.1415926;
String result = String.format("%.2f", num);
System.out.println(result); // 3.14
```

## 字符/字符串

| 类型   | 位数 |
| ------ | ---- |
| char   | 16位 |
| String | -    |

常用的一些方法

```Java
// 字符类型
char charValue = 'A';
// 字符串类型
String stringValue = "Hello, Java!";


// ---------- 获取字符串核心信息 ----------
String s = "Hello";
int length = s.length(); // 5
char charAt = s.charAt(1); // 'e'
// 避免越界（需手动判断）
char safeChar = (5 < s.length()) ? s.charAt(5) : '\0'; // 类似getOrNull
// 首次出现的索引
int index1 = s.indexOf('l'); // 2
int index2 = s.indexOf("ll"); // 2
// 最后一次出现的索引
int lastIndex1 = s.lastIndexOf('l'); // 3
int lastIndex2 = s.lastIndexOf("lo"); // 3


// ---------- 获取字符串截取 ----------
String sub1 = "abcdef".substring(2); // "cdef"
// 截取范围[start,end)
String sub2 = "abcdef".substring(1, 4); // "bcd"
// 截取前n个字符
String take3 = "abcdef".substring(0, Math.min(3, "abcdef".length())); // "abc"
// 截取后n个字符
String takeLast3 = "abcdef".substring("abcdef".length() - 3); // "def"
// 舍弃前n个字符
String drop2 = "abcdef".substring(2); // "cdef"
// 舍弃后n个字符
String dropLast2 = "abcdef".substring(0, "abcdef".length() - 2); // "abcd"


// ---------- 字符串替换 ----------
String replaceAll = "abac".replace('a', 'x'); // "xbxc"
String replaceFirst = "abac".replaceFirst("a", "x"); // "xbac"
// 替换范围（需手动拼接）
String original = "abcdef";
String replaceRange = original.substring(0, 1) + "xyz" + original.substring(4); // "axyzef"


// ---------- 字符串分割 ----------
String[] split1 = "a,b,c".split(","); // ["a", "b", "c"]
String[] split2 = "a;b,c".split("[;,]"); // ["a", "b", "c"]


// ---------- 字符串大小写转换 ----------
String upper = "hello".toUpperCase(); // "HELLO"
String lower = "HELLO".toLowerCase(); // "hello"


// ---------- 字符串去空格/空白字符 ----------
String trimAll = " abc \t\n".trim(); // "abc"
String trimStart = " abc ".replaceAll("^\\s+", ""); // "abc "
String trimEnd = " abc ".replaceAll("\\s+$", ""); // " abc"


// ---------- 字符串判断与校验 ----------
boolean isEmpty1 = "".isEmpty(); // true
boolean isEmpty2 = " ".isEmpty(); // false
// 判断是否为空白字符
boolean isBlank1 = "".isBlank(); // true（Java 11+）
boolean isBlank2 = " ".isBlank(); // true（Java 11+）
// 判断指定开头
boolean startsWith = "https://www.baidu.com".startsWith("https"); // true
// 判断指定结尾
boolean endsWith = "test.txt".endsWith(".txt"); // true


// ---------- 判断字符/字符串是否相同 ----------
boolean equals = "abc".equals("abc"); // true
// 忽略大小写
boolean equalsIgnoreCase = "Admin".equalsIgnoreCase("admin"); // true
// 比较引用
String s1 = "abc";
String s2 = "abc"; // 常量池复用，引用相同
String s3 = new String(new char[]{'a', 'b', 'c'}); // 新对象，引用不同
boolean refEqual1 = s1 == s2; // true
boolean refEqual2 = s1 == s3; // false
```

## 布尔类型

```Java
boolean b = true;
```

# 集合类型

## 数组 Array

```Java
// 创建并初始化
String[] strArray = {"a", "b", "c"};

// 原始类型数组
int[] intArray = {1, 2, 3, 4};
double[] doubleArray = {1.1, 2.2};
float[] floatArray = {1.1f, 2.2f};
long[] longArray = {1L, 2L, 3L, 4L};
short[] shortArray = {1, 2, 3, 4};
byte[] byteArray = {1, 2, 3, 4};
boolean[] booleanArray = {true, false};
char[] charArray = {'a', 'b', 'c'};


// ---------- 常用操作 ----------
// 遍历
for (int num : intArray) {
    System.out.println(num); // 打印1、2、3、4
}
// 查找元素索引
int index = -1;
for (int i = 0; i < intArray.length; i++) {
    if (intArray[i] == 3) {
        index = i;
        break;
    }
} // 找到返回2，未找到返回-1
// 过滤元素（需手动实现或用Stream）
List<Integer> evenList = new ArrayList<>();
for (int num : intArray) {
    if (num % 2 == 0) {
        evenList.add(num);
    }
} // [2,4]
// 排序
int[] sortedArray = intArray.clone();
Arrays.sort(sortedArray); // 原数组不变，sortedArray为[1,2,3,4]
// 安全访问
Integer safeGet = (10 < intArray.length) ? intArray[10] : null; // 返回null
```

## List

Java中List分为**不可变**和**可变**（通过`List.of()`创建不可变列表，`ArrayList`为可变列表）

```Java
// 不可变List（Java 9+）
List<Integer> list = List.of(1, 2, 3);
// list.set(0, 10); // 报错：UnsupportedOperationException
// list.add(4); // 报错：UnsupportedOperationException

// 可变List
List<Integer> mList = new ArrayList<>(List.of(1, 2, 3));
mList.add(4); // 可以扩容
mList.set(0, 100); // 可以修改


// ---------- 遍历集合 ----------
// 增强for循环
for (int num : list) {
    System.out.println(num);
}
// 下标+值
for (int i = 0; i < list.size(); i++) {
    System.out.println("index:" + i + ",value:" + list.get(i));
}
// 迭代器
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}


// ---------- 查找元素 ----------
// 找到对应的元素
Integer item = null;
for (Integer num : list) {
    if (num == 10) {
        item = num;
        break;
    }
}
// 找到最后一个匹配（Java 8+ Stream）
Integer last = list.stream().filter(num -> num > 5).reduce((first, second) -> second).orElse(null);
// 查找满足条件的索引
int findIndex = -1;
for (int i = 0; i < list.size(); i++) {
    // 假设元素是对象，判断id==1
    // if (list.get(i).getId() == 1) {
    //     findIndex = i;
    //     break;
    // }
}
// 元素是否在list里
boolean contains = list.contains(3);
boolean isIn = 3 == list.get(0) || 3 == list.get(1) || 3 == list.get(2); // 简化判断


// ---------- List转换/映射操作 ----------
// 映射（Java 8+ Stream）
List<Pair> pairs = list.stream()
        .map(num -> new Pair(num, "name" + num)) // 假设Pair是自定义键值对类
        .collect(Collectors.toList());

// 转为Map（Java 8+ Stream）
Map<Integer, String> map1 = list.stream()
        .collect(Collectors.toMap(num -> num, num -> "name" + num));

// 筛选
List<Integer> evenNumbers = list.stream()
        .filter(num -> num % 2 == 0)
        .collect(Collectors.toList());

// 获取前两个
List<Integer> take2 = list.stream()
        .limit(2)
        .collect(Collectors.toList());

// 获取后两个
List<Integer> takeLast2 = list.stream()
        .skip(list.size() - 2)
        .collect(Collectors.toList());

// 舍弃前1个
List<Integer> drop1 = list.stream()
        .skip(1)
        .collect(Collectors.toList());


// ---------- List排序 ----------
List<Integer> sortedAsc = new ArrayList<>(list);
sortedAsc.sort(Integer::compareTo); // 升序
List<Integer> sortedDesc = new ArrayList<>(list);
sortedDesc.sort(Collections.reverseOrder()); // 降序
// 按条件排序（例如字符串按长度）
List<String> listString = List.of("apple", "banana", "cat");
List<String> sortedByLength = new ArrayList<>(listString);
sortedByLength.sort(Comparator.comparingInt(String::length));
```

## Map

Map是**键值对（Key-Value）** 容器，分为「不可变 Map」和「可变 Map」

| 实现类          | 特点                           | 适用场景                       |
| --------------- | ------------------------------ | ------------------------------ |
| HashMap（默认） | 无序、查询快（哈希表）、键唯一 | 日常键值映射（如配置项、缓存） |
| LinkedHashMap   | 有序（保留插入顺序）、查询快   | 需有序的键值映射（如历史记录） |
| TreeMap         | 键自然排序                     | 需按键排序的映射（如成绩排名） |

```Java
// 1. 不可变Map（Java 9+）
Map<String, Integer> immutableMap = Map.of(
        "a", 1,
        "b", 2,
        "c", 3
);
// immutableMap.put("a", 1); // 报错：UnsupportedOperationException

// 2. 可变Map
Map<String, Integer> mutableMap = new HashMap<>();
mutableMap.put("apple", 5);
mutableMap.put("banana", 3);
mutableMap.put("apple", 1); // 覆盖值


// ---------- 常用操作 ----------
mutableMap.put("apple", 1); // 修改值
Integer n = mutableMap.get("a"); // 可能为null
int age = mutableMap.getOrDefault("age", 0); // 为空时返回0
mutableMap.putIfAbsent("orange", 2); // 不存在则添加
mutableMap.remove("apple"); // 删除键


// ---------- 获取集合元素 ----------
// 遍历键值对
List<String> entries = new ArrayList<>();
for (Map.Entry<String, Integer> entry : mutableMap.entrySet()) {
    entries.add(entry.getKey() + ": " + entry.getValue());
} // 结果类似["apple: 5", "banana: 3"]


// 筛选
Map<String, Integer> filtered = new HashMap<>();
for (Map.Entry<String, Integer> entry : mutableMap.entrySet()) {
    if (entry.getKey().startsWith("a") && entry.getValue() > 2) {
        filtered.put(entry.getKey(), entry.getValue());
    }
}
for (Map.Entry<String, Integer> entry : filtered.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
} // 输出 apple: 5
```

## Set

Set是**无序、不可重复元素**的容器，分为「不可变 Set」和「可变 Set」

```Java
// 1. 不可变Set（Java 9+）
Set<String> immutableSet = Set.of("a", "b", "c", "a"); // 自动去重，实际为["a","b","c"]

// 2. 可变Set
Set<Integer> mutableSet = new HashSet<>(Set.of(1, 2, 3));


// ---------- 常用操作 ----------
mutableSet.add(4);
mutableSet.addAll(List.of(5, 6));

mutableSet.remove(3);
mutableSet.removeAll(List.of(5, 6));

mutableSet.clear(); // 清空


// 判断是否包含
boolean has2 = mutableSet.contains(2); // 返回true


// ---------- 通用操作 ----------
int size = immutableSet.size(); // 返回3

// 遍历
for (String s : immutableSet) {
    System.out.println(s);
}

// 转为List
List<String> setToList = new ArrayList<>(immutableSet); // ["a","b","c"]


// 交集（共同元素）
Set<Integer> set1 = Set.of(1, 2, 3);
Set<Integer> set2 = Set.of(3, 4, 5);
Set<Integer> intersection = new HashSet<>(set1);
intersection.retainAll(set2); // [3]

// 并集（合并去重）
Set<Integer> union = new HashSet<>(set1);
union.addAll(set2); // [1,2,3,4,5]

// 差集（A有B无）
Set<Integer> difference = new HashSet<>(set1);
difference.removeAll(set2); // [1,2]
```

# 空值处理（对应Kotlin可空类型）

Java中通过`null`表示空值，需手动处理空指针异常（NPE）

```Java
// 声明可空变量
String age = null; // 所有引用类型都可赋值为null

// 安全访问（手动判空）
Integer a = null;
if (age != null) {
    a = Integer.parseInt(age); // 非空时才转换
}

// 强制使用（可能抛NPE）
Integer b = Integer.parseInt(age); // 若age为null，抛NullPointerException
```

# 条件控制

## if else

```Java
// 基础写法
if (a > b) {
    // ...
} else if (a > c) {
    // ...
} else {
    // ...
}

// 简洁写法（三元运算符）
int c = (a > b) ? a : b;

// 区间判断
if (x >= 1 && x <= 8) {
    System.out.println("x 在区间内");
}
```

## switch（对应Kotlin的when）

```Java
int x = 0;
switch (x) {
    case 0:
    case 1:
        System.out.println("x=0 / x=1");
        break;
    default:
        System.out.println("otherwise");
}
// 输出 x=0 / x=1


switch (x) {
    case 1:
        System.out.println("x == 1");
        break;
    case 2:
        System.out.println("x == 2");
        break;
    default:
        System.out.println("x 不是 1");
        System.out.println("x 不是 2");
}
// 输出 x 不是 1 x 不是 2


// 区间判断（Java 17+ 模式匹配）
switch (x) {
    case int n when n >= 0 && n <= 10:
        System.out.println("x 在该区间范围内");
        break;
    default:
        System.out.println("x 不在该区间范围内");
}
// 输出 x 在该区间范围内
```

# 循环

## for

```Java
char[] charArr = {'a', 'b', 'c', 'd'};

// 增强for循环
for (char c : charArr) {
    System.out.print(c);
}

// 普通for循环（下标+值）
for (int i = 0; i < charArr.length; i++) {
    System.out.print("the element at " + i + " is " + charArr[i] + " ");
}

// 正序打印[1,4]
for (int i = 1; i <= 4; i++) {
    System.out.print(i);
} // 结果 1234

// 倒序打印[4,1]
for (int i = 4; i >= 1; i--) {
    System.out.print(i);
} // 结果 4321

// 步进打印（间隔1个）
for (int i = 1; i <= 10; i += 2) {
    System.out.print(i);
} // 13579

// 直到打印[1,4)
for (int i = 1; i < 4; i++) {
    System.out.print(i);
} // 123
```

## while

先判断后循环

```Java
int num = 0;
while (num > 0) {
    System.out.print(num--); // 无输出（初始num=0不满足条件）
}
```

## do...while

先循环后判断

```Java
int num = 0;
do {
    System.out.print(num--);
} while (num >= 0); // 输出 0（先执行一次，再判断）
```

## 跳过和跳出循环

```Java
for (int i = 1; i <= 5; i++) {
    if (i == 2) {
        continue; // 跳过当前循环
    }
    System.out.print(i);
    if (i == 3) {
        break; // 跳出当前循环
    }
}
// 输出 13
```

## 标签（对应Kotlin的标签）

```Java
loop: for (int i = 1; i <= 5; i++) {
    System.out.println(i);
    for (int j = 1; j <= 5; j++) {
        System.out.print(j);
        if (j == 2) {
            continue loop; // 跳到外层循环继续
        }
    }
}
// 输出类似：1 12 2 12 3 12 ...


loop: for (int i = 1; i <= 5; i++) {
    System.out.println(i);
    for (int j = 1; j <= 5; j++) {
        System.out.print(j);
        if (j == 2) {
            break loop; // 跳出外层循环
        }
    }
}
// 输出：1 12
```

# 类和对象

Java中类可包含：`构造函数`、`代码块`、`方法`、`属性`、`内部类`、`静态成员`等

## 类的属性

通过`private`字段+`getter/setter`实现属性访问控制（JavaBean规范）

```Java
public class Student {
    private int id; // 不可变（仅getter）
    private int age; // 可变（getter+setter）
    private String name; // 可变（getter+setter）

    // 构造函数
    public Student(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
    }

    // getter
    public int getId() {
        return id;
    }

    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

    // setter（id无setter，不可变）
    public void setAge(int age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }
}


// 使用
Student stu = new Student(0, 1, "w"); // 必须用new关键字
int id = stu.getId(); // 访问属性
stu.setAge(20); // 修改属性
int age = stu.getAge();
```

带逻辑的setter：

```Java
private int age;

public void setAge(int age) {
    this.age = (age > 0) ? age : 0; // 年龄小于0则设为0
}
```

## 构造函数

### 默认构造函数

若未显式定义构造函数，编译器会生成默认无参构造函数

```Java
public class Student {
    // 编译器隐式生成无参构造函数
}

// 使用
Student stu = new Student();
```

### 有参构造函数

```Java
public class Student {
    private int id;
    private int age;
    private String name;

    // 有参构造函数
    public Student(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
        System.out.println("Student " + id + " " + age + " " + name);
    }

    public void printAge() {
        System.out.println("Student " + name + " " + age);
    }
}

// 使用
Student stu = new Student(0, 1, "w");
stu.setAge(20);
stu.printAge(); // 输出 Student w 20
```

### 构造函数重载（对应Kotlin的次构造函数）

```Java
public class Student {
    private int id;
    private int age;
    private String name;

    // 主构造函数
    public Student(int id) {
        this.id = id;
        System.out.println("Student " + id);
    }

    // 构造函数重载（次构造函数）
    public Student(int id, int age, String name) {
        this(id); // 调用主构造函数
        this.age = age;
        this.name = name;
    }

    public void printAge() {
        System.out.println("Student " + id + " " + age);
    }
}

// 使用
Student stu1 = new Student(123);
Student stu2 = new Student(456, 20, "W");
```

## 抽象类

抽象类使用`abstract`修饰，可包含抽象方法（无实现）和具体方法（有实现）

```Java
public abstract class Animal {
    // 抽象方法（必须被子类实现）
    public abstract void eat();

    // 具体方法（子类可重写）
    public void sleep() {
        System.out.println("sleeping");
    }
}

public class Dog extends Animal {
    @Override
    public void eat() {
        System.out.println("eating");
    }

    @Override
    public void sleep() {
        super.sleep(); // 调用父类方法
        System.out.println("Dog sleeping");
    }
}

// 调用
Dog dog = new Dog();
dog.eat();
dog.sleep();
```

## 嵌套类（静态内部类）

Java中嵌套类默认是静态的，不持有外部类引用

```Java
public class Outer {
    private int bar = 1;

    // 嵌套类（静态内部类）
    public static class Nested {
        public int foo() {
            // 无法直接访问Outer的非静态成员bar
            return 2;
        }
    }
}

// 使用
int demo = new Outer.Nested().foo();
System.out.println(demo); // 输出 2
```

## 内部类（非静态内部类）

非静态内部类持有外部类引用，可访问外部类成员

```Java
public class Outer {
    private int bar = 1;
    String v = "成员属性";

    // 内部类（非静态）
    public class Inner {
        public int foo() {
            return bar; // 访问外部类成员
        }

        public void innerTest() {
            Outer o = Outer.this; // 获取外部类引用
            System.out.println("内部类可以引用外部类的成员，例如：" + o.v);
        }
    }
}

// 使用
int demo = new Outer().new Inner().foo();
System.out.println(demo); // 1
new Outer().new Inner().innerTest(); // 内部类可以引用外部类的成员，例如：成员属性
```

## 匿名内部类

通过接口或抽象类实现匿名内部类

```Java
public interface TestInterface {
    void test();
}

public class Test {
    String v = "成员属性";

    public void setInterface(TestInterface test) {
        test.test();
    }
}

// 实现
Test test = new Test();
// 匿名内部类实例
test.setInterface(new TestInterface() {
    @Override
    public void test() {
        System.out.println("匿名内部类的实例");
    }
});

// 保存引用（避免重复创建）
TestInterface testInterface = new TestInterface() {
    @Override
    public void test() {
        System.out.println("实现接口");
    }
};
test.setInterface(testInterface);
```

## 类的修饰符

- 类属性修饰符

```Java
abstract    // 抽象类  
final       // 类不可继承  
enum        // 枚举类  
public      // 公共类（默认不写时为包私有）
```

- 访问权限修饰符

```Java
private    // 仅当前类可见
protected  // 当前类、子类、同包可见
public     // 所有地方可见
default    // 同包可见（默认，不写修饰符时）
```

# 继承

Java中所有类默认继承`Object`类（根类），`Object`提供以下方法：

```Java
// 比较两个对象是否"内容相等"
public boolean equals(Object obj)

// 返回对象的哈希值
public int hashCode()

// 返回对象的字符串表示
public String toString()
```

类如需被继承，不能用`final`修饰（默认非final，可继承）

```Java
public class Base {
    private int p;

    public Base(int p) {
        this.p = p;
    }
}

public class Derived extends Base {
    public Derived(int p) {
        super(p); // 必须调用父类构造函数
    }
}
```

## 构造函数

### 子类有构造函数

子类构造函数必须通过`super()`调用父类构造函数

```Java
public class Person {
    protected String name;
    protected int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class Student extends Person {
    private String no;
    private int score;

    // 子类构造函数必须调用父类构造函数
    public Student(String name, int age, String no, int score) {
        super(name, age); // 调用父类构造函数
        this.no = no;
        this.score = score;
    }

    // 使用
    public static void main(String[] args) {
        Student s = new Student("jeremy", 18, "2019001", 100);
        System.out.println("学生名:" + s.name); // 学生名:jeremy
        System.out.println("年龄:" + s.age); // 年龄:18
    }
}
```

### 子类多构造函数

```Java
/**用户基类**/
public class Person {
    protected String name;

    public Person(String name) {
        this.name = name;
    }

    /**次级构造函数**/
    public Person(String name, int age) {
        this(name);
        System.out.println("-------基类次级构造函数---------");
    }
}

/**子类继承 Person 类**/
public class Student extends Person {

    /**次级构造函数**/
    public Student(String name, int age, String no, int score) {
        super(name, age); // 调用父类次级构造函数
        System.out.println("-------继承类次级构造函数---------");
        System.out.println("学生名：" + name);
        System.out.println("年龄：" + age);
    }

    public static void main(String[] args) {
        Student s = new Student("Runoob", 18, "S12345", 89);
    }
}
```

## 方法重写

父类方法需用`public`/`protected`修饰（不能是`private`），子类用`@Override`重写

```Java
/**用户基类**/
public class Person {
    // 可被重写（非final）
    public void study() {
        System.out.println("我毕业了");
    }
}

/**子类继承 Person 类**/
public class Student extends Person {

    @Override
    public void study() { // 重写方法
        System.out.println("我在读大学");
    }

    public static void main(String[] args) {
        Student s = new Student();
        s.study(); // 我在读大学
    }
}
```

多实现冲突处理（实现多个接口有同名方法）：

```Java
public interface A {
    default void f() {
        System.out.println("A");
    }

    void bar();
}

public interface B {
    default void f() {
        System.out.println("B");
    }

    default void bar() {
        System.out.println("bar B");
    }
}

public class C implements A, B {
    @Override
    public void f() {
        A.super.f(); // 调用A的f()
        B.super.f(); // 调用B的f()
    }

    @Override
    public void bar() {
        B.super.bar(); // 调用B的bar()
        System.out.println("bar C");
    }
}
```

## 属性重写

Java中属性不能直接重写，子类可声明同名属性（隐藏父类属性），需通过方法访问

```Java
public class Foo {
    protected int x = 10;

    public int getX() {
        return x;
    }
}

public class Bar extends Foo {
    private int x = 20; // 隐藏父类属性

    @Override
    public int getX() { // 重写getter实现属性"重写"效果
        return x;
    }
}
```

## 装饰器模式

```Java
public class Coffee {
    protected int cost;

    public int getCost() {
        return cost;
    }
}

public class SimpleCoffee extends Coffee {
    public SimpleCoffee() {
        cost = 10;
    }
}

public abstract class CoffeeDecorator extends Coffee {
    protected Coffee coffee;

    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    @Override
    public int getCost() {
        return coffee.getCost();
    }
}

/// 添加牛奶
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public int getCost() {
        return super.getCost() + 3;
    }
}

/// 添加糖
public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public int getCost() {
        return super.getCost() + 2;
    }
}

// 使用
Coffee coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
System.out.println("Coffee cost: " + coffee.getCost()); // 15
```

# 接口

## 实现接口

使用`interface`定义接口，Java 8+支持默认方法（`default`）

```Java
public interface MyInterface {
    // 抽象方法（必须实现）
    void bar();

    // 默认方法（可不用实现）
    default void foo() {
        System.out.println("foo");
    }
}

public class Child implements MyInterface {
    @Override
    public void bar() { // 必须实现
        System.out.println("bar");
    }
}

// 调用
Child child = new Child();
child.bar(); // bar
child.foo(); // foo
```

## 接口中的属性

接口中属性默认是`public static final`（常量）

```Java
public interface MyInterface {
    String NAME = "jeremy"; // 隐式为public static final
}

public class Child implements MyInterface {
    // 可直接使用接口常量
    public void printName() {
        System.out.println(NAME);
    }
}

// 调用
Child child = new Child();
child.printName(); // jeremy
```

## 接口与抽象类的区别

| 对比点   | 抽象类     | 接口          |
| -------- | ---------- | ------------- |
| 是否是类 | ✔ 是       | ✘ 不是类      |
| 构造函数 | ✔ 有       | ✘ 无          |
| 字段     | ✔ 真实字段 | ✘ 只能是常量  |
| 默认实现 | ✔ 支持     | ✔ Java 8+支持 |
| 多实现   | ✘ 单继承   | ✔ 多实现      |
| 关系     | 强 is-a    | 弱 can-do     |
| 适用场景 | 模板、基类 | 能力、规范    |

# 扩展（对应Kotlin扩展函数）

Java无原生扩展函数，可通过工具类静态方法模拟

```Java
// 被扩展类
public class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

// 扩展工具类
public class UserExtensions {
    // 模拟扩展函数
    public static void print(User user) {
        System.out.println("用户名 " + user.getName());
    }
}

// 使用
User user = new User("jeremy");
UserExtensions.print(user); // 用户名 jeremy
```

为集合添加扩展方法示例：

```Java
public class ListExtensions {
    public static void swap(List<Integer> list, int index1, int index2) {
        int tmp = list.get(index1);
        list.set(index1, list.get(index2));
        list.set(index2, tmp);
    }
}

// 使用
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));
ListExtensions.swap(list, 0, 2);
System.out.println(list); // [3, 2, 1]
```

# 数据类（对应Kotlin数据类）

Java中需手动重写`equals()`、`hashCode()`、`toString()`，或使用Lombok简化

```Java
import java.util.Objects;

public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 手动重写equals
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return age == user.age && Objects.equals(name, user.name);
    }

    // 手动重写hashCode
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    // 手动重写toString
    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }

    // copy方法（手动实现）
    public User copy(String name, Integer age) {
        return new User(
                name != null ? name : this.name,
                age != null ? age : this.age
        );
    }

    // getter
    public String getName() { return name; }
    public int getAge() { return age; }
}

// 使用
User oldUser = new User("jeremy", 18);
User newUser = oldUser.copy(null, 20); // 只修改age
System.out.println("oldUser:" + oldUser); // oldUser:User{name='jeremy', age=18}
System.out.println("newUser[name:" + newUser.getName() + ", age:" + newUser.getAge() + "]"); // newUser[name:jeremy, age:20]
```

# 密封类（对应Kotlin密封类）

Java 17+支持密封类（`sealed`），子类需用`final`/`sealed`/`non-sealed`修饰且在同一包或模块

```Java
// 密封类
public sealed class Result permits Success, Error, Loading {
}

// 成功
public final class Success extends Result {
    private String data;

    public Success(String data) {
        this.data = data;
    }

    public String getData() {
        return data;
    }
}

// 错误
public final class Error extends Result {
    private String message;

    public Error(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}

// 加载中（单例）
public final class Loading extends Result {
    public static final Loading INSTANCE = new Loading();

    private Loading() {}
}

// 使用（可穷举判断）
public class Main {
    public static void handleResult(Result result) {
        if (result instanceof Success) {
            System.out.println("Success: " + ((Success) result).getData());
        } else if (result instanceof Error) {
            System.out.println("Error: " + ((Error) result).getMessage());
        } else if (result instanceof Loading) {
            System.out.println("Loading...");
        }
        // 无需else，编译器确保覆盖所有子类
    }
}
```