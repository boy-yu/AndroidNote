# Lifecycle

所有生命周期感知型组件（ViewModel/LiveData）均基于它实现。核心作用是**将组件的生命周期状态暴露为可观察对象，让外部类无需耦合在 Activity/Fragment 的生命周期回调中**，解决传统 “在 onResume/onPause 中手动管理资源” 的冗余问题。

## 基本用法

定义生命周期观察者:

```kotlin
import android.util.Log
import androidx.lifecycle.DefaultLifecycleObserver
import androidx.lifecycle.LifecycleOwner

class LocationLifecycleObserver : DefaultLifecycleObserver {
    // 监听ON_RESUME事件（组件进入前台）
    override fun onResume(owner: LifecycleOwner) {
        Log.d("Lifecycle", "开始定位：组件回到前台")
        super.onResume(owner)
    }

    // 监听ON_PAUSE事件（组件退到后台）
    override fun onPause(owner: LifecycleOwner) {
        Log.d("Lifecycle", "停止定位：组件退到后台")
        super.onPause(owner)
    }


    // 监听ON_DESTROY事件（组件销毁）
    override fun onDestroy(owner: LifecycleOwner) {
        Log.d("Lifecycle", "释放定位资源：组件销毁")
        super.onDestroy(owner)
    }
}
```

在Activity中注册
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 注册观察者：无需重写生命周期回调
        lifecycle.addObserver(LocationLifecycleObserver())
    }
}
```

## 进阶用法

1. 基于 Lifecycle.State 手动判断状态
   适用于需要主动检查组件生命周期状态的场景：

   ```kotlin
   class DataRepository {
       fun fetchData(lifecycle: Lifecycle, callback: (String) -> Unit) {
           // 仅当组件处于活跃状态（STARTED/RESUMED）时执行回调
           if (lifecycle.currentState.isAtLeast(Lifecycle.State.STARTED)) {
               callback("模拟网络请求数据")
           }
       }
   }
   
   // Activity中调用
   val repository = DataRepository()
   repository.fetchData(lifecycle) { data ->
       tvData.text = data
   }
   ```

2. **LifecycleCoroutineScope（生命周期感知的协程作用域**
   结合 Kotlin 协程推荐的用法，协程会随组件销毁自动取消，避免泄漏：

   ```kotlin
   class MainActivity : AppCompatActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)
           
           // 生命周期感知的协程：Activity销毁时自动取消
           lifecycleScope.launch {
               // 模拟耗时任务
               delay(3000)
               println("协程执行完成：${lifecycle.currentState}")
           }
   
           // 仅在RESUMED状态执行，暂停/销毁时挂起
           lifecycleScope.launchWhenResumed {
               // 仅前台可交互时执行的逻辑（如实时刷新）
           }
       }
   }
   ```

## 注意事项

1. 观察者注解兼容：`@OnLifecycleEvent`在高版本已标记为过时，推荐使用`DefaultLifecycleObserver:
   ```kotlin
   class LocationObserver : DefaultLifecycleObserver {
       override fun onResume(owner: LifecycleOwner) {
           super.onResume(owner)
           startLocation()
       }
   
       override fun onPause(owner: LifecycleOwner) {
           super.onPause(owner)
           stopLocation()
       }
   }
   ```

2. 避免在观察者中执行耗时操作：所有生命周期回调运行在主线程，耗时操作需开协程；

3. 及时移除观察者：若观察者持有外部资源（如网络连接），需在`onDestroy`前手动移除（`lifecycle.removeObserver(observer)`）。

---

# ViewModel

专门存储 “与界面相关数据” 的类，生命周期独立于 Activity/Fragment 的配置变化（如屏幕旋转），仅在组件真正销毁（`finish()`）时回收。

核心解决：

1. 配置变化导致数据丢失;
2. UI 逻辑与数据逻辑耦合;

## 基本用法

**创建基础 ViewModel 类**

```kotlin
import androidx.lifecycle.ViewModel

// 无需Application上下文 → 继承ViewModel
class CounterViewModel : ViewModel() {
    // 存储界面数据（屏幕旋转不丢失）
    var count = 0

    // 封装数据操作逻辑（单一职责）
    fun increment() {
        count++
    }

    fun decrement() {
        count--
    }
}
```

Activity/Fragment 中获取 ViewModel 实例

```kotlin
import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.activity.viewModels
import androidx.appcompat.app.AppCompatActivity
import com.jeremy.jetpackdemo.databinding.ActivityViewModelBinding

class ViewModelActivity : AppCompatActivity() {
    lateinit var binding: ActivityViewModelBinding

    // Kotlin委托by viewModels()（简化ViewModelProvider）
    private val counterViewModel: CounterViewModel by viewModels()
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        binding = ActivityViewModelBinding.inflate(layoutInflater)
        setContentView(binding.root)
        updateCountUI()
        // 绑定UI事件
        binding.btnAdd.setOnClickListener {
            counterViewModel.increment()
            updateCountUI()
        }

        binding.btnSub.setOnClickListener {
            counterViewModel.decrement()
            updateCountUI()
        }

    }

    private fun updateCountUI() {
        binding.tvData.text = counterViewModel.count.toString()
    }
}
```

## 进阶用法

1. **ViewModelFactory**

   解决 ViewModel 无法直接传参的问题，推荐的工厂模式：

   ```kotlin
   import androidx.activity.viewModels
   import androidx.appcompat.app.AppCompatActivity
   import androidx.lifecycle.ViewModel
   import androidx.lifecycle.ViewModelProvider
   
   /// 模拟用户数据
   data class UserInfo(val uid: String, val name: String, val age: Int)
   
   /// 模拟用户数据仓库
   class UserRepository {
       companion object {
           fun fetchUser(uid: String): UserInfo {
               return UserInfo(uid, "jeremy", 18)
           }
       }
   }
   
   // 1. 定义带参数的ViewModel
   class UserViewModel(private val uid: String) : ViewModel() {
       /// 根据用户ID加载用户数据
       fun loadUserInfo(): UserInfo = UserRepository.fetchUser(uid)
   }
   // 2. 定义ViewModel工厂
   class UserViewModelFactory(private val uid: String) : ViewModelProvider.Factory {
       override fun <T : ViewModel> create(modelClass: Class<T>): T {
           // 反射创建带参数的ViewModel实例
           if (modelClass.isAssignableFrom(UserViewModel::class.java)) {
               @Suppress("UNCHECKED_CAST")
               return UserViewModel(uid).loadUserInfo() as T
           }
           throw IllegalArgumentException("Unknown ViewModel class")
       }
   }
   
   // 3. Activity中获取带参数的ViewModel
   class UserActivity : AppCompatActivity() {
       private val userId = "1001"
   
       // 传入工厂创建ViewModel
       private val userViewModel: UserViewModel by viewModels {
           UserViewModelFactory(userId)
       }
   }
   ```

2. **跨 Fragment 共享 ViewModel（同 Activity 下）**

   “Fragment 通信” 进阶方案，无需接口回调

   ```kotlin
   // Activity下的两个Fragment共享同一个ViewModel
   class HomeFragment : Fragment() {
       // 共享ViewModel：使用activityViewModels()而非viewModels()
       private val sharedViewModel: SharedViewModel by activityViewModels()
   
       override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
           super.onViewCreated(view, savedInstanceState)
           // 发送数据到ViewModel
           btnSend.setOnClickListener {
               sharedViewModel.updateMessage("来自HomeFragment的消息")
           }
       }
   }
   
   class DetailFragment : Fragment() {
       private val sharedViewModel: SharedViewModel by activityViewModels()
   
       override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
           super.onViewCreated(view, savedInstanceState)
           // 观察ViewModel数据变化
           sharedViewModel.message.observe(viewLifecycleOwner) { msg ->
               tvMsg.text = msg
           }
       }
   }
   
   // 共享的ViewModel
   class SharedViewModel : ViewModel() {
       private val _message = MutableLiveData<String>()
       val message: LiveData<String> = _message
   
       fun updateMessage(msg: String) {
           _message.value = msg
       }
   }
   ```

## 注意事项

1. 禁止持有组件引用：ViewModel 中不能持有 Activity/Fragment/View 的强引用（会导致内存泄漏），如需上下文仅用`AndroidViewModel`的`getApplication()`;

1. 实例创建规范：必须通过`ViewModelProvider`或`by viewModels()`创建，不能直接`new`(否则无法管理生命周期);

3. 资源释放：重写`onCleared()`释放非内存资源(如取消网络请求、注销监听器);
   ```kotlin
   override fun onCleared() {
       super.onCleared()
       // 释放资源：取消网络请求、关闭数据库连接等
       userRepository.cancelRequest()
   }
   ```

4. 数据范围：仅存储 “界面相关数据”，不存储大量缓存(推荐用 Room/SharedPreferences);

---

# LiveData

基于 Lifecycle 的可观察数据持有者，核心特性：

1.  数据变化自动通知观察者；
2. 仅在组件活跃（STARTED/RESUMED）时更新 UI；
3. 组件销毁自动取消订阅;

是 ViewModel 与 UI 之间的 “安全数据管道”。

## 基本用法

```kotlin
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel

/// 模拟网络数据
class NetViewModel : ViewModel() {
    // 私有可变LiveData（仅内部修改）
    private val _title = MutableLiveData<String>().apply {
        value = "默认标题" // 初始化数据
    }

    // 对外暴露不可变LiveData（防止外部随意修改）
    val title: LiveData<String> = _title

    /// 封装数据修改逻辑
    fun updateTitle(title: String) {
        _title.value = title // 主线程修改
    }
}
```

```kotlin
import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.activity.viewModels
import androidx.appcompat.app.AppCompatActivity
import com.jeremy.jetpackdemo.databinding.ActivityLiveDataBinding

class LiveDataActivity : AppCompatActivity() {
    private val viewModel: NetViewModel by viewModels()
    lateinit var binding: ActivityLiveDataBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        binding = ActivityLiveDataBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // 观察LiveData：仅在Activity活跃时更新UI
        viewModel.title.observe(this) {
            binding.tvData.text = it // 数据变化自动刷新
        }

        // 触发数据修改
        binding.btn.setOnClickListener {
            viewModel.updateTitle("Jetpack实战：LiveData用法")
        }
    }
}
```

## 进阶用法

1. **子线程更新 LiveData（postValue）**
   网络请求场景核心用法，`postValue`用于子线程异步更新

   ```kotlin
   package com.jeremy.jetpackdemo.liveData
   
   import androidx.lifecycle.LiveData
   import androidx.lifecycle.MutableLiveData
   import androidx.lifecycle.ViewModel
   import androidx.lifecycle.viewModelScope
   import kotlinx.coroutines.Dispatchers
   import kotlinx.coroutines.launch
   
   /// 模拟网络数据
   class NetViewModel : ViewModel() {
       private val _list = MutableLiveData<List<String>>()
   
       val list: LiveData<List<String>> = _list
   
       /// 模拟数据
       fun fetchList() {
           /// 开启携程
           viewModelScope.launch(Dispatchers.IO) {
               /// 模拟网络数据
               Thread.sleep(2000)
               val data = listOf("1", "2", "3")
               // 子线程更新：必须用postValue
               _list.postValue(data)
           }
       }
   }
   ```

2. **MediatorLiveData(多数据源合并)**
   适用于多个 LiveData 联动的场景（如 “用户名 + 密码” 双验证）：

   ```kotlin
   import androidx.lifecycle.LiveData
   import androidx.lifecycle.MediatorLiveData
   import androidx.lifecycle.MutableLiveData
   import androidx.lifecycle.ViewModel
   
   class LoginViewModel : ViewModel() {
   
       private val _userName = MutableLiveData<String>()
       private val _password = MutableLiveData<String>()
   
   
       // 合并两个LiveData：仅当用户名和密码都不为空时，登录按钮可用
       val isLoginEnable: LiveData<Boolean> = MediatorLiveData<Boolean>().apply {
           // 添加第一个数据源
           addSource(_userName) { updateLoginEnable() }
           // 添加第二个数据源
           addSource(_password) { updateLoginEnable() }
       }
   
       private fun updateLoginEnable() {
           val name = _userName.value ?: ""
           val pwd = _password.value ?: ""
           (isLoginEnable as MediatorLiveData<Boolean>).value = name.isNotEmpty() && pwd.isNotEmpty()
       }
   
       // 更新用户名/密码
       fun updateUserName(name: String) {
           _userName.value = name
       }
   
       fun updatePassword(pwd: String) {
           _password.value = pwd
       }
   }
   ```

   

3. 

## 注意事项

1. `value` vs `postValue`：主线程用`value`，子线程必须用`postValue`(直接用`value`会抛异常);

2. 数据封装：必须私有化`MutableLiveData`，对外暴露`LiveData`，避免外部直接修改数据;

3. Fragment 观察：必须用`viewLifecycleOwner`而非`this`，避免 Fragment 已销毁但观察仍存在

   ```kotlin
   // 错误：用this（Fragment实例）
   newsViewModel.newsTitle.observe(this) { ... }
   // 正确：用viewLifecycleOwner（Fragment的视图生命周期）
   newsViewModel.newsTitle.observe(viewLifecycleOwner) { ... }
   ```

4. 避免粘性事件：LiveData 会缓存最新数据，新观察者会收到历史数据。如需避免，可自定义无粘性 LiveData;

5. 空值处理：观察时需判空，避免空指针(如`it ?: return@observe`);

---

# WorkManager(仅了解后续补充)

替代传统 Service/AlarmManager 的后台任务组件，适配 Android 8.0 + 后台限制。任务会被持久化到本地数据库，设备重启后仍能执行。支持设置执行约束（如网络、电量）、重试策略、任务链、周期性执行；遵循系统电量优化规则，避免过度消耗电量。

不适合即时响应的任务（如点击按钮后立即执行的前台操作）,也不适合前台交互的任务（需用户等待结果的操作）和短时间内频繁执行的高频任务（如每秒刷新数据）

## 核心组件

| 组件        | 作用                                                         |
| ----------- | ------------------------------------------------------------ |
| Worker      | 任务执行的核心类：继承该类并重写 `doWork()` 方法，实现具体的后台任务逻辑（运行在后台线程）。 |
| WorkRequest | 任务请求：封装任务的执行规则（如约束、重试策略），分为两种：① `OneTimeWorkRequest`：单次任务；② `PeriodicWorkRequest`：周期性任务（最小周期 15 分钟）。 |
| WorkManager | 任务调度器：负责提交、管理、取消任务，查询任务状态。         |
| Constraints | 任务执行约束：设置任务执行的前置条件（如网络类型、电量充足、设备充电中、存储可用等）。 |
| WorkInfo    | 任务状态信息：包含任务的 ID、状态（ENQUEUED/RUNNING/SUCCEEDED/FAILED/CANCELLED）、返回结果等，可通过 LiveData 监听。 |

## 基本用法

```kotlin
import android.content.Context
import androidx.work.Worker
import androidx.work.WorkerParameters

// 后台任务：模拟更新天气数据
class WeatherUpdateWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    override fun doWork(): Result {
        // 后台任务逻辑（运行在子线程，可执行耗时操作）
        val cityId = inputData.getString("city_id") ?: return Result.failure()
        
        // 模拟网络请求更新天气
        val weather = WeatherRepository.fetchWeather(cityId)
        // 保存到本地数据库
        WeatherRepository.saveWeather(weather)

        // 返回任务结果
        return when {
            weather != null -> Result.success() // 任务成功
            else -> Result.retry() // 任务失败，重试
        }
    }
}
```

调度一次性任务

```kotlin
import androidx.work.Constraints
import androidx.work.NetworkType
import androidx.work.OneTimeWorkRequest
import androidx.work.WorkManager
import androidx.work.workDataOf

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. 设置任务执行条件（仅网络可用时执行）
        val constraints = Constraints.Builder()
            .setRequiredNetworkType(NetworkType.CONNECTED) // 网络可用
            .setRequiresBatteryNotLow(true) // 电池不低
            .build()

        // 2. 构建任务（传递参数）
        val weatherWorkRequest = OneTimeWorkRequestBuilder<WeatherUpdateWorker>()
            .setConstraints(constraints) // 设置执行条件
            .setInputData(workDataOf("city_id" to "101010100")) // 传递参数
            .build()

        // 3. 提交任务
        WorkManager.getInstance(this).enqueue(weatherWorkRequest)

        // 4. 监听任务结果
        WorkManager.getInstance(this)
            .getWorkInfoByIdLiveData(weatherWorkRequest.id)
            .observe(this) { workInfo ->
                if (workInfo.state.isFinished) {
                    when (workInfo.state) {
                        Result.success().state -> println("天气更新成功")
                        Result.failure().state -> println("天气更新失败")
                        Result.retry().state -> println("天气更新重试")
                        else -> {}
                    }
                }
            }
    }
}
```

## 拓展用法

1. **调度周期性任务**

   系统限制最小周期为 15 分钟，无法设置更短：
   ```kotlin
   import androidx.work.PeriodicWorkRequest
   import java.util.concurrent.TimeUnit
   
   // 构建周期性任务（每15分钟更新一次天气）
   val periodicWorkRequest = PeriodicWorkRequestBuilder<WeatherUpdateWorker>(
       15, TimeUnit.MINUTES, // 周期：15分钟
       5, TimeUnit.MINUTES // 弹性时间：允许提前/延后5分钟执行
   )
       .setConstraints(constraints)
       .setInputData(workDataOf("city_id" to "101010100"))
       .build()
   
   // 提交周期性任务
   WorkManager.getInstance(this).enqueue(periodicWorkRequest)
   ```

2. **任务链（多个任务按顺序执行）**
   适用于 “先下载→后处理→再上传” 等场景：

   ```kotlin
   import androidx.work.OneTimeWorkRequestBuilder
   import androidx.work.WorkManager
   import androidx.work.beginWith
   import androidx.work.then
   
   // 1. 定义三个任务：下载→处理→上传
   val downloadWork = OneTimeWorkRequestBuilder<DownloadWorker>().build()
   val processWork = OneTimeWorkRequestBuilder<ProcessWorker>().build()
   val uploadWork = OneTimeWorkRequestBuilder<UploadWorker>().build()
   
   // 2. 构建任务链（按顺序执行）
   val workChain = WorkManager.getInstance(this)
       .beginWith(downloadWork)
       .then(processWork)
       .then(uploadWork)
   
   // 3. 提交任务链
   workChain.enqueue()
   
   // 4. 监听任务链结果
   workChain.state.observe(this) { state ->
       if (state.isFinished) {
           println("任务链执行完成")
       }
   }
   ```

3. **取消任务**

   适用于 “用户手动关闭后台更新” 场景：
   ```kotlin
   // 根据任务ID取消
   WorkManager.getInstance(this).cancelWorkById(periodicWorkRequest.id)
   
   // 取消所有任务
   WorkManager.getInstance(this).cancelAllWork()
   ```

## 注意事项

1. 周期性任务限制：**最小周期 15 分钟**，弹性时间不能超过周期的 50%；

2. 任务执行线程：`doWork()`运行在子线程，无需手动开线程；

3. 任务结果：

   `Result.success()`：任务成功，结束；

   `Result.failure()`：任务失败，不重试；

   `Result.retry()`：任务失败，按重试策略重试（默认 10 分钟后）；

4. 数据传递：`inputData`/`outputData`仅支持基本类型，大小限制约 10KB；

5. 兼容性：自动适配不同 Android 版本（<23 用 AlarmManager，≥23 用 JobScheduler）；

6. 避免滥用：仅用于 “非即时性后台任务”（如数据同步、日志上传），即时任务用 ForegroundService。

---

# Navigation(先放放)

简化单 Activity 多 Fragment 架构的导航组件，统一管理 Fragment 跳转、参数传递、返回栈、深层链接。解决传统`FragmentTransaction`导航逻辑分散、返回栈混乱的问题。



