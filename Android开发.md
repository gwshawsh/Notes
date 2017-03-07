
Android开发 
====



[TOC]

_____
###Markdown操作OmniMarkupPreviwer：实时在浏览器中预览。快捷键如下：
* Ctrl+Alt+O: Preview Markup in Browser.
* Ctrl+Alt+X: Export Markup as HTML.
* Ctrl+Alt+C: Copy Markup as HTML.

_____

### 一、正则表达式


+ #### 手机号

	```java
		public static boolean isMobileNO(String mobiles) {
			Pattern p = Pattern.compile("^((13[0-9])|(15[^4,\\d])|(18[0-9]))\\d{8}$");
			Matcher m = p.matcher(mobiles);
			return m.matches();
			}

	```

_____

### 二、Java
* ####  Java 非静态内部类会默认持有外部类的引用。

* #### 正则表达式转义
		matches 、replaceAll、replaceFirst、split等函数参数为正则表达式，匹配"."等需要转义`"\\\."`。replace不支持正则。
		

+ #### StringBuffer 线程安全，效率低。Stringbuilder非多线程，效率高。

+ #### 泛型的限制 
	1. 在使用 String.class List.class 不能使用泛型形式（List<?>.class 是错误的）
	2. 在使用 instanceof 时候只能使用通配泛型（List<?>）  （只能 o instanceof List<?>或者 o instanceof List 而不能 o instanceof List<String>）
	3. 不能创建泛型数组


+ #### Java printf(n%)  换行


+ #### 枚举避免使用ordinal
最好添加一个 private final int order 变量，构造函数中赋值，然后获取顺序。

+ #### EnumSet.of(...) 创建枚举集合; new EnumMap<Enum,Object>(Enum.class)


+ #### Collections.unmodifiableXX() 创建不可改变的集合


+ #### java.lang.reflect   Class.newInstance


+ #### 对象传给print、字符串联操作符（+）、assert、调试器打印 会自动调用toString方法


+ #### Java没有可变参数，多个构造函数时可以用 Builder 代替


+ #### Java 读取long double 是非原子的


+ #### 调用构造函数
构造函数的第一行 可以用 **this(xx,....)**等，调用本类其他的构造函数，super类似，也需要放在首行，调用父类的构造方法


+ #### 后台线程
Thread.setDeamon(true) 将线程变成后台线程，进程若只剩下后台线程，这个进程会结束。


+ #### 获取父类名字
Object类中的getClass()是final的，因此 `super.getClass().getName()`仍然调用Object中的方法，获取的还是子类的名字，获取父类名字应该用 `getClass().getSuperClass().getName()` 


+ #### try return finally
* try中有return,finally在return中间运行（在return之后，但不影响return结果）
```java
    try{
        i = 1;
        return i;
    }finally{
        i++;
    }//结果 是 1
```
* try中有return,finally中也有return,finally中return值有效
```java
    try{
        return 1;
    }finally{
        return 2;
    }//结果 是 2
```
* try中有 **System.exit(0);**,Java虚拟机会直接退出，finally并不会执行。 
* 总结：**finally比return和break后执行且不影响return结果；如果finally中有return，会提前终止并以finally中return值返回。**


+ #### synchronized 和 Lock
1. Lock可以尝试获取锁 `lock.tryLock()`，也可以尝试一段时间后放弃`lock.tryLock(2,TimeUnit.SECONDS)`。
2. synchronized 不可以，但使用简单，更安全。


+ #### 线程休眠
jdk5之后用 `TimeUnit.SECONDS.sleep(1)` 代替 `Thread.sleep(1000)`


+ #### 用一个检查感兴趣条件的while循环包围wait（）


+ #### final 的 instant variable(实例变量) 没有 default value


+ #### public class的名字必须和文件名相同


+ #### Java 中的Type
##### Type
它是所有类型的公共接口。包括原始类型、参数化类型、数组类型、类型变量和基本类型。ParameterizedType, TypeVariable, WildcardType,GenericArrayType这四个接口都是它的子接口。
##### GenericDeclaration
这个接口Class、Method、Constructor都有实现，我们就是要用这个接口的getTypeParameters方法，它返回一个TypeVariable[]数组，这个数组里面就是我们定义的类型变量T和K，顺序与我们声明时一样。如果用循环语句将数组打印出来，你会发现只会输出T和K，这可不是我们想要的结果，那么想要获得预期的结果怎么办呢？请继续往下看。
##### TypeVariable
它表示类型变量。比如T，比如K extends Comparable<? super T> & Serializable，这个接口里面有个getBounds()方法，它用来获得类型变量上限的Type数组，如果没有定义上限，则默认设定上限为Object，请注意TypeVariable是接口，实际得到的是TypeVariableImpl实现类，下面几个接口都一样。
    拿T和K来说明，T没有定义任何上限，所以它就有一个默认上限java.lang.Object，实际跟踪代码的时候你会发现T的bounds属性为空，只有在调用了getBounds()方法后，才会有一个Type[1]数组[class java.lang.Object]。而对于K来说，调用了getBounds方法后，得到的数组是[java.lang.Comparable<? super T>, interface java.io.Serializable]，它们的类型却是不一样的，第1个是ParameterizedType，而第二个是Class
##### ParameterizedType
ParameterizedType表示参数化类型，就是上面说的java.lang.Comparable<? super T>，再比如List<T>，List<String>，这些都叫参数化类型。得到Comparable<? super T>之后，再调用getRawType()与getActualTypeArguments()两个方法，就可以得到声明此参数化类型的类(java.lang.Comparable)和实际的类型参数数组([? super T])，而这个? super T又是一个WildcardType类型。
##### WildcardType
它用来描述通配符表达式，上面返回的? super T正好是这个类型。然后调用getUpperBounds()上限和getLowerBounds()下限这两个方法，获得类型变量?的限定类型(上下限)，对于本例的通配符(?)，它的上限为java.lang.Object，下限为T
通过上面几个接口的分析，可以将Person类的泛型参数都解析出来，那么Person的超类以及实现的接口该怎么处理呢？Class类里面同样在1.5版本加入了getGenericSuperclass()与getGenericInterfaces()两个方法，用于返回带参数化类型的超类与接口。


+ #### 自增运算符
```java
int count = 0;
count = count++;
//count的值为0
```


+ #### 在序列化类中，不要使用构造函数为final变量赋值。


+ #### Integer.valueOf() -128~127是从对象池中取的。

+ #### 接口中可以有实现
```java
    interface A{
        void A();
    }
    interface B{
        public static final A = new A(){
            void A(){
                System.out.println("Bingo!");
            }
        }
    }

```

+ #### 最好不要在构造函数中声明初始化其他类

+ #### String 常量池
```java
String s = new String("哈哈哈")
```
不会检查、添加常量池

+ #### 集合默认申请长度 10
为避免多次申请内存，最好指定初始容量

+ #### 反序列化时构造函数不执行

+ #### ArrayList是动态数组；Vector是线程安全的动态数组

+ #### 子列表是原始列表的视图
```java
    List list = new ArrayList<Object>(100);
    list.subList(20,30).clear;// 删除list中20~30位置的元素
```
subList返回list的视图后，不能对list再进行修改，会报ConcurrentModificationException。可以用Collections.unmodifiableList 保护原list

+ #### 垃圾回收

##### （一）对象是否“已死”
1. 引用计数
2. 可达性
##### （二）垃圾收集算法
1. 标记-清楚
2. 复制
3. 标记-整理

_____
### 三、Android


+ #### EditText输入法
EditText 启动获得焦点会弹出 
输入法取消办法：在AndroidManifest.xml中对应的anctivity中添加 android:windowSoftInputMode="stateHidden"，Fragment同样适用。但是这种方法会导致快速点击控件时，出现输入法闪动问题。 edit.setInputType(InputType.TYPE_NULL)则不会;


+ #### ListView setSelection 选中并移动到
ListView中setSelection，因为是你自己触发的select事件，你自己肯定知道，它默认认为没有必要通知，因此不触发事件，也不通知监听器。只是刷新选中位置，并滚动界面，请求重新布局。
	很多操作需要等待某个UI组件加载完才能执行，可以用post(Runnable)来实现。
	例：
```java
    listView.post(new Runnable() {
				public void run() {	
					//执行操作
					listView.setSelection(i);
				}
			});
```
在listView加载完成后执行操作。


+ #### Padding与margin的区别。
Padding控制本控件内子控件的距离。Margin控制本控件在母控件的距离。


+ #### "<"符号 可以用 `"&lt"`来实现； "&" 用 `"&amp"`。


+ #### 键盘弹出压挤屏幕控件
AndroidManifest.xml 文件中界面对应的 < activity> 里加入
android:windowSoftInputMode="adjustPan" 键盘就会覆盖屏幕 android:windowSoftInputMode="stateVisible|adjustResize" 屏幕整体上移
    

+ #### Edittext获取焦点隐藏显示输入法、键盘
`
```java 
    private void setViewFocus(View v) {
    		v.setFocusable(true);
    		v.setFocusableInTouchMode(true);
    		v.requestFocus();
    		v.requestFocusFromTouch();
    	}

	private void openInput(Context context, View editText) {
		InputMethodManager m = (InputMethodManager) getActivity()
				.getSystemService(Context.INPUT_METHOD_SERVICE);
		m.showSoftInput(editText, 0);
	}

	private void closeInput(Context context, View editText) {
		InputMethodManager manager = (InputMethodManager) getActivity()
				.getSystemService(Context.INPUT_METHOD_SERVICE);
		manager.hideSoftInputFromWindow(editText.getWindowToken(), 0);
	}
    protected void closeInput(View editText) {
        InputMethodManager manager = (InputMethodManager) 
                getSystemService(Context.INPUT_METHOD_SERVICE);
        manager.hideSoftInputFromWindow(editText.getWindowToken(), 0);
    }
    protected void closeInputMethod() {
        InputMethodManager manager = (InputMethodManager)
                getSystemService(Context.INPUT_METHOD_SERVICE);
        manager.hideSoftInputFromWindow(getCurrentFocus().getWindowToken(), InputMethodManager.HIDE_NOT_ALWAYS);
    }
```
方法一：
在AndroidMainfest.xml中选择哪个activity，设置windowSoftInputMode属性为adjustUnspecified|stateHidden

例如：<activity android:name=".Main"
android:label="@string/app_name"
android:windowSoftInputMode="adjustUnspecified|stateHidden"
android:configChanges="orientation|keyboardHidden">
<intent-filter>
<action android:name="android.intent.action.MAIN" />
<category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
</activity>

方法二：
让EditText失去焦点，使用EditText的clearFocus方法
例如：EditText edit=(EditText)findViewById(R.id.edit);
edit.clearFocus();

方法三：
强制隐藏Android输入法窗口
例如：EditText edit=(EditText)findViewById(R.id.edit);
InputMethodManager imm = (InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
imm.hideSoftInputFromWindow(edit.getWindowToken(),0);

2.EditText始终不弹出软件键盘
例：EditText edit=(EditText)findViewById(R.id.edit);
edit.setInputType(InputType.TYPE_NULL);

以上都会导致点击时输入法闪动，终极方法Edittext.setInputType(InputType.NULL),这样会彻底禁用输入法，但是也会屏蔽输入类型设置 NUMBER、TEXT都不起作用。如果是自定义键盘输入的话可以用正则匹配。
	

+ #### BigDecimal 的 add 函数。 
	BigDecimal a = BigDecimal.ZERO;
	a.add(new BigDecimal("1"));
	//a的值还是零。。。。
	


+ #### List 中有选择框等控件等，会屏蔽点击 
android:descendantFocusability="blocksDescendants"加到 item 可以解决


+ #### ProgressDialog 内存泄露
在 Activity的  onDestroy  里要写上 pd.dismiss。或者使用弱引用private WeakReference<Context> context;否则，显示ProgressDialog的线程出问题会因为Activity泄露而隐藏实际的出错信息。


+ #### 可以代替繁琐的findviewbyid
	public <T extends View> T $(int id){
		return (T) super.findViewById(id);
	}
	public <T extends View> T $(View view,int id){
		return (T) view.findViewById(id);
	}


+ #### 去标题栏
	1、在AndroidManifest.xml的配置文件里面的<activity>标签添加属性：
	android:theme="@android:style/Theme.NoTitleBar.Fullscreen"
	2、在Activity的onCreate()方法中的super()和setContentView()两个方法之间加入下面两条语句
	this.requestWindowFeature(Window.FEATURE_NO_TITLE);//去掉标题栏
	this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);//去掉信息栏


+ #### Android快速截图
	1. 截图：
	adb shell screencap -p /sdcard/Download/12.png
	2. 从设备拉到本地电脑
	adb pull /sdcard/Download/12.png


+ #### {@link State#RESET}注释


+ #### textView.setTextAppearance(context,R.style.normal_button);//动态切textstyle


+ #### Fragment 不要自定义构造函数，在恢复现场时系统只能调用默认的构造函数。


+ #### ListView 单条更新

 	private void updateSingleRow(ListView listView, long id){
		if (listView != null) {                  
		int start = listView.getFirstVisiblePosition();                  
		for (int i = start, j = listView.getLastVisiblePosition(); i <= j; i++){
			if (id == ((Messages) listView.getItemAtPosition(i)).getId()) {                          
				View view = listView.getChildAt(i - start);                          
				getView(i, view, listView);                          
				break;                      
			} 
		}                 
	}


+ #### ListView 两次快速的notifyDataSetChanged会导致某个item 不刷新问题。详见点击两个item会同时改变背景色问题。用ListView单条更新来解决。
	View view = mListView.getChildAt(position-mListView.getFirstVisiblePosition());
	getView(position, view, mListView);
	

+ #### EventBus
	

1. 添加订阅者：EventBus.getDefault().register(this);
2. 订阅者所在类可以定义以下一个或多个方法用以接收事件：
        
        public void onEvent(MsgEvent1 msg)
        public void onEventMainThread(MsgEvent1 msg)
        public void onEventBackgroundThread(MsgEvent1 msg)
        public void onEventAsync(MsgEvent1 msg)
        
3. 发布者发布事件：EventBus.getDefault().post(new MsgEvent1("主线程发的消息1"));
4. 取消订阅：EventBus.getDefault().unregister(this);


+ #### 启动Activity方法
	
		public static void actionStart(Context context, String data1, String data2) {
			Intent intent = new Intent(context, SecondActivity.class);
			intent.putExtra("param1", data1);
			intent.putExtra("param2", data2);
			context.startActivity(intent);
		}
		
		SecondActivity.actionStart(FirstActivity.this, "data1", "data2");

+ #### 确认窗口
	private void showDialog() {
		AlertDialog.Builder builder = new AlertDialog.Builder(
				SellActivity.this);
		builder.setTitle("系统提示");
		builder.setMessage("退出将清空销售列表，确定要退出吗？");
		builder.setNegativeButton("否", new DialogInterface.OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int which) {
				dialog.cancel();
			}
		});

		builder.setPositiveButton("是", new DialogInterface.OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int which) {
				dialog.cancel();				
				SellActivity.this.finish();
			}
		});
		builder.show();
	}


+ #### 一个ListView的item要响应三个点击区域，整体要响应滑动
	用touchlistener，重写onTouch 可以根据点击位置判断区域。（可能产生适配问题）。


+ #### Android自定义键盘 （巨坑）
1）不能单独改变某个按键样式，只能通过多键盘堆叠实现
	2）默认键盘不对称，会向右偏移



+ #### 小微商户两步导出数据库 
	adb shell中：cp /data/data/com.hisense.pos.xwpos/databases/xwpos.db /sdcard
	cmd中：		 adb pull  /sdcard/xwpos.db

+ #### startActivityForResult 无法调用singleTask的Activity。
在B开启之后的瞬间（未等B返回任何result）,A的onActivityResult方法就会被调用,并且收到一个RESULT_CANCEL的request code。


+ #### intent-filter 
一条<intent-filter>元素至少应该包含一个<action>，否则任何Intent请求都不能和该<intent-filter>匹配。如果Intent请求的Action和<intent-filter>中个某一条<action>匹配，那么该Intent就通过了这条<intent-filter>的动作测试。如果Intent请求或<intent-filter>中没有说明具体的Action类型，那么会出现下面两种情况。
	(1) 如果<intent-filter>中没有包含任何Action类型，那么无论什么Intent请求都无法和这条<intent-filter>匹配；
	(2)反之，如果Intent请求中没有设定Action类型，那么只要<intent-filter>中包含有Action类型，这个Intent请求就将顺利地通过<intent-filter>的行为测试

	

+ #### onTouch（onTouchListener中） 优于 onTouchEvent（View中）先执行。

+ #### gettextsize返回像素单位
setTextSize(int size)默认单位sp,可以使用setTextSize(type,size) type TypedValue.COMPLEX_UNIT_PX 限定。


+ #### ListView嵌套ScrollView，重写LisctView 的 onMeasure 方法
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2,MeasureSpec.AT_MOST);
		super.onMeasure(widthMeasureSpec, expandSpec);
	}

+ #### Fragment 不会立即 commit
FragmentTransaction执行commit方法后，并非Fragment事务就立即执行，依赖于系统的处理性能。当然你可以调用executePendingTransactions()方法立即执行。其实这样做通常没有必要，除非Fragment事务依赖于其他现场的工作任务。

+ #### 一个View.animate().setListener(...)之后，再次View.animate() 会调用上一个listener。


+ #### ListView的 item 布局如果不是透明的，会覆盖Listview的 android:listSelector 效果

+ #### 闪屏
Activity的 theme 是透明（android:windowIsTranslucent）时，从其他栈Activity返回时（例如其他应用、singleInstance的Activity、调用系统图库相机 等）会出现闪现桌面的情况。


+ #### 自定义控件

1. 继承ViewGroup（LinearLayout 等）
2. 资源文件 attrs 中声明 styleable
3. XML 文件中引用，并声明属性
4. 构造函数中通过以下代码获取xml中对应属性 `R.styleable.IconText_left_text` （styleable名字 + " _ " + 对应属性名字）
```java
public class TabView extends LinearLayout {       
public TabView(Context context, AttributeSet attrs) {
    super(context, attrs);
    LayoutInflater.from(context).inflate(R.layout.share_topbar_tip, this);
    TypedArray a = context.obtainStyledAttributes(attrs,R.styleable.IconText);
    String leftIconTextString = a.getString(R.styleable.IconText_left_text);
    a.recycle();
    findControls();
    addListeners();
}
}
```
```xml
<declare-styleable name="IconText">
    <attr name="left_text" format="string"></attr>
    <attr name="right_text" format="string"></attr>
</declare-styleable>
```

```xml
<com.hisense.pos.xwpos.ui.TabView 
android:layout_width="match_parent"
android:layout_height="match_parent"
xmlns:app="http://schemas.android.com/apk/res-auto"
app:left_text = "全部交易"
/> 
```
```java


```java
TypedArray a = context.obtainStyledAttributes(attrs,R.styleable.IconText);
String leftText = a.getString(R.styleable.IconText_left_text);
a.recycle();
```

+ #### ImageView 变形
ImageView的图片最好用src设置，用background设置会导致图片变形始终填充控件，并且设置scaleType无效。

+ #### EditText 不弹出输入法
有时候EditText设置focus后，点击仍然不弹出输入法，设置Hint属性试试。


+ #### StringBuffer问题
StringBuffer没有覆盖equals() 和 hashCode()方法，因此，**将StringBuffer存进Java集合类中时会出现问题**。



+ #### TextView 代替 Button
如果不是必须用Button，尽量用TextView 代替 Button，在部分机器上Button 的背景设置会出现各种问题。

+ #### ScrollView 中嵌套 ListView 
要重写ListView；并且在ListView外层包裹LinearLayout（不包裹，ListView底部显示不全）

```java
public class ExpandableListViewForScrollView extends ExpandableListView {
	public ExpandableListViewForScrollView(Context context) {
		super(context);
	}
	public ExpandableListViewForScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public ExpandableListViewForScrollView(Context context, AttributeSet attrs,
			int defStyle) {
		super(context, attrs, defStyle);
	}
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2,
				MeasureSpec.AT_MOST);
		super.onMeasure(widthMeasureSpec, expandSpec);
	}
}
```


```xml
<ScrollView
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:background="#f0eff4"
            >
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:padding="@dimen/dp20"
                >
            <com.hisense.pos.xwpos.commonui.ExpandableListViewForScrollView
                android:id="@+id/lb_list_plus"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@drawable/shape_corner_white"
                android:paddingLeft="@dimen/dp10"
                android:paddingRight="@dimen/dp10"
                android:paddingTop="@dimen/dp5"
                android:paddingBottom="@dimen/dp5"
                />
            </LinearLayout>
        </ScrollView>
```

+ #### fastjson key和属性名不一致
fastjson的key是根据javabean里面的getter和setter方法来的，不是根据属性名的，所以会出现这个问题，你在属性的get和set方法上面写上标注，说明转成什么就行了比如 @JSONField(name="SOMETHING") 

+ #### okhttp Gzip不自动unzip response
Just omit the accept-encoding header from your code. OkHttp will add its own accept-encoding header, and if the server responds with gzip then OkHttp will silently unzip it for you.
```java
OkHttpClient.Builder builder = new OkHttpClient.Builder();
        builder.addInterceptor(
                new Interceptor() {
                    @Override
                    public Response intercept(Chain chain) throws IOException {
                        Request.Builder builder = chain.request().newBuilder();
                        builder.addHeader(HTTP.CONTENT_TYPE, "application/x-www-form-urlencoded; charset=UTF-8");
                        //builder.addHeader("Accept-Encoding", "gzip");  不要添加，OkHttp会自动压缩解压；添加后反而不能自动解压
                        Request request = builder.build();
                        Response response = chain.proceed(request);
                        return response;
                    }
                }
        );
```

+ #### 找不到layout 文件
setContentView(R.layout.activity) 找不到R.layout.activity文件，但是R.layout.activity存在，可能与横竖屏有关。

+ #### 微信分享
微信分享没有WXMediaMessage.title 和description 不能分享给朋友，只能分享到朋友圈。出现朋友列表，点击没反应。

+ #### View的位置参数
x = left + translationX   
y = top + translationY  
width = right - left    
height = bottom - top

+ #### MotionEvent
getX/getY 相对于当前View左上角x和y;getRawX/getRawY 相对于手机屏幕左上角x和y.

+ #### TouchSlop滑动最小距离
get(getContext()).getScaledTouchSlop();

+ #### onPause 和 onResume
第一个activity的onPause执行完后，第二个activity的onResume才能执行，因此不要在onPause中执行耗时操作

+ #### android:process
android:process = ":remote" //当前应用的私有进程，xxx.xxx.xxx:remote,其他应用的组件不可以跟它跑在同一个进程中。
android:process ="xxx.xxx.xxx.remote" //全局进程，其他应用通过ShareUID跟他跑在同一个进程中。（需要相同的签名）

+ #### Serializable 和 Parcelable
Serializable  在反序列化的时候会检测serialVersionUID值，不相等报异常。serialVersionUID = 1L 系统会默认生成一个hash值。静态成员和 **transient**关键字标记的不参与序列化过程。

+ #### ThreadLocal
不同线程访问同一个ThreadLocal对象，获得的值是不一样的。

+ #### include 仅支持android:layout_*属性
如果设置了android:layout_*属性，必须android:layout_height a 和ndroid:layout_width后才能生效。

+ #### 在一项操作中混用基本类型和装箱类型，装箱类型会拆箱。

+ #### View滑动
1. View自身的scrollTo/scrollBy （只能将View中的内容进行移动，并不能将View本身移动）
2. 通过动画 （View动画是对影像操作，如果结束后需要保留最终状态 需要有：android:fillAfter="true"，并且响应位置还是原区域;属性动画则不需要 注：）
3. 改变View的LayoutParams

+ #### overridePendingTransition Activity的过渡动画，必须在startActivity 或 finish后调用才有效果。

+ #### 无限循环的属性动画需要在Activity退出时及时停止，否则会内存泄露

+ #### View动画后setVisibility(View.GONE) 无效，需要用 view.clearAnimation()清除状态

+ #### Activity.getWindow.getDecorView() 可以获得 decor view

+ #### RxJava
```java
_apiService.login(mobile, verifyCode)
            .// 略  显示提示框、切换线程
            .flatMap(result -> {    // 可以运行在IO线程
                if (result.getStatus() == RESTResult.FAILURE) {
                    HttpResponseCode code = result.getCode();
                    // 根据不同code进行不同处理
                    ...

                    return Observable.error(new ServerException(result.getMessage()));
                }
                return Observable.just(result.getData());
            })
            .subscribe(new Observer<User>() {
                @Override
                public void onCompleted() {
                    hideLoadingDialog();
                }

                @Override
                public void onError(Throwable e) {
                    e.printStackTrace();
                    hideLoadingDialog();

                    if(e instanceof ServerException){
                        Toast.makeText(_context, e.getMessage(), Toast.LENGTH_SHORT).show();
                    }else{
                        Toast.makeText(_context, "请求失败,请稍后重试", Toast.LENGTH_SHORT).show();
                    }
               }

                @Override
                public void onNext(User user) {
                    // 直接是User对象
                }
            });
```


+ #### Android Studio  .arr 文件
```java 
①.将aar包复制到libs目录下 

②.配置build.gradle文件：

加入

  repositories {
        flatDir {
        dirs 'libs'
    }

compile(name:'camerascan-1.0', ext:'aar')
```

+ #### SparseArray ArrayMap HashMap
SparseArray 当key 是int时可以避免自动装箱
ArrayMap 以时间换取空间，两个数组分别存key 和 value，空间占用比HashMap小，采用二分法查找，效率比hash低，不适用于大量数据。

+ #### 耗时计算
```java
start = System.nanoTime() 
TimeUnit.NANOSECONDS.toMillis(System.nanoTime() - start)+"毫秒"
```

+ #### Assert 正确使用方式

```java
if(BuildConfig.DEBUG && !(ctx instanceof FragmentActivity))
      throw new RuntimeException();
```

+ #### ImageView的scaleType

Android中ImageView的scaleType有8个可选项

1 matrix不对图片进行缩放，对原图从view的左上角绘制图片（图片不变形）；

2 fitXY将图片全部绘制到view中，但是图片会变形；（图片变形，充满view）

3 fitStart、fitCenter、fitEnd三个属性会选择图片的较长的边为基准对图片进行缩放处理，正因为如此，图片不会完全充满view，不同之处在于图片在view中绘制的锚点不同；（图片不变形，不能充满view）

4 center不对图片进行缩放处理，选取view及图片的中心点进行绘制；（图片不变形）

5 centerCrop会保证图片充满view，因此会选取图片中较短的边为基准做缩放处理；（图片不变形，充满view）

6 centerInside保证图片显示在view中间，当图片大于view时，会选取图片较长的边为基准对图片进行缩小，当图片宽高小于view时，直接将图片显示到view中间。（图片不变形）
