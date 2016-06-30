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
-pstsShouldExpand    ��ָʾtabs�Ƿ�ȿ�

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
1. 
compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'

## ����Android Studioʹ��

### 1����������
1.�Ӵ�java����ʱ�Ŀ����ڴ�
��gradle.properties�ļ�����ӣ�
```
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m
```

### 2�����ʹ��
1.GsonFormat
���Խ�json��ʽ�����ݿ���ת����java����
    








