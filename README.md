# Androidѧϰ�ʼ� 

��ǩ���ո�ָ����� ��� Android Java

---


[TOC]


## һ����������
### 1��PagerSlidingTabStrip ʹ��
PagerSlidingTabStrip��һ��ҳ��ָʾ�ؼ�����Ҫ���ViewPagerʹ�á�

1.ʹ��Android Studio�������

��build.gradle���dependencies�����
```
compile 'com.astuetz:pagerslidingtabstrip:1.0.1'
```

2.�ڲ����ļ������ViewPager�ϣ�
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

�������ԣ�
- pstsIndicatorColor��tabs�·�ָʾ����ɫ
- pstsDividerColor  ��tabs֮��ķָ�����ɫ
- pstsIndicatorHeight��ָʾ���߶�
- pstsShouldExpand    ��ָʾtabs�Ƿ�ȿ�

3.��`onCreate`�����������Frgemnt�����`onCreateView`���У����ÿؼ�����ViewPager��

```
private PagerSlidingTabStrip mTabs;
private ViewPager mViewPager;
.........
mTabs = (PagerSlidingTabStrip) findViewById(R.id.tabs);
mViewPager = (ViewPager) findViewById(R.id.pager);
mViewPager.setAdapter(new PagerAdapter(getSupportFragmentManager()));

mTabs.setViewPager(mViewPager);
```

### 2��SwipeRefreshLayoutʹ��
SwipeRefreshLayout��supportV4���һ������ˢ�¿ؼ�����Ҫ���ListViewʹ�á�

1.�ڲ����ļ��ʹ��SwipeRefreshLayout����סListView
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

2.�ڴ����������������
```
private SwipeRefreshLayout mSwipeRefreshLayout;
mSwipeRefreshLayout = (SwipeRefreshLayout) view.findViewById(R.id.refresh_layout);
mSwipeRefreshLayout.setOnRefreshListener(this);
mSwipeRefreshLayout.setColorSchemeResources(android.R.color.holo_blue_bright,
                                            android.R.color.holo_orange_light,
                                            android.R.color.holo_red_light,
                                            android.R.color.holo_green_light);

```
- setOnRefreshListener����ˢ�»ص�����Ҫʵ��`onRefresh`����
- setColorSchemeResources����ˢ�¶���ʱ����ɫ

> �Զ�ˢ�£���Ҫʹ�ã�
```
mSwipeRefreshLayout.post(new Runnable() {
    @Override
    public void run() {
        mSwipeRefreshLayout.setRefreshing(true);
        onRefresh();
    }
});
```

### 3��ImageLoaderʹ��
ImageLoader��һ��ǿ���ͼƬ���ؿ⡣

1. ��build.gradle�ļ����������
```
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'
```
2. ��ʼ��ImageLoader

```
ImageLoaderConfiguration config =
		new ImageLoaderConfiguration.Builder(context)
				.denyCacheImageMultipleSizesInMemory()
				.threadPriority(Thread.NORM_PRIORITY - 2)
				.tasksProcessingOrder(QueueProcessingType.LIFO)
				.build();
ImageLoader.getInstance().init(config);
```

- denyCacheImageMultipleSizesInMemory: Ĭ������£�Imageloader���ʹ�ù���ͬһͼƬ�Ĳ�ͬ�ߴ�������ݽ��л��棬���ô˷�����ͬһͼƬֻ����һ���ߴ�Ľ������ݡ�
- threadPriority: ����ͼƬ�����̵߳����ȼ�
- tasksProcessingOrder: ����ͼƬ��������ʾ��˳��Ĭ��Ϊ`FIFO`

3. ����ImageLoader�ļ���ѡ��

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

4. ��������ʾͼƬ

```
mImageLoader.displayImage(images.get(0), holder.newsImage, mOptions);
```

### 4��Gsonʹ��

1. ��build.gradle�ļ����������
```
compile 'com.google.code.gson:gson:2.3.1'
```
2. ����ʹ��

```
Gson gson = new Gson();
Logo logo = gson.fromJson(jsonString, Logo.class);  
```

> ע�⣺Logo����ı�������Ҫ��Json�������һ�£��������ִ���

### 5��DiskLruCacheʹ��
��һ�ο���DiskLruCache������google android��DisplayingBitmapsʾ����������ڻ�����������ص�ͼƬ�ļ�����ʵ����Ҳ����ʹ�������������������ļ���������ܽ���DiskLruCache��ʹ�ã�

#### 1. ����[DiskLruCache.java](https://github.com/googlesamples/android-DisplayingBitmaps/blob/master/Application/src/main/java/com/example/android/displayingbitmaps/util/DiskLruCache.java)�ļ���

#### 2. �򿪴��̻���
```
public static DiskLruCache open(File directory, int appVersion, int valueCount, long maxSize)
```

- directory��ָ������·��
- appVersion��ָ��Ӧ�ð汾������汾�ű仯������Ŀ¼�ᱻ���
Ӧ�ð汾����ͨ���������õ���
```
PackageInfo info = context.getPackageManager().getPackageInfo(context.getPackageName(), 0);  
int version = info.versionCode;
```
- valueCount��ָ��������Ŀ����һ��key��Ӧ���ٸ������ļ���һ������Ϊ1
- maxSize��ָ��������ܴ�С

#### 3. ���ļ���ӵ�����

1. ͨ��`DiskLruCache.edit()`����һ��`DiskLruCache.Editor`����
2. ͨ��`Editor.newOutputStream()`����һ�������
3. ����Ҫ���������д����һ��������������С�
4. ��һ���ɹ������`Editor.commit()`�ύ���ݣ����ʧ�������`Editor.abort()`���������ύ
5. ����`DiskLurCache.flush()`ǿ�ƽ�����ˢ����ϵͳ��

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

Editor������Ҫһ������key�����key����������Ϊ�����ļ����ļ�����ֱ��ʹ��url��̫���ʣ��Ƽ���url����md5ת�����ο��������£�

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

#### 4. �ӻ������ȡ�ļ�

1. ͨ��`DiskLruCache.get()`�õ�һ��`DiskLruCache.Snapshot`����
2. ͨ��`Snapshot.getInputStream()`�õ�һ��������
3. ������õ�����������ȡ���ݼ��ɡ�

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

#### 5. �ӻ������Ƴ��ļ�
```
public synchronized boolean remove(String key) throws IOException  
```
һ������£����������ֶ�ȥ�Ƴ�ĳ���ļ����������С�ﵽopenʱ�趨�����ֵʱ��DiskLruCache���Զ��Ƴ�����Ļ��档����������ȷ��ĳ��key��Ӧ�������Ѿ���ʱ������Ҫ����ʱ�������ֶ��Ƴ���

#### 6. ��ȡ��ǰռ�û����С
```
public synchronized long size() 
```
��ȡ��ǰ�����С�������ʱ��һ����̨ɾ���������ڽ��У����ֵ���ܻ���趨�����ֵ��

#### 7. ��ջ���
```
public void delete() throws IOException
```
�رղ�ɾ�����л��棬�����������ɾ������Ŀ¼�µ������ļ����������ǻ��洴�����ļ���

#### 8. journal����
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

journal�ļ�ǰ5�зֱ�Ϊ�ַ�������"libcore.io.DiskLruCache"������汾��Ӧ�ó���汾�����������հ��С�
������ÿһ�ж���һ������ļ�¼�ÿ����¼���ֵͨ���ո�ֿ����ֱ��ʾ״̬����ֵ�Լ��ض���״ֵ̬��

- DIRTY�б�ʾ�ü�¼�����ڱ��������и��¡�
ÿһ��ɹ���DIRTY��������������CLEAN��REMOVE������
DIRTY�к���û��ƥ��CLEAN��REMOVE��˵����ʱ�ļ���Ҫ��ɾ����

- CLEAN��ָ��һ���������Ѿ��ɹ����������߱���ȡ��
һ���Ѿ��������к�����ŵ���ÿһ��ֵ�ĳ��ȡ�
- READ��ָ����LRU�ɷ��ʵĻ����
- REMOVE��ָ�����Ѿ���ɾ���Ļ����

journal�ļ�ż���ᱻѹ����������������С���ʱ�ļ���journal.tmp����ѹ��ʱ���ᱻʹ�ã�������汻�򿪵Ļ�����������ھ�Ӧ�ð���ɾ������

#### 9. С��
- DiskLruCacheʹ���������ڲ��࣬һ����DiskLruCache.Editor����д�뻺����Ϣ��һ����DiskLruCache.Snapshot������ȡ�����ļ���
- �����ļ���ΪCLEAN�ļ���DIRTY�ļ���CLEAN�ļ�Ϊ������ɵ��ļ���DIRTY�ļ�������ʱ��������޸ġ�
- journal�ļ���¼����Щ�ؼ����Ѿ������桢��Щ�����޸ġ���Щ���ڶ�ȡ��
- ���������ͬLruCacheһ��ʹ��LinkedHashMap���ܹ�ֱ�ӵ���LinkedHashMap.eldest()�����������δ��ʹ�õ��ʡȥ��LRU�㷨��ʵ�֡�
- ����ռ��Ѿ���дjournal�����������̳߳ص��߳��첽����һ�Ƿ�ֹ�����߳��������⣬���ǽ����ͬ�����⡣

### 6��

## ����Android Studioʹ��

### 1����������

#### 1. �Ӵ�java����ʱ�Ŀ����ڴ�

��gradle.properties�ļ�����ӣ�
```
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m
```

#### 2. ���ñ����Զ����ɱ���
```
buildTypes {
    release {
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    debug {
//            buildConfigField "boolean", "DEBUG", "true" //�Ƿ�Ϊ���԰汾
        buildConfigField "String", "EXIT_TYPE", "\"stack\""
    }
}
```

### 2�����ʹ��
#### 1. GsonFormat
���Խ�json��ʽ�����ݿ���ת����java����

#### 2. ButterKnifeʹ��
ButterKnife��һ�����԰�ָ���Ĳ����ļ���ʹ��ע�ⷽʽ������Ӧ�ؼ����롣
- ��װ`Android ButterKnife Zelezny`
- ��`build.gradle`�ļ�����ӱ�������
```
compile 'com.jakewharton:butterknife:6.1.0'
```
- ������ƶ��������ļ����ϣ�ѡ��`Generate`->`Generate ButterKnife Injections`��ѡ����Ҫ���ɿ��ƴ���Ŀؼ����Ϳ����ˡ�
- ��`onCreate()`���������
```
ButterKnife.inject(this);
```


## ����AndroidӦ�û���
### 1��Android assetsĿ¼ʹ��
�����assetsĿ¼�µ�ԭ����Դ����ϵͳ����ʱ�����������б��룬���ԣ����ǲ���ʹ��R.assets������ʽ�����ʣ�ͬʱ��apk��װ��assetsĿ¼Ҳ�����ѹ��apk��װ��Ŀ¼�����Ǳ�apk����һ��ģ����ԣ��޷�ֱ�ӻ�ȡassetsĿ¼�ľ���·����AndroidΪ���ṩ��`AssetManager`�����ࡣ�����ṩһЩ�ͼ���api����������һ���򵥵�����������ʽ���򿪺Ͷ�ȡ��Ӧ�ð󶨵�raw�ļ���

1. ��ȡ`AssetManager`
����`getAssets()`������ȡ

2. ���÷���
- final String[] list(String path)
����ָ��·�����µ������ļ���Ŀ¼��
- final InputStream open(String fileName)
ʹ��ACCESS_STREAMMING��assets��ָ�����ļ�
- final InputStream open(String fileName, int accessMode)
ʹ����ʾ �ķ���ģʽ��assets��ָ�����ļ�

3. ����assetsĿ¼�µ���ҳ��
mWebView.loadUrl("file:///android_asset/http/index.html");
> ���ַ�ʽ���Լ���assetsĿ¼�µ���ҳ����������ҳ��ص�css, js, ͼƬ���ļ�Ҳ��һ����ء�

### 2�� AndroidӦ��ȫ������
��`styles.xml��������ʽ��

```
<style name="NoTitleFullscreen" parent="Theme.AppCompat.NoActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
</style>
```

### 3���˳�apk�ļ��ַ�ʽ

#### 1. ʹ�ù㲥��ʽ
���Դ���һ��BaseActivity��������ע����մ���һ��`�˳��㲥`�������е���`finish()`������finish��Activity������Activity���̳д�BaseActivity������Ҫ�˳�Ӧ�õ�ʱ�򣬷���`�˳��㲥`������finishȫ��Activity�������˳�Ӧ�á�
ʾ���������£�

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

- ����Activity

```
Intent intent = new Intent();
intent.setAction(BaseActivity.EXIT_ACTION);
sendBroadcast(intent);
```

#### 2. ��������Activity��List
����������Activity���ӵ�List�������Ҫ�˳�Ӧ��ʱ�����Դ�List��ȡ�����е�Activity������`finish()`������

ʾ�����룺

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

- ��BaseActivity�м�����ActivityStack��������Ƴ���
����
```
ActivityStack.addActivity(this);
```
�Ƴ�
```
ActivityStack.removeActivity(this);
```

- �˳�Ӧ��
```
ActivityStack.finishAllActivity();
```

#### 3��ʹ��launch modeʵ��
����һ���յ�Activity�����������`finish()`�����Լ�������Ҫ�˳�Ӧ�õĵط���`FLAG_ACTIVITY_NEW_TASK`��`FLAG_ACTIVITY_CLEAR_TASK`������Activity��

ʾ�����룺

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

- �˳�����
```
Intent intent = new Intent(this, ExitActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
startActivity(intent);
```

### 4��ActionBarʹ��

#### 1. ����Action��ť
- ��Menu.xml�ļ�������item

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

- ��д`onCreateOptionsMenu`���������������menu����
```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main_menu, menu);

    return true;
}
```

#### 2. ��ӦAction��ť
��д`onOptionsItemSelected`�����������洦����Ӧ��ť��
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

#### 3. ���ӷ�����һҳ����
- `AndroidManifest.xml`�ļ��У�����Ҫ���ӵ�`Activity`�ı�ǩ�����`��Activity`
```
<activity
    android:name=".ui.NewsDetailActivity"
    android:parentActivityName=".ui.MainActivity">
</activity>
```
- ��`onCreate`���������`ActionBar.setDisplayHomeAsUpEnabled`����
```
mActionBar = getSupportActionBar();
if (mActionBar != null) {
    mActionBar.setDisplayHomeAsUpEnabled(true);
}
```

#### 4. ����Action��ͼ
- ��`<item>`��ǩ�������������������е�һ����
-`actionViewClass`��ʵ��action�Ĺ�����
-`actionLayout`������action����ɵĲ���
ʾ����
```
<item
    android:id="@+id/action_search"
    android:icon="@drawable/ic_search"
    android:title="@string/action_search"
    app:actionViewClass="android.support.v7.widget.SearchView"
    app:showAsAction="ifRoom|collapseActionView"/>
```

#### 5. ����Action��ͼ
��`onCreateOptionsMenu`�ص��������ȡ`ActionView`������
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

#### 6. ��ӦAction��ͼ��չ������£
��`onCreateOptionsMenu`�ص����������`MenuItemCompat.setOnActionExpandListener`���ûص�����
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

    // Any other things you have to do when creating the options menu��

    return true;
}
```

#### 7. ����Action�����ṩ
- ��`menu`�е�`<item>`��ǩ������`actionProviderClass`����
```
<item android:id="@+id/action_share"
    android:title="@string/share"
    app:showAsAction="ifRoom"
    app:actionProviderClass="android.support.v7.widget.ShareActionProvider"/>
```
- ��`onCreateOptionsMenu`�ص�������
```
MenuItem shareItem = menu.findItem(R.id.action_share);
ShareActionProvider shareActionProvider = (ShareActionProvider) MenuItemCompat.getActionProvider(shareItem);

Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/*");
intent.putExtra(Intent.EXTRA_STREAM, "Hello");

shareActionProvider.setShareIntent(intent);
```

### 5������������SQL���ݿ�

#### 1. �������ݿ�������Ϣ

ͨ����һ�����ﶨ�����ݿ���Ҫ�������ݵ������Ϣ����`TABLE_NAME`��`COLUMN_NEWS_ID`�ȡ�

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

#### 2. ʹ�� SQL �������ߴ������ݿ�

`SQLiteOpenHelper` ������һ�����õ� API������ʹ�ô����ȡ�������ݿ������ʱ��ϵͳ��ֻ����Ҫ֮ʱ������ Ӧ������������ִ�п��ܳ������еĲ����������͸������ݿ⡣ ��ֻ����� `getWritableDatabase()` �� `getReadableDatabase()`��

> ע�⣺�������ǿ��ܳ������У������ȷ�����ں�̨�߳��е��� getWritableDatabase() �� getReadableDatabase() �� ����ʹ�� AsyncTask �� IntentService��


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

#### 3. �����ݲ��������ݿ�
ͨ����һ�� `ContentValues` ���󴫵��� `insert()` ���������ݲ������ݿ⣺
```
ContentValues values = new ContentValues();
values.put(NewsEntry.COLUMN_NEWS_ID, news.getId());
values.put(NewsEntry.COLUMN_NEWS_TITLE, news.getTitle());
if (news.getImages().size() > 0) {
    values.put(NewsEntry.COLUMN_NEWS_IMAGE, news.getImages().get(0));
}
mDatabase.insert(NewsEntry.TABLE_FAV_NEWS_NAME, null, values);
```

#### 4. �����ݿ��ȡ����
Ҫ�����ݿ��ж�ȡ��Ϣ����ʹ�� `query()` ���������䴫����ѡ�������������С��÷������ `insert()` �� `update()` ��Ԫ�أ��������б�������ϣ����ȡ�����ݣ�������ϣ����������ݡ� ��ѯ�Ľ������ `Cursor` �����з��ظ�����

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

����һ��Ĳο���
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

### 6�������ı���ʾ����
```
Typeface font = Typeface.createFromAsset(getAssets(), "splash.ttf");
textView.setTypeface(font);
```

### 7���򵥵ĵ���ʱ����
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

> `onTick()`�ص��������ġ�

## �ġ�Androidϵͳ����

### 1��SystemServer����

#### 1. SystemServer����




