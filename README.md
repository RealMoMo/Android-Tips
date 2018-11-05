# Android-Tips

### This project will be continuously updated(本项目会持续更新)

内容是我自己平时学习与工作积累的代码与准则，并没有什么原理剖析。如有错误欢迎指正,如有侵权,请联系我删除。

### Tips
* [框架源码分析]()  TODO待更新
* [adb常用调试命令](https://github.com/RealMoMo/Android-Tips/blob/master/adb.md) 
* Android Tools命名空间原来是有大用处的。大致有三种主要功能：</br>
  xml中的错误处理</br>
  xml 预览</br>
  资源压缩</br>
[具体阅读地址点我](https://www.jianshu.com/p/2912bcba4465)
* 如果你觉得在安装Eclipse后还需要配置android开发环境很麻烦，你可以直接使用ADT Bundle，它是一个懒人套餐。反正我就是怎么过来的。[下载链接](http://www.androiddevtools.cn/)
* 使用Toast时，建议定义一个全局Toast对象。可以避免连续显示Toast 时不能取消上一次 Toast 消息的情况。
* 灵活使用ViewStub Merge Include标签优化布局。
* 静态变量不要直接或者间接引用Activity、Service等。这会使用Activity以及它所引用的所有对象无法释放，若用户操作时间一长，内存就会狂升。
* 在Activity.onPause()或 Activity.onStop()回调中，关闭当前 activity 正在执行的的动画。
* ImageView Selector不生效的解决方案
<br>1.ImageView要加上clickable="true"，不然它的selector 是不会有效果。</br>
2.ImageView selector的默认图片item要放到最后。
* 自定义progressbar seekbar ui样式。发现最后进度条的粗细和滑块一样大。请设置android:maxHeight&android:minHeight,这是指定进度条最大/小高度的（此高度并非SeekBar整个控件的高度）。
* 了解与开发原生Setting[干货链接](http://blog.csdn.net/joychanger/article/details/51336527)
* Android framework系统默认设置修改,主要是6.0以下版本[干货链接](https://blog.csdn.net/cbk861110/article/details/25216765)
* 设置ScrollView始终显示滚动条
```
android:scrollbars="vertical"
android:fadeScrollbars="false"
```
* ListView、GridView都要实现Item的点击事件与长按事件。则Item长按监听回调函数要return true,否则长按是会执行setOnItemClickListener。
* 禁止listview的item项获得焦点，而让item的子控件获得焦点。
```
//设置item项的子控件能够获得焦点（默认为false，即默认item项的子控件是不能获得焦点的）
listView.setItemsCanFocus(true); 
```
* 补充上点：没有出现的获得焦点高亮效果，可能有以下原因：</br>
1.例如:ImageView默认不能获得焦点，应该设置属性为：android:focusable="true" （如果是ImageButton或Button等则不需要设置，他们默认是可以获得焦点的）</br>
2.没有为该ImageView设置自定义drawable图片的的selector（该ImageView其实已经获得焦点了，只是没有看出来而已）。
* PopUpWindow都设置点击外面消失了，怎么还不消失。噢，还差一句。
```
mPopUpWindow.setBackgroundDrawable(new ColorDrawable(0x00000000));
```
* 需要PopUpWindow在某个View某位置弹出。使用PopUpWindow.showAtLocation(View parent, int gravity, int x, int y)
```
    PopUpWindow mPopTextSettingWindow ;
	private void showPopupWindow(View targetView) {
        //TODO do something
        ...
        
		int[] location = new int[2];
		targetView.getLocationOnScreen(location);
		int w = mPopTextSettingWindow.getWidth();
		int h = mPopTextSettingWindow.getHeight();
		//targetView正上方弹出
		mPopTextSettingWindow.showAtLocation(targetView, Gravity.NO_GRAVITY,
				location[0] - w / 2 + targetView.getWidth() / 2, location[1] - h -10);
	}
```
* 设置系统的触摸提示音功能。
```
//打开关闭触摸声音
		Settings.System.putInt(mContext.getContentResolver(), 
				Settings.System.SOUND_EFFECTS_ENABLED, isBeepSound ? 0 : 1);
```

* 系统闪烁标签blink,闪烁频率500毫秒,不支持修改。BlinkLayout源码位置：Layoutinflate中的一个private类型的内部类，继承自FrameLayout。

```
<blink
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Blink Layout"></TextView>

</blink>
```
![](https://github.com/RealMoMo/Android-Tips/blob/master/img/blinklayout.gif)
* 长按MenuItem会Toast该Item的Title。
* 设置系统壁纸。
```
WallpaperManager mWallManager = WallpaperManager.getInstance(getActivity());
mWallManager.suggestDesiredDimensions(wallpaperWidthPX,wallpaperHeightPX);
Intent intent = new Intent();
intent.setAction(Intent.ACTION_WALLPAPER_CHANGED);
mWallManager.setBitmap(mBitmap);//还有setDrawable()等方法。
mContext.sendBroadcast(intent);
```
* 获取系统壁纸。
```
WallpaperManager.getInstance(getContext()).getDrawable();
```
* 多个EditText控件，例如注册登录界面。需当前EditText按回车按钮跳转到指定EditText。
```
android:imeOptions="actionNext"  
android:singleLine="true"  
android:nextFocusForward="@+id/et_address"
```
* 动态监控数据库某值的变化，可继承下ContentObserver类。
```
    /**
	 * 
	 * @author RealMo
	 * VolumeObserver 主要音量变化（遥控）
	 */
	private class VolumeObserver extends ContentObserver {


        public VolumeObserver(ContentResolver resolver) {
            super(new Handler());
         
           
        }
        
        @Override
        public void onChange(boolean selfChange) {
        	// TODO Auto-generated method stub
        	super.onChange(selfChange);
        	 Log.d("realmo", "VolumeObserver===========");
        	 isMute = false;
				setMuteViewImage(isMute);
				// 更新音量条进度
				currentVolume = mAudiomanager
						.getStreamVolume(AudioManager.STREAM_MUSIC); // 获取当前值
				//TODO do something
        }
	}


//init
VolumeObserver  mVolObserver = new VolumeObserver(mContext.getContentResolver());
			//监听相应的数据库内容
			mContext.getContentResolver().registerContentObserver(
					Uri.parse("content://mstar.tv.usersetting/soundsetting/Volume")
		             , true, mVolObserver);
```
* 终止Android中HandlerThread的方法。
```
mHandlerThread.getLooper().quit();
```
* 保存图片并通知系统相册刷新。
```
Intent intent= new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
   // 最后通知图库更新
    intent.setData(Uri.fromFile(file));
    sendBroadcast(intent);
```
* 删除图片并通知系统相册刷新。
```
    private void notifyFileSystemChanged(String filePath) {
        if (filePath == null){
            return;
        }
        File f = new File(filePath);
        Intent intent;
        if (f.isDirectory()) {
            intent = new Intent(Intent.ACTION_MEDIA_MOUNTED);
            intent.setClassName("com.android.providers.media", "com.android.providers.media.MediaScannerReceiver");
            intent.setData(Uri.fromFile(Environment.getExternalStorageDirectory()));
        } else {
            //test not work in some devices
            intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
            intent.setData(Uri.fromFile(new File(path)));
        }
        context.sendBroadcast(intent);
    }
```
* 应用可以监听home键，通过广播。噢！还必须是动态广播。而且是不能拦截Home键，除非改底层。
```
//广播监听Home,并不能拦截Home键

//register
registerReceiver(mHomeKeyEventReceiver, new IntentFilter(Intent.ACTION_CLOSE_SYSTEM_DIALOGS));
//unregister
unregisterReceiver(mHomeKeyEventReceiver);

//init broadcastreceiver
 private BroadcastReceiver mHomeKeyEventReceiver = new BroadcastReceiver() {
        String SYSTEM_REASON = "reason";
        String SYSTEM_HOME_KEY = "homekey";

		@Override
		public void onReceive(Context context, Intent intent) {
			String action = intent.getAction();
			if (action.equals(Intent.ACTION_CLOSE_SYSTEM_DIALOGS)) {
				//TODO your task
			}
		}
	};
```
* 设置系统语言。
```
//设置英语为系统语言
Locale locale = new Locale("es", "es");
IActivityManager am = ActivityManagerNative.getDefault();
Configuration config = am.getConfiguration();
config.setLocale(locale);
am.updateConfiguration(config);
```
* 获取当前系统语言。
```
Locale locale = getResources().getConfiguration().locale;
String language = locale.getLanguage();
```
* 实现WindowManager添加的view，点击以外区域退出。添加WindowManager.LayoutParams.FLAG_WATCH_OUTSIDE_TOUCH的flag。不过点击外面，对应后面可见app也会有接收到该触摸响应。所以，建议是全屏的view，再通过拦截触摸事件等方式实现。
```
private WindowManager.LayoutParams mBarParams;
// init layout Params
mBarParams = new WindowManager.LayoutParams();
//重点添加此flag
mBarParams.flags = WindowManager.LayoutParams.FLAG_WATCH_OUTSIDE_TOUCH;

//再对addView的View进行ontouch监听
case MotionEvent.ACTION_OUTSIDE:{
//做退出的具体逻辑处理
}

```
* Java内存分配机制</br>
  ![](https://github.com/RealMoMo/Android-Tips/blob/master/img/Java_memory_allocation_mechanism.png)</br>
    </br>
 ![](https://github.com/RealMoMo/Android-Tips/blob/master/img/Jvm_memory_management.png)</br>
    </br>
 ![](https://github.com/RealMoMo/Android-Tips/blob/master/img/Java_classification_of_references.png)</br>
* Preference（不是SharedPreferences）的xml属性一定要给key属性。否则不会记录用户更新的数据。
* Eclipse有时报64k方法问题，实际上远没到达64k方法数，我的解决方式：架包以外部形式引用。
* Eclipse编译gc问题，我的解决方式：架包以外部形式引用;调整架包顺序。
* EditText selectAllOnFocus属性---获得焦点即全选
* 当App不需要Launcher Activity（既没有应用入口），AS不能直接run该App。需在AS  Run/Debug Configuration -> Android Application -> General -> Activity -> select the option "Do not launch Activity"。
* Glide加载图片，显示的图片仍是之前的图片。原因：因为Glide加载图片会将图片缓存到本地，如果url不变则直接读取缓存不会再重新加载。
```
//解决方式1：返回不同的url。

//解决方式2：加上这2个设置。
Glide.with(this)
                .diskCacheStrategy( DiskCacheStrategy.NONE )//禁用磁盘缓存
                .skipMemoryCache( true )//跳过内存缓存
```
* handler.post运行线程并不是开启一个新线程。[点击链接](http://bbs.csdn.net/topics/390786210)
```
//直接调用的Runnable的run方法，并不是strat()，所以仅仅是当做一个有run()的普通类使用而已，并不是开启了一个新的线程。
    private static void handleCallback(Message message) {
        message.callback.run();
    }

```
* Activity与Fragment的生命周期，估计各位都很熟悉。但View的生命周期呢？见下图：</br>
![]( https://github.com/RealMoMo/Android-Tips/blob/master/img/view_life.png)

* view.performClick()触摸该View点击事件。
* activity.moveTaskToBack(true);按Back键回桌面，但不销毁该Activity。
* ANR异常保存在/data/anr/traces.txt。
* ViewPager切换动画。
 ```

//设置viewpager切换动画
ViewPager.setPageTransformer(true,new MyTrans());

//实现viewPager的切换动画
class MyTrans implements  ViewPager.PageTransformer{

    /**
     * 根据偏移百分比，计算每个View的偏移比
     *
     * @param page     当前Fragment的View
     * @param position view的偏移百分比
     */
    @Override
    public void transformPage(View page, float position) {
        //TODO do your animation


    }
}
```
* 动态加载Fragment的布局按钮不能生效onClick属性。
* 判断viewpager能否继续左滑，继而触发某些事件。
```
//判断viewpager是否能继续左滑。(即已到最右的一页，判断准则有无惯性滑动)---若有内容的滑动是有惯性滑动状态。(当然排除极端无聊的用户)

    /**
     * 1.有内容的滑动的状态值变化：
     * 1--->2---->0
     *
     * 2.无内容的滑动的状态值变化：
     * 1--->0
     *
     */
    boolean isLeftScroll;

    @Override
    public void onPageScrollStateChanged(int state) {
        //根据state进行判断
        switch (state){
            //手指滑动---状态值：1
            case ViewPager.SCROLL_STATE_DRAGGING:{

            }break;
            //停止状态----状态值：0
            case ViewPager.SCROLL_STATE_IDLE:{
                //如果是最后一页(继续左滑是没有惯性滑动的)，且没有惯性滑动时，跳转其他界面
                if(!isLeftScroll &&viewPager.getCurrentItem()==(viewPager.getAdapter().getCount()-1)){
                    Intent intent = new Intent(this,CityChoiceActivity.class);
                    startActivity(intent);
                    finish();
                }

                isLeftScroll =false;

            }break;
            //惯性滑动--状态值：2
            case ViewPager.SCROLL_STATE_SETTLING:{
                    isLeftScroll=true;
            }break;
        }
    }

```
* 只用于应用内的广播，用LocalBroadcastManager本地广播。效率高，安全性更好。
* 避免使用隐式 Intent 广播敏感信息，信息可能被其他注册了对应BroadcastReceiver 的 App 接收。
* 不要在 Activity#onDestroy()内执行释放资源的工作，例如一些工作线程的销毁和停止，因为onDestroy()执行的时机可能较晚。可根据实际需要，在Activity#onPause()/onStop()中结合 isFinishing()的判断来执行。
* Android5.0 以后安全性要求较高的应用 应该使 用 window.setFlag(LayoutParam.FLAG_SECURE) 禁止截图&录屏。
* SharedPreference提交数据时，尽量使用Editor.apply() (异步),而非Editor.commit() (同步)。
* Canvas的drawText绘制文本是不会自动换行的。而StaticLayout是android中处理文字换行的一个工具类，StaticLayout已经实现了文本绘制换行处理。
* 如果使用Context.startActivity启动外部应用，最好做一下异常预防，因为寻找不到对应的应用时，会抛出异常。
* 别在用View分隔控件了。Space是Android 4.0中新增的一个控件，它实际上可以用来分隔不同的控件，其中形成一个空白的区域.这是一个轻量级的视图组件，它可以跳过Draw，对于需要占位符的任何场景来说都是很棒的。
* ValueAnimator.reverse() 这个方法可以很顺利地取消正在运行的动画。
* 自定义ViewGroup神器-ViewDragHelper，主要处理拖拽和设置子View的位置。
* 不要用intent传递大量的数据，这样有可能导致ANR或者报TransactionTooLargeException异常。
* 获取控件在屏幕的绝对坐标位置
```
int[] viewLocation = new int[2];
View.getLocationOnScreen(viewLocation);
```
* 我们知道首次加载界面在oncreate甚至onresume中View.getWidth和View.getHeight获得一个view的高度和宽度是0。若想获取控件的准确宽高等信息。常见通过View.post()。不过，我更喜欢通过监听OnGlobalLayoutListener。当然，如果你的需求只是加载界面获取一次控件的宽高信息。View.post()无疑更简洁。但若控件大小或者位置有改变，监听OnGlobalLayoutListener显然更强大。它是ViewTreeObserver的内部类，当一个视图树的布局发生改变时，可以被ViewTreeObserver监听到，这是一个注册监听视图树的观察者(observer)，在视图树的全局事件改变时得到通知。
```
        //Only use once
        private int viewHeight = 0;
		private int[] viewLocation = new int[2];
		view.getViewTreeObserver().addOnGlobalLayoutListener(
				new OnGlobalLayoutListener() {

					@Override
					public void onGlobalLayout() {
						// getting view height & width & screen postion
						viewHeight = view.getHeight();
						view.getLocationOnScreen(viewLocation);
						// removeOnGlobalLayoutListener
						view.getViewTreeObserver()
								.removeOnGlobalLayoutListener(this);
						
						

					}
				});
				
				
		//Get in real time
		onresume(){
		    //TODO addOnGlobalLayoutListener
		    ....
		}
		
		onpause(){
		    //TODO removeOnGlobalLayoutListener
		    ...
		}
```
* 有时候我们希望程序抛出异常时能把异常信息保存到制定文件夹的文件里，getStackTraceString就能够将异常信息转换成字符串的形式。
```
try {  
        //TODO  
    } catch (Exception e) {  
        String exceptionStr = Log.getStackTraceString(e);  
    }  
```
* 我常用SystemClock.sleep(long ms)模拟网络延迟，并且不会抛出InterruptedException。
* Activity的recreate()：一个Activity又一次创建自己一个新实例的方法。调用该方法目标Activity会又一次走一遍自己的生命周期。
* Android平台不建议使用枚举,Android官方的性能优化相关课程提过存在性能问题。[大神胡凯传送门](http://hukai.me/)
* Android进程保活招式大全[传送门](https://juejin.im/entry/586bcd07570c350068b1fba2)
* Java8愉快且方便的处理时间的类：LocalDate、LocalTime、LocalDateTime。相比Date来说，它们更安全、更精确也更明确。
* 为了减轻应用程序主进程的内存压力，对于耗内存比较多的界面（比如视频播放界面、flash播放界面等），可以在AndroidManifest.xml文件中对应的Activity标签下调用“android:process=".processname"”单开一个进程，但在退出这个界面的时候一定要在该界面的onDestory方法中调用System的kill方法来杀掉该进程。
* 在res/values/arrays.xml文件中定义的单个数组的元素个数不宜过大，过大会导致加载数据时非常慢，有时候你需要使用数组资源时数据有可能还没加载完成。
* 可以通过为application、activity自定义主题的方式来关掉多点触摸功能，只需要在自定义的主题下添加这两个标签：
```
  <item name="android:windowEnableSplitTouch">false</item>
  <item name="android:splitMotionEvents">false</item>
```
* 我个人比较喜欢关于Android矢量图的学习文章，是外文的。[干货链接](https://www.androiddesignpatterns.com/2016/11/introduction-to-icon-animation-techniques.html)
* 使用 Canvas.drawBitmapMesh实现仿真水波纹效果，网上搜一大把。
* Android Studio使用自己Framework架包。因为原生有些API的限制或反射等问题。需要使用到自己Framework架包。如果只是直接导入架包到项目，依旧编译不通过。需以下步骤：
```
//1.架包的依赖方式为编译时使用。

//2.project的build.gradle增加
allprojects {
    repositories {
        google()
        jcenter()
    }


//add this
    gradle.projectsEvaluated {
        tasks.withType(JavaCompile) {
            options.compilerArgs << "-Xlint:unchecked" << "-Xlint:deprecation"
            options.compilerArgs.add('-Xbootclasspath/p:app/libs/framework-full.jar') //写架包的绝对路径或相对路径
        }
    }
}

//3.app的build.gradle增加
defaultConfig {
    ...

    multiDexEnabled true

}

//4.显示依旧报错，但编译可以通过。
```
* View类中的getDrawingCache()等一系列方法可以用于获取View显示的Bitmap对象。
* TouchSlop，系统所能识别出的被认为是最小的滑动距离，ViewConfiguration.get(context).getScaledTouchSlop()。常用于自定义View。
* Context的作用域</br>
![](https://github.com/RealMoMo/Android-Tips/blob/master/img/context.png)
* 修改Toast字体大小。
```
	Toast toast = Toast.makeText(IndexActivity.this, "这个是一个测试", Toast.LENGTH_SHORT);
	// 修改Toast字体大小
	LinearLayout linearLayout = (LinearLayout) toast.getView();
	TextView messageTextView = (TextView) linearLayout.getChildAt(0);
	messageTextView.setTextSize(25);
	// 显示Toast
	toast.show();
```
* Android Studio3.0生成apk名字配置。
```
//最简单打包后应用名称(还可以根据debug release等各版本设置对应apk名字)
android.applicationVariants.all { variant ->
    variant.outputs.all {
        outputFileName = "customName.apk"
    }

}
```
* 截屏
```
public static Bitmap screenShot(int width, int height) {
		Class<?> demo = null;
		Bitmap bitmap = null;
		try {
			demo = Class.forName("android.view.SurfaceControl");
		} catch (Exception e) {
			e.printStackTrace();

		}
		try {
			Method method = demo.getMethod("screenshot", int.class, int.class);
			Object obj = method.invoke(null, width, height);
			bitmap = (Bitmap) obj;

		} catch (Exception e) {
			e.printStackTrace();
		}
		return bitmap;
	}
```
* Android推荐的数据结构:</br>
ArrayMap<K,V> 替代 HashMap<K,V> </br>
ArraySet<K,V> 替代 HashSet<K,V> </br>
SparseArray<V> 替代 HashMap<Integer,V> </br>
SparseBooleanArray 替代 HashMap<Integer,Boolean> </br>
SparseIntArray 替代 HashMap<Integer,Integer> </br>
SparseLongArray 替代 HashMap<Integer,Long> </br>
LongSparseArray<V> 替代 HashMap<Long,V> </br>
* UncaughtExceptionHandler接口，利用此接口可以对未捕获的异常善后。
* ValueAnimator.reverse() 这个方法可以很顺利地取消正在运行的动画。
* ViewParent.requestDisallowInterceptTouchEvent()——Android系统触摸事件机制大多时候能够默认处理，不过有时候你需要使用这个方法来剥夺父级控件的控制权。
* 设置布局动画属性android:animateLayoutChanges="true",需要更炫的可以自定义动画添加android:layoutAnimation="@anim/your_animation"。
```
     <!--Button点击事件只是对TextView.setVisibility-->
    <!--开启布局默认动画-->
    <LinearLayout
        android:animateLayoutChanges="true"    
        android:layout_width="match_parent"
        android:layout_height="200dp">

        <TextView
            android:text="666"
            android:id="@+id/tv1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

        <Button
            android:text="with Animation"
            android:onClick="one"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>


    <LinearLayout

        android:layout_width="match_parent"
        android:layout_height="200dp">

        <TextView
            android:text="666"
            android:id="@+id/tv2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

        <Button
            android:text="none Animation"
            android:onClick="two"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>
```
![](https://github.com/RealMoMo/Android-Tips/blob/master/img/animateLayoutChanges.gif)
* 深入理解 Java 垃圾回收机制 -> [链接](http://www.importnew.com/16173.html)
* Quick App 快应用[官方文档链接](https://www.quickapp.cn/)
* TextView通过代码设置Drawable没显示图片？请添加以下代码
```
    Drawable drawable = drawable = getResources().getDrawable(R.drawable.wifi_active);
    //important
    drawable.setBounds(0,0,drawable.getMinimumWidth(),drawable.getMinimumHeight());
    //该方法源码已说明The Drawables must already have had {@link Drawable#setBounds} called.
    tvWiFi.setCompoundDrawables(null,drawable,null,null);
```
* 初级入门JNI开发的[专题](https://www.jianshu.com/c/a25bf14495d7)
* 底层支持多用户，需设置的系统属性
```
//method 1:
setprop fw.max_users 8  
setprop fw.show_multiuserui true 

//method 2:
//配置多用户：

// /frameworks/base/core/res/res/values/config.xml
//修改其中的config_multiuserMaximumUsers的值为4 （看要支持几个用户）
//修改之后，设置中能看到用户这个菜单，而不是本来就有的账户。
```
* setBackgroundResource(0) 可以移除 View 的背景色
* 如果想把一个view保存为Bitmap，正常情况下用第一种方法就可以了，但是如果是ScrollView，则必须采用第二种方法。
```
//方法一:
public Bitmap createViewBitmap(View v) {  
    Bitmap bitmap = Bitmap.createBitmap(v.getWidth(), v.getHeight(),  
            Bitmap.Config.ARGB_8888);  
    Canvas canvas = new Canvas(bitmap);  
    v.draw(canvas);  
    return bitmap;  
} 

//方法二:
    /**
     * 截取scrollview的屏幕
     * @param scrollView
     * @return
     */
    public static Bitmap getBitmapByView(ScrollView scrollView) {
        int h = 0;
        Bitmap bitmap = null;
        // 获取scrollview实际高度
        for (int i = 0; i < scrollView.getChildCount(); i++) {
            h += scrollView.getChildAt(i).getHeight();
            scrollView.getChildAt(i).setBackgroundColor(
                    Color.parseColor("#ffffff"));
        }
        // 创建对应大小的bitmap
        bitmap = Bitmap.createBitmap(scrollView.getWidth(), h,
                Bitmap.Config.RGB_565);
        final Canvas canvas = new Canvas(bitmap);
        scrollView.draw(canvas);
        return bitmap;
    }
```
* Android6.0之后getResources().getColor()方法被废弃了，大家可以用ContextCompat.getColor(context, R.color.color_name)替换，ContextCompat是v4包里的，请放心使用，另外还有getDrawable()等方法。
* 实现Android一键锁屏 [传送门](http://www.cnblogs.com/chenyg32/p/3719714.html)
* 另一种通过广播方式，触发锁屏并且不会熄屏。测试环境原生Android5.0可以，6.0以上不可以。
```
adb shell am broadcast -a com.android.internal.policy.impl.PhoneWindowManager.DELAYED_KEYGUARD  --ei 'seq' 1
//seq 具体值看实际情况通常是1
```
* 展示时间用TextClock控件，计时用Chronometer控件。(往往很容易忽略Date&Time的原生控件)
* 入门了解与学习InstantApp系列 [链接](https://segmentfault.com/a/1190000011648067)
* Deep Links和App Links的对比
</br>![](https://github.com/RealMoMo/Android-Tips/blob/master/img/deeplines_applinks.jpg)
* 对Constraint Layout执行漂亮的动画用[ConstraintSet](https://juejin.im/entry/58b2fd59570c350069704265?utm_source=gold-miner&utm_medium=readme&utm_campaign=github)
* 修改WifiP2p设备名称(本人对此的具体应用环境:修改无线投屏名称)
```
    public void setDeviceName(String devName) {
        WifiP2pManager manager = (WifiP2pManager) getSystemService(Context.WIFI_P2P_SERVICE);
        WifiP2pManager.Channel channel = manager.initialize(this, getMainLooper(), null);
        try {
            Class[] paramTypes = new Class[3];
            paramTypes[0] = WifiP2pManager.Channel.class;
            paramTypes[1] = String.class;
            paramTypes[2] = WifiP2pManager.ActionListener.class;
            Method setDeviceName = manager.getClass().getMethod(
                    "setDeviceName", paramTypes);
            setDeviceName.setAccessible(true);

            Object arglist[] = new Object[3];
            arglist[0] = channel;
            arglist[1] = devName;
            arglist[2] = new WifiP2pManager.ActionListener() {

                @Override
                public void onSuccess() {

                }

                @Override
                public void onFailure(int reason) {

                }
            };

            setDeviceName.invoke(manager, arglist);

        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }

    }
```
* CameraView适配建议
<br/>![](https://github.com/RealMoMo/Android-Tips/blob/master/img/cameraview.png)
* 监听时间的广播需动态注册
* Intent.ACTION_DATE_CHANGED不广播的原因:已经广播过的日期改变，就不会再广播了，比如时间设置了2012-04-20 23：59， 到了2012-04-21 00：00的时候有该广播，但再把时间调回到2012-04-20 23：59甚至是更久以前，是不会有该广播了，除非把时间调到未来，也就是还没有广播的时间，如2012-04-21 23：59，才会继续有该广播。
* CoordinatorLayout的Behavior，原生自带的有:BottomSheetBehavior,HeaderBehavior,HeaderScrollingViewBehavior,ViewOffsetBehavior。更复杂的需求，需[自定义Behavior](https://blog.csdn.net/yanzhenjie1003/article/details/51946749)
* View滑动方式：1.layout方法 2.offsetLeftAndRight & offsetTopAndBottom方法 3.LayoutParams 4.Animation 5.scrolltTo || scrollBy 6.Scroller 7.ViewDragHelper
* 在Java的try、catch、finally中,try是必须的,catch和finally都不是必须的。
* Java提供的7种阻塞队列是:(常应用于生产者和消费者，如线程池。)
</br>ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
</br>LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
</br>PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。
</br>DelayQueue：一个使用优先级队列实现的无界阻塞队列。
</br>SynchronousQueue：一个不存储元素的阻塞队列。
</br>LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
</br>LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。
* 善用Android Studio中的'favorites'和'bookmark'功能
* 注解语法说明图</br>
![](https://github.com/RealMoMo/Android-Tips/blob/master/img/annotation_grammer.png)
* 语言适配，请把相应的默认语言加上。
```code
例如：
values-zh-rCN
values-zh-rHK
请补上
values-zh
(
why? 
避免有些是设置values-zh-rTW，若没有对应相应适配。会优先匹配values-zh,再没有才匹配values。
)
```
* Intent.FLAG_EXCLUDE_STOPPED_PACKAGES：
如果设置该属性，广播只会发送给已经开启的应用</br>
Intent.FLAG_INCLUDE_STOPPED_PACKAGES
如果设置该属性，广播会发送给所有的应用</br>
当然，系统默认是添加Intent.FLAG_EXCLUDE_STOPPED_PACKAGES属性
* 常用drawalbe-selector。但还有一个color-selector，常应用android:textColor。
```
//1.建color文件夹 res/color/
//2.建color-selector文件，color_test_selector.xml
<?xml version="1.0" encoding="utf-8"?>
<selector
  xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:color="@color/system_color" />
    <item android:state_focused="true" android:color="@color/system_color" />
    <item android:color="@color/color_white_light" />
</selector>
//3.应用
//3.1布局中的应用
android:textColor="@color/color_test_selector"
//3.2代码中的应用
textView.setTextColor(getResources().getColorStateList(R.color.color_test_selector));
```
* 动态设置控件id
```
//method1: 
TextView tv = new TextView(this);
int id = View.generateViewId();//api>=17
tv.setId(id);

//method2:
//2.1在values中创建ids.xml文件
//2.2文件内容：
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <item name="circle_centerview" type="id" />

</resources>

//2.3代码中使用
TextView tv = new TextView(this);
tv.setId(R.id.circle_centerview);

```
* 图像渲染利器RenderScript
* 简易的视图切换ViewAnimator及其子类</br>
![](https://github.com/RealMoMo/Android-Tips/blob/master/img/ViewAnimator.png)
* 继承AbstractProcessor处理编译时注解代码。要建Java工程，Android工程目前不可以直接使用。
* 获取外部存储设备的路径。
```
//method1 还包含内部存储路径（需自行过滤）
private String[] getStoragePaths(Context context) {

        StorageManager storageManager = (StorageManager) context
                .getSystemService(Context.STORAGE_SERVICE);

        List<String> pathsList = new ArrayList<String>();
        try {
            Class<?>[] paramClasses = {};
            Method getVolumeList = StorageManager.class.getMethod("getVolumeList", paramClasses);
            Object[] params = {};
            Object[] invokes = (Object[]) getVolumeList.invoke(storageManager, params);
            if (invokes != null) {

                for (int i = 0; i < invokes.length; i++) {
                    Object obj = invokes[i];
                    Method getPath = obj.getClass().getMethod("getPath", new Class[0]);
                    String path = (String) getPath.invoke(obj, new Object[0]);
                   pathsList.add(path);
                   
                   //realmo add test usb label
                    Method getUserLabel = obj.getClass().getMethod("getUserLabel",new Class[0]);
                    String lable = (String) getUserLabel.invoke(obj,new Object[0]);
                    Log.d("realmo","label:"+lable);
                    Log.d("realmo","path:"+path);
                            

                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }



        return pathsList.toArray(new String[pathsList.size()]);
    }
    
   
//method2
private String[] getStoragePaths(Context context) {   
        List<String> pathsList = new ArrayList<String>();

        StorageManager storageManager = (StorageManager) cxt
                .getSystemService(Context.STORAGE_SERVICE);
        try {
		    Method method = StorageManager.class
                    .getDeclaredMethod("getVolumePaths");
            method.setAccessible(true);
            Object result = method.invoke(storageManager);
            if (result != null && result instanceof String[]) {
                String[] pathes = (String[]) result;
                StatFs statFs;
                for (String path : pathes) {

                    if (!TextUtils.isEmpty(path) && new File(path).exists()) {
                        statFs = new StatFs(path);
                        if (statFs.getBlockCount() * statFs.getBlockSize() != 0) {
                            if (path.contains("/sdcard"))
                                continue;
                            pathsList.add(path);
                        }
                    }
                }
            }
       } catch (Exception e) {
            e.printStackTrace();
            File externalFolder = Environment.getExternalStorageDirectory();
            if (externalFolder != null) {
                pathsList.add(externalFolder.getAbsolutePath());
            }
        }        
		
		return pathsList.toArray(new String[pathsList.size()]);
    }
```
* 复制数组  System.arraycopy(Object src,  int  srcPos,Object dest, int destPos,int length);
* 获取前台Activity的包名类名
```
	private String getForegroundActivity(){
		ActivityManager am = (ActivityManager) mContext
				.getSystemService(android.content.Context.ACTIVITY_SERVICE);
		List<RunningTaskInfo> lst = am.getRunningTasks(1);
		return lst.get(0).topActivity.flattenToString();
	}
```
* View.BaseSaveState 保存View状态
* 监听App所有Activity的生命周期，实现Application.ActivityLifecycleCallbacks接口即可
* 加载大图，局部显示,了解下BitmapRegionDecoder
* 干货---[Android 屏幕绘制机制及硬件加速](https://blog.csdn.net/qian520ao/article/details/81144167)
* [Android各类型动画总结](https://github.com/OCNYang/Android-Animation-Set)
* StackTraceElement实现自己日志输出
* 隐式启动判断是否有匹配Intent的Activity  Intent.resolveActivity方法
```
Intent intent = new Intent(LOCK_SCREEN_ACTIVITY_NAME);
        //判断是否存在
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }else{

        }
```
* [深入理解Android插件化技术](https://zhuanlan.zhihu.com/p/33017826)  以及[Demo](https://github.com/vimerzhao/PluginDemo)
* ExifInterface 获取&设置图片信息
* 乘&除2的次方量级，请用效率更好的位运算符 
```
<<  :    左移运算符，num << 1,相当于num乘以2  

>>  :    右移运算符，num >> 1,相当于num除以2
```
* WebView的内存泄露问题,主要以下两种解决方案。</br>
1.独立进程法</br>
独立进程法顾名思义是让包含WebView的Acitivy以android:process=":web"的形式指定单独进程，然后在需要退出的时候使用System.exit(0)结束整个进程，内存自然回收了。该方法简单暴力，并有以下优点</br>
1.1.每个独立的进程都能分配独立的内存，这样的话，你的app可以获得双倍的内存，其中一半给Webview吃。增大Webview获得的内存，变相的减小内存泄露产生OOM的概率。</br>
1.2.在适当时机直接杀掉Webview独立进程，什么内存泄露，内存占用巨大的问题都见鬼去吧。要问什么时机？比如退出app时，检测到没有Webview页面时。</br>
1.3.Webview发生崩溃时不会导致app闪退，就像第二点说的，因为Webview是在独立进程中，如果发生崩溃，主进程还安然无事，app还在运行中，没有闪退，不闪的才是健康的。</br>
</br>2.源码解决法</br>
从源码得知， WebView.destroy()方法的执行时间在onDetachedFromWindow之前，所以就会导致不能正常进行unregister()，从而造成内存泄露。
```
//在Activity的onDestroy里方法里如下代码
@Override
protected void onDestroy() {
   if (mWebView != null) {
            try {
                ViewGroup parent = (ViewGroup) mWebView.getParent();
                if (parent != null) {
                    parent.removeView(mWebView);
                }
                mWebView.removeAllViews();
                mWebView.destroy();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        super.onDestroy();
}
```


### Development tools
* Git
* adb
* aapt
* 如何录制Demo运行的gif。用GifCam、FFmpeg都可以视频转gif。
* Genymotion不解释
* 代码对比：Beyond compare
* 压缩图片资源利器:[TinyPNG](https://tinypng.com/)
* CMD神器：cmder
* 颜色吸管
* 在线换算dp px sp等单位 [Android Pixel Calculator](http://angrytools.com/android/pixelcalc/)
* IDE自定义主题 [Color-themes](http://color-themes.com/?view=index)
*  [腾讯文档](https://docs.qq.com/)
*  可视化插值器[interpolator](http://inloop.github.io/interpolator/)
*  [Android SVG to VectorDrawable](http://inloop.github.io/svg2android/)
* [有道云笔记](https://note.youdao.com)构建自己的笔记系统
* 神器[Vysor](http://www.vysor.io/)PC操作Android设备
* Fiddler抓包工具
* Source Insight
* [Track TopActivity](https://github.com/109021017/android-TopActivity)
* Icon库</br>
[阿里巴巴矢量图标库](http://www.iconfont.cn/plus)</br> [IcoMoon](https://icomoon.io/app/#/select)</br> [Flaticon](https://www.flaticon.com/)
* 思维导图Xmind
* MarkDown编辑器[typora](https://www.typora.io/)
* 网络接口测试神器[Postman](https://www.getpostman.com/)
* 支持在线录制屏幕[Showmore](https://showmore.com/zh/)
 

###  IDE plugin
* [CodeGlance](https://github.com/Vektah/CodeGlance)   可用于快速定位代码，类似于Sublime编辑器右侧定位视图。
* [Alibaba java编码规约插件](https://github.com/alibaba/p3c)
* [GsonFormat](https://github.com/zzz40500/GsonFormat)   将JSON格式数据解析成实体
* [ButterKnifeZelezny](https://github.com/avast/android-butterknife-zelezny)   通过注解，省略findViewById等其他设置
* [Android Studio Prettify](https://github.com/Haehnchen/idea-android-studio-plugin)   自动生成findViewById
* Android Parcelable code generator & Android Parcelable code  generator(for kotlin)       自动生成Parcelable接口对应的实现代码
* [Gradle Dependencies Helper](https://github.com/siosio/GradleDependenciesHelperPlugin)   maven gradle 依赖支持自动补全
* [GradleDependenciesAndPluginsHelper](https://github.com/bestwu/gradle-dependencies-plugins-helper-plugin)   一样支持gradle 依赖自动补全
* [TranslationPlugin](https://github.com/YiiGuxing/TranslationPlugin) 翻译插件
* [AndroidLocalizationer](https://github.com/westlinkin/AndroidLocalizationer)翻译对应各国String资源
* [AndroidLocalizePlugin](https://github.com/Airsaid/AndroidLocalizePlugin)也是翻译对应各国String资源
* [JsonToKotlinClass ](https://github.com/wuseal/JsonToKotlinClass)
* [Android Resource Usage Count](https://github.com/niorgai/Android-Resource-Usage-Count)


### CutsomView
* Google推出的[FlexboxLayout](https://github.com/google/flexbox-layout)
* [HeaderAndFooterRecyclerView](https://github.com/cundong/HeaderAndFooterRecyclerView)
* [UltimateRecyclerView](https://github.com/cymcsg/UltimateRecyclerView)
* [SwipeRecyclerView](https://github.com/yanzhenjie/SwipeRecyclerView)
* 圆形ImageView最多star的项目[CircleImageView](https://github.com/hdodenhof/CircleImageView)
* Google推出适用于Android可扩展媒体播放器[ExoPlayer](https://github.com/google/ExoPlayer)
* [SmoothProgressBar](https://github.com/castorflex/SmoothProgressBar)
* [CoordinatorTabLayout](https://github.com/hugeterry/CoordinatorTabLayout)
* [NavigationTabBar](https://github.com/Devlight/NavigationTabBar)
* [PhotoView](https://github.com/chrisbanes/PhotoView)你懂的
* [PreviewSeekBar](https://github.com/rubensousa/PreviewSeekBar),还可以搭配ExoPlayer玩耍
* [StyleableToast](https://github.com/Muddz/StyleableToast)
* [Toasty](https://github.com/GrenderG/Toasty)
* 来疯直播安卓控件[SopCastComponent](https://github.com/LaiFeng-Android/SopCastComponent)
* 各式各样选择器[AndroidPicker](https://github.com/gzu-liyujiang/AndroidPicker)
* 国人代码家出品[AndroidImageSlider](https://github.com/daimajia/AndroidImageSlider)
* [android-floating-action-button](https://github.com/futuresimple/android-floating-action-button)
* [Context-Menu.Android](https://github.com/Yalantis/Context-Menu.Android)
* Yalantis出品UI一流[Side-Menu.Android](https://github.com/Yalantis/Side-Menu.Android)
* [vlayout](https://github.com/alibaba/vlayout)
* [UltraViewPager](https://github.com/alibaba/UltraViewPager)
* 不错的Loading控件[GABottleLoading](https://github.com/Ajian-studio/GABottleLoading)
* 很炫的[GADownloading](https://github.com/Ajian-studio/GADownloading)
* [SwipeBackLayout](https://github.com/ikew0ng/SwipeBackLayout)
* [ObservableScrollView](https://github.com/ksoichiro/Android-ObservableScrollView)
* [SmileyRating](https://github.com/sujithkanna/SmileyRating)
* [PasscodeView](https://github.com/kevalpatel2106/PasscodeView)
* 仿最美应用的底栏[ZuiMeiTAG](https://github.com/huage2580/ZuiMeiTAG)
* Florent出品[ArcLayout](https://github.com/florent37/ArcLayout)
* 验证码[CaptchaImageView](https://github.com/jineshfrancs/CaptchaImageView)
* 类似Github贡献热点图[TContributionsView](https://github.com/barryhappy/TContributionsView)
* 国人基于exoPlayer开发的播放器[yjPlay](https://github.com/yangchaojiang/yjPlay)
* 简单好用的[ColorTextView](https://github.com/zhonghanwen/ColorTextView)
* 强大下拉刷新控件[SmartRefreshLayout](https://github.com/scwang90/SmartRefreshLayout)
* 灵动的红鲤鱼[Fish](https://github.com/Jichensheng/Fish_2)
* 仿百度贴吧Loading小球[BaiduWave](https://github.com/Jichensheng/BaiduWave)
* 各种分布图控件[WilliamChart](https://github.com/diogobernardino/WilliamChart)
* 仿微信拍照控件（轻触拍照，长按摄像）[CameraView](https://github.com/CJT2325/CameraView)
* 老字号下拉刷新控件[android-Ultra-Pull-To-Refresh](https://github.com/liaohuqiu/android-Ultra-Pull-To-Refresh)
* 如作者介绍：可添加样式&过滤器等生成各效果的ImageView[StyleImageView](https://github.com/chengdazhi/StyleImageView)
* [android-advancedrecyclerview](https://github.com/h6ah4i/android-advancedrecyclerview)
* [BottomBar](https://github.com/roughike/BottomBar)
* 帮您快速实现跑马灯效果[MarqueeViewLibrary](https://github.com/gongwen/MarqueeViewLibrary)
* [MarqueeView](https://github.com/sfsheng0322/MarqueeView)
* [AndroidPileLayout](https://github.com/xmuSistone/AndroidPileLayout)
* [ScalingLayout](https://github.com/iammert/ScalingLayout)
* Android滚动选择控件[WheelView](https://github.com/wangjiegulu/WheelView)
* A cool search view animation library[JJSearchViewAnim](https://github.com/android-cjj/JJSearchViewAnim)
* 可搭配ViewPager相互控制的弹性动画View[BezierRoundView](https://github.com/RealMoMo/BezierRoundView)
* 密码控件[PasswordInput](https://github.com/EthanCo/PasswordInput)
* [TouchImageView](https://github.com/MikeOrtiz/TouchImageView)
* [CircleProgress](https://github.com/MyLifeMyTravel/CircleProgress)
* 很炫的LoadingView[AnimatedCircleLoadingView](https://github.com/jlmd/AnimatedCircleLoadingView)
* [MagicCircle](https://github.com/DevinShine/MagicCircle)
* [CircleSeekbar](https://github.com/feeeei/CircleSeekbar)
* [CircleMenu](https://github.com/Hitomis/CircleMenu)
* [CalendarView](https://github.com/huanghaibin-dev/CalendarView)
* [FilterMenu](https://github.com/linroid/FilterMenu)
* 我要说啥：Boom！ [BoomMenu](https://github.com/Nightonke/BoomMenu)
* [CircularFloatingActionMenu](https://github.com/oguzbilgener/CircularFloatingActionMenu)
* Tencent团队开发的UI库[QMUI_Android](https://github.com/QMUI/QMUI_Android)
* [AnimatedPieView](https://github.com/razerdp/AnimatedPieView)
* [BasePopup](https://github.com/razerdp/BasePopup)
* 能添加阴影的ImageView,实测效果没作者项目那么好。[PaletteImageView](https://github.com/DingMouRen/PaletteImageView)
* 可拖动，展示菜单，能自动吸边和半隐藏的悬浮球[FloatBall](https://github.com/huxq17/FloatBall)
* 本人表示用不溜...[FlyoutMenus](https://github.com/ShamylZakariya/FlyoutMenus)
* [BezierMaker](https://github.com/venshine/BezierMaker)
* [CircleMenu](https://github.com/ImangazalievM/CircleMenu)
* 颜色选择控件[colorpicker](https://github.com/QuadFlask/colorpicker)
* Auto fit[android-autofittextview](https://github.com/grantland/android-autofittextview)
* 水波纹[RippleEffect](https://github.com/traex/RippleEffect)
* 富文本编辑[richeditor](https://github.com/wasabeef/richeditor-android)
* 虽说star少，但真的不错[CircleLayout](https://github.com/nelson1110/CircleLayout)
* 毛玻璃效果[RealtimeBlurView](https://github.com/mmin18/RealtimeBlurView)
* [BGABanner](https://github.com/bingoogolapple/BGABanner-Android)
* [FlycoTabLayout](https://github.com/H07000223/FlycoTabLayout)
* B站开源的播放器[ijkplayer](https://github.com/Bilibili/ijkplayer)
* 基于上面B站开发的[GSYVideoPlayer](https://github.com/CarGuo/GSYVideoPlayer)
* Airbnb精品库[lottie-android](https://github.com/airbnb/lottie-android)
* [AndroidPicturePicker](https://github.com/ValuesFeng/AndroidPicturePicker)
* [PictureSelector](https://github.com/LuckSiege/PictureSelector)
* Animation effects to text, not really textview [HTextView](https://github.com/hanks-zyh/HTextView)
* [AndroidCharts](https://github.com/HackPlan/AndroidCharts)
* 金融类自定义View[FinancialCustomerView](https://github.com/scsfwgy/FinancialCustomerView)
* [MaterialDateTimePicker](https://github.com/wdullaer/MaterialDateTimePicker)
* [androidWheelView](https://github.com/weidongjian/androidWheelView)
* Google [cameraview](https://github.com/google/cameraview)
* 自定义仪表盘View[DashboardView](https://github.com/woxingxiao/DashboardView)
* 可视化气泡样式Seekbar[BubbleSeekBar](https://github.com/woxingxiao/BubbleSeekBar)
* [DiagonalLayout](https://github.com/florent37/DiagonalLayout)
* 搭配ViewPager [PageIndicatorView](https://github.com/romandanylyk/PageIndicatorView)
* 支持双向范围选择 [RangeSeekBar](https://github.com/Jay-Goo/RangeSeekBar)
* [TableView](https://github.com/evrencoskun/TableView)
* 密码控件[LolliPin](https://github.com/omadahealth/LolliPin)
* 悬浮控件[FloatWindow](https://github.com/yhaolpz/FloatWindow)
* 别家收集的 [AndroidCustomView](https://github.com/lygttpod/AndroidCustomView)
* 见其名 [SuperTextView](https://github.com/chenBingX/SuperTextView)
* 另一个SuperTextView [SuperTextView](https://github.com/lygttpod/SuperTextView)
* [RippleView](https://github.com/siriscac/RippleView)
* [FlickerProgressBar](https://github.com/LineChen/FlickerProgressBar)
* Simple and fantastic wheel view [WheelPicker](https://github.com/AigeStudio/WheelPicker)
* 守望先锋的加载动画View [OverWatchLoading](https://github.com/zhangyuChen1991/OverWatchLoading)
* Cool [SpaceTabLayout](https://github.com/long1eu/SpaceTabLayout)
* 点赞控件[LikeAnimation](https://github.com/frogermcs/LikeAnimation)
* 关于svg的控件[pathview](https://github.com/geftimov/android-pathview)
* [PathAnimView](https://github.com/mcxtzhang/PathAnimView)
* 粒子动画显示文字 [ParticleTextView](https://github.com/Yasic/ParticleTextView)
* 一个实现图片花式3D翻转效果的自定义View [Roll3DImageView](https://github.com/zhangyuChen1991/Roll3DImageView)
* Android 3D立体无限旋转容器 [StereoView](https://github.com/ImmortalZ/StereoView)
* 自定义View实现翻页效果 [BookPage](https://github.com/AnliaLee/BookPage)
* 标签可拖动排序的GridView [HandyGridView](https://github.com/huxq17/HandyGridView)
* 只拍摄该指定区域里的图像 [FocusSurfaceView](https://github.com/CGmaybe10/FocusSurfaceView)
* 任意View边沿渐变透明,实现思路简单清晰[EdgeTranslucent](https://github.com/qinci/EdgeTranslucent)
* 滚动显示TextView的数字 [RandomTextView](https://github.com/AndroidMsky/RandomTextView)
* ProCamera是一款基于Camera2 API的相机 [ProCamera](https://github.com/18Gray/ProCamera)
* [ImageEditor-Android](https://github.com/siwangqishiq/ImageEditor-Android)
* 强大、可定制、易扩展的 ViewPager 指示器框架 [MagicIndicator](https://github.com/hackware1993/MagicIndicator)
* 3d旋转切换view [LoopRotarySwitch](https://github.com/dalong982242260/LoopRotarySwitch)
* 多Smile噢 [SmileyLoadingView](https://github.com/andyxialm/SmileyLoadingView)
* 图片手势控件 [PinchImageView](https://github.com/boycy815/PinchImageView)
* 滑块拼图验证码控件 [Captcha](https://github.com/luozhanming/Captcha)
* 滚动数字控件 [ScrollNumber](https://github.com/a-voyager/ScrollNumber)
* 友人的自定义控件合集 [UIUtil](https://github.com/Dsiner/UIUtil)
* [MaterialStepperView](https://github.com/fython/MaterialStepperView)
* A circular seekbar [Croller](https://github.com/harjot-oberai/Croller)
* SimpleLineView会依次展现路径动画 [SimpleLineView](https://github.com/XingdongYu/SimpleLineView)
* 点赞控件 [GoodView](https://github.com/venshine/GoodView)
* 轮盘样式的 Fragment 选择菜单 [SpinMenu](https://github.com/Hitomis/SpinMenu)
* Give a custom shape to any android view [ShapeOfView](https://github.com/florent37/ShapeOfView)
* 高仿小米时钟 [MiClockView](https://github.com/MonkeyMushroom/MiClockView)
* [RopeProgressBar](https://github.com/cdeange/RopeProgressBar)
* [XhsEmoticonsKeyboard](https://github.com/w446108264/XhsEmoticonsKeyboard)
* Great!扩展性也不错！[TextPathView](https://github.com/totond/TextPathView)
* 高仿新版58 加载动画 [shapeLoadingView](https://github.com/zzz40500/android-shapeLoadingView)
* [ExpandableTextView](https://github.com/Manabu-GT/ExpandableTextView)
* 本人强推的阴影效果 [Long-Shadows](https://github.com/harjot-oberai/Long-Shadows)
* [3D-Layout](https://github.com/florent37/Android-3D-Layout)
* [Space-Navigation-View](https://github.com/armcha/Space-Navigation-View)
* 九宫格控件 [NineGridView](https://github.com/jeasonlzy/NineGridView)
 


### Utils Class
* [AndroidCommon](https://github.com/h4de5ing/AndroidCommon)
* [AndroidUtilCode](https://github.com/Blankj/AndroidUtilCode)
* [android-utils](https://github.com/jingle1267/android-utils)
* [Android_Utils](https://github.com/RealMoMo/Android_Utils)
* [android-common](https://github.com/litesuits/android-common)



### MIT License

    Copyright (c) 2018 RealMo

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.



 
