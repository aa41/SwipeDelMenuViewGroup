欢迎关注我

CSDN: http://blog.csdn.net/zxt0601

# SwipeDelMenuViewGroup
相关博文：

http://blog.csdn.net/zxt0601/article/details/52303781

【1 序言】
侧滑删除的轮子网上有很多，最初在github上看过一个，还是ListView时代，那是一个自定义ListView 实现侧滑删除的，当初就觉得这种做法不是最佳，万一我项目里又同时有自定义ListView的需求，会增加复杂度。

写这篇文章之前又通过毒度搜了一下，排名前几的CSDN文章，都是通过自定义ListVIew和ViewGropup实现的滑动删除。

况且现在是RecyclerView时代，难不成我要把那些代码再自定义RecyclerView写一遍么。

我想说No，网上大多数的做法代码侵入性太强，尽量不要继承 ListVIew 做什么事，换成 RecyclerView 呢，扩展性太局限了，

本文的做法只要在 Item 的根布局换成这个 自定义ViewGroup 即可，完全不 care 你用 RecyclerView 还是 ListVIew，耦合性为 0


听说隔壁iOS 侧滑删除是一个系统自带的控件，那么我们Android党能否也自定义一个ViewGroup控件，然后一劳永逸，每次简单拿来用就好了呢?

自定义ViewGroup实现侧滑删除简单，难得是还要同时 处理多指滑动的屏蔽，防止两个侧滑菜单同时出现，等等，

有办法将这些东西都用一个ViewGroup搞定么？

看本文如何巧用static类变量来解决这些矛盾冲突。

【2 功能预览】：

CstSwipeDelMenu 无阻塞式交互效果：

![image](https://github.com/mcxtzhang/SwipeDelMenuViewGroup/blob/master/gif/swipeDelete.gif) 

CstIOSSwipeDelMenu 高仿IOS 阻塞式交互效果：

![image](https://github.com/mcxtzhang/SwipeDelMenuViewGroup/blob/master/gif/swipeDelete2.gif) 

包含且不仅包含以下功能

1 侧滑拉出菜单。

2 点击除了这个item的其他位置，菜单关闭。

3 侧滑过程中，不许父控件上下滑动。

4 多指同时滑动，屏蔽后触摸的几根手指。

5 不会同时展开两个侧滑菜单。

6 侧滑菜单时 拦截了长按事件。

7 侧滑时，拦截了点击事件

【3 使用预览】

需要效果一 ：//((CstSwipeDelMenu)holder.getConvertView()).setIos(false);//这句话关掉IOS阻塞式交互效果

需要效果二 ：直接使用，可能大部分公司比较钟爱IOS效果，我忍痛默认IOS


```
<?xml version="1.0" encoding="utf-8"?>
<mcxtzhang.swipedelmenu.view.CstSwipeDelMenu xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clickable="true">

    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:gravity="center"
        android:text="Content布局，项目中替换成你原来的Item布局" />

    <Button
        android:id="@+id/btnDelete"
        android:layout_width="60dp"
        android:layout_height="match_parent"
        android:background="@color/red_ff4a57"
        android:text="删除" />

    <Button
        android:id="@+id/update"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:clickable="true"
        android:text="更新" />

    <RelativeLayout
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:background="@color/red_ff4a57"
        android:clickable="true">

        <TextView
            android:id="@+id/tv_delete"
            android:layout_width="100dp"
            android:layout_height="wrap_content"
            android:layout_centerVertical="true"
            android:drawablePadding="5dp"
            android:drawableTop="@drawable/point_icon_delete"
            android:gravity="center"
            android:text="删除"
            android:textColor="@android:color/white" />
    </RelativeLayout>

</mcxtzhang.swipedelmenu.view.CstSwipeDelMenu>
```

就这么简单，
只需要在 侧滑删除的item的layout的xml里，将父控件换成我们的自定义ViewGroup即可。

第一个子View放置item的内容即可(正式项目里一般是一个ViewGroup)，

从第二个子View开始，是我们的侧滑菜单区域，如我们的demo图，是三个Button。
