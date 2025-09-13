---
title : 'Android（6）'
date : 2024-07-01T22:30:13+08:00
lastmod: 2024-07-01T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android（6）

## 记账本小项目

![BillPager](https://cdn.jsdelivr.net/gh/kennems/blog-image/BillPager.gif)

### BillPagerActivity.java

```java
package com.showguan.chapter08;

import android.app.DatePickerDialog;
import android.content.Intent;
import android.media.Image;
import android.os.Bundle;
import android.util.TypedValue;
import android.view.View;
import android.widget.DatePicker;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.viewpager.widget.PagerTabStrip;
import androidx.viewpager.widget.ViewPager;

import com.showguan.chapter08.adapter.BillPagerAdapter;
import com.showguan.chapter08.database.BillDBHelper;
import com.showguan.chapter08.util.DateUtil;

import org.w3c.dom.Text;

import java.util.Calendar;

public class BillPagerActivity extends AppCompatActivity implements View.OnClickListener, DatePickerDialog.OnDateSetListener {

    private Calendar calendar;
    private TextView tv_month;
    private ViewPager vp_bill;
    private BillDBHelper mDBhelper;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bill_pager);
        TextView tv_title = findViewById(R.id.tv_title);
        TextView tv_subtitle = findViewById(R.id.tv_subtitle);
        ImageView iv_back = findViewById(R.id.iv_back);
        iv_back.setOnClickListener(this);
        tv_month = findViewById(R.id.tv_month);

        tv_title.setText("帐单列表");
        tv_subtitle.setText("添加账单");
        tv_subtitle.setOnClickListener(this);
        calendar = Calendar.getInstance();
        tv_month.setText(DateUtil.getMonth(calendar));
        tv_month.setOnClickListener(this);

        mDBhelper = BillDBHelper.getInstance(this);
        mDBhelper.openReadLink();
        mDBhelper.openWriteLink();
        initViewPager();
    }

    // 初始化ViewPager
    private void initViewPager() {
        PagerTabStrip pts_bill = findViewById(R.id.pts_bill);
        pts_bill.setTextSize(TypedValue.COMPLEX_UNIT_SP, 17);
        vp_bill = findViewById(R.id.vp_bill);
        BillPagerAdapter adapter = new BillPagerAdapter(getSupportFragmentManager(), calendar.get(Calendar.YEAR));
        vp_bill.setAdapter(adapter);
        vp_bill.setCurrentItem(calendar.get(Calendar.MONTH));
    }

    @Override
    public void onClick(View v) {
        if(v.getId() == R.id.tv_month){
            DatePickerDialog dialog = new DatePickerDialog(this, this,
                    calendar.get(Calendar.YEAR),
                    calendar.get(Calendar.MONTH),
                    calendar.get(Calendar.DAY_OF_MONTH)
            );
            dialog.show();
        } else if (v.getId() == R.id.tv_subtitle) {
            Intent intent = new Intent(this, BillAddActivity.class);
            intent.setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
            startActivity(intent);
        } else if (v.getId() == R.id.iv_back) {
            finish();
        }
    }

    @Override
    public void onDateSet(DatePicker view, int year, int month, int dayOfMonth) {
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.MONTH, month);
        calendar.set(Calendar.DAY_OF_MONTH, dayOfMonth);
        tv_month.setText(DateUtil.getMonth(calendar));
        vp_bill.setCurrentItem(calendar.get(Calendar.MONTH));
    }

    @Override
    public void onPointerCaptureChanged(boolean hasCapture) {
        super.onPointerCaptureChanged(hasCapture);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mDBhelper.closeLink();
    }
}
```

### BillAddActivity.java

```java
package com.showguan.chapter08;

import android.app.DatePickerDialog;
import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.DatePicker;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.RadioButton;
import android.widget.RadioGroup;
import android.widget.TextView;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.showguan.chapter08.database.BillDBHelper;
import com.showguan.chapter08.entity.BillInfo;
import com.showguan.chapter08.util.DateUtil;
import com.showguan.chapter08.util.ToastUtil;

import java.util.Calendar;
import java.util.Date;

public class BillAddActivity extends AppCompatActivity implements View.OnClickListener, DatePickerDialog.OnDateSetListener {

    private static final String TAG = "Kennem";

    private TextView tv_date;
    private Calendar calendar;
    private TextView tv_subtitle;
    private TextView tv_title;
    private RadioButton rb_in;
    private RadioButton rb_out;
    private EditText et_remark;
    private EditText et_amount;
    private Button btn_save;
    private RadioGroup rg_type;
    private BillDBHelper mDBhelper;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bill_add);

        tv_title = findViewById(R.id.tv_title);
        tv_subtitle = findViewById(R.id.tv_subtitle);
        rg_type = findViewById(R.id.rg_type);
        rb_in = findViewById(R.id.rb_in);
        rb_out = findViewById(R.id.rb_out);
        et_remark = findViewById(R.id.et_remark);
        et_amount = findViewById(R.id.et_amount);
        btn_save = findViewById(R.id.btn_save);
        ImageView iv_back = findViewById(R.id.iv_back);
        iv_back.setOnClickListener(this);

        tv_title.setText("请记录账单");
        tv_subtitle.setText("账单列表");
        tv_subtitle.setOnClickListener(this);

        tv_date = findViewById(R.id.tv_date);
        calendar = Calendar.getInstance();
        tv_date.setText(DateUtil.getDate(calendar));
        tv_date.setOnClickListener(this);
        btn_save.setOnClickListener(this);
        mDBhelper = BillDBHelper.getInstance(this);
        mDBhelper.openReadLink();
        mDBhelper.openWriteLink();
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.tv_date) {
            DatePickerDialog dialog = new DatePickerDialog(this, this,
                    calendar.get(Calendar.YEAR),
                    calendar.get(Calendar.MONTH),
                    calendar.get(Calendar.DAY_OF_MONTH)
            );
            dialog.show();
        } else if (v.getId() == R.id.btn_save) {
            BillInfo bill = new BillInfo();
            bill.date = tv_date.getText().toString();
            bill.type = rg_type.getCheckedRadioButtonId() == R.id.rb_in ?
                    BillInfo.BILL_TYPE_INCOME : BillInfo.BILL_TYPE_COSE;
            bill.remark = et_remark.getText().toString();
            bill.amount = Double.parseDouble(et_amount.getText().toString());
            Log.d(TAG, bill.toString());
            if (mDBhelper.save(bill) > 0) {
                ToastUtil.show(this, "添加账单成功");
            }
        } else if (v.getId() == R.id.iv_back) {
            finish();
        } else if (v.getId() == R.id.tv_subtitle) {
            Intent intent = new Intent(this, BillPagerActivity.class);
            intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
            startActivity(intent);
        }
    }

    @Override
    public void onDateSet(DatePicker view, int year, int month, int dayOfMonth) {
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.MONTH, month);
        calendar.set(Calendar.DAY_OF_MONTH, dayOfMonth);
        tv_date.setText(DateUtil.getDate(calendar));
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
    }
}
```

### BillPagerAdapter.java

```java
package com.showguan.chapter08.adapter;

import android.util.Log;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;
import androidx.fragment.app.FragmentManager;
import androidx.fragment.app.FragmentPagerAdapter;

import com.showguan.chapter08.fragment.BillFragment;

public class BillPagerAdapter extends FragmentPagerAdapter {

    private final int mYear;
    private static final String TAG = "Kennem";

    public BillPagerAdapter(@NonNull FragmentManager fm, int year) {
        super(fm, BEHAVIOR_RESUME_ONLY_CURRENT_FRAGMENT);
        this.mYear = year;
    }

    @NonNull
    @Override
    public Fragment getItem(int position) {
        Log.d(TAG, String.valueOf(position));
        int month = position + 1;
        String zeroMonth = month < 10 ? "0" + month : "" + month;
        String yearMonth = mYear + "-" + zeroMonth;
        Log.d(TAG, yearMonth);
        return BillFragment.newInstance(yearMonth);
    }

    @Override
    public int getCount() {
        return 12;
    }

    @Nullable
    @Override
    public CharSequence getPageTitle(int position) {
        return (position + 1) + "月份";
    }
}

```

### BillListAdapter.java

```java
package com.showguan.chapter08.adapter;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.TextView;

import com.showguan.chapter08.R;
import com.showguan.chapter08.entity.BillInfo;

import java.util.List;

public class BillListAdapter extends BaseAdapter {


    private final List<BillInfo> mBillInfoList;
    private final Context mContext;

    public BillListAdapter(Context context, List<BillInfo> billInfoList) {
        this.mContext = context;
        this.mBillInfoList = billInfoList;
    }

    @Override
    public int getCount() {
        return mBillInfoList.size();
    }

    @Override
    public Object getItem(int position) {
        return mBillInfoList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder viewHolder;
        if(convertView == null){
            viewHolder = new ViewHolder();
            convertView = LayoutInflater.from(mContext).inflate(R.layout.item_bill, null);
            viewHolder.tv_date = convertView.findViewById(R.id.tv_date);
            viewHolder.tv_remark = convertView.findViewById(R.id.tv_remark);
            viewHolder.tv_amount = convertView.findViewById(R.id.tv_amount);
            convertView.setTag(viewHolder);
        }else{
            viewHolder = (ViewHolder) convertView.getTag();
        }
        BillInfo billInfo = mBillInfoList.get(position);
        viewHolder.tv_date.setText(billInfo.date);
        viewHolder.tv_remark.setText(billInfo.remark);
        viewHolder.tv_amount.setText(String.format("%s %d 元", billInfo.type == 0 ? "+" : "-" ,(int)billInfo.amount));
        return convertView;
    }

    public final class ViewHolder{
        public TextView tv_date;
        public TextView tv_remark;
        public TextView tv_amount;
    }

}

```

### BillDBHelper.java

```java
package com.showguan.chapter08.database;

import android.annotation.SuppressLint;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;

import androidx.annotation.Nullable;

import com.showguan.chapter08.entity.BillInfo;

import java.util.ArrayList;
import java.util.List;

public class BillDBHelper extends SQLiteOpenHelper {

    private static String TAG = "Kennem";

    private static final String DB_NAME = "bill.db";
    private static final String TABLE_BILL_INFO = "bill_info";
    private static final int DB_VERSION = 2;
    private static BillDBHelper mHelper = null;
    private SQLiteDatabase mRDB = null;
    private SQLiteDatabase mWDB = null;

    private BillDBHelper(@Nullable Context context) {
        super(context, DB_NAME, null, DB_VERSION);
    }

    public static BillDBHelper getInstance(Context context) {
        if (mHelper == null) {
            mHelper = new BillDBHelper(context);
        }
        return mHelper;
    }

    public SQLiteDatabase openReadLink() {
        if (mRDB == null || !mRDB.isOpen()) {
            mRDB = mHelper.getReadableDatabase();
        }
        return mRDB;
    }

    public SQLiteDatabase openWriteLink() {
        if (mWDB == null || !mWDB.isOpen()) {
            mWDB = mHelper.getReadableDatabase();
        }
        return mWDB;
    }


    public void closeLink() {
        if (mRDB != null && mRDB.isOpen()) {
            mRDB.close();
            mRDB = null;
        }

        if (mWDB != null && mWDB.isOpen()) {
            mWDB.close();
            mWDB = null;
        }
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        //_id 保证id不是不是关键字
        String sql = null;
        sql = "CREATE TABLE IF NOT EXISTS " + TABLE_BILL_INFO + " (" +
                "_id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL," +
                " date VARCHAR NOT NULL," +
                " type VARCHAR NOT NULL," +
                " amount DOUBLE NOT NULL," +
                " remark VARCHAR NOT NULL);";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }

    public long save(BillInfo billInfo) {
        ContentValues cv = new ContentValues();
        cv.put("date", billInfo.date);
        cv.put("type", billInfo.type);
        cv.put("amount", billInfo.amount);
        cv.put("remark", billInfo.remark);
        return mWDB.insert(TABLE_BILL_INFO, null, cv);
    }

    @SuppressLint("Range")
    public List<BillInfo> queryByMonth(String yearMonth){
        List<BillInfo> list = new ArrayList<>();
        String sql = "select * from " + TABLE_BILL_INFO + " where date like '" + yearMonth + "%';";
        Log.d(TAG, sql);
        Cursor cursor = mRDB.rawQuery(sql, null);
        while(cursor.moveToNext()){
            BillInfo bill = new BillInfo();
            bill.id = cursor.getInt(cursor.getColumnIndex("_id"));
            bill.date = cursor.getString(cursor.getColumnIndex("date"));
            bill.type = cursor.getInt(cursor.getColumnIndex("type"));
            bill.amount = cursor.getDouble(cursor.getColumnIndex("amount"));
            bill.remark = cursor.getString(cursor.getColumnIndex("remark"));
            Log.d(TAG, bill.toString());
            list.add(bill);
        }
        return list;

    }


}

```

### BillInfo.java

```java
package com.showguan.chapter08.entity;

public class BillInfo {
    public int id;
    public String date;
    public int type;
    public double amount;
    public String remark;

    public static final int BILL_TYPE_INCOME = 0;
    public static final int BILL_TYPE_COSE = 1;

    @Override
    public String toString() {
        return "BillInfo{" +
                "id=" + id +
                ", date='" + date + '\'' +
                ", type=" + type +
                ", amount=" + amount +
                ", remark='" + remark + '\'' +
                '}';
    }
}

```

### BillFragment.java

```java
package com.showguan.chapter08.fragment;

import android.os.Bundle;

import androidx.fragment.app.Fragment;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ListView;

import com.showguan.chapter08.R;
import com.showguan.chapter08.adapter.BillListAdapter;
import com.showguan.chapter08.database.BillDBHelper;
import com.showguan.chapter08.entity.BillInfo;

import java.util.List;


public class BillFragment extends Fragment {


    public BillFragment() {
    }

    public static BillFragment newInstance(String yearMonth) {
        BillFragment fragment = new BillFragment();
        Bundle args = new Bundle();
        args.putString("yearMonth", yearMonth);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_bill, container, false);
        ListView lv_bill = view.findViewById(R.id.lv_bill);
        BillDBHelper mDBHelper = BillDBHelper.getInstance(getContext());
        Bundle arguments = getArguments();
        String yearMonth = arguments.getString("yearMonth");
        List<BillInfo> billInfoList = mDBHelper.queryByMonth(yearMonth);
        BillListAdapter adapter = new BillListAdapter(getContext(), billInfoList);
        lv_bill.setAdapter(adapter);
        return view;
    }

}
```

## 广播

- 广播的收发过程分为三个步骤：
  - 发送标准广播
  - 定义广播接收器
  - 开关广播接收器

### 有序广播

![SortedBoard](https://cdn.jsdelivr.net/gh/kennems/blog-image/SortedBoard.gif)

对多个有序广播设置权重，设置其接受的次序：

#### Java

##### BoardOrderActivity.java

```java
package com.showguan.chapter09;

public class BoardOrderActivity extends AppCompatActivity implements View.OnClickListener {
    public static final String ORDER_ACTION = "com.showguan.chapter09.order";
    private OrderAReceiver orderAReceiver;
    private OrderBReceiver orderBReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_board_order);
        Button btn_send_broadcast = findViewById(R.id.btn_send_broadcast);
        btn_send_broadcast.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        Intent intent = new Intent(ORDER_ACTION);
        sendOrderedBroadcast(intent, null);
    }

    @Override
    protected void onStart() {
        super.onStart();
        orderAReceiver = new OrderAReceiver();
        IntentFilter filterA = new IntentFilter(ORDER_ACTION);
        filterA.setPriority(8);
        registerReceiver(orderAReceiver, filterA);

        orderBReceiver = new OrderBReceiver();
        IntentFilter filterB = new IntentFilter(ORDER_ACTION);
        filterB.setPriority(10);
        registerReceiver(orderBReceiver, filterB);
    }

    @Override
    protected void onStop() {
        super.onStop();
        unregisterReceiver(orderAReceiver);
        unregisterReceiver(orderBReceiver);
    }
}
```

##### OrderAReceiver.java

```java
package com.showguan.chapter09.receiver;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

import com.showguan.chapter09.BoardOrderActivity;

public class OrderAReceiver extends BroadcastReceiver {
    private static final String TAG = "Kennem";
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent != null && intent.getAction().equals(BoardOrderActivity.ORDER_ACTION)){
            Log.d(TAG, "接收器A收到一个有序广播");
        }
    }
}
```

##### OrderBReceiver.java

```java
package com.showguan.chapter09.receiver;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

import com.showguan.chapter09.BoardOrderActivity;

public class OrderBReceiver extends BroadcastReceiver {
    private static final String TAG = "Kennem";

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent != null && intent.getAction().equals(BoardOrderActivity.ORDER_ACTION)) {
            Log.d(TAG, "接收器B收到一个有序广播");
        }
    }
}
```

- 一个广播存在多个接收器， 这些接收器需要排队收听广播，这意味着该广播是有条有序广播
- 先收到广播的接收器A，既可以让其他接收器继续收听广播，也可中断广播不让其他接收器收听。

![image-20240706144254963](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240706144254963.png)

### 静态注册广播

- 在代码中注册接收器，该方式被称作动态注册。
- 在`AndroidManifest.xml`中注册接收器，该方式被称作静态注册。
