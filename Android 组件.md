# 常用组件

## TextView & AppCompatTextView 

### 常用属性

| 属性                                          | 说明说明                                 |
| --------------------------------------------- | ---------------------------------------- |
| 基础文本属性                                  |                                          |
| `android:textStyle="bold/italic"`             | 粗体/斜体                                |
| `android:fontFamily="sans-serif"`             | 字体样式                                 |
| `android:gravity="center"`                    | 文本对齐方式                             |
| `android:padding="8dp"`                       | 内边距                                   |
| 行数与截断                                    |                                          |
| `android:maxLines="1"`                        | 最大行数                                 |
| `android:lines="1"`                           | 固定 1 行                                |
| `android:ellipsize="end"`                     | 超长文字省略号：end/start/middle/marquee |
| 字体方向与对齐                                |                                          |
| `android:textAlignment="center"`              | 文本对齐（比 gravity 更精准）            |
| `android:includeFontPadding="false"`          | 去掉顶部多余字体内边距（非常实用）       |
| `android:lineSpacingExtra="4dp"`              | 行距                                     |
| `android:letterSpacing="0.1"`                 | 字间距                                   |
| 内容展示控制                                  |                                          |
| `android:autoLink="Web"`                      | Phone                                    |
| `android:inputType="text"`                    | 控制输入类型（EditText 用）              |
| `android:breakStrategy="balanced"`            | 控制换行策略（Android 7+）               |
| 内置的图标                                    |                                          |
| `android:drawableStart="@drawable/ic_search"` | 文本左侧图标                             |
| `android:drawablePadding="6dp"`               | 图标与文字间距                           |



## Button & AppCompatButton

### 常用属性

| 属性                                 | 说明                                              |
| ------------------------------------ | ------------------------------------------------- |
| 行为相关                             |                                                   |
| `android:onClick="onSubmit"`         | 绑定Activity点击方法(不推荐)                      |
| `android:clickable="false"`          | 禁用点击，但仍可显示 ripple（水波纹）             |
| `android:focusable="true/false"`     | 控制按钮能否获取焦点。                            |
| `android:longClickable="true"`       | 支持长按事件（默认 true）                         |
| 背景 & 状态控制                      |                                                   |
| `android:background="@drawable/xxx"` | 自定义按钮背景（圆角、渐变、selector 等都用这个） |
| `android:insetLeft="0dp"`            | 去自动内边距（小按钮必备）                        |
| `android:minHeight="0dp"`            | 最小高度限制                                      |
| `android:stateListAnimator="@null"`  | 禁用按压浮动动画                                  |
| `android:drawableTint`               | 给按钮图标自动着色                                |
| `android:lineHeight`                 | 精准控制多行按钮行高                              |
| `android:includeFontPadding="false"` | 顶部对齐更精准                                    |



## ImageView & AppCompatImageView

### 差异



### 常用属性

## EditText & AppCompatEditText

### 差异



### 常用属性



---

# 常用布局

## ConstraintLayout



## LinearLayout



## FrameLayout



## RelativeLayout



# 通用属性