Android 中 Activity、Fragment、View 的生命周期是「层层依附」的关系：**View 依附于 Fragment/Activity，Fragment 依附于 Activity**，三者的生命周期既相互联动，又有各自独立的触发逻辑和核心作用。

## 各自生命周期核心详解

## Activity 生命周期

Activity 是 Android 最核心的页面容器，生命周期完全由系统（AMS）控制，核心作用是「管理页面的创建、可见、可交互、销毁」，是 Fragment 和 View 的「生命周期宿主」。

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

## Fragment 生命周期

Fragment 是「模块化页面组件」，生命周期既受宿主 Activity 联动影响，也受开发者手动操作（add/remove/show/hide）控制，核心作用是「拆分复杂页面，复用 UI 逻辑」。

```plsql
onAttach → onCreate → onCreateView → onActivityCreated → onStart → onResume → [运行] → onPause → onStop → onDestroyView → onDestroy → onDetach
```

- `onAttach()`:Fragment 绑定到 Activity 时（仅 1 次）
- `onCreate()`:Fragment 创建时
- `onCreateView()`:创建 Fragment 的 UI 视图（首次 / 重建时）
- `onActivityCreated()`:Activity 的 onCreate 完成后(API 28+ 已废弃，逻辑移到`onViewCreated`)
- `onViewCreated()`:Activity 的 onCreate 完成后(替代 onActivityCreated)
- `onStart()`:与 Activity 的 onStart 同步（Fragment 可见）
- `onResume()`:与 Activity 的 onResume 同步（Fragment 可交互）
- `onPause()`:与 Activity 的 onPause 同步（Fragment 不可交互）
- `onStop()`:与 Activity 的 onStop 同步（Fragment 不可见）
- `onDestroyView()`:Fragment 的 View 被销毁（如 remove / 替换）
- `onDestroy()`:Fragment 实例销毁时
- `onDetach()`:与 Activity 解绑

## View 生命周期

View 是 Android UI 的最小单元（如 Button、TextView），生命周期完全由父 ViewGroup（如 Activity 的 DecorView、Fragment 的布局根 View）控制，核心作用是「绘制 UI、处理交互」。

```plsql
构造方法 → onAttachedToWindow → onMeasure → onLayout → onDraw → [交互/重绘] → onDetachedFromWindow
```

- `onAttachedToWindow()`:View 附加到 Window（Activity/Fragment 显示）
- `onMeasure()`:测量 View 宽高（首次 / 布局变化）
- `onLayout()`:确定 View 位置（首次 / 布局变化）
- `onDraw()`: 绘制 View 内容（首次 / 重绘）
- `onVisibilityChanged()`:View 可见性变化（VISIBLE/INVISIBLE/GONE）
- `onDetachedFromWindow()`:View 从 Window 移除（Activity/Fragment 销毁）

**重绘触发场景:**

View 没有「暂停 / 停止」状态，仅通过「重绘」更新 UI，触发重绘的场景：

- 调用 `invalidate()`：触发 `onDraw`（仅重绘，不重新测量 / 布局）
- 调用 `requestLayout()`：触发 `onMeasure`→`onLayout`→`onDraw`（完整重绘）
- 屏幕旋转、View 可见性变化、父布局重绘

## Activity、Fragment、View 生命周期核心区别

 **核心维度对比表**

| 对比维度         | Activity                               | Fragment                                              | View                                                |
| ---------------- | -------------------------------------- | ----------------------------------------------------- | --------------------------------------------------- |
| 依附关系         | 顶级组件，不依附任何组件（由系统管理） | 依附 Activity（1 个 Activity 可绑定多个 Fragment）    | 依附 ViewGroup（Activity/Fragment 的布局容器）      |
| 生命周期主导者   | 系统（AMS）完全控制                    | 系统（联动 Activity）+ 开发者（add/remove）           | 开发者 / 父 ViewGroup（如 LayoutInflater、addView） |
| 核心生命周期数量 | 7 个核心方法（onCreate→onDestroy）     | 11 个核心方法（onAttach→onDetach）                    | 6 个核心方法（构造→onDetachedFromWindow）           |
| 重建触发         | 屏幕旋转、配置变化、系统回收           | 随 Activity 重建、replace/remove                      | 随父容器（Activity/Fragment）重建、removeView       |
| 可见性控制       | 由系统控制（onStart/onStop）           | `show/hide`（无生命周期）、`add/remove`（全生命周期） | `setVisibility()`（VISIBLE/INVISIBLE/GONE）         |
| 状态保存         | `onSaveInstanceState`（系统回调）      | 随 Activity 的`onSaveInstanceState`                   | 无系统回调，需手动保存                              |
| 内存泄漏风险     | 持有 Context 强引用、未取消定时器      | 持有 Activity 强引用、onDestroyView 未释放 View       | onDetachedFromWindow 未释放监听 / 动画              |
| 核心作用         | 页面容器，管理整体生命周期             | 拆分页面，复用 UI 逻辑                                | 最小 UI 单元，绘制交互                              |

### 关键区别解析

**依附层级：Activity > Fragment > View**

- Activity 是「根容器」，所有 Fragment 和 View 的生命周期都依赖 Activity 的状态（Activity 销毁，Fragment/View 必销毁）；
- Fragment 是「中间层」，可在 Activity 内动态添加 / 移除，一个 Activity 可包含多个 Fragment；
- View 是「叶子节点」，必须依附于 ViewGroup（如 LinearLayout、FrameLayout），ViewGroup 本身也是 View。

**生命周期可控性：View > Fragment > Activity**

- Activity 完全被动：生命周期由系统触发（如切后台、旋转屏幕），开发者无法手动调用`onCreate`/`onDestroy`；
- Fragment 半主动：系统联动（随 Activity）+ 开发者手动控制（`add/remove`触发全生命周期，`show/hide`仅改可见性）；
- View 完全主动：生命周期由开发者 / 父容器控制（`new View()`创建、`addView()`附加、`removeView()`移除），无系统自动回调。

**重建逻辑：Activity 整体重建，Fragment 视图重建，View 完全重建**

- Activity 旋转时「实例 + 视图全重建」（除非配置`android:configChanges="orientation|screenSize"`）；
- Fragment 旋转时「实例保留，视图重建」（`onDestroyView`→`onCreateView`）；
- View 旋转时「完全重建」（构造方法重新调用，需重新绑定数据）。

## 生命周期联动场景

**Activity 启动时的联动流程**

```xml-dtd
Activity: onCreate 
→ Fragment: onAttach → onCreate → onCreateView → onViewCreated → onStart 
→ Activity: onStart 
→ Fragment: onResume 
→ Activity: onResume
```

**Activity 切后台（按 Home）的联动流程**

```xml-dtd
Activity: onPause 
→ Fragment: onPause 
→ Activity: onStop 
→ Fragment: onStop
```

**Activity 旋转（未配置 configChanges）的联动流程**

```falsexml-dtd
Activity: onPause → Fragment: onPause 
→ Activity: onStop → Fragment: onStop 
→ Fragment: onDestroyView 
→ Activity: onDestroy 
→ Activity: onCreate 
→ Fragment: onAttach → onCreate → onCreateView → onViewCreated → onStart 
→ Activity: onStart 
→ Fragment: onResume 
→ Activity: onResume
```

**Fragment 手动 `add/remove` 的联动流程**

- `add Fragment`

  ```xml-dtd
  Fragment: onAttach → onCreate → onCreateView → onViewCreated → onStart → onResume
  ```

- `remove Fragment`

  ```xml-dtd
  Fragment: onPause → onStop → onDestroyView → onDestroy → onDetach
  ```

## 避坑指南

**Activity 常见坑**

- `onStop` 可能被系统跳过（低内存时直接销毁），不要依赖`onStop`保存关键数据，关键数据在`onPause`保存；
- 旋转屏幕未配置`configChanges`，导致数据丢失 → 解决方案：`onSaveInstanceState` 保存 / 恢复数据，或配置`configChanges`；
- `onResume` 多次调用（如切前台），避免重复初始化（如多次注册广播）→ 解决方案：加标记位判断

**Fragment 常见坑**

- `onDestroyView` 未释放 View 引用 → 解决方案：在`onDestroyView`中清空 View 变量（`binding = null`）；
- 持有 Activity 强引用 → 解决方案：用`requireContext()`（弱引用）或`ViewModel`共享数据；
- `show/hide` 误以为触发生命周期 → 解决方案：`show/hide`仅改可见性，需手动处理`onHiddenChanged`。

**View 常见坑**

- `onDetachedFromWindow` 未释放资源（如动画、监听）→ 解决方案：在该方法中停止动画、注销监听；
- `onMeasure` 多次调用导致性能问题 → 解决方案：避免在`onMeasure`中执行复杂逻辑；
- 自定义 View 未处理`MeasureSpec` → 解决方案：正确解析`MeasureSpec`（EXACTLY/AT_MOST/UNSPECIFIED）。
