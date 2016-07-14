# Android学习笔记 

标签（空格分隔）： 编程 Android Java

---


[TOC]


## 一、第三方库
### 1、PagerSlidingTabStrip 使用
PagerSlidingTabStrip是一个页面指示控件，需要配合ViewPager使用。

1.使用Android Studio添加依赖

在build.gradle里的dependencies里，增加
```
compile 'com.astuetz:pagerslidingtabstrip:1.0.1'
```

2.在布局文件里放至ViewPager上：
```
    <com.astuetz.PagerSlidingTabStrip
        android:id="@+id/pager_tabs"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:textColor="#FFFFFFFF"
        android:background="#3ba79e"
        app:pstsShouldExpand="true"
        app:pstsIndicatorHeight="4dp"
        app:pstsIndicatorColor="#205841"
        android:textSize="18sp"/>
```

常用属性：
- pstsIndicatorColor：tabs下方指示条颜色
- pstsDividerColor  ：tabs之间的分隔条颜色
- pstsIndicatorHeight：指示条高度
- pstsShouldExpand    ：指示tabs是否等宽

3.在`onCreate`方法（如果是Frgemnt里就在`onCreateView`）中，将该控件绑定至ViewPager。

```
private PagerSlidingTabStrip mTabs;
private ViewPager mViewPager;
.........
mTabs = (PagerSlidingTabStrip) findViewById(R.id.tabs);
mViewPager = (ViewPager) findViewById(R.id.pager);
mViewPager.setAdapter(new PagerAdapter(getSupportFragmentManager()));

mTabs.setViewPager(mViewPager);
```

### 2、SwipeRefreshLayout使用
SwipeRefreshLayout是supportV4里的一个下拉刷新控件。需要配合ListView使用。

1.在布局文件里，使用SwipeRefreshLayout包裹住ListView
```
<android.support.v4.widget.SwipeRefreshLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/refresh_layout">

    <ListView
        android:id="@+id/list_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</android.support.v4.widget.SwipeRefreshLayout>
```

2.在代码里设置相关内容
```
private SwipeRefreshLayout mSwipeRefreshLayout;
mSwipeRefreshLayout = (SwipeRefreshLayout) view.findViewById(R.id.refresh_layout);
mSwipeRefreshLayout.setOnRefreshListener(this);
mSwipeRefreshLayout.setColorSchemeResources(android.R.color.holo_blue_bright,
                                            android.R.color.holo_orange_light,
                                            android.R.color.holo_red_light,
                                            android.R.color.holo_green_light);

```
- setOnRefreshListener设置刷新回调，需要实现`onRefresh`方法
- setColorSchemeResources设置刷新动画时的颜色

> 自动刷新：需要使用：
```
mSwipeRefreshLayout.post(new Runnable() {
    @Override
    public void run() {
        mSwipeRefreshLayout.setRefreshing(true);
        onRefresh();
    }
});
```

### 3、ImageLoader使用
ImageLoader是一个强大的图片加载库。

1. 在build.gradle文件里添加依赖
```
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'
```
2. 初始化ImageLoader

```
ImageLoaderConfiguration config =
		new ImageLoaderConfiguration.Builder(context)
				.denyCacheImageMultipleSizesInMemory()
				.threadPriority(Thread.NORM_PRIORITY - 2)
				.tasksProcessingOrder(QueueProcessingType.LIFO)
				.build();
ImageLoader.getInstance().init(config);
```

- denyCacheImageMultipleSizesInMemory: 默认情况下，Imageloader会对使用过的同一图片的不同尺寸解码数据进行缓存，调用此方法后，同一图片只缓存一个尺寸的解码数据。
- threadPriority: 设置图片加载线程的优先级
- tasksProcessingOrder: 设置图片加载与显示的顺序，默认为`FIFO`

3. 设置ImageLoader的加载选项

```
private DisplayImageOptions mOptions =
		new DisplayImageOptions.Builder()
				.showImageOnLoading(R.drawable.no_image)
				.showImageOnFail(R.drawable.no_image)
				.cacheInMemory(true)
				.cacheOnDisk(true)
				.considerExifParams(true)
				.build();
```

4. 加载与显示图片

```
mImageLoader.displayImage(images.get(0), holder.newsImage, mOptions);
```

### 4、Gson使用

1. 在build.gradle文件里添加依赖
```
compile 'com.google.code.gson:gson:2.3.1'
```
2. 代码使用

```
Gson gson = new Gson();
Logo logo = gson.fromJson(jsonString, Logo.class);  
```

> 注意：Logo类里的变量名需要和Json数据里的一致，否则会出现错误。

### 5、DiskLruCache使用
第一次看到DiskLruCache，是在google android的DisplayingBitmaps示例代码里，用于缓存从网络下载的图片文件。其实我们也可以使用它来缓存其他数据文件。下面简单总结下DiskLruCache的使用：

#### 1. 下载[DiskLruCache.java](https://github.com/googlesamples/android-DisplayingBitmaps/blob/master/Application/src/main/java/com/example/android/displayingbitmaps/util/DiskLruCache.java)文件。

#### 2. 打开磁盘缓存
```
public static DiskLruCache open(File directory, int appVersion, int valueCount, long maxSize)
```

- directory：指定缓存路径
- appVersion：指定应用版本，如果版本号变化，缓存目录会被清空
应用版本可以通过下面代码得到：
```
PackageInfo info = context.getPackageManager().getPackageInfo(context.getPackageName(), 0);  
int version = info.versionCode;
```
- valueCount：指定缓存数目，即一个key对应多少个缓存文件，一般设置为1
- maxSize：指定缓存的总大小

#### 3. 将文件添加到缓存

1. 通过`DiskLruCache.edit()`创建一个`DiskLruCache.Editor`对象。
2. 通过`Editor.newOutputStream()`创建一个输出流
3. 将需要保存的数据写至上一步创建的输出流中。
4. 上一步成功则调用`Editor.commit()`提交数据，如果失败则调用`Editor.abort()`放弃数据提交
5. 调用`DiskLurCache.flush()`强制将缓存刷新至系统。

```
String key = hashKeyForDisk(url);
final DiskLruCache.Editor editor = mDiskLruCache.edit(key);
if (editor != null) {
    outputStream = editor.newOutputStream(DISK_CACHE_INDEX);
    if (url.startsWith("http://") || url.startsWith("https://")) {
        if (Http.downloadUrlToStream(url, outputStream)) {
                LogUtil.log("download <" + url + "> success.");
                editor.commit();
        } else {
            LogUtil.log("download <" + url + "> failed.");
            editor.abort();
        }
    }

    mDiskLruCache.flush();
}
```

Editor操作需要一个参数key，这个key将会用来作为缓存文件的文件名，直接使用url不太合适，推荐将url进行md5转换，参考代码如下：

```
public static String hashKeyForDisk(String key) {
    String cacheKey;
    try {
        final MessageDigest mDigest = MessageDigest.getInstance("MD5");
        mDigest.update(key.getBytes());
        cacheKey = bytesToHexString(mDigest.digest());
    } catch (NoSuchAlgorithmException e) {
        cacheKey = String.valueOf(key.hashCode());
    }
    return cacheKey;
}

private static String bytesToHexString(byte[] bytes) {
    // http://stackoverflow.com/questions/332079
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < bytes.length; i++) {
        String hex = Integer.toHexString(0xFF & bytes[i]);
        if (hex.length() == 1) {
            sb.append('0');
        }
        sb.append(hex);
    }
    return sb.toString();
}
```

#### 4. 从缓存里读取文件

1. 通过`DiskLruCache.get()`得到一个`DiskLruCache.Snapshot`对象。
2. 通过`Snapshot.getInputStream()`得到一个输入流
3. 从上面得到的输入流中取数据即可。

```
InputStream inputStream = null;
if (mDiskLruCache != null) {
    final String key = hashKeyForDisk(data);
    try {
        final DiskLruCache.Snapshot snapshot = mDiskLruCache.get(key);
        if (snapshot == null) {
            return null;
        }

        inputStream = snapshot.getInputStream(DISK_CACHE_INDEX);
        return inputStream;
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

#### 5. 从缓存中移除文件
```
public synchronized boolean remove(String key) throws IOException  
```
一般情况下，我们无需手动去移除某个文件，当缓存大小达到open时设定的最大值时，DiskLruCache会自动移除多余的缓存。不过，当你确认某个key对应的数据已经过时，即需要更新时，可以手动移除。

#### 6. 获取当前占用缓存大小
```
public synchronized long size() 
```
获取当前缓存大小，如果这时有一个后台删除操作正在进行，这个值可能会比设定的最大值大。

#### 7. 清空缓存
```
public void delete() throws IOException
```
关闭并删除所有缓存，这个方法将会删除缓存目录下的所有文件，包括不是缓存创建的文件。

#### 8. journal解析
```
libcore.io.DiskLruCache
1
1
1

DIRTY a20684960ed6a28df4015d759a29fe3f
CLEAN a20684960ed6a28df4015d759a29fe3f 627030
READ a20684960ed6a28df4015d759a29fe3f
DIRTY 90d2558df8a7d8c5df3a9358ddf1ff0f
CLEAN 90d2558df8a7d8c5df3a9358ddf1ff0f 214157
```

journal文件前5行分别为字符串常量"libcore.io.DiskLruCache"、缓存版本、应用程序版本、缓存数、空白行。
接下来每一行都是一条缓存的记录项，每条记录项的值通过空格分开，分别表示状态、键值以及特定的状态值。

- DIRTY行表示该记录项正在被创建或有更新。
每一项成功的DIRTY操作后必须紧跟着CLEAN或REMOVE操作。
DIRTY行后面没有匹配CLEAN或REMOVE行说明临时文件需要被删除。

- CLEAN行指明一个缓存项已经成功被发布或者被读取。
一个已经发布的行后面跟着的是每一个值的长度。
- READ行指明了LRU可访问的缓存项。
- REMOVE行指明了已经被删除的缓存项。

journal文件偶尔会被压缩用来减少冗余的行。临时文件“journal.tmp”在压缩时将会被使用，如果缓存被打开的话，如果它存在就应该把它删除掉。

#### 9. 小结
- DiskLruCache使用了两个内部类，一个是DiskLruCache.Editor用来写入缓存信息，一个是DiskLruCache.Snapshot用来读取缓存文件。
- 缓存文件分为CLEAN文件和DIRTY文件，CLEAN文件为缓存完成的文件，DIRTY文件用来临时标记正在修改。
- journal文件记录了哪些关键字已经被缓存、哪些正在修改、哪些正在读取。
- 缓存入口项同LruCache一样使用LinkedHashMap，能够直接调用LinkedHashMap.eldest()函数返回最久未被使用的项，省去了LRU算法的实现。
- 整理空间已经重写journal操作留给了线程池单线程异步处理，一是防止了主线程阻塞问题，二是解决了同步问题。

### 6、

## 二、Android Studio使用

### 1、配置属性

#### 1. 加大java编译时的可用内存

在gradle.properties文件里，增加：
```
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m
```

#### 2. 设置编译自动生成变量
```
buildTypes {
    release {
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    debug {
//            buildConfigField "boolean", "DEBUG", "true" //是否为调试版本
        buildConfigField "String", "EXIT_TYPE", "\"stack\""
    }
}
```

### 2、插件使用
#### 1. GsonFormat
可以将json格式的数据快速转换成java代码

#### 2. ButterKnife使用
ButterKnife是一个可以按指定的布局文件，使用注解方式生成相应控件代码。
- 安装`Android ButterKnife Zelezny`
- 在`build.gradle`文件里添加编译依赖
```
compile 'com.jakewharton:butterknife:6.1.0'
```
- 将光标移动到布局文件名上，选择`Generate`->`Generate ButterKnife Injections`，选中需要生成控制代码的控件名就可以了。
- 在`onCreate()`方法里调用
```
ButterKnife.inject(this);
```


## 三、Android应用积累
### 1、Android assets目录使用
存放在assets目录下的原生资源，在系统编译时，不会对其进行编译，所以，我们不能使用R.assets这样形式来访问，同时，apk安装后，assets目录也不会解压到apk安装的目录，而是被apk绑定在一起的，所以，无法直接获取assets目录的绝对路径。Android为此提供了`AssetManager`工具类。该类提供一些低级的api，允许你以一个简单的数据流的形式来打开和读取与应用绑定的raw文件。

1. 获取`AssetManager`
调用`getAssets()`方法获取

2. 常用方法
- final String[] list(String path)
返回指定路径的下的所有文件和目录名
- final InputStream open(String fileName)
使用ACCESS_STREAMMING打开assets下指定的文件
- final InputStream open(String fileName, int accessMode)
使用显示 的访问模式打开assets下指定的文件

3. 加载assets目录下的网页：
mWebView.loadUrl("file:///android_asset/http/index.html");
> 这种方式可以加载assets目录下的网页，并且与网页相关的css, js, 图片等文件也会一起加载。

### 2、 Android应用全屏设置
在`styles.xml里增加样式：

```
<style name="NoTitleFullscreen" parent="Theme.AppCompat.NoActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
</style>
```

### 3、退出apk的几种方式

#### 1. 使用广播方式
可以创建一个BaseActivity，在其中注册接收处理一个`退出广播`，在其中调用`finish()`方法，finish掉Activity，其他Activity都继承此BaseActivity，当需要退出应用的时候，发送`退出广播`，即可finish全部Activity，进而退出应用。
示例代码如下：

- BaseActivity.java

```
public class BaseActivity extends AppCompatActivity {
    public static final String EXIT_ACTION = "exit.action";
    private BroadcastReceiver mExitReceiver = new ExitReceiver();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction(EXIT_ACTION);

        registerReceiver(mExitReceiver, intentFilter);
    }

    private class ExitReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            LogUtil.log("action = " + action);
            if (action.equals(EXIT_ACTION)) {
                BaseActivity.this.finish();
            }
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(mExitReceiver);
    }
}
```

- 其他Activity

```
Intent intent = new Intent();
intent.setAction(BaseActivity.EXIT_ACTION);
sendBroadcast(intent);
```

#### 2. 建立保存Activity的List
所有启动的Activity都加到List里，这样，要退出应用时，可以从List里取出所有的Activity来调用`finish()`方法。

示例代码：

- ActivityStack.java
```
public class ActivityStack {
    public static ArrayList<Activity> mActivityStacks = new ArrayList<>();

    public static void addActivity(Activity activity) {
        mActivityStacks.add(activity);
    }

    public static void removeActivity(Activity activity) {
        mActivityStacks.remove(activity);
    }

    public static void finishAllActivity() {
        LogUtil.log("finish all activity...");
        for (Activity activity : mActivityStacks) {
            activity.finish();
        }

        mActivityStacks.clear();
    }
}
```

- 在BaseActivity中加入至ActivityStack，或从中移除：
加入
```
ActivityStack.addActivity(this);
```
移除
```
ActivityStack.removeActivity(this);
```

- 退出应用
```
ActivityStack.finishAllActivity();
```

#### 3、使用launch mode实现
创建一个空的Activity，在里面调用`finish()`结束自己。在需要退出应用的地方以`FLAG_ACTIVITY_NEW_TASK`与`FLAG_ACTIVITY_CLEAR_TASK`启动该Activity。

示例代码：

- ExitActivity.java
```
public class ExitActivity extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        LogUtil.log(getClass().getSimpleName() + " onCreate...");
        finish();
    }
}
```

- 退出代码
```
Intent intent = new Intent(this, ExitActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
startActivity(intent);
```

### 4、ActionBar使用

#### 1. 增加Action按钮
- 在Menu.xml文件里增加item

```
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_search"
        android:icon="@drawable/ic_search"
        android:title="@string/action_search"
        app:actionViewClass="android.support.v7.widget.SearchView"
        app:showAsAction="ifRoom|collapseActionView"/>

    <item
        android:id="@+id/action_favourite"
        android:icon="@drawable/ic_favorite_red"
        android:title="@string/favourite"
        app:showAsAction="always"/>

    <item
        android:id="@+id/action_settings"
        android:title="@string/action_settings"
        app:showAsAction="never"/>

</menu>
```

- 重写`onCreateOptionsMenu`方法，在里面加载menu布局
```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main_menu, menu);

    return true;
}
```

#### 2. 响应Action按钮
重写`onOptionsItemSelected`方法，在里面处理相应按钮：
```
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.action_favourite:
            Intent intent = new Intent(MainActivity.this, FavoriteListActivity.class);
            startActivity(intent);
            return true;

        case R.id.action_settings:
            return true;

        default:
            return super.onOptionsItemSelected(item);
    }
}
```

#### 3. 增加返回上一页操作
- `AndroidManifest.xml`文件中，在需要增加的`Activity`的标签里添加`父Activity`
```
<activity
    android:name=".ui.NewsDetailActivity"
    android:parentActivityName=".ui.MainActivity">
</activity>
```
- 在`onCreate`方法里调用`ActionBar.setDisplayHomeAsUpEnabled`方法
```
mActionBar = getSupportActionBar();
if (mActionBar != null) {
    mActionBar.setDisplayHomeAsUpEnabled(true);
}
```

#### 4. 增加Action视图
- 在`<item>`标签里增加下面两个属性中的一个：
-`actionViewClass`：实现action的工具类
-`actionLayout`：描述action的组成的布局
示例：
```
<item
    android:id="@+id/action_search"
    android:icon="@drawable/ic_search"
    android:title="@string/action_search"
    app:actionViewClass="android.support.v7.widget.SearchView"
    app:showAsAction="ifRoom|collapseActionView"/>
```

#### 5. 配置Action视图
在`onCreateOptionsMenu`回调方法里获取`ActionView`并配置
```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main_activity_actions, menu);

    MenuItem searchItem = menu.findItem(R.id.action_search);
    SearchView searchView =
            (SearchView) MenuItemCompat.getActionView(searchItem);

    // Configure the search info and add any event listeners...

    return super.onCreateOptionsMenu(menu);
}
```

#### 6. 响应Action视图的展开与收拢
在`onCreateOptionsMenu`回调方法里，调用`MenuItemCompat.setOnActionExpandListener`设置回调方法
```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.options, menu);
    // ...

    // Define the listener
    OnActionExpandListener expandListener = new OnActionExpandListener() {
        @Override
        public boolean onMenuItemActionCollapse(MenuItem item) {
            // Do something when action item collapses
            return true;  // Return true to collapse action view
        }

        @Override
        public boolean onMenuItemActionExpand(MenuItem item) {
            // Do something when expanded
            return true;  // Return true to expand action view
        }
    };

    // Get the MenuItem for the action item
    MenuItem actionMenuItem = menu.findItem(R.id.myActionItem);

    // Assign the listener to that action item
    MenuItemCompat.setOnActionExpandListener(actionMenuItem, expandListener);

    // Any other things you have to do when creating the options menu…

    return true;
}
```

#### 7. 增加Action数据提供
- 在`menu`中的`<item>`标签里增加`actionProviderClass`属性
```
<item android:id="@+id/action_share"
    android:title="@string/share"
    app:showAsAction="ifRoom"
    app:actionProviderClass="android.support.v7.widget.ShareActionProvider"/>
```
- 在`onCreateOptionsMenu`回调里配置
```
MenuItem shareItem = menu.findItem(R.id.action_share);
ShareActionProvider shareActionProvider = (ShareActionProvider) MenuItemCompat.getActionProvider(shareItem);

Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/*");
intent.putExtra(Intent.EXTRA_STREAM, "Hello");

shareActionProvider.setShareIntent(intent);
```

### 5、保存数据至SQL数据库

#### 1. 定义数据库的相关信息

通常在一个类里定义数据库需要存在数据的相关信息，如`TABLE_NAME`、`COLUMN_NEWS_ID`等。

```
public class NewsEntry implements BaseColumns {
    public static final String TABLE_FAV_NEWS_NAME = "news_fav";
    public static final String TABLE_LATEST_NEWS_NAME = "news_latest";
    public static final String COLUMN_ID = "_id";
    public static final String COLUMN_NEWS_ID = "news_id";
    public static final String COLUMN_NEWS_TITLE = "news_title";
    public static final String COLUMN_NEWS_IMAGE = "news_image";
}
```

#### 2. 使用 SQL 辅助工具创建数据库

`SQLiteOpenHelper` 类中有一组有用的 API。当您使用此类获取对您数据库的引用时，系统将只在需要之时而不是 应用启动过程中执行可能长期运行的操作：创建和更新数据库。 您只需调用 `getWritableDatabase()` 或 `getReadableDatabase()`。

> 注意：由于它们可能长期运行，因此请确保您在后台线程中调用 getWritableDatabase() 或 getReadableDatabase() ， 比如使用 AsyncTask 或 IntentService。


```
public class DailyNewsDBHelper extends SQLiteOpenHelper {
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "daily_news.db";

    private static final String TEXT_TYPE = " TEXT";
    private static final String INTEGER_TYPE = " INTEGER UNIQUE";
    private static final String COMMA_SEP = ",";
    private static final String SQL_CREATE_FAV_NEWS_ENTRIES = "CREATE TABLE " + NewsEntry.TABLE_FAV_NEWS_NAME + " (" +
            NewsEntry.COLUMN_ID + " INTEGER PRIMARY KEY," +
            NewsEntry.COLUMN_NEWS_ID + INTEGER_TYPE + COMMA_SEP +
            NewsEntry.COLUMN_NEWS_TITLE + TEXT_TYPE + COMMA_SEP +
            NewsEntry.COLUMN_NEWS_IMAGE + TEXT_TYPE +
            " )";

    private static final String SQL_CREATE_LATEST_NEWS_ENTRIES = "CREATE TABLE " + NewsEntry.TABLE_LATEST_NEWS_NAME + " (" +
            NewsEntry.COLUMN_ID + " INTEGER PRIMARY KEY," +
            NewsEntry.COLUMN_NEWS_ID + INTEGER_TYPE + COMMA_SEP +
            NewsEntry.COLUMN_NEWS_TITLE + TEXT_TYPE + COMMA_SEP +
            NewsEntry.COLUMN_NEWS_IMAGE + TEXT_TYPE +
            " )";

    private static final String SQL_DELETE_FAV_ENTRIES = "DROP TABLE IF EXISTS " + NewsEntry.TABLE_FAV_NEWS_NAME;
    private static final String SQL_DELETE_LATEST_ENTRIES = "DROP TABLE IF EXISTS " + NewsEntry.TABLE_LATEST_NEWS_NAME;

    public DailyNewsDBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_FAV_NEWS_ENTRIES);
        db.execSQL(SQL_CREATE_LATEST_NEWS_ENTRIES);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL(SQL_DELETE_FAV_ENTRIES);
        db.execSQL(SQL_DELETE_LATEST_ENTRIES);

        onCreate(db);
    }

    @Override
    public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        onUpgrade(db, oldVersion, newVersion);
    }
}
```

#### 3. 将数据插入至数据库
通过将一个 `ContentValues` 对象传递至 `insert()` 方法将数据插入数据库：
```
ContentValues values = new ContentValues();
values.put(NewsEntry.COLUMN_NEWS_ID, news.getId());
values.put(NewsEntry.COLUMN_NEWS_TITLE, news.getTitle());
if (news.getImages().size() > 0) {
    values.put(NewsEntry.COLUMN_NEWS_IMAGE, news.getImages().get(0));
}
mDatabase.insert(NewsEntry.TABLE_FAV_NEWS_NAME, null, values);
```

#### 4. 从数据库读取数据
要从数据库中读取信息，请使用 `query()` 方法，将其传递至选择条件和所需列。该方法结合 `insert()` 和 `update()` 的元素，除非列列表定义了您希望获取的数据，而不是希望插入的数据。 查询的结果将在 `Cursor` 对象中返回给您。

```
cursor = mDatabase.query(NewsEntry.TABLE_FAV_NEWS_NAME, null, null, null, null, null, null);

if (cursor.moveToFirst()) {
    do {
        News news = new News();
        news.setId(cursor.getInt(1));
        news.setTitle(cursor.getString(2));
        String imageUrl = cursor.getString(3);
        ArrayList<String> images = new ArrayList<>();
        images.add(imageUrl);
        news.setImages(images);

        newsList.add(news);
    } while (cursor.moveToNext());
}

cursor.close();
```

完整一点的参考：
```
SQLiteDatabase db = mDbHelper.getReadableDatabase();

// Define a projection that specifies which columns from the database
// you will actually use after this query.
String[] projection = {
    FeedEntry._ID,
    FeedEntry.COLUMN_NAME_TITLE,
    FeedEntry.COLUMN_NAME_UPDATED,
    ...
    };

// How you want the results sorted in the resulting Cursor
String sortOrder =
    FeedEntry.COLUMN_NAME_UPDATED + " DESC";

Cursor c = db.query(
    FeedEntry.TABLE_NAME,  // The table to query
    projection,                               // The columns to return
    selection,                                // The columns for the WHERE clause
    selectionArgs,                            // The values for the WHERE clause
    null,                                     // don't group the rows
    null,                                     // don't filter by row groups
    sortOrder                                 // The sort order
    );
```

### 6、设置文本显示字体
```
Typeface font = Typeface.createFromAsset(getAssets(), "splash.ttf");
textView.setTypeface(font);
```

### 7、简单的倒计时操作
```
new CountDownTimer(30000, 1000) {

   public void onTick(long millisUntilFinished) {
       mTextField.setText("seconds remaining: " + millisUntilFinished / 1000);
   }

   public void onFinish() {
       mTextField.setText("done!");
   }
}.start();
```

> `onTick()`回调是阻塞的。

## 四、Android系统积累

### 1、SystemServer分析

#### 1. SystemServer启动




