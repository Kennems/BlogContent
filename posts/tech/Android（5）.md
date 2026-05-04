---
title : 'Android（5）'
date : 2024-06-29T22:30:13+08:00
lastmod: 2024-06-29T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode

---

# Android（5）

## 高级控件

### SpinnerDialog + ArrayAdapter

![spinnerDialog](https://cdn.jsdelivr.net/gh/kennems/blog-image/spinnerDialog.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".SpinnerDropdownActivity"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="下拉模式的列表框"
        android:textSize="17sp"/>

    <Spinner
        android:id="@id/sp_dialog"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:spinnerMode="dialog"/>

</LinearLayout>
```

### item_select.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:gravity="center"
    android:textColor="#0000ff"
    android:textSize="17sp"
    tools:text="火星">

</TextView>


```

### Java

```java
package com.showguan.chapter08;

import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Spinner;

import androidx.appcompat.app.AppCompatActivity;

import com.showguan.chapter08.util.ToastUtil;


public class SpinnerDialogActivity extends AppCompatActivity implements AdapterView.OnItemSelectedListener {

    private Spinner sp_dialog;
    private static final String[]  starArray = new String[]{
            "水星", "金星", "地球", "火星", "木星", "土星", "天王星", "海王星"
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_spinner_dialog);
        sp_dialog = findViewById(R.id.sp_dialog);
        ArrayAdapter<String> starAdapter = new ArrayAdapter<>(this, R.layout.item_select,  starArray);

        sp_dialog.setPrompt("🌤选择行星");
        sp_dialog.setAdapter(starAdapter);
        sp_dialog.setSelection(0);
        sp_dialog.setOnItemSelectedListener(this);
    }



    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        ToastUtil.show(this, "您选择了： " + starArray[position]);
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {

    }
}
```

### SpinnerDropdown

![spinnerDropdown](https://cdn.jsdelivr.net/gh/kennems/blog-image/spinnerDropdown.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".SpinnerDropdownActivity"
    android:orientation="vertical">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="下拉模式的列表框"
        android:textSize="17sp"/>

    <Spinner
        android:id="@id/sp_dropdown"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:spinnerMode="dropdown"/>

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08;

import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Spinner;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.showguan.chapter08.util.ToastUtil;

public class SpinnerDropdownActivity extends AppCompatActivity implements AdapterView.OnItemSelectedListener {

    private Spinner sp_dropdown;
    private static final String[]  starArray = new String[]{
            "水星", "金星", "地球", "火星", "木星", "土星", "天王星", "海王星"
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_spinner_dropdown);
        sp_dropdown = findViewById(R.id.sp_dropdown);
        ArrayAdapter<String> starAdapter = new ArrayAdapter<>(this, R.layout.item_select,  starArray);

        sp_dropdown.setAdapter(starAdapter);
        sp_dropdown.setSelection(0);
        sp_dropdown.setOnItemSelectedListener(this);

    }

    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        ToastUtil.show(this, "您选择了： " + starArray[position]);
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {

    }
}
```

### SimpleAdapter简单适配器

![SpinerIcon](https://cdn.jsdelivr.net/gh/kennems/blog-image/SpinerIcon.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".SpinnerDropdownActivity">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="行星的简单适配器"
        android:textSize="17sp" />

    <Spinner
        android:id="@id/sp_icon"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:spinnerMode="dropdown" />

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08; // 包名

public class SpinnerIconActivity extends AppCompatActivity implements AdapterView.OnItemSelectedListener {

    // 定义一个整型数组，存储星球图标的资源ID
    private static final int[] iconArray = {
            R.drawable.shuixing, R.drawable.jinxing, R.drawable.diqiu,
            R.drawable.huoxing, R.drawable.muxing, R.drawable.tuxing,
    };

    // 定义一个字符串数组，存储星球的名称
    private static final String[] starArray = new String[]{
            "水星", "金星", "地球", "火星", "木星", "土星", "天王星", "海王星"
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) { // onCreate方法，活动启动时调用
        super.onCreate(savedInstanceState); // 调用父类的onCreate方法
        setContentView(R.layout.activity_spinner_icon); // 设置活动的布局文件
        List<Map<String, Object>> list = new ArrayList<>(); // 创建一个列表，用于存储图标和名称

        for (int i = 0; i < iconArray.length; i++) { // 遍历图标数组
            Map<String, Object> item = new HashMap<>(); // 创建一个HashMap对象
            item.put("icon", iconArray[i]); // 将图标资源ID放入Map中
            item.put("name", starArray[i]); // 将名称放入Map中
            list.add(item); // 将Map添加到列表中
        }

        // 创建一个SimpleAdapter，连接数据和布局
        SimpleAdapter startAdapter = new SimpleAdapter(this,
                list, // 数据源
                R.layout.item_simple, // 列表项布局
                new String[]{"icon", "name"}, // Map中的键
                new int[]{R.id.iv_icon, R.id.tv_name}); // 布局中的视图ID

        Spinner sp_icon = findViewById(R.id.sp_icon); // 获取Spinner控件
        sp_icon.setAdapter(startAdapter); // 为Spinner设置适配器
        sp_icon.setSelection(0); // 默认选中第0项
        sp_icon.setOnItemSelectedListener(this); // 设置选项选中监听器
    }

    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) { // 当选择一个选项时调用
        ToastUtil.show(this, "你选择的是：" + starArray[position]); // 显示选择的星球名称
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) { // 当没有选项被选择时调用

    }
}
```

## BaseAdapter基本适配器

![SpinnerBaseAdapter](https://cdn.jsdelivr.net/gh/kennems/blog-image/SpinnerBaseAdapter.gif)

BaseAdapter是一个抽象类，所以需要重写

### PlanetBaseAdapter.java

```java
package com.showguan.chapter08.adapter; // 包名

import android.content.Context; // 导入Context类
import android.view.LayoutInflater; // 导入LayoutInflater类
import android.view.View; // 导入View类
import android.view.ViewGroup; // 导入ViewGroup类
import android.widget.BaseAdapter; // 导入BaseAdapter类
import android.widget.ImageView; // 导入ImageView类
import android.widget.TextView; // 导入TextView类

import com.showguan.chapter08.R; // 导入项目的R类
import com.showguan.chapter08.bean.Planet; // 导入自定义的Planet类

import java.util.List; // 导入List接口

public class PlanetBaseAdapter extends BaseAdapter { // 定义PlanetBaseAdapter类，继承自BaseAdapter

    private Context mContext; // 上下文对象
    private List<Planet> mPlanetList; // 存储Planet对象的列表

    // 构造方法，初始化上下文和列表
    public PlanetBaseAdapter(Context mContext, List<Planet> mPlanetList) {
        this.mContext = mContext; // 初始化上下文
        this.mPlanetList = mPlanetList; // 初始化星球列表
    }

    @Override
    public int getCount() { // 返回列表项的数量
        return mPlanetList.size(); // 返回星球列表的大小
    }

    @Override
    public Object getItem(int position) { // 获取指定位置的列表项
        return mPlanetList.get(position); // 返回指定位置的Planet对象
    }

    @Override
    public long getItemId(int position) { // 获取指定位置项的ID
        return position; // 返回位置作为项的ID
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) { // 获取每个列表项的视图
        // 根据布局文件item_base.xml生成转换视图对象
        View view = LayoutInflater.from(mContext).inflate(R.layout.item_base, null);
        ImageView iv_icon = view.findViewById(R.id.iv_icon); // 获取图标视图
        TextView tv_name = view.findViewById(R.id.tv_name); // 获取名称视图
        TextView tv_desc = view.findViewById(R.id.tv_desc); // 获取描述视图

        // 给控件设置数据
        Planet planet = mPlanetList.get(position); // 获取当前星球对象
        iv_icon.setImageResource(planet.image); // 设置图标资源
        tv_name.setText(planet.name); // 设置星球名称
        tv_desc.setText(planet.desc); // 设置星球描述

        return view; // 返回生成的视图
    }
}
```

### item_base.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal">

    <ImageView
        android:id="@+id/iv_icon"
        android:layout_width="0dp"
        android:layout_height="60dp"
        android:layout_weight="1"
        tools:src="@drawable/diqiu" />

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="3"
        android:orientation="vertical">

        <TextView
            android:id="@+id/tv_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="地球"
            android:textColor="@color/black"
            android:textSize="20sp" />

        <TextView
            android:id="@+id/tv_desc"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:text="地球"
            android:textColor="@color/black"
            android:textSize="12sp"
            tools:text="地球快一点毁灭吧！地球快一点毁灭吧！地球快一点毁灭吧！地球快一点毁灭吧！地球快一点毁灭吧！地球快一点毁灭吧！"/>

    </LinearLayout>


</LinearLayout>
```

### BaseAdapterActivity.java

```java
package com.showguan.chapter08; // 包名

public class BaseAdapterActivity extends AppCompatActivity implements AdapterView.OnItemSelectedListener { // 定义活动类，实现选项选择监听器

    private static final String[] starArray = new String[]{ // 定义星球名称数组
            "水星", "金星", "地球", "火星", "木星", "土星", "天王星", "海王星"
    };
    private List<Planet> planetList; // 存储Planet对象的列表

    @Override
    protected void onCreate(Bundle savedInstanceState) { // 活动创建时调用
        super.onCreate(savedInstanceState); // 调用父类的onCreate方法
        setContentView(R.layout.activity_base_adapter); // 设置活动的布局文件
        Spinner sp_planet = findViewById(R.id.sp_planet); // 获取Spinner控件
        planetList = Planet.getDefaultList(); // 获取默认的星球列表
        PlanetBaseAdapter adapter = new PlanetBaseAdapter(this, planetList); // 创建适配器
        sp_planet.setAdapter(adapter); // 为Spinner设置适配器
        sp_planet.setSelection(0); // 默认选中第0项
        sp_planet.setOnItemSelectedListener(this); // 设置选项选中监听器
    }

    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) { // 当选择一个选项时调用
        ToastUtil.show(this, "您选择的是" + planetList.get(position).name); // 显示选择的星球名称
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) { // 当没有选项被选择时调用

    }
}

```

## BaseAdapter优化

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) { // 获取每个列表项的视图
        ViewHolder holder = null;
        if (convertView == null) {
            convertView = LayoutInflater.from(mContext).inflate(R.layout.item_base, null);
            holder = new ViewHolder();
            holder.iv_icon = convertView.findViewById(R.id.iv_icon); // 获取图标视图
            holder.tv_name = convertView.findViewById(R.id.tv_name); // 获取名称视图
            holder.tv_desc = convertView.findViewById(R.id.tv_desc); // 获取描述视图
            // 将视图持有者保存到转换视图当中
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }
        Planet planet = mPlanetList.get(position);
        holder.iv_icon.setImageResource(planet.image);
        holder.tv_name.setText(planet.name);
        holder.tv_desc.setText(planet.desc);
        return convertView;
}
```

## 列表视图ListView

![ListView](https://cdn.jsdelivr.net/gh/kennems/blog-image/ListView.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".ListViewActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <CheckBox
            android:id="@+id/ck_divider"
            android:layout_width="0dp"
            android:layout_height="40dp"
            android:layout_weight="1"
            android:gravity="start|center"
            android:text="显示分割线"
            android:textSize="17sp" />

        <CheckBox
            android:id="@+id/ck_bgc"
            android:layout_width="0dp"
            android:layout_height="40dp"
            android:layout_weight="1"
            android:gravity="start|center"
            android:text="显示按压背景"
            android:textSize="17sp" />

    </LinearLayout>


    <ListView
        android:id="@+id/lv_planet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:divider="@null"
        android:dividerHeight="0dp"
        android:listSelector="@color/transparent" />

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08;

public class ListViewActivity extends AppCompatActivity implements AdapterView.OnItemClickListener, View.OnClickListener, CompoundButton.OnCheckedChangeListener {

    // 星球列表
    private List<Planet> planetList;
    // 分割线复选框
    private CheckBox ck_divider;
    // 背景色复选框
    private CheckBox ck_bgc;
    // 列表视图
    private ListView lv_planet;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_view);
        
        // 初始化列表视图
        lv_planet = findViewById(R.id.lv_planet);
        
        // 初始化分割线复选框并设置监听器
        ck_divider = findViewById(R.id.ck_divider);
        ck_divider.setOnCheckedChangeListener(this);
        
        // 初始化背景色复选框并设置监听器
        ck_bgc = findViewById(R.id.ck_bgc);
        ck_bgc.setOnCheckedChangeListener(this);

        // 获取默认星球列表
        planetList = Planet.getDefaultList();
        
        // 创建适配器并设置给列表视图
        PlanetBaseAdapter adapter = new PlanetBaseAdapter(this, planetList);
        lv_planet.setAdapter(adapter);
        
        // 设置列表项点击监听器
        lv_planet.setOnItemClickListener(this);
    }

    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        // 当列表项被点击时，显示对应星球名称的 Toast 消息
        ToastUtil.show(this, "您选择的是" + planetList.get(position).name);
    }

    @Override
    public void onClick(View v) {
        // 空实现，暂时没有需要处理的点击事件
    }

    @Override
    public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
        // 当复选框状态改变时调用
        if (buttonView.getId() == R.id.ck_divider) {
            // 处理分割线复选框的状态改变
            if (ck_divider.isChecked()) {
                // 如果复选框被选中，设置黑色分割线和高度
                Drawable black = getResources().getDrawable(R.color.black, getTheme());
                lv_planet.setDivider(black);
                lv_planet.setDividerHeight(Utils.dip2px(this, 1));
            } else {
                // 如果复选框未选中，移除分割线
                lv_planet.setDivider(null);
                lv_planet.setDividerHeight(Utils.dip2px(this, 0));
            }
        } else if (buttonView.getId() == R.id.ck_bgc) {
            // 处理背景色复选框的状态改变
            if (ck_bgc.isChecked()) {
                // 如果复选框被选中，设置列表项的选择器背景
                lv_planet.setSelector(R.drawable.list_selector);
            } else {
                // 如果复选框未选中，设置透明背景
                Drawable transparent = getResources().getDrawable(R.color.transparent, getTheme());
                lv_planet.setSelector(transparent);
            }
        }
    }
}

```

## ListView注意点

在ListView中添加按钮并设置监听时， 会导致原来的ListView中`onItemClick`无效，此时需要在XML文件中设置`android:descendantFocusability="blocksDescendants`才能同时生效。

![ListFocus](https://cdn.jsdelivr.net/gh/kennems/blog-image/ListFocus.gif)

![image-20240630191703768](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240630191703768.png)

```xml
    android:descendantFocusability="blocksDescendants"
```

## 网格视图GridView

![GridView](https://cdn.jsdelivr.net/gh/kennems/blog-image/GridView.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <GridView
        android:id="@+id/gv_planet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:numColumns="2"
        android:background="@color/pink"
        android:columnWidth="100dp"
        android:verticalSpacing="10dp"
        android:stretchMode="none"/>

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08;

import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.GridView;

import androidx.appcompat.app.AppCompatActivity;

import com.showguan.chapter08.adapter.PlanetGridAdapter;
import com.showguan.chapter08.entity.Planet;
import com.showguan.chapter08.util.ToastUtil;

import java.util.List;

// GridViewActivity 类实现了 AdapterView.OnItemClickListener 接口
public class GridViewActivity extends AppCompatActivity implements AdapterView.OnItemClickListener {

    // 声明一个 Planet 对象的 List，用于存储行星数据
    private List<Planet> planetList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置活动的布局文件
        setContentView(R.layout.activity_grid_view);

        // 获取 GridView 控件
        GridView gv_planet = findViewById(R.id.gv_planet);
        // 获取默认的行星列表
        planetList = Planet.getDefaultList();
        // 创建一个适配器，将行星列表传递给适配器
        PlanetGridAdapter adapter = new PlanetGridAdapter(this, planetList);
        // 设置 GridView 的适配器
        gv_planet.setAdapter(adapter);
        // 设置 GridView 的 item 点击事件监听器
        gv_planet.setOnItemClickListener(this);
        // 设置 GridView 的拉伸模式
        gv_planet.setStretchMode(GridView.STRETCH_COLUMN_WIDTH);
    }

    // 当 GridView 的 item 被点击时调用此方法
    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        // 显示一个 Toast，提示用户选择了哪个行星
        ToastUtil.show(this, "你选择了" + planetList.get(position).name);
    }
}
```

## 使用GridView重构购物车

![image-20240630190848784](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240630190848784.png)

### XML

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/AntiqueWhite1"
    android:orientation="vertical">

    <include layout="@layout/title_shopping" />

    <GridView
        android:id="@+id/gv_channel"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:numColumns="2" />

</LinearLayout>
```

### Java

#### GoodsGridAdapter.java

```java
package com.showguan.chapter08.adapter;

import android.content.Context;
import android.net.Uri;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;

import com.showguan.chapter08.R;
import com.showguan.chapter08.ShoppingChannelActivity;
import com.showguan.chapter08.entity.GoodsInfo;

import java.util.List;

public class GoodsGridAdapter extends BaseAdapter {

    public Context mContext;
    public List<GoodsInfo> mGoodsInfo;
    private AddCartListener mAddCartListener;


    public GoodsGridAdapter(Context mContext, List<GoodsInfo> mGoodsInfo, AddCartListener addCartListener) {
        this.mContext = mContext;
        this.mGoodsInfo = mGoodsInfo;
        this.mAddCartListener = addCartListener;
    }

    @Override
    public int getCount() {
        return mGoodsInfo.size();
    }

    @Override
    public Object getItem(int position) {
        return mGoodsInfo.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        GoodsInfo goodsInfo = mGoodsInfo.get(position);
        ViewHolder holder;
        if(convertView == null){
            holder = new ViewHolder();
            // 将 XML 布局文件扩展为相应的 View 对象的类
            convertView = LayoutInflater.from(mContext).inflate(R.layout.item_goods, null);
            holder.iv_thumb = convertView.findViewById(R.id.iv_thumb);
            holder.tv_name = convertView.findViewById(R.id.tv_name);
            holder.tv_price = convertView.findViewById(R.id.tv_price);
            holder.btn_add = convertView.findViewById(R.id.btn_add);
            convertView.setTag(holder);
        }else{
            holder = (ViewHolder) convertView.getTag();
        }
        GoodsInfo info = mGoodsInfo.get(position);

        holder.iv_thumb.setImageURI(Uri.parse(info.picPath));
        holder.tv_name.setText(info.name);
        holder.tv_price.setText(String.valueOf(info.price));
        holder.btn_add.setOnClickListener(v -> {
//            ShoppingChannelActivity activity = (ShoppingChannelActivity) mContext;
//            activity.addToCart(info.id, info.name);
            mAddCartListener.addToCart(info.id, info.name);
        });


        return convertView;
    }
    public final class ViewHolder{
        public ImageView iv_thumb;
        public TextView tv_name;
        public TextView tv_price;
        public Button btn_add;
    }


    public interface AddCartListener{
        abstract void addToCart(int goodsId, String goodsName);
    }
}
```

#### ShoppingChannelActivity.java

```java
package com.showguan.chapter08;

// ShoppingChannelActivity 类实现了 GoodsGridAdapter.AddCartListener 和 View.OnClickListener 接口
public class ShoppingChannelActivity extends AppCompatActivity implements GoodsGridAdapter.AddCartListener, View.OnClickListener {
    private static String TAG = "Kennem"; // 日志标签

    private ShoppingDBHelper mDBHelper; // 数据库助手对象
    private TextView tv_title; // 显示标题的 TextView
    private GridView gv_channel; // 显示商品的 GridView
    private TextView tv_count; // 显示购物车商品数量的 TextView

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置活动的布局文件
        setContentView(R.layout.activity_shopping_channel);

        // 初始化控件
        tv_title = findViewById(R.id.tv_title);
        tv_title.setText("手机商城");

        tv_count = findViewById(R.id.tv_count);
        gv_channel = findViewById(R.id.gv_channel);

        // 设置点击事件监听器
        findViewById(R.id.iv_back).setOnClickListener(this);
        findViewById(R.id.iv_cart).setOnClickListener(this);

        // 初始化数据库助手并打开读写链接
        mDBHelper = ShoppingDBHelper.getInstance(this);
        mDBHelper.openWriteLink();
        mDBHelper.openReadLink();

        // 显示商品信息
        showGoods();
    }

    @Override
    protected void onResume() {
        super.onResume();
        // 在活动恢复时，显示购物车的总信息
        showCartTotalInfo();
    }

    // 显示购物车总信息的方法
    private void showCartTotalInfo() {
        int count = mDBHelper.countCartInfo();
        MyApplication.getInstance().goodsCount = count;
        tv_count.setText(String.valueOf(count));
    }

    // 显示商品信息的方法
    private void showGoods() {
        List<GoodsInfo> list = mDBHelper.queryAllGoods();
        GoodsGridAdapter adapter = new GoodsGridAdapter(this, list, this);
        gv_channel.setAdapter(adapter);
    }

    // 添加商品到购物车的方法
    @Override
    public void addToCart(int goodsId, String name) {
        mDBHelper.insertCartInfo(goodsId);
        int count = ++MyApplication.getInstance().goodsCount;
        tv_count.setText(String.valueOf(count));
        ToastUtil.show(this, "已添加一部 " + name + "到购物车成功！");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // 在活动销毁时，关闭数据库链接
        mDBHelper.closeLink();
    }

    // 处理点击事件的方法
    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.iv_back) {
            // 点击返回按钮时，结束当前活动
            finish();
        } else if (v.getId() == R.id.iv_cart) {
            // 点击购物车按钮时，启动购物车活动
            Intent intent = new Intent(this, ShoppingCartActivity.class);
            intent.setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
            startActivity(intent);
        }
    }

    @Override
    public void onPointerCaptureChanged(boolean hasCapture) {
        super.onPointerCaptureChanged(hasCapture);
    }
}
```

## ViewPager

![ViewPager](https://cdn.jsdelivr.net/gh/kennems/blog-image/ViewPager.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".ViewPageActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/vp_content"
        android:layout_width="match_parent"
        android:layout_height="300dp" />

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08;

import android.os.Bundle;
import android.view.View;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.viewpager.widget.ViewPager;

import com.showguan.chapter08.adapter.ImagePagerAdapter;
import com.showguan.chapter08.entity.GoodsInfo;
import com.showguan.chapter08.util.ToastUtil;

import java.util.ArrayList;

public class ViewPageActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener {

    // 商品信息列表
    private ArrayList<GoodsInfo> mGoodsList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置当前活动的布局文件
        setContentView(R.layout.activity_view_page);

        // 获取 ViewPager 控件
        ViewPager vp_content = findViewById(R.id.vp_content);

        // 获取默认的商品信息列表
        mGoodsList = GoodsInfo.getDefaultList();

        // 创建 ImagePagerAdapter 适配器
        ImagePagerAdapter adapter = new ImagePagerAdapter(this, mGoodsList);

        // 为 ViewPager 设置适配器
        vp_content.setAdapter(adapter);

        // 设置页面变更监听器
        vp_content.addOnPageChangeListener(this);
    }

    /**
     * 在翻页过程中触发
     * @param position 当前页面位置
     * @param positionOffset 页面偏移百分比
     * @param positionOffsetPixels 页面偏移像素
     */
    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
        // 可以在这里实现页面滚动时的相关逻辑
    }

    /**
     * 页面选中时触发
     * @param position 选中页面的位置
     */
    @Override
    public void onPageSelected(int position) {
        // 显示当前选中页面的商品名称
        ToastUtil.show(this, "当前滑动的页面为 " + mGoodsList.get(position).name);
    }

    /**
     * 翻页状态改变时触发
     * @param state 页面状态
     */
    @Override
    public void onPageScrollStateChanged(int state) {
        // 可以在这里实现页面滚动状态变化时的相关逻辑
    }
}
```

## 翻页标签栏PagerTabStrip

![PagerTab](https://cdn.jsdelivr.net/gh/kennems/blog-image/PagerTab.gif)

### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".PagerTabActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/vp_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.viewpager.widget.PagerTabStrip
            android:id="@+id/pts_tab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

    </androidx.viewpager.widget.ViewPager>

</LinearLayout>
```

### Java

```java
package com.showguan.chapter08;

import android.graphics.Color;
import android.os.Bundle;
import android.util.TypedValue;
import android.view.View;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.viewpager.widget.PagerTabStrip;
import androidx.viewpager.widget.ViewPager;

import com.showguan.chapter08.adapter.ImagePagerAdapter;
import com.showguan.chapter08.entity.GoodsInfo;
import com.showguan.chapter08.util.ToastUtil;

import java.util.ArrayList;

public class PagerTabActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener {
    // 商品信息列表
    private ArrayList<GoodsInfo> mGoodsList;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_pager_tab);

        initPagerStrip();
        initViewPager();

    }

    // 初始化翻页标签栏
    private void initPagerStrip() {
        PagerTabStrip pts_tab = findViewById(R.id.pts_tab);
        pts_tab.setTextSize(TypedValue.COMPLEX_UNIT_SP, 20);
        pts_tab.setTextColor(Color.GRAY);
    }

    private void initViewPager() {
        // 获取 ViewPager 控件
        ViewPager vp_content = findViewById(R.id.vp_content);

        // 获取默认的商品信息列表
        mGoodsList = GoodsInfo.getDefaultList();

        // 创建 ImagePagerAdapter 适配器
        ImagePagerAdapter adapter = new ImagePagerAdapter(this, mGoodsList);

        // 为 ViewPager 设置适配器
        vp_content.setAdapter(adapter);

        // 设置页面变更监听器
        vp_content.addOnPageChangeListener(this);
        vp_content.setCurrentItem(2);
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {
        ToastUtil.show(this, "当前滑动的页面为 " + mGoodsList.get(position).name);

    }

    @Override
    public void onPageScrollStateChanged(int state) {

    }
}
```

## 简单的启动引导页

![launch](https://cdn.jsdelivr.net/gh/kennems/blog-image/launch.gif)

### XML

#### activity_launch_simple.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".LaunchSimpleActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/vp_launch"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

#### item_launch.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".LaunchSimpleActivity">

    <ImageView
        android:id="@+id/iv_launch"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="fitXY" />

    <RadioGroup
        android:id="@+id/rg_indicate"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        android:orientation="horizontal"
        android:paddingBottom="20dp" />

    <Button
        android:id="@+id/btn_launch"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="点击开始美好生活"
        android:textColor="@color/blue"
        android:textSize="22sp"
        android:visibility="gone" />

</RelativeLayout>
```

### Java

#### LaunchSimpleActivity.java

```java
package com.showguan.chapter08;

import android.annotation.SuppressLint;
import android.os.Bundle;
import android.view.View;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.viewpager.widget.ViewPager;

import com.showguan.chapter08.adapter.LaunchPagerAdapter;

public class LaunchSimpleActivity extends AppCompatActivity {

    // 定义一个数组，用于存储启动界面的背景图片资源ID
    private static int[] launchImageArray = {
            R.drawable.guide_bg1,
            R.drawable.guide_bg2,
            R.drawable.guide_bg3,
            R.drawable.guide_bg4
    };

    // 当Activity被创建时调用
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置布局文件
        setContentView(R.layout.activity_launch_simple);

        // 查找布局文件中的ViewPager控件
        ViewPager vp_launch = findViewById(R.id.vp_launch);
        // 创建适配器对象，将图片数组传递进去
        LaunchPagerAdapter adapter = new LaunchPagerAdapter(this, launchImageArray);
        // 为ViewPager设置适配器
        vp_launch.setAdapter(adapter);
    }
}

```

#### LaunchPagerAdapter.java

```java
package com.showguan.chapter08.adapter;

import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.viewpager.widget.PagerAdapter;

import com.showguan.chapter08.ViewPageActivity;
import com.showguan.chapter08.entity.GoodsInfo;

import java.util.ArrayList;
import java.util.List;

public class ImagePagerAdapter extends PagerAdapter {
    private final Context mContext;
    private final ArrayList<GoodsInfo> mGoodsList;

    private List<ImageView> mViewList = new ArrayList<>();


    public ImagePagerAdapter(Context context, ArrayList<GoodsInfo> goodsInfos) {
        this.mContext = context;
        this.mGoodsList = goodsInfos;
        for (GoodsInfo goodsInfo : mGoodsList) {
            ImageView view = new ImageView(mContext);
            view.setLayoutParams(new ViewGroup.LayoutParams(
                    ViewGroup.LayoutParams.MATCH_PARENT,
                    ViewGroup.LayoutParams.WRAP_CONTENT
            ));
            view.setImageResource(goodsInfo.pic);
            mViewList.add(view);
        }
    }

    @Override
    public int getCount() {
        return mViewList.size();
    }

    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return (view == object);
    }

    // 实例化指定位置的页面，并将其添加到容器中
    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        ImageView item = mViewList.get(position);
        container.addView(item);
        return item;
    }

    // 从容器中销毁指定位置的页面
    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        container.removeView(mViewList.get(position));
    }

    @Nullable
    @Override
    public CharSequence getPageTitle(int position) {
        return mGoodsList.get(position).name;
    }
}

```

## 碎片Fragment ⭐⭐⭐⭐⭐

传统的Activity并不能很好的处理大屏问题，所以急需一个碎片化的东西能够划区域的展示内容，并且有属于自己的独立可操作空间，所以就出现了Fragment。

## StaticFragment

![image-20240701200338169](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240701200338169.png)

### XML

#### activity_fragment_static.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".FragmentStaticActivity"
    android:orientation="vertical">

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fragment_static"
        android:name="com.showguan.chapter08.fragment.StaticFragment"
        android:layout_width="match_parent"
        android:layout_height="60dp"/>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textSize="@dimen/common_font_size"
        android:gravity="center"
        android:text="每个页面的具体内容："/>

</LinearLayout>
```

#### fragment_static.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/pink"
    android:orientation="horizontal"
    tools:context=".fragment.StaticFragment">

    <TextView
        android:id="@+id/tv_adv"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:gravity="center"
        android:text="广告图片"
        android:textColor="@color/brown"
        android:textSize="@dimen/common_font_size" />

    <ImageView
        android:id="@+id/iv_adv"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="4"
        android:scaleType="fitCenter"
        android:src="@drawable/adv" />

</LinearLayout>
```

### Java

#### FragmentStaticActivity.java

```java
package com.showguan.chapter08;

import com.showguan.chapter08.adapter.PlanetBaseAdapter;
import com.showguan.chapter08.entity.Planet;

public class FragmentStaticActivity extends AppCompatActivity {

    private static final String TAG = "fragment";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_fragment_static);
    }
}
```

#### StaticFragment.java

```java
package com.showguan.chapter08.fragment;

import android.content.Context;
import android.os.Bundle;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

import com.showguan.chapter08.R;

public class StaticFragment extends Fragment {

    private static final String TAG = "fragment";

    @Override
    public void onAttach(@NonNull Context context) {
        super.onAttach(context);
        Log.d(TAG, "Fragment onAttach: ");
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d(TAG, "Fragment onCreate: ");
    }

    @Override
    public void onStart() {
        super.onStart();
        Log.d(TAG, "Fragment onStart: ");
    }

    @Override
    public void onResume() {
        super.onResume();
        Log.d(TAG, "Fragment onResume: ");
    }

    @Override
    public void onPause() {
        super.onPause();
        Log.d(TAG, "Fragment onPause: ");
    }

    @Override
    public void onStop() {
        super.onStop();
        Log.d(TAG, "Fragment onStop: ");
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "Fragment onDestroy: ");
    }

    @Override
    public void onDetach() {
        super.onDetach();
        Log.d(TAG, "Fragment onDetach: ");
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        Log.d(TAG, "Fragment onCreateView: ");
        return inflater.inflate(R.layout.fragment_static, container, false);
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        Log.d(TAG, "Fragment onActivityCreated: ");
    }

}
```

## Fragment注册

静态注册在布局文件中直接指定Fragment，而动态注册直到代码中才动态添加Fragment。

## Fragment生命周期

![image-20240630223900670](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240630223900670.png)

 ![image-20240630233229233](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240630233229233.png)

## DynamicFragment

用`DynamicFragment`重构普通的`PagerTabStrip`

![dynamicFragment](https://cdn.jsdelivr.net/gh/kennems/blog-image/dynamicFragment.gif)

### XML

#### activity_fragment_dynamic.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".FragmentDynamicActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/vp_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <androidx.viewpager.widget.PagerTabStrip
            android:id="@+id/pts_tab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

    </androidx.viewpager.widget.ViewPager>

</LinearLayout>

```

#### fragment_dynamic.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".fragment.DynamicFragment"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/iv_pic"
        android:layout_width="match_parent"
        android:layout_height="360dp"
        android:scaleType="fitCenter"
        tools:src="@drawable/adv" />

    <TextView
        android:id="@+id/tv_desc"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="left"
        android:textColor="@color/black"
        android:textSize="17sp"
        tools:text="Test" />

</LinearLayout>
```

### Java

#### FragmentDynamicActivity.java

```java
package com.showguan.chapter08;

import android.graphics.Color;
import android.os.Bundle;
import android.util.Log;
import android.util.TypedValue;

import androidx.appcompat.app.AppCompatActivity;
import androidx.viewpager.widget.PagerTabStrip;
import androidx.viewpager.widget.ViewPager;

import com.showguan.chapter08.adapter.MobilePagerAdapter;
import com.showguan.chapter08.entity.GoodsInfo;
import com.showguan.chapter08.util.ToastUtil;

import java.util.ArrayList;

public class FragmentDynamicActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener {
    private static String TAG = "Kennem";

    // 商品信息列表
    private ArrayList<GoodsInfo> mGoodsList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_fragment_dynamic);
        initPagerStrip();
        initViewPager();
    }

    // 初始化翻页标签栏
    private void initPagerStrip() {
        PagerTabStrip pts_tab = findViewById(R.id.pts_tab);
        pts_tab.setTextSize(TypedValue.COMPLEX_UNIT_SP, 20);
        pts_tab.setTextColor(Color.GRAY);
        pts_tab.setDrawFullUnderline(false);
        pts_tab.setTabIndicatorColor(Color.BLUE);
    }

    private void initViewPager() {
        // 获取 ViewPager 控件
        ViewPager vp_content = findViewById(R.id.vp_content);
        // 获取默认的商品信息列表
        mGoodsList = GoodsInfo.getDefaultList();
        Log.d(TAG, mGoodsList.toString());
        // 创建 MobilePagerAdapter 适配器
        MobilePagerAdapter adapter = new MobilePagerAdapter(getSupportFragmentManager(), mGoodsList);
        // 为 ViewPager 设置适配器
        vp_content.setAdapter(adapter);
        // 设置页面变更监听器
        vp_content.addOnPageChangeListener(this);
        vp_content.setCurrentItem(2); // 设置当前显示的页
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
        // 页面滑动时触发
    }

    @Override
    public void onPageSelected(int position) {
        // 页面选中时触发
        ToastUtil.show(this, "当前滑动的页面为 " + mGoodsList.get(position).name);
    }

    @Override
    public void onPageScrollStateChanged(int state) {
        // 页面滑动状态改变时触发
    }
}
```

#### MobilePagerAdapter.java

```java
package com.showguan.chapter08.adapter;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;
import androidx.fragment.app.FragmentManager;
import androidx.fragment.app.FragmentPagerAdapter;

import com.showguan.chapter08.entity.GoodsInfo;
import com.showguan.chapter08.fragment.DynamicFragment;

import java.util.List;

public class MobilePagerAdapter extends FragmentPagerAdapter {
    // 商品信息列表
    private final List<GoodsInfo> mGoodsList;

    /**
     * 构造函数，初始化适配器
     * @param fm FragmentManager 用于管理 Fragment 的事务
     * @param mGoodsList 商品信息列表
     */
    public MobilePagerAdapter(@NonNull FragmentManager fm, List<GoodsInfo> mGoodsList) {
        super(fm, BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT);
        this.mGoodsList = mGoodsList;
    }

    /**
     * 获取指定位置的 Fragment
     * @param position 位置索引
     * @return 对应位置的 Fragment 实例
     */
    @NonNull
    @Override
    public Fragment getItem(int position) {
        // 从列表中获取商品信息
        GoodsInfo info = mGoodsList.get(position);
        // 创建并返回新的 DynamicFragment 实例
        return DynamicFragment.newInstance(position, info.pic, info.description);
    }

    /**
     * 获取列表中的项目数量
     * @return 商品信息列表的大小
     */
    @Override
    public int getCount() {
        return mGoodsList.size();
    }

    /**
     * 获取页面标题
     * @param position 位置索引
     * @return 对应位置的页面标题
     */
    @Nullable
    @Override
    public CharSequence getPageTitle(int position) {
        // 返回商品名称作为页面标题
        return mGoodsList.get(position).name;
    }
}
```

#### DynamicFragment.java

```java
package com.showguan.chapter08.fragment;

import android.os.Bundle;

import androidx.fragment.app.Fragment;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import com.showguan.chapter08.R;

public class DynamicFragment extends Fragment {

    /**
     * 静态工厂方法，用于创建新的 DynamicFragment 实例并传递参数
     * @param position 位置索引
     * @param pic_id 图片资源 ID
     * @param desc 描述文本
     * @return 新的 DynamicFragment 实例
     */
    public static DynamicFragment newInstance(int position, int pic_id, String desc) {
        DynamicFragment fragment = new DynamicFragment();
        // 参数打包
        Bundle args = new Bundle();
        args.putInt("position", position);
        args.putInt("image_id", pic_id);
        args.putString("desc", desc);
        fragment.setArguments(args);
        return fragment;
    }

    /**
     * 创建并返回该 fragment 的视图层次结构
     * @param inflater 用于实例化布局 XML 文件的布局填充器对象
     * @param container 父级视图，null 表示没有父级视图
     * @param savedInstanceState 用于保存的状态
     * @return 返回 fragment 的根视图
     */
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {

        // 使用给定的布局文件实例化视图
        View view = inflater.inflate(R.layout.fragment_dynamic, container, false);

        // 获取传递的参数
        Bundle arguments = getArguments();
        if(arguments != null){
            // 获取并设置 ImageView 和 TextView 的内容
            ImageView iv_pic = view.findViewById(R.id.iv_pic);
            TextView tv_desc = view.findViewById(R.id.tv_desc);
            iv_pic.setImageResource(arguments.getInt("image_id", R.drawable.diqiu));
            tv_desc.setText(arguments.getString("desc", ""));
        }
        return view;
    }
}
```

