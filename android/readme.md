



#### Activity 

4种状态

* 运行状态：位于栈顶时

* 暂停状态：不再处于栈顶，但是仍然可见时
* 停止状态：不在处于栈顶时
* 销毁状态：从栈中移除后



7段生命周期：

|    方法     |   生存期   | 备注 |
| :---------: | :--------: | :--: |
| onCreate()  | 完整生存期 |      |
|  onStart()  | 可见生存期 |      |
| onResume()  | 前台生存期 |      |
|  onPause()  | 前台生存期 |      |
|  onStop()   | 可见生存期 |      |
| onDestroy() | 完整生存期 |      |
| onRestart() |     —      |      |



4种启动模式

* standard

* singleTop

* singleTask

* singleInstance



#### 控件

TextView

Button

EditText

ImageView

ProgressBar

AlertDialog



#### 布局

**LinearLayout**：线性布局

orientation, layout_gravity, layout_weight

**RelativeLayout**：相对布局



**FrameLayout**：帧布局



#### 控件继承关系

* View
  * ImageView
  * TextView
    * EditText
    * Button
  * ViewGroup
    * LinearLayout
    * RelativeLayout
    * ...



#### 数据持久化

文件存储

SharedPreferences存储

数据库存储



#### CRUD

create

retrieve

update

delete



#### 异步消息处理机制

四个部分组成：

Message

Handler

MessageQueue

Lopper



OkHttp

Retrofit



apache httpd



gson



协程



JetPack

Room



LitePal





