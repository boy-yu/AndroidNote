# Fragment 的基本使用

## 静态加载 Fragment

静态加载是将 Fragment 直接写入 Activity 布局文件，适用于界面固定、无需动态修改的场景（如平板双页模式的固定布局）。

**创建 Fragment 布局文件**
先创建左侧Fragment

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <androidx.appcompat.widget.AppCompatTextView
        android:id="@+id/tv_left"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="左侧 Fragment"
        android:textSize="20sp" />

</FrameLayout>
```

```kotlin
class LeftFragment : Fragment() {
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        // 参数说明：
        // inflater：布局填充器，用于加载 XML 布局
        // container：Fragment 的父容器（Activity 布局中的 <fragment> 标签）
        // savedInstanceState：用于恢复数据（如屏幕旋转）
        return inflater.inflate(R.layout.fragment_left, container, false)
    }
}
```

> 同理在创建一个右侧的Fragment 与左侧代码相同

**在 Activity 布局中静态引用 Fragment**

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <!-- 静态引用左侧 Fragment -->
    <fragment
        android:id="@+id/left_fragment"
        android:name="com.jeremy.fragmentdemo.LeftFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" /> <!-- 占屏幕 1/2 宽度 -->
  
    <!-- 静态引用右侧 Fragment -->
    <fragment
        android:id="@+id/right_fragment"
        android:name="com.jeremy.fragmentdemo.RightFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" /><!-- 占屏幕 1/2 宽度 -->
</LinearLayout>
```

### 静态加载的关键注意事项

1. **`android:name` 必须完整**：必须指定 Fragment 的全类名（包名 + 类名），否则系统无法找到该 Fragment，会抛出 `ClassNotFoundException`。
2. **必须设置 `id` 或 `tag`**：`<fragment>` 标签需指定 `android:id` 或 `android:tag`，系统用于唯一标识 Fragment，否则运行报错。
3. **不可动态修改**：静态加载的 Fragment 一旦创建，无法在运行时通过代码替换或移除（如需动态操作，用动态加载）。

---

## 动态加载 Fragment

动态加载是通过 `FragmentManager` 和 `FragmentTransaction` 在代码中操作 Fragment（添加、替换、移除），适用于需要动态更新界面的场景（如手机端点击列表切换详情）。

**准备 Fragment 容器（Activity 布局中预留位置）**

修改 `activity_main.xml`，将右侧静态 Fragment 替换为 `FrameLayout`（容器）

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    android:orientation="horizontal">

    <!-- 静态引用左侧 Fragment -->
    <fragment
        android:id="@+id/left_fragment"
        android:name="com.jeremy.fragmentdemo.LeftFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />

    <!-- 右侧为容器，用于动态加载 Fragment -->
    <FrameLayout
        android:id="@+id/right_container"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="2" />
</LinearLayout>
```

**获取 FragmentManager 和开启事务**

- `FragmentManager`：Fragment 的管理器，负责管理 Fragment 实例和事务，通过 `supportFragmentManager`（兼容库）获取
- `FragmentTransaction`：事务类，封装了 Fragment 的所有动态操作（add/replace/remove），需通过 `beginTransaction()` 开启

**创建 AnotherRightFragment**

新建 `AnotherFragment.kt` 和 `fragment_another.xml`，结构与 RightFragment 一致，仅文本不同。

**编写动态替换 Fragment 的代码**

``` kotlin
class MainActivity : AppCompatActivity() {
    lateinit var binding: ActivityMainBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // 初始加载默认 Fragment（RightFragment）
        replaceFragment(RightFragment())

        // （可选）给左侧 Fragment 的按钮添加点击事件，切换 Fragment
        val leftFragment =
            supportFragmentManager.findFragmentById(R.id.left_fragment) as LeftFragment
        leftFragment.view?.findViewById<TextView>(R.id.tv_left)?.setOnClickListener {
            // 点击左侧文本，切换到 AnotherRightFragment（需提前创建该 Fragment）
            replaceFragment( AnotherFragment())
        }
    }

    // 封装 Fragment 替换方法
    private fun replaceFragment(fragment: Fragment) {
        // 1. 获取 FragmentManager
        val fragmentManager = supportFragmentManager
        // 2. 开启事务
        val transaction = fragmentManager.beginTransaction()
        // 3. 替换容器中的 Fragment（参数：容器ID、要显示的Fragment）
        transaction.replace(R.id.right_container, fragment)
        // 4. （可选）添加到返回栈：按返回键可回退到上一个 Fragment
        //  参数为 null 表示不指定栈名，也可传入字符串标识栈
        transaction.addToBackStack(null)
        // 5. 提交事务（必须调用，否则操作不生效）
        transaction.commit()

    }
}
```

### 动态加载的核心操作

| 操作          | 方法                             | 作用                                     | 示例代码                                                     |
| ------------- | -------------------------------- | ---------------------------------------- | ------------------------------------------------------------ |
| 添加 Fragment | `add(containerId, fragment)`     | 向容器中添加 Fragment（不替换现有）      | `transaction.add(R.id.container, fragment)`                  |
| 替换 Fragment | `replace(containerId, fragment)` | 移除容器中所有 Fragment，添加新 Fragment | `transaction.replace(R.id.container, fragment)`              |
| 移除 Fragment | `remove(fragment)`               | 从容器中移除指定 Fragment                | `val fragment = findFragmentById(...)`; `transaction.remove(fragment)` |
| 隐藏 Fragment | `hide(fragment)`                 | 隐藏 Fragment（视图不可见，实例仍存在）  | `transaction.hide(fragment)`                                 |
| 显示 Fragment | `show(fragment)`                 | 显示之前隐藏的 Fragment                  | `transaction.show(fragment)`                                 |
| 加入返回栈    | `addToBackStack(name)`           | 保存事务状态，支持返回键回退             | `transaction.addToBackStack("tag")`                          |

### 动态加载的关键注意事项

1. **事务提交时机**：

   `commit()` 必须在 Activity 的 `onSaveInstanceState()` 之前调用（否则会抛出 `IllegalStateException`）。若需在 `onSaveInstanceState()` 之后提交，用 `commitAllowingStateLoss()`（但可能丢失状态，谨慎使用）。

2. **`addToBackStack()` 的作用**:

   不添加时，替换 Fragment 后，按返回键会直接退出 Activity；添加后，按返回键会回退到上一个 Fragment（事务回滚）。

3. **Fragment 实例获取**：

   动态添加的 Fragment 可通过 `findFragmentById(容器ID)` 或 `findFragmentByTag(标签)` 获取，例如：

   ```kotlin
   val rightFragment = supportFragmentManager.findFragmentById(R.id.right_container) as RightFragment
   ```

4. **容器必须是 ViewGroup 子类**：

   动态加载的容器需是 `FrameLayout`、`LinearLayout` 等 ViewGroup 子类，不能是普通 View（如 TextView）。

# Fragment 的生命周期

### Fragment 生命周期回调

- `onAttach(context: Context)`:获取 Activity 引用，初始化通信通道

  **触发时机**:Fragment 与 Activity 建立关联时（**仅一次**）

  **关键注意事项**:可通过 `context` 强转为 Activity（如 `val activity = context as MainActivity`）

- `onCreate(savedInstanceState: Bundle?)`:初始化非 UI 数据（如 ViewModel、数据请求）

  **触发时机**:Fragment 初始化时(**仅一次**）

  **关键注意事项**:`savedInstanceState` 用于恢复数据（如屏幕旋转前的状态）

- `onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?)`:加载布局文件，初始化 UI 控件

  **触发时机**:创建 Fragment 视图时

  **关键注意事项**:返回加载的 View 实例，若无需视图，返回 null（如纯逻辑 Fragment）

- `onViewCreated(view: View, savedInstanceState: Bundle?)`:初始化控件事件（如按钮点击、RecyclerView 适配）

  **触发时机**:视图创建完成后（替代过时的 `onActivityCreated`）

  **关键注意事项**:推荐在此方法中操作 UI（视图已完全创建），避免在 `onCreateView` 中直接操作控件

- `onActivityCreated(savedInstanceState: Bundle?)`:早期用于依赖 Activity 初始化的场景

  **触发时机**:Activity 的 `onCreate()` 执行完毕后

  **关键注意事项**:推荐用 `onViewCreated` 替代，避免使用过时 API

- `onStart()`: 启动动画、初始化可见状态的资源

  **触发时机**:Fragment 可见时（与 Activity 同步）

  **关键注意事项**:与 Activity 的 `onStart()` 几乎同时触发

- `onResume()`:启动交互逻辑（如监听触摸事件、开始播放视频）

  **触发时机**:Fragment 可交互时（处于前台）

  **关键注意事项**:与 Activity 的 `onResume()` 同步

- `onPause()`:暂停耗时操作（如视频播放、网络请求）

  **触发时机**:Fragment 失去焦点但仍可见时

  **关键注意事项**: 与 Activity 的 `onPause()` 同步，不能做耗时操作

- `onStop()`:释放可见资源（如停止视频播放、注销广播）

  **触发时机**:Fragment 完全不可见时

  **关键注意事项**:与 Activity 的 `onStop()` 同步

- `onDestroyView()`:释放视图相关资源（如解绑 ViewBinding、RecyclerView 适配器）

  **触发时机**:Fragment 视图被销毁时（如替换、移除）

  **关键注意事项**:此时 Fragment 实例仍存在，仅视图被销毁

- `onDestroy()`:释放所有资源（如取消网络请求、关闭数据库）

  **触发时机**:Fragment 实例销毁前

  **关键注意事项**:若 `setRetainInstance(true)`，则不会触发（保留实例）

- `onDetach()`:清空 Activity 引用（避免内存泄漏）

  **触发时机**: Fragment 与 Activity 解除关联时（**仅一次**）

  **关键注意事项**:最后一个回调，之后 Fragment 实例完全销毁

###  Fragment 与 Activity 生命周期的关联

| Activity 生命周期状态 | Fragment 对应的生命周期状态                                  | 典型场景                                                     |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `onCreate()`          | `onAttach()` → `onCreate()` → `onCreateView()` → `onViewCreated()` | Activity 首次创建，Fragment 初始化                           |
| `onStart()`           | `onStart()`                                                  | Activity 可见，Fragment 也可见                               |
| `onResume()`          | `onResume()`                                                 | Activity 可交互，Fragment 也可交互                           |
| `onPause()`           | `onPause()`                                                  | Activity 失去焦点，Fragment 也失去焦点                       |
| `onStop()`            | `onStop()`                                                   | Activity 完全不可见，Fragment 也不可见                       |
| `onDestroy()`         | `onDestroyView()` → `onDestroy()` → `onDetach()`             | Activity 销毁，Fragment 也销毁                               |
| 屏幕旋转（重建）      | `onPause()` → `onStop()` → `onDestroyView()` → `onDetach()` → `onAttach()` → `onCreate()` → `onCreateView()` → `onViewCreated()` → `onStart()` → `onResume()` | 视图销毁后重建，实例可能保留（若 `setRetainInstance(true)`） |

### 生命周期关键场景演示

**Fragment 首次创建并显示**

`onAttach()` → `onCreate()` → `onCreateView()` → `onViewCreated()` → `onActivityCreated()` → `onStart()` → `onResume()`

**按 Home 键退到后台**

`onPause()` → `onStop()`

**从后台返回前台**

`onStart()` → `onResume()`

**动态替换 Fragment（添加到返回栈）**

原 Fragment 流程:`onPause()` → `onStop()` → `onDestroyView()`

新 Fragment 流程：`onAttach()` → `onCreate()` → `onCreateView()` → `onViewCreated()` → `onActivityCreated()` → `onStart()` → `onResume()`

**按返回键回退 Fragment**

新 Fragment 流程：`onPause()` → `onStop()` → `onDestroyView()` → `onDestroy()` → `onDetach()`

原 Fragment 恢复流程：`onCreateView()` → `onViewCreated()` → `onActivityCreated()` → `onStart()` → `onResume()`

# 状态保存与恢复

## 通过 savedInstanceState 保存

与 Activity 类似，Fragment 可通过 `onSaveInstanceState()` 保存数据，在重建时恢复：

```kotlin
// 保存数据（视图销毁前调用）
override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    val userInput = "需要保存的内容"
    outState.putString("key_input", userInput)
}

// 恢复数据（在 onCreate 或 onViewCreated 中）
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    if (savedInstanceState != null) {
        val restoredInput = savedInstanceState.getString("key_input")
        // 更新 UI
    }
}
```

## 通过 setRetainInstance(true) 保留实例

若 Fragment 无需重建实例（仅视图重建），可在 `onCreate()` 中调用 `setRetainInstance(true)`，让系统保留 Fragment 实例（仅销毁视图）：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setRetainInstance(true) // 保留 Fragment 实例，屏幕旋转时不销毁
}
```

> 注意：仅适用于 “非 UI 数据” 的保留（如网络请求回调、ViewModel 实例），UI 控件仍会重建，需通过 `onViewCreated` 重新绑定。
> **此方案已被废弃**

# Fragment 与 Activity 的通信

Fragment 与 Activity 通信需遵循 “**低耦合、高内聚**”

- 禁止 Fragment 直接操作 Activity 的 UI 控件（如直接修改 Activity 中的 TextView 文本）
- 禁止 Activity 直接访问 Fragment 的私有变量（如 `fragment.mTextView`）
- 推荐通过 “接口回调”“Bundle 传参” 等方式通信

## Activity向Fragment传递消息

### 通过 findFragmentById 或 findFragmentByTag 获取实例

适用于静态加载和动态加载的 Fragment，直接调用 Fragment 的公有方法：

```kotlin
// Activity 中获取 Fragment 实例并调用方法
val leftFragment = supportFragmentManager.findFragmentById(R.id.left_fragment) as LeftFragment
leftFragment.updateUI("更新后的文本") // 调用 Fragment 的公有方法

// Fragment 中定义公有方法
class LeftFragment : Fragment() {
    // 公有方法，供 Activity 调用
    fun updateUI(text: String) {
        view?.findViewById<TextView>(R.id.tv_left)?.text = text
    }
}
```

### 通过 setArguments 传递初始化参数

适用于 **Activity 中创建** Fragment 创建时传递数据（如启动 Fragment 时传入 ID、标题）：

```kotlin
// Activity 中创建 Fragment 并传递参数
val bundle = Bundle()
bundle.putString("key_title", "新闻详情")
val rightFragment = RightFragment()
rightFragment.arguments = bundle // 传递参数

// Fragment 中接收参数（在 onCreate 或 onViewCreated 中）
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
    val title = arguments?.getString("key_title") // 取出参数
    view.findViewById<TextView>(R.id.tv_title).text = title
}
```

## Fragment向Activity传递消息

### 通过 requireActivity() 获取 Activity 实例

适用于 Fragment 已与 Activity 关联的场景（`onAttach()` 之后），调用 Activity 的公有方法：

```kotlin
// Fragment 中获取 Activity 实例并调用方法
val mainActivity = requireActivity() as MainActivity
mainActivity.updateButton("更新按钮")


// Activity 中定义公有方法
class MainActivity : AppCompatActivity() {
  ...
  fun updateButton(text: String) {
    binding.btnUpdate.text = text
	}
}
```

### 接口回调（推荐，低耦合）

适用于 Fragment 向 Activity 传递事件（如点击 Fragment 中的按钮，通知 Activity 执行逻辑）

1. 在 Fragment 中定义接口

   ```kotlin
   class LeftFragment : Fragment() {
       // 定义回调接口
       interface OnFragmentClickListener {
           fun onButtonClick(message: String) // 回调方法，传递数据
       }
   
       private var listener: OnFragmentClickListener? = null // 接口实例
   }
   ```

2. 在 `onAttach` 中绑定 Activity 到接口

   ```kotlin
   override fun onAttach(context: Context) {
       super.onAttach(context)
       // 检查 Activity 是否实现了接口
       if (context is OnFragmentClickListener) {
           listener = context // 绑定 Activity 到接口
       } else {
           throw IllegalStateException("Activity 必须实现 OnFragmentClickListener 接口")
       }
   }
   ```

3. Activity 实现接口并处理回调

   ```kotlin
   class MainActivity : AppCompatActivity(), LeftFragment.OnFragmentClickListener {
       override fun onButtonClick(message: String) {
           Toast.makeText(
             this, 
             "收到 Fragment 消息：$message",
             Toast.LENGTH_SHORT
           ).show()
           binding.btnUpdate.text = message
       }
   }
   ```

4. Fragment 中触发回调

   ```kotlin
   // Fragment 中按钮点击事件触发回调
   override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
       super.onViewCreated(view, savedInstanceState)
       view.findViewById<Button>(R.id.btn_send).setOnClickListener {
           listener?.onButtonClick("更新按钮") // 触发回调
       }
   }
   ```

5. 解除接口绑定（避免内存泄漏）

   ```kotlin
   override fun onDetach() {
       super.onDetach()
       listener = null // 解除绑定，避免 Activity 内存泄漏
   }
   ```

## 通信避坑指南

1. **避免强引用导致内存泄漏**

   不在 Fragment 中持有 Activity 的静态引用

   不在 Fragment 中用匿名内部类持有 Activity 引用（如 `object : OnClickListener`），如需使用，用弱引用（`WeakReference<MainActivity>`）

2. **接口回调必须判空**

   触发回调前检查 `listener != null`，避免因 `onDetach()` 后 `listener` 为 null 导致空指针

3. **`arguments` 仅支持可序列化数据**

   传递的数据需是基本类型、`String`、`Parcelable`、`Serializable` 等，不可传递大数据（如 Bitmap）或非序列化对象

4. **动态加载 Fragment 传参建议**

   推荐用静态工厂方法创建 Fragment 并传递参数，避免直接 new 实例后设置 `arguments`

   例如:

   ```kotlin
   // Fragment 中定义静态工厂方法（推荐）
   companion object {
       fun newInstance(title: String): RightFragment {
           val fragment = RightFragment()
           val bundle = Bundle()
           bundle.putString("key_title", title)
           fragment.arguments = bundle
           return fragment
       }
   }
   
   // Activity 中创建 Fragment
   val rightFragment = RightFragment.newInstance("新闻详情")
   ```

   