# 生命周期

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

当屏幕旋转:

`onPause()`→`onSaveInstanceState()`→`onStop()`→`onDestroy()` → 新实例 `onCreate()`→`onStart()`→`onRestoreInstanceState()`→`onResume()`

## 数据保存与恢复

**onSaveInstanceState**

```kotlin
// 保存数据（Activity 销毁前调用，如屏幕旋转、系统回收）
override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    val userInput = "需要保存的内容"
    outState.putString("key_input", userInput) // 存入 Bundle（支持基本类型、序列化对象）
}
```

**onRestoreInstanceState**

```kotlin
// 恢复数据（两种方式）
// 方式1：在 onCreate 中恢复（适用于初始化时依赖数据的场景）
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_first)
    if (savedInstanceState != null) {
        val restoredInput = savedInstanceState.getString("key_input") // 取出数据
        // 更新 UI
    }
}

// 方式2：重写 onRestoreInstanceState（专门用于恢复数据，调用时机晚于 onCreate）
override fun onRestoreInstanceState(savedInstanceState: Bundle) {
    super.onRestoreInstanceState(savedInstanceState)
    val restoredInput = savedInstanceState.getString("key_input")
    // 更新 UI
}
```

# Activity 启动模式

Activity 启动模式（Launch Mode）是 Android 用于管理「任务栈（Task Stack）」中 Activity 实例的核心规则，核心作用是**控制 Activity 实例的创建、复用、栈内位置**，解决重复创建、返回栈混乱、资源浪费等问题。

任务栈是 Android 管理 Activity 的「后进先出（LIFO）」栈结构，每个 App 对应至少一个任务栈（默认栈），核心规则：

- 启动新 Activity → 入栈（栈顶）；
- 按返回键 → 栈顶 Activity 出栈并销毁；
- 启动模式决定「新 Activity 是新建实例，还是复用已有实例」「实例放在哪个栈」。

## standard — 标准模式

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

## singleTop — 栈顶复用

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

## singleTask — 栈内复用

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

## singleInstance — 全局唯一

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

## 四大启动模式对比速查表

| 启动模式       | 实例创建规则                | 任务栈行为                         | 适用场景                             | 核心注意事项                             |
| -------------- | --------------------------- | ---------------------------------- | ------------------------------------ | ---------------------------------------- |
| standard       | 每次启动新建实例            | 新实例入当前栈顶                   | 普通页面（列表→详情、表单页）        | 避免频繁点击导致栈过深                   |
| singleTop      | 栈顶复用，非栈顶新建        | 复用则调用 onNewIntent，否则入栈顶 | 推送落地页、搜索页、栈顶防重复的页面 | 必须重写 onNewIntent 并 setIntent        |
| singleTask     | 栈内单例，清空上方 Activity | 复用则移到栈顶，否则新建入栈       | 首页、核心页、需清空中间页的页面     | 注意清空上方 Activity 导致的业务数据丢失 |
| singleInstance | 全局单例，独占独立栈        | 切换到独立栈，返回时先销毁自身栈   | 系统级全局页面（来电、锁屏）         | 极度慎用，返回逻辑复杂                   |

# Intent 与 Activity 通信

Intent 是 “意图”，用于组件间通信（如启动 Activity、传递数据），分为**显式 Intent**（明确指定目标组件）和**隐式 Intent**（通过动作、类别匹配目标组件）。

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
            // 处理返回数据
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



