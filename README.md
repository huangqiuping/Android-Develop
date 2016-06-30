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
-pstsShouldExpand    ：指示tabs是否等宽

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
1. 
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'

## 二、Android Studio使用

### 1、配置属性
1.加大java编译时的可用内存
在gradle.properties文件里，增加：
```
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m
```

### 2、插件使用
1.GsonFormat
可以将json格式的数据快速转换成java代码
    








