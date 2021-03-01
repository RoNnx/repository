### 生命周期
![image](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.it610.com%2Fimage%2Fproduct%2F530198331d3f4144bc1da8c79ef40d8e.jpg&refer=http%3A%2F%2Fimg.it610.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1617105431&t=015fbd8c7e9f96ff7fdaaea46fb67c23)

##### 常见知识点
* A启动B后的生命周期回调：
    * B为正常主题的Activity时：A.onPause->B
.onCreate->B.onStart->B.onResume->A.onStop
    * B为透明主题时：A.onPause->B.onCreate->B.onStart->B.onResume
* onStart和onStop通常指的是当前活动是否位于前台这个角度，而onResume和onPause从是否可见这个角度来讲的（有待商榷，根据Activity的启动流程来看，DecorView在onResume调用后才可见）
* onPause 不做耗时操作应该根据具体业务场景在onStop或onDestroy中进行操作，因为要前一个Activity调用onPause后才会启动第二个Activity
* 异常启动流程：屏幕方向变化、内存不足被关闭等特殊情况。
    * 当Activity重建时，Activity会委托Window通知所有子View调用onSaveInstanceState
    * onRestoreInstanceState在onStart后执行，onSaveInstanceState在onStop之前调用
    * 如果在XML配置configChanges后可以避免Activity重建，并且会调用onConfigurationChanged
### 启动模式
##### 四大启动模式（不考虑显示设置Flag的情况）
* standard：标准启动模式（默认启动模式），每次都会启动一个新的activity实例
* singleTop：单独使用使用这种模式时，如果Activity实例位于当前任务栈顶，就重用栈顶实例，而不新建，并回调该实例onNewIntent()方法，否则走新建流程
* singleTask：这种模式启动的Activity只会存在相应的Activity的taskAffinit任务栈中，同一时刻系统中只会存在一个实例，已存在的实例被再次启动时，会重新唤起该实例，并清理当前Task任务栈该实例之上的所有Activity，同时回调onNewIntent()方法
* singleInstance：这种模式启动的Activity独自占用一个Task任务栈，同一时刻系统中只会存在一个实例，已存在的实例被再次启动时，只会唤起原实例，并回调onNewIntent()方
##### Intent.FLAG_ACTIVITY_NEW_TASK
可以简单理解为在Activity目标taskAffinity的Task中启动。但实际使用时比较复杂，后续需要再深入学习
##### 特殊知识点
* 在5.0（不包括5.0）以前的系统中使用startActivityForResult启动singleTask的Activity时在调用启动方法后会立刻回调onActivityResult
* 非Activity调用startActivity时必须要设置Intent.FLAG_ACTIVITY_NEW_TASK