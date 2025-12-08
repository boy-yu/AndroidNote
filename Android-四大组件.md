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

Activity 启动模式（Launch Mode）是 Android 用于管理「任务栈（Task Stack）」中 Activity 实例的核心规则，核心作用是**控制 Activity 实例的创建、复用、栈内位置**，解决重复创建、返回栈混乱、资源浪费等问题。

任务栈是 Android 管理 Activity 的「后进先出（LIFO）」栈结构，每个 App 对应至少一个任务栈（默认栈），核心规则：

- 启动新 Activity → 入栈（栈顶）；
- 按返回键 → 栈顶 Activity 出栈并销毁；
- 启动模式决定「新 Activity 是新建实例，还是复用已有实例」「实例放在哪个栈」。

### standard — 标准模式

> Activity的默认启动方式。

每启动一个`Activity`都会创建一个实例

<img src="./assets/17067221102e3a9a~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-4647697-4647702.awebp" alt="standard图示" style="zoom: 67%;" />

**设置方式(二选一既可)**

```xml
<activity
    android:name=".StandardActivity"
    android:launchMode="standard" /> <!-- 可省略，默认就是standard -->
```

```kotlin
// Activity中启动StandardActivity
val intent = Intent(this, StandardActivity::class.java)
// standard模式无需设置Flag，默认行为
startActivity(intent)
```

> 频繁启动会创建多个实例，可能导致栈过深
> 若需避免重复创建，优先用 `singleTop` 模式。

### singleTop — 栈顶复用

如果在栈顶，就不在创建实例。反之则创建。复用之前的页面

复用实例时，不会走 `onCreate`，而是走 `onNewIntent(intent)` 方法

应用场景：当前要跳转的页面已经在栈顶时，比如说**消息通知跳转**

![singleTop图示](./assets/170672210d283162~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-4647789-4647790.awebp)

> 此时Activity的生命周期为:`oldAonPause()`->`onNewIntent`->`newAResume`

特征：如果在栈顶，就不在创建实例。反之则创建。

**设置方式(二选一既可)**

```xml
<!-- 方式1：清单文件设置 -->
<activity
    android:name=".SingleTopActivity"
    android:launchMode="singleTop" />
```

```kotlin
<!-- 方式2：Intent Flag动态设置（优先级更高） -->
val intent = Intent(this, SingleTopActivity::class.java)
intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP)
startActivity(intent)
```

**重写 `onNewIntent` 处理新 Intent**

```kotlin
class SingleTopActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_single_top)
        Log.d("SingleTop", "onCreate：创建新实例")
        // 初始化逻辑（仅首次创建执行）
        handleIntent(intent)
    }

    // 复用实例时执行（替代onCreate处理新Intent）
    override fun onNewIntent(intent: Intent?) {
        super.onNewIntent(intent)
        Log.d("SingleTop", "onNewIntent：复用实例")
        // 更新当前Intent（否则getIntent()还是旧的）
        setIntent(intent)
        // 处理新Intent的参数
        handleIntent(intent)
    }

    // 统一处理Intent参数（onCreate和onNewIntent共用）
    private fun handleIntent(intent: Intent?) {
        intent?.extras?.let { bundle ->
            val data = bundle.getString("key")
            Log.d("SingleTop", "接收参数：$data")
        }
    }
}
```

> 必须重写 `onNewIntent` 并调用 `setIntent(intent)`，否则 `getIntent()` 获取的是旧 Intent
> 仅栈顶复用，非栈顶仍会新建，若需「全局单例」用 `singleTask`

### singleTask — 栈内复用

![singleTask图示](./assets/1706722110ca94aa~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-5112271.awebp)

特征：如果栈内存在，就**将后入栈的实例全部弹出**。

> 注意:
> 如果其他App进程开启了Activity1，这个时候会创建新的任务栈
> 如果以该启动模式启动的Activity1已经活动在后台的一个任务栈中，那么启动后，后台任务栈会一起切换到前台。

 **设置方式(二选一既可)**

```xml
<!-- 方式1：清单文件设置（推荐，可配合taskAffinity） -->
<activity
    android:name=".SingleTaskActivity"
    android:launchMode="singleTask"
    <!-- 可选：指定任务栈名称（默认用App默认栈） -->
    android:taskAffinity="com.example.myapp.singletask_stack" />
```

```kotlin
<!-- 方式2：Intent Flag动态设置（效果等价） -->
val intent = Intent(this, SingleTaskActivity::class.java)
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_SINGLE_TOP)
// FLAG_ACTIVITY_CLEAR_TOP：清空目标实例之上的Activity
// FLAG_ACTIVITY_SINGLE_TOP：复用栈顶的目标实例
startActivity(intent)
```

**重写 `onNewIntent` 处理参数（同 singleTop）**

```kotlin
class SingleTaskActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_single_task)
        Log.d("SingleTask", "onCreate：创建新实例")
        handleIntent(intent)
    }

    override fun onNewIntent(intent: Intent?) {
        super.onNewIntent(intent)
        Log.d("SingleTask", "onNewIntent：复用实例，清空上方Activity")
        setIntent(intent)
        handleIntent(intent)
    }

    private fun handleIntent(intent: Intent?) {
        // 处理参数逻辑
    }
}
```

> `singleTask` 会清空目标实例之上的 Activity，需注意业务逻辑
> `taskAffinity` 尽量用默认（App 包名），自定义栈名易导致任务栈混乱
> 不要滥用：仅核心页面使用，否则会破坏正常的返回栈逻辑。

### singleInstance — 全局唯一

> 跟前三种启动模式不同，他并不要求实例在同一个任务栈

![singleInstance图示](./assets/1706722110604665~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75-5112478.awebp)

**整个系统中仅存在一个实例**，且该实例独占一个独立的任务栈（与 App 默认栈分离）

其他应用 / 页面启动该 Activity 时，会切换到其独占的栈

返回时先清空该栈的 Activity，再回到原栈

**设置方式**

```xml
<!-- 清单文件设置（仅支持清单，无对应Intent Flag） -->
<activity
    android:name=".SingleInstanceActivity"
    android:launchMode="singleInstance" />
```

> 极度慎用：独占栈会导致返回逻辑复杂（用户按返回键可能跳转到非预期页面）；
> 仅系统级 / 全局唯一页面使用（如：手机的设置主页面、闹钟提醒页）；
> 与其他 Activity 通信需用跨栈方式（如：广播、EventBus、ViewModel + SharedFlow）

### 四大启动模式对比速查表

| 启动模式       | 实例创建规则                | 任务栈行为                         | 适用场景                             | 核心注意事项                             |
| -------------- | --------------------------- | ---------------------------------- | ------------------------------------ | ---------------------------------------- |
| standard       | 每次启动新建实例            | 新实例入当前栈顶                   | 普通页面（列表→详情、表单页）        | 避免频繁点击导致栈过深                   |
| singleTop      | 栈顶复用，非栈顶新建        | 复用则调用 onNewIntent，否则入栈顶 | 推送落地页、搜索页、栈顶防重复的页面 | 必须重写 onNewIntent 并 setIntent        |
| singleTask     | 栈内单例，清空上方 Activity | 复用则移到栈顶，否则新建入栈       | 首页、核心页、需清空中间页的页面     | 注意清空上方 Activity 导致的业务数据丢失 |
| singleInstance | 全局单例，独占独立栈        | 切换到独立栈，返回时先销毁自身栈   | 系统级全局页面（来电、锁屏）         | 极度慎用，返回逻辑复杂                   |

### 常见 Intent Flag 补充

| Flag 常量                  | 作用                                           | 适用场景                         |
| -------------------------- | ---------------------------------------------- | -------------------------------- |
| `FLAG_ACTIVITY_CLEAR_TOP`  | 清空目标 Activity 之上的所有 Activity          | 配合 singleTask 实现栈内单例     |
| `FLAG_ACTIVITY_NEW_TASK`   | 启动新任务栈（常用于 Service 中启动 Activity） | Service / 广播中启动 Activity    |
| `FLAG_ACTIVITY_CLEAR_TASK` | 清空当前任务栈，再启动新 Activity              | 退出登录后跳登录页，清空所有历史 |
| `FLAG_ACTIVITY_NO_HISTORY` | 启动的 Activity 不加入任务栈（返回时直接销毁） | 临时弹窗页、过渡页               |

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

# Broadcast Receiver

广播，在我们的应用中起着一个非常重要的角色。就比如说我们经常使用的`Intent`、`IntentFilter`，就有着广播的作用。

 **BroadcastReceiver 是做什么的**

它有 3 大能力：

| 能力                       | 说明                                                 |
| -------------------------- | ---------------------------------------------------- |
| **监听系统广播（最常用）** | 网络变化、电量变化、开机完成、耳机插拔、安装卸载 App |
| **App 内部事件通知**       | 例如登录成功→通知其他页面刷新 UI                     |
| **跨 App 发送消息**        | 比如支付宝启动微信支付（老机制，现在逐渐限制）       |

## 两种广播

虽然是两种广播形式，但是他们同样要干一件事情，就是继承`BroadcastReceiver`，并重写`onReceive()`方法。

### 应用内广播

在 Activity / Fragment 里写：

```kotlin
private val receiver = object : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        Log.d("Receiver", "收到广播: ${intent?.action}")
    }
}

/// 监听广播
override fun onResume() {
    super.onResume()
    registerReceiver(receiver, IntentFilter("广播的Key"))
}
/// 释放广播
override fun onPause() {
    super.onPause()
    unregisterReceiver(receiver)
}
```

###  静态注册（Manifest 注册）

在 manifest 中声明：

```xml
<receiver android:name=".MyReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```

Receiver 代码：

```kotlin
class MyReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        Log.d("Receiver", "开机完成广播收到")
    }
}
```

## 如何发送广播

```kotlin
/// 发送广播
val intent = Intent("MY_ACTION")
sendBroadcast(intent)

/// 接收广播
private val receiver = object : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        Log.d("Receiver", "收到广播: ${intent?.action}")
    }
}

/// 监听广播
override fun onResume() {
    super.onResume()
    registerReceiver(receiver, IntentFilter("MY_ACTION"))
}
/// 释放广播
override fun onPause() {
    super.onPause()
    unregisterReceiver(receiver)
}
```

---

# Services

Service 是 Android 四大组件之一，核心作用是**在后台执行长时间运行的操作**（无 UI 界面），其生命周期独立于 Activity，即使应用切后台 / Activity 销毁，Service 仍可继续运行。

## Service 核心基础

### 核心特性

- 运行在**主线程**（默认）：若执行耗时操作（如网络请求、文件读写），需手动创建子线程，否则会阻塞主线程导致 ANR；
- 生命周期：分「启动型」（`startService`）和「绑定型」（`bindService`）两种核心启动方式，对应不同生命周期；
- 进程特性：默认运行在应用主进程，可通过`android:process`配置独立进程（慎用，跨进程通信成本高）。

### 两种基础启动方式

#### startService

- **核心特点**

  启动后与调用者解耦，独立运行

- **生命周期**

  `onCreate(创建)`→`onStartCommand(开始)`→`onDestroy(销毁)`

- **通信方式**

  无直接通信（可通过广播 / EventBus）

#### bindService

- **核心特点**

  与调用者（如 Activity）绑定，共存亡

- **生命周期**

  `onCreate(创建)`→`onBind(绑定)`→`onUnbind(解绑)`→`onDestroy(销毁)`

- **通信方式**

  可通过 Binder/AIDL 双向通信

## 三大核心 Service 类型

### 后台 Service（普通启动型 Service）

**定义**

- 最基础的 Service，通过`startService`启动，在后台执行耗时任务；
- **Android 8.0+ 严格限制**：后台 Service 启动后会被系统快速限制（约 1 分钟后停止），仅前台可见时才能正常运行。

适用于应用前台运行时的短时后台任务（如：下载小文件、数据同步）

> 注意：Android 8.0+ 不推荐单独使用，优先用「前台 Service」或「WorkManager」替代。

```kotlin
// 1. 定义 Service 类
class MyBackgroundService : Service() {
    private val workThread = Thread {
        while (!Thread.currentThread().isInterrupted) {
            try {
                Thread.sleep(1000)
                Log.d("Service", "后台任务执行中...")
            } catch (_: Exception) {
                Thread.currentThread().interrupt()
                break
            }
        }
    }

    override fun onCreate() {
        super.onCreate()
        Log.d("Service", "服务创建成功")
    }


    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        if (!workThread.isAlive) {
            workThread.start()
        }
        // 返回值决定Services被系统杀死后的重启策略
        return  START_STICKY // 内存充足时重启，intent为null
    }

    override fun onDestroy() {
        super.onDestroy()
        workThread.interrupt() // 停止子线程
        Log.d("Service", "服务销毁")
    }


    // 绑定型Service才需要实现，启动型返回null即可
    override fun onBind(intent: Intent?): IBinder? = null
}

// 2. 清单文件注册（必须）
<service
    android:name=".MyBackgroundService"
    android:exported="false" /> <!-- 不暴露给其他应用 -->

// 3. 使用Services
// 启动
val intent = Intent(this, MyBackgroundService::class.java)
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    startForegroundService(intent) // 8.0+ 前台启动（但无通知会崩溃，仅演示）
} else {
    startService(intent)
}

// 停止（需手动调用，否则Service一直运行）
stopService(Intent(this, MyBackgroundService::class.java))
```

> 必须在子线程执行耗时操作，否则主线程 ANR；
> Android 8.0+ 直接用`startService`会抛异常，需用`startForegroundService`，但必须在 5 秒内调用`startForeground`展示通知（否则崩溃）；
> 不要依赖`START_STICKY`：系统低内存时仍会杀死 Service，且重启概率低。
>
> 

### 前台 Service（Foreground Service）

**定义**

- [后台 Service + 系统通知]的组合，启动后会在状态栏显示一个**持久通知**，告知用户应用正在后台运行
- 系统优先级高，不会被轻易杀死（即使应用切后台），是 Android 8.0+ 后台长任务的核心方案。

需长期后台运行的任务（如：音乐播放、导航、实时定位、大文件下载）;

必须向用户明示后台行为（通知不可隐藏）。

```kotlin
package com.example.imui.servers

import android.R
import android.app.NotificationChannel
import android.app.NotificationManager
import android.app.Service
import android.content.Intent
import android.os.Build
import android.os.IBinder
import android.util.Log
import androidx.core.app.NotificationCompat

class ForegroundService : Service() {
    /// 通知ID
    private val NOTIFICATION_ID = 1001

    /// 通知渠道ID
    private val CHANNEL_ID = "ForegroundServiceChannel"

    override fun onCreate() {
        super.onCreate()
        // 8.0+ 必须创建通知渠道
        createNotificationChannel()
        val notification = NotificationCompat
            .Builder(this, CHANNEL_ID)
            // 设置通知标题
            .setContentTitle("前台服务运行中")
            // 设置通知内容
            .setContentText("前台服务正在运行中...")
            // 设置通知图标
            .setSmallIcon(R.drawable.ic_dialog_info)
            .build()
        // 启动前台服务（5秒内调用，否则崩溃）
        startForeground(NOTIFICATION_ID, notification)
    }

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Thread {
            for (i in 0..5) {
                Thread.sleep(1000)
                Log.d("ForegroundService", "任务执行中：$i")
            }
            stopForeground(STOP_FOREGROUND_REMOVE) // 移除通知
            stopSelf() // 停止服务
        }.start()
        return START_NOT_STICKY
    }

    override fun onBind(intent: Intent?): IBinder? = null

    override fun onDestroy() {
        super.onDestroy()
        Log.d("ForegroundService", "onDestroy")
    }

    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                "前台服务通知",
                NotificationManager.IMPORTANCE_LOW // 低优先级，不弹窗
            )
            val manager = getSystemService(NotificationManager::class.java)
            manager.createNotificationChannel(channel)
        }
    }
}


/// 清单权限申明
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_SPECIAL_USE" />
    <!-- Android 13+ 部分场景需额外权限（如定位、媒体） -->
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

<service
    android:name=".servers.ForegroundService"
    android:exported="false"
    android:foregroundServiceType="specialUse">
    <!--从 Android 14 (API 34) 开始，前台服务(Foreground Services)必须声明特定的服务类型-->
    <property
        android:name="android.app.PROPERTY_SPECIAL_USE_FGS_SUBTYPE"
        android:value="示例用途说明" />
</service>

// Activity中启动
val intent = Intent(this, ForegroundService::class.java)
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    startForegroundService(intent)
} else {
    startService(intent)
}
```

> 必须申请`FOREGROUND_SERVICE`权限（Android 9+），13+ 需申请`POST_NOTIFICATIONS`权限才能显示通知；
> 通知渠道不可省略（8.0+），建议设置低优先级（`IMPORTANCE_LOW`）避免弹窗打扰用户；
> 任务结束后需手动调用`stopForeground`+`stopSelf`停止服务，否则通知会一直显示。

### 绑定 Service（Bound Service）

**核心特点**

- 通过`bindService`启动，与调用者（如 Activity）绑定，调用者销毁则 Service 自动解绑；
- 支持「双向通信」：Activity 可调用 Service 的方法，Service 也可回调 Activity。

需与前台 UI 交互的后台任务（如：音乐播放器的播放 / 暂停控制、实时获取后台计算数据）。

```kotlin
package com.example.imui.servers

import android.app.Service
import android.content.Intent
import android.os.Binder
import android.os.IBinder
import android.util.Log
import java.util.Timer
import java.util.TimerTask

class BoundService : Service() {
    // 自定义Binder，暴露Service的方法给Activity
    inner class MyBinder : Binder() {
        fun getService(): BoundService = this@BoundService
    }

    private val binder = MyBinder()
    private var count = 0
    private var timer: Timer? = null

    // 对外暴露的方法
    fun startCount() {
        timer = Timer()
        timer?.schedule(object : TimerTask() {
            override fun run() {
                count++
                // 回调Activity（可通过接口）
                onCountChangeListener?.onCountChange(count)
            }
        }, 0, 1000)
    }

    // 对外暴露的方法
    fun getCount(): Int = count

    fun stopCount() {
        timer?.cancel()
        count = 0
    }

    // 回调接口（Service通知Activity）
    interface OnCountChangeListener {
        fun onCountChange(count: Int)
    }

    var onCountChangeListener: OnCountChangeListener? = null

    override fun onBind(intent: Intent?): IBinder {
        Log.d("BoundService", "onBind")
        return binder
    }

    override fun onUnbind(intent: Intent?): Boolean {
        stopCount()
        Log.d("BoundService", "onUnbind")
        return super.onUnbind(intent)
    }

    override fun onDestroy() {
        super.onDestroy()
        stopCount()
        Log.d("BoundService", "onDestroy")
    }
}

// 2. 清单文件注册
<service
    android:name=".servers.BoundService"
    android:exported="false" />

class MainActivity : AppCompatActivity(), BoundService.OnCountChangeListener {

    private lateinit var binding: MainActivityBinding

    private var boundService: BoundService? = null
    private var isBound = false

    // 绑定Service的回调
    private val serviceConnection = object : ServiceConnection {
        override fun onServiceConnected(className: ComponentName, service: IBinder) {
            // 获取Service实例
            val binder = service as BoundService.MyBinder
            boundService = binder.getService()
            boundService?.onCountChangeListener = this@MainActivity
            isBound = true
            // 调用Service的方法
            boundService?.startCount()
        }

        override fun onServiceDisconnected(arg0: ComponentName) {
            isBound = false
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        binding = ActivityTextBinding.inflate(layoutInflater)
        setContentView(binding.root)
        // 绑定Service
        val intent = Intent(this, BoundService::class.java)
        bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE)
    }

    // 接收Service的回调
    override fun onCountChange(count: Int) {
        runOnUiThread {
            Log.d("Activity", "当前计数：$count")
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        // 解绑Service
        if (isBound) {
            unbindService(serviceConnection)
            isBound = false
        }
    }
}
```

> 绑定 Service 的生命周期与调用者绑定，Activity 销毁前必须调用`unbindService`，否则会内存泄漏；
> 跨进程绑定需用 AIDL(复杂度高，非必要不使用)；
> 可结合`startService`+`bindService`：既保持 Service 独立运行，又能通信（需手动管理生命周期）

### IntentService

已废弃，替代方案：WorkManager/CoroutineWorker

**核心特点**

- 官方封装的「异步 + 自动停止」Service，基于`HandlerThread`实现，处理完任务自动销毁
- **Android 11+ 标记为废弃**，推荐用`WorkManager`或`CoroutineService`替代

一次性后台任务（如：批量处理数据、单次文件上传）, 新项目直接用`WorkManager`，无需再用 IntentService

**替代方案示例:**

```kotlin
// 1. 定义Worker
class MyWorker(context: Context, params: WorkerParameters) : CoroutineWorker(context, params) {
    override suspend fun doWork(): Result {
        // 后台执行任务
        for (i in 1..5) {
            delay(1000)
            Log.d("WorkManager", "任务执行中：$i")
        }
        return Result.success()
    }
}

// 2. Activity中启动
val workRequest = OneTimeWorkRequestBuilder<MyWorker>()
    .setConstraints(Constraints.Builder()
        .setRequiredNetworkType(NetworkType.CONNECTED) // 网络可用时执行
        .build())
    .build()

WorkManager.getInstance(this).enqueue(workRequest)
```

## 各类型 Service 对比速查表

| 类型          | 核心优势               | 核心限制                     | 适用场景                         | Android 8.0+ 兼容性      |
| ------------- | ---------------------- | ---------------------------- | -------------------------------- | ------------------------ |
| 后台 Service  | 实现简单               | 系统快速限制，易被杀死       | 前台短时任务                     | 差（不推荐）             |
| 前台 Service  | 优先级高，不被轻易杀死 | 需显示通知，需申请权限       | 长时后台任务（音乐、导航、下载） | 优（推荐）               |
| 绑定 Service  | 与 UI 双向通信         | 与调用者共存亡，无法独立运行 | 需 UI 交互的后台任务             | 中（需结合前台）         |
| IntentService | 自动异步、自动停止     | 已废弃，不支持并发           | （历史）一次性后台任务           | 差（替代为 WorkManager） |

