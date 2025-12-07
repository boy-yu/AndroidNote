# Activiy

## Activiy生命周期

<img src="./assets/1707b6bfddac8589~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-4644870-4644872.awebp" alt="img" style="zoom:50%;" />

- `onCreate(创建)`:Activity**第一次创建**的时候调用
- `onRestart(重启)`:页面从onStop(停止)重启的时候调用
- `onStart(启动)`:页面可见,但不可交互
- `onResume(展示)`:页面可见且页面可交互
- `onPause(暂停)`:页面被部分遮挡/页面即将不可见
- `onStop(停止)`:页面被完全遮挡,不可见
- `onDestory(销毁)`:页面被完全销毁的时候调用

当A页面跳转到B页面:
会触发的生命周期为`AonPause`->`BonCreate`->`BonStart`->`BonResume`->`AonStop`

但B页面返回A页面:

会触发的生命周期为`BonPause`->`AonRestart`->`AonStart`->`AonResume`->`BonStop`->`BonDestory`

> 因为栈里面有一个A页面 所以会走一次`onRestart(重启)`

---

## 启动模式

### standard — 标准模式

> Activity的默认启动方式。

每启动一个`Activity`都会创建一个实例

<img src="./assets/17067221102e3a9a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-4647697-4647702.awebp" alt="standard图示" style="zoom: 67%;" />

### singleTop — 栈顶复用

如果在栈顶，就不在创建实例。反之则创建。复用之前的页面

应用场景：当前要跳转的页面已经在栈顶时，比如说**消息通知跳转**

![singleTop图示](./assets/170672210d283162~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-4647789-4647790.awebp)

> 此时Activity的生命周期为:`oldAonPause()`->`onNewIntent`->`newAResume`

特征：如果在栈顶，就不在创建实例。反之则创建。

### singleTask — 栈内复用

![singleTask图示](./assets/1706722110ca94aa~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-5112271.awebp)

特征：如果栈内存在，就**将后入栈的实例全部弹出**。

> 注意:
> 如果其他App进程开启了Activity1，这个时候会创建新的任务栈
> 如果以该启动模式启动的Activity1已经活动在后台的一个任务栈中，那么启动后，后台任务栈会一起切换到前台。

### singleInstance — 全局唯一

> 跟前三种启动模式不同，他并不要求实例在同一个任务栈

![singleInstance图示](./assets/1706722110604665~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-5112478.awebp)

**系统中**只会有一个实例。

## 事件分发机制

### 事件序列解析

所谓的安卓事件是什么？具体来说的就是**点击和滑动**两个操作；抽象着来说就是下面的表格。

| MotionEvent/事件类型 | 具体操作       |
| -------------------- | -------------- |
| ACTION_DOWN          | 点下View       |
| ACTION_UP            | 抬起View       |
| ACTION_MOVE          | 滑动View       |
| ACTION_CANCEL        | 非人为因素取消 |

> 事件序列一般组成:
> 点击的事件组成就是:Down(按下) --> Up(抬起)
> 滑动的事件组成就是:Down(按下) --> Move(滑动) --> Move .... --> Up(抬起)
>
> 

### 事件分发

使用到的函数

- `dispatchTouchEvent()`:用于事件分发
- `onTouchEvent()`:消费事件
- `onInterceptTouchEvent()`:判断是否拦截事件，仅存在于ViewGroup

分发对象

- Activity
- ViewGroup
- View

### Activity的事件分发

```kotlin
fun dispatchTouchEvent(ev: MotionEvent): Boolean {
    // 从判断语句中可以得出所有事件的起点就是Down
    if (ev.action == MotionEvent.ACTION_DOWN) {
        // TODO 实现逻辑
    }
    // 向上传递至ViewGroup，调用其dispatchTouchEvent
    if (getWindow().superDispatchTouchEvent(ev)) {
        return true
    }
    return onTouchEvent(ev)
}
```

其中`getWindow()`获得就是一个`Window`抽象类，根据其子类`PhoneWindow`我们可以很容易得知最后调用的其实就是`ViewGroup`的`dispatchTouchEvent()`方法

```kotlin
/**
 * 实际上就是判断事件是否是DOWN事件，event的坐标是否在边界内等
 */
fun onTouchEvent(ev: MotionEvent): Boolean {
    if (mWindow.shouldCloseOnTouch(this, ev)) {
        finish()
        return true
    }
    return false
}
```

最后就是`Activity`中的`onTouchEvent()`方法了，这个模块干的事情在注释中也就很清晰明了了。

### ViewGroup的事件分发

[有点绕先搁置](https://juejin.cn/post/6844903972411539469)

## Activity之间的数据传输

在`Activity`的跳转之间，其实存在很多需要带上数据的情况。举个例子来说，就像是从`Activity A`一个新闻列表中选取了一条新闻，那这个时候就需要跳转进入`Activity B`，而查询新闻的方式我们就假设是他的`id`那这个时候，就需要将这个`id`从`Activity A`送到`Activity B`中。

### Intent + Bundle

**使用方法:**

```kotlin
/// Intent + Bundle（基础）
val intent = Intent(this, BActivity::class.java)
intent.putExtra("name", "Jeremy")
intent.putExtra("age", 22)
startActivity(intent)

override fun onCreate(savedInstanceState: Bundle?) {
...
/// 接收值
val name = intent.getStringExtra("name")
val age = intent.getIntExtra("age", 0)
}
```

### 传对象（Parcelable）

```kotlin
/// 传递一个对象(官网推荐做法)
// 定义一个 Parcelable 对象
@Parcelize
data class User(val name: String,val age: Int) : Parcelable
val user = User("Jeremy", 22)

val intent = Intent(this, BActivity::class.java)
intent.putExtra("user", user)
startActivity(intent)


override fun onCreate(savedInstanceState: Bundle?) {
...
/// 接收值
val user = intent.getParcelableExtra<User>("user")
}
```

### 传列表（Parcelable List）

```kotlin
val intent = Intent(this, BActivity::class.java)
intent.putParcelableArrayListExtra("list", arrayListOf(user1,user2))
startActivity(intent)

/// 接受值
override fun onCreate(savedInstanceState: Bundle?) {
...
/// 接收值
val list = intent.getParcelableArrayListExtra<User>("list")
}
```

### 返回数据给上一个页面

使用 `ActivityResultLauncher`首先我们需要在`Activity A`注册一个回调用于接收返回值

``` kotlin
val launcher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
    // RESULT_CODE:指代的上级Activity的返回码
    if (it.resultCode == RESULT_CODE) {
        val data = it.data
        if (data != null) {
            val user = data.getParcelableExtra("user", User::class.java)
            binding.text.text = user?.name
        }
    }
}
```

然后在传递值:

```kotlin
// 注意这个使用的是launcher
val intent = Intent(this, TextActivity::class.java)
intent.putExtra("user", user)
launcher.launch(intent)
```

最后在`Activity B`合适的位置把数据返回给`Activity A`

``` kotlin
val intent = Intent()
intent.putExtra("user", user)
// 这里我们使用的是RESULT_OK 那么在Activity A 也要使用RESULT_OK 才能接收到
setResult(RESULT_OK, intent)
finish()
```

> 我们的数据，也就是`Intent`中使用`putExtra(key, value)`放置的数据最后都被封装进了`Bundle`，所以说`Bundle`其实就是我们`Activity`之间数据传递的媒介。

---

# ContentProvider

ContentProvider 是 Android 用来在不同应用之间共享数据的组件。它负责对外暴露增删改查（CRUD）接口，让其它 App 能使用统一的方式访问你的数据。

你可以把它理解成：

🟦 **Android 里的“数据库服务端 + API 服务器”**
🟩 **外部应用通过`URI`调用它来读写数据**
🟧 **系统通过它实现跨应用数据共享（联系人、短信、图片等）**

## ContentProvider 的访问流程

访问 ContentProvider 不是直接去找 SQLite
而是通过 **URI → ContentResolver(内容解析器) → ContentProvider(内容提供商)**

```
你（App A） ── ContentResolver ──► ContentProvider（App B） ──► 数据库/文件
```

- ContentProvider = 服务端 API
- ContentResolver = 客户端请求器
- URI = API 地址

这三个一起构成 Android 的跨应用访问系统。

ContentProvider 用来做什么?

### 什么是 URI

比如访问联系人：

```less
content://com.android.contacts/contacts/1
```

格式

```less
content://[authority]/[path]/[id]
```

- **authority**：提供者的独一 ID（类似域名）
- **path**：访问的数据表类型
- **id**：具体某一条

### ContentResolver 又是什么

所有访问 ContentProvider 的入口！

```kotlin
/// 查询
contentResolver.query()
/// 插入
contentResolver.insert()
/// 更新
contentResolver.update()
/// 删除
contentResolver.delete()
```

ContentProvider 的 6 个核心方法

```kotlin
query()    // 查询
insert()   // 插入
update()   // 更新
delete()   // 删除
getType()  // 数据类型
onCreate() // 初始化
```

所有外部 App 想操作你的数据，都会走这 6 个方法。

## 访问通讯录

申请访问通讯录权限

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" />
```

添加运行时权限 

``` kotlin
override fun onCreate(savedInstanceState: Bundle?) {
  ...
  ActivityCompat.requestPermissions(
  	this,
		arrayOf(Manifest.permission.READ_CONTACTS),
		100
	)
}
```

接收回调：

```kotlin
override fun onRequestPermissionsResult(
    requestCode: Int,
    permissions: Array<out String>,
    grantResults: IntArray,
) {
    val isGranted = grantResults[0] == PackageManager.PERMISSION_GRANTED
    if (requestCode == 100 && isGranted) {
        /// 访问通讯录数据
        loadContacts()
    }
}
```

访问通讯录数据:

```kotlin
private fun loadContacts() {
    val list = mutableListOf<Pair<String, String>>() // name, phone

    /// 查询数据
    val cursor = contentResolver.query(
        // 位置暴露的唯一表示
        ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
        // 返回列（字段）
        arrayOf(
            ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME,
            ContactsContract.CommonDataKinds.Phone.NUMBER
        ),
        // 设置条件，相当于数据库中的where
        null,
        // 和selection联合使用，用于替换selection中的 ?
        null,
        // 排列顺序，相当于数据库中的order by
        null
    )

    cursor?.use {
        val nameIndex = it.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME)
        val numberIndex = it.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER)

        while (it.moveToNext()) {
            val name = it.getString(nameIndex)
            val number = it.getString(numberIndex)
            //可以把 list 返回给 Adapter 或 UI
            list.add(name to number)
            Log.d("Contacts", "Name: $name, Phone: $number")
        }
    }
}
```

---



