# Android-Tips

### 本项目会持续更新

内容是我自己平时学习与工作积累的代码与准则，并没有什么原理剖析。如有错误欢迎指正,如有侵权,请联系我删除。


* Android Tools命名空间原来是有大用处的。大致有三种主要功能：</br>
  xml中的错误处理</br>
  xml 预览</br>
  资源压缩</br>
[具体阅读地址点我](https://www.jianshu.com/p/2912bcba4465)
* 如果你觉得在安装Eclipse后还需要配置android开发环境很麻烦，你可以直接使用ADT Bundle，它是一个懒人套餐。反正我就是怎么过来的。[链接](http://www.androiddevtools.cn/)
* 使用Toast时，建议定义一个全局Toast对象。可以避免连续显示Toast 时不能取消上一次 Toast 消息的情况。
* 灵活使用ViewStub Merge Include标签优化布局。
* 静态变量不要直接或者间接引用Activity、Service等。这会使用Activity以及它所引用的所有对象无法释放，若用户操作时间一长，内存就会狂升。
* 在Activity.onPause()或 Activity.onStop()回调中，关闭当前 activity 正在执行的的动画。
* ImageView Selector不生效的解决方案
<br>1.ImageView要加上clickable="true"，不然它的selector 是不会有效果。</br>
2.ImageView selector的默认图片item要放到最后。

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
//TODO add img

* 长按MenuItem会Toast该Item的Title。
* 设置系统壁纸。
```
WallpaperManager mWallManager = WallpaperManager
				.getInstance(getActivity());
		mWallManager.suggestDesiredDimensions(wallpaperWidthPX,
				wallpaperHeightPX);
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
* 保存&删除图片并通知系统相册刷新。
```
Intent intent= new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
   // 最后通知图库更新
    intent.setData(Uri.fromFile(file));
    sendBroadcast(intent);
```
* 应用可以监听home键，通过广播。噢！还必须是动态广播。而且是不能拦截Home键，除非改底层。
* 设置系统语言。
```
//设置英语为系统语言
Locale locale = new Locale("es", "es");
IActivityManager am = ActivityManagerNative
								.getDefault();
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
*只用于应用内的广播，用LocalBroadcastManager本地广播。效率高，安全性更好。
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


//TODO update 开发工具
* 如何录制Demo运行的gif。用GifCam、FFmpeg都可以视频转gif。
* Genymotion不解释
* 代码对比：Beyond compare

//TODO update AS插件

//TODO update 工具类以及自定义View



 
