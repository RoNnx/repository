### 生命周期
##### Fragment生命周期
![image](https://upload-images.jianshu.io/upload_images/944365-cc85e7626552d866.png?imageMogr2/auto-orient/strip|imageView2/2/w/317/format/webp)
##### Fragment和Activity的生命周期对照
![image](https://upload-images.jianshu.io/upload_images/944365-0f9670e55a52403c.png?imageMogr2/auto-orient/strip|imageView2/2/w/340/format/webp)

##### 对比Activity新增的几个回调
* onAttach：Fragment和Activity建立关联的时候调用（获得activity的传递的值）
* onCreateView：为Fragment创建视图（加载布局）时调用（给当前的fragment绘制UI布局，可以使用线程更新UI）
* onActivityCreated：当Activity中的onCreate方法执行完后调用（表示activity执行oncreate方法完成了的时候会调用此方法）
* onDestroyView：Fragment中的布局被移除时调用（表示fragment销毁相关联的UI布局）
* onDetach：Fragment和Activity解除关联的时候调用（脱离activity）
### Fragment使用
##### FragmentManager的获取
* getSupportFragmentManager()，获取当前Activity的Fragment管理器
* getChildFragmentManager()，获取当前Fragment的Fragment管理器
##### FragmentTransaction
* attach/detach：分别从onCreateView到onResume和onPause到onDestroyView，Fragment实例还保存，其中attach不可单独使用（比如要先add后detach再attach）
* add/remove：有加入回退栈时和attach/detach一样，没有加入回退栈则会重建和销毁Fragment实例
* replace：相当于remove + add
* hide/show：只是隐藏和显示
* commit/commitAllowingStateLoss：提交fragemnt操作的事务
    * commit方法并不立即执行transaction中包含的动作，而是把它加入到UI线程队列中。 如果想要立即执行，可以在commit之后立即调用FragmentManager的executePendingTransactions方法
    * commit方法必须在状态存储（onSaveInstanceState）之前调用，否则会抛出异常，如果觉得状态丢失没关系，可以调用commitAllowingStateLoss。但是除非万不得已， 一般不推荐用这个方法，会掩盖很多错误
* addToBackStack：将当前创建的事务加入回退栈。而出栈的方法是在FragmentManager里面，因为popBackStack是操作回退栈里面的事务，与beginTransaction在同一层级
##### Fragment中获取Context
* getActivity()和getContext()，但是可能为null
* 推荐使用onAttach方法的参数获取
##### Fragment+ViewPager
* FragmentPagerAdapter：使用attach/detach进行切换，切换时不销毁Fragment实例，适用于固定的，少量的Fragment
* FragmentStatePagerAdapter：使用add/remove进行切换，切换时会销毁Fragment实例，适用于fragment数量很多的情况，remove销毁fragment实例可以节省资源和提高性能
##### 无UI的fragment
* 使用Fragment来接管Activity的回调（必须是Activity和Fragment都用的回调）实现在Activity内可以直接通过调用方法传递回调实现，比如onActivityResult和onRequestPermissionsResult
### Activity和Fragment之间的通信
* 构造函数直接传递数据，缺点是Activity异常情况重建时Fragment重新添加时不会调用构造函数而是无参构造函数会造成数据丢失
* Fragment.setArguments传递数据，优点即使重新添加也会保留之前setArguments设置的Bundle数据，缺点是只能传递可序列化的数据
* Activity直接调用Fragment的方法
* Activity实现接口，Fragment通过onAttach回调得到接口对象
* 广播，EventBus等