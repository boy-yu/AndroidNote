## Android Application

### Application 的核心定义

`Application` 是 Android 系统为每个应用进程提供的**全局核心组件**，也是应用的 “进程级入口”—— 它在应用进程创建时最先初始化（早于所有 Activity/Fragment/Service），在进程销毁时最后结束，贯穿整个应用的生命周期；一个应用默认只会创建一个 `Application` 实例，是全局唯一的 “上下文载体” 和 “统一管理中枢”。

> `Application` 不是界面组件，也不负责用户交互，核心作用是**承载全局逻辑**（如初始化、状态管理），而非处理界面相关操作。

### Application 核心特性

| 特性              | 书中关键描述                                                 | 对比 Activity/Fragment                          |
| ----------------- | ------------------------------------------------------------ | ----------------------------------------------- |
| 生命周期最长      | “进程生则 Application 生，进程死则 Application 死”，仅 `onCreate()` 执行一次 | 组件级生命周期（如 Activity 可多次创建 / 销毁） |
| 全局唯一性        | 一个应用进程中只有一个 Application 实例，所有组件共享该实例  | 可同时存在多个 Activity/Fragment 实例           |
| 上下文安全性      | Application Context 不依附于任何组件，无内存泄漏风险         | Activity Context 持有组件引用，易泄漏           |
| 无界面 / 无任务栈 | 无界面、无布局，也不依赖任务栈，无法直接启动 Activity / 弹 Dialog | 有界面、依赖任务栈，可处理交互操作              |

### Application 核心价值

- **全局初始化入口**

  适合初始化第三方库、全局工具类等.

  ```kotlin
  class MyApplication : Application() {
      override fun onCreate() {
          super.onCreate()
          // 初始化网络库（Retrofit）、数据库（Room）、图片加载库（Glide）
          RetrofitManager.init(context)
          AppDatabase.getInstance(context)
          Glide.init(context)
      }
  }
  ```

- **提供安全的全局上下文**

  “避免在工具类 / 单例中持有 Activity Context”（会导致组件无法回收），而 `Application Context` 是 “安全的全局上下文”

  ```kotlin
  class MyApplication : Application() {
      companion object {
          lateinit var context: Context // 全局上下文
      }
  
      override fun onCreate() {
          super.onCreate()
          context = applicationContext
      }
  }
  ```

- **跨组件共享全局状态**

  用 `Application` 存储 “用户登录状态、Token” 等全局数据，实现跨 Activity 共享

  ```kotlin
  class MyApplication : Application() {
      companion object {
          lateinit var context: Context
          var isLogin = false // 全局登录状态（跨 Activity 共享）
          var userToken: String = "" // 全局 Token
      }
  }
  
  // 任意 Activity 中直接访问
  if (!MyApplication.isLogin) {
      // 未登录，跳转登录页
      startActivity(Intent(this, LoginActivity::class.java))
  }
  ```

- **全局组件管理**

  结合 `Application` 封装 `ActivityCollector` 工具类，统一管理所有 Activity 实例，实现 “一键销毁所有 Activity”

  ```kotlin
  /// 通过监听实现 Activity的关闭
  object ActivityUtils : Application.ActivityLifecycleCallbacks {
  
      private val activityList = mutableListOf<Activity>()
  
      fun removeActivity(act: Activity) = activityList.remove(act)
      fun finishAll() {
          activityList.forEach { if (!it.isFinishing) it.finish() }
          activityList.clear()
      }
  
      override fun onActivityCreated(
          activity: Activity,
          savedInstanceState: Bundle?
      ) {
          activityList.addFirst(activity)
          Log.d("ActivityLifecycleImpl", "onActivityCreated: ${activity.localClassName}")
      }
  
      override fun onActivityDestroyed(activity: Activity) {
          activityList.remove(activity);
          Log.d("ActivityLifecycleImpl", "onActivityDestroyed: ${activity.localClassName}")
      }
  }
  
  class MyApplication : Application() {
      companion object {
          lateinit var context: Context
      }
     
      override fun onCreate() {
          super.onCreate()
          context = applicationContext
        	// 初始化监听
          registerActivityLifecycleCallbacks(ActivityLifecycleImpl)
      }
  }
  
  /// 注册 Application
  android:name=".MyApplication"
  ```

### Application的Context和Activity的Context的区别

**Application 的 Context（以下简称 AppContext）和 Activity 的 Context（以下简称 AcContext）的核心区别在于 “生命周期维度” 和 “功能边界”** —— 前者是 “进程级全局上下文”，后者是 “组件级界面上下文”，

#### 启动 Activity

- **AcContext**：可直接启动（默认关联当前任务栈），书中所有 Activity 跳转案例均用此：

  ```kotlin
  // 标准写法（Activity 中跳转）
  startActivity(Intent(this, SecondActivity::class.java)) 
  // this = ActivityContext
  ```

- **AppContext**：直接启动会崩溃，必须添加 `FLAG_ACTIVITY_NEW_TASK`（创建新任务栈），不推荐此用法（易导致任务栈混乱）

  ```kotlin
  // 书中不推荐的写法（仅应急使用）
  val intent = Intent(MyApplication.context, SecondActivity::class.java)
  intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
  MyApplication.context.startActivity(intent)
  ```

####  弹出 Dialog

- **AcContext**：正常弹 Dialog（依赖 Activity 的窗口），以“强制下线” 的 Dialog：

  ```kotlin
  // 强制下线案例的 Dialog（用 ActivityContext）
  AlertDialog.Builder(this) // this = ActivityContext
      .setTitle("强制下线")
      .setMessage("请重新登录")
      .show()
  ```

- **AppContext:**弹 Dialog 直接崩溃(无窗口载体)

### 关键注意事项

1. **必须在清单文件注册**:
   自定义 `Application` 需在 `AndroidManifest.xml` 的 `<application>` 标签中指定 `android:name=".MyApplication"`，否则系统会使用默认实例

2. **禁止做耗时操作**:
   `onCreate()` 运行在主线程，若执行网络请求、大数据解析等耗时操作，会导致应用启动卡顿

3. **禁止持有组件引用**:
   不要在 `Application` 中持有 Activity/Fragment 的强引用，否则会导致组件无法回收

4. **Context 场景限制**:

   Application Context 不能用于启动 Activity（需任务栈）、弹 Dialog（需窗口载体），仅适用于全局工具、初始化等场景

### 总结

`Application` 是 Android 应用的 “全局管家”—— 它不负责具体的界面交互，而是承担 “全局初始化、状态管理、上下文提供” 的核心职责，是实现应用 “全局化、低耦合” 开发的基础。合理使用 `Application` 可统一管理全局逻辑，避免重复代码，同时解决 Context 泄漏等常见问题。
