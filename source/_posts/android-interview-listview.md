---
title: android开发之 ListView优化策略
tags:
  - 安卓架构师面试题
categories:
  - 安卓面试
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2021-08-14 15:30:14
desc:
keywords: 安卓架构师面试题、Android ListView效率提升
---


<!--more-->

# ListView优化

## 1.ListView如何提高效率

​		当convertView为空时，用setTag()方法为每个View绑定一个存放控件的ViewHolder对象。当convertView不为空时，重复利用已经创建的view的时候，使用getTag()方法获取绑定的ViewHolder对象，这样就避免了findViewById对控件的层层查询，二十快速定位到控件。

```java
//在外面先定义，ViewHolder静态类
static class ViewHolder {
    public ImageView img;
    public TextView title;
    public TextView info;
}
//然后重写getView
@Override
public View getView(int position, View convertView, ViewGroup parent) {
    ViewHolder holder;
    if(convertView == null) {
        holder = new ViewHolder();
        convertView = mInflater.inflate(R.layout.list_item, null);
        holder.img = (ImageView)item.findViewById(R.id.img)
        holder.title = (TextView)item.findViewById(R.id.title);
        holder.info = (TextView)item.findViewById(R.id.info);
        convertView.setTag(holder);
    } else {
        holder = (ViewHolder)convertView.getTag();
    }
        holder.img.setImageResource(R.drawable.ic_launcher);
        holder.title.setText("Hello");
        holder.info.setText("World");
    }
                                                                                                
    return convertView;
}
```



1. 复用convertView，使用历史的view，提升效率。
2. 自定义静态类ViewHolder，减少findViewById的次数。使用ViewHolder的好处是缓存了显示数据的试图（View），加快了UI的响应速度。
3. 异步加载数据，分页加载数据
4. 使用WeakReference引用ImageView对象

## 2. ListView如何实现分页加载

 1. 设置Listview的滚动监听器：setOnScrollListener(new OnScrollListener{....}) 在监听器中有两个方法：滚动状态发生变化的方法 onScrollStateChanged() 和ListView被滚动时调用的方法 onScroll()

 2. 在滚动状态发生改变的方法中，有三种状态：

     	1. SCROLL_STATE_TOUGH_SCROLL   //手指按下移动的状态，触摸滑动
          	2. SCROLL_STATE_FLING  //惯性滑动，滑翔
          	3. SCROLL_STATE_IDLE   //静止状态

    对不同的状态进行处理，分批加载数据，只关心静止状态。如果最后一个可见条目就是数据适配器（集合）里的最后一个，此时可加载更多的数据。在每次加载的时候，计算出滚动的数量，当滚动的数量大于总数量的时候，提示用户没有更多数据了。

```java
listView.setOnScrollListener(new AbsListView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(AbsListView absListView, int scrollState) {
                if (scrollState == AbsListView.OnScrollListener.SCROLL_STATE_IDLE) {
                    if (absListView.getLastVisiblePosition() == absListView.getCount() - 1) {
                        loadData();
                    }
                }
            }

            @Override
            public void onScroll(AbsListView absListView, int firstVisibleItem, int visibleItemCount, int totalItemCount) {
                lastItem = firstVisibleItem + visibleItemCount - 1;
            }
        });
```

## 3. 如何在ScrollView中嵌入ListView

​	在ScrollView中添加一个ListView会导致ListView控件显示不全，通常只会显示一条，这是因为两个控件的滚动事件冲突导致。所以需要通过ListView的item数量去计算ListView的显示高度，从而使其完整显示。现阶段最好的解决办法是，自定义ListView，重写onMeasure()方法，设置全部显示：

```java
public class ScrollListView extends ListView {
    public ScrollListView(Context context) {
        super(context);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec, expandSpec);
    }
}
```

## 4.ListView中的图片错位问题是如何产生的？

​	图片错位问题的本质源于我们的ListView中使用了缓存convertView。假设一种场景，一个ListView一屏显示了9个item，那么在加载第十个item的时候，事实上该item是重复使用了第一个item，也就是说在第一个item从网络中下载图片并最终要显示的时候，其实该item已经不在当前显示区域了，此时显示的结果可能在第10个item上输出图像，这就导致了图片错位的问题。所以解决办法在于，可见的显示，不可见的隐藏。

## 5.关于ListView中的图片优化策略

1. 不要直接用图片路径循环BitmapFactory.decodeFile(); 使用Options保存图片大小，不要加载图片到内存中去。
2. 图片对其进行压缩处理。
3. 在ListView中取图片时不要直接拿路径去取，而是使用WeakReference代替强引用（使用WeakReference、SoftReference、WeakHashMap来存储图片信息，保证资源可以即使释放）。
4. getView()中做图片转换时，产生的中间变量及时释放。

**异步加载图片的思想：**

1. 先从内存缓存中获取图片显示（内存缓冲）
2. 获取不到的话从SD卡中获取（SD卡缓冲）
3. 都获取不到的话从网络下载图片并保存到SD卡同时加入内存并显示

**原理：**

1. 先从内存中加载，没有则开启线程中SD卡或者网络中获取，从子线程中获取，否则快速滑动屏幕会感觉到不流畅。
2. 与此同时，在adapter中设置一个busy变量，表示当前ListView是否处于滑动状态，如果是滑动状态则仅从内存中获取图片，否则再开线程去SD卡或者网络获取图片。
3. 图片获取的线程使用线程池管理，避免每次都new一条新的线程；或者使用AsyncTask类，其内部也用到了线程池。从网络获取图片时，先将其保存到SD卡，然后再加载到内存，这样做的好处是加载到内存时可以做下压缩处理，减少图片所占内存。
