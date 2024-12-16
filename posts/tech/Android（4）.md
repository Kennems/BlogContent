---
title : 'Android（4）'
date : 2024-06-26T22:30:13+08:00
lastmod: 2024-06-29T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android（4）

就算是最低的15x15都已经不错了，以后还有时间去学，上了研究生也没人能保证你有15x15的收入

## 内容提供者(ContentProvider)

![content_provider](https://cdn.jsdelivr.net/gh/kennems/blog-image/content_provider.gif)

CountentProvider为App存取内部数据提供统一的外部接口，让不同的应用之间得以共享数据

Uri 通用资源标识符 Universal Resource Identifer， 代表数据操作的地址，每一个ContentProvider都会有唯一的地址。ContentProvider使用的Uri语法结构如下：

- `content://authority/data_path/id`
- `content://`是通用前缀，表示该Uri用于ContentProvider定位资源。
- `authority`是授权者名称，用来确定具体由哪一个ContentProvider提供资源。因此一般anthority都由类的小写全称组成，以保证唯一性。
- `data_path`是数据路径，用来确定请求的是哪个数据集
- `id`是数据编号，用来请求单条数据。**如果是多条这个字段忽略**。

### server内容提供程序

![image-20240627214055877](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240627214055877.png)

![image-20240627214117171](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240627214117171.png)

#### UserInfoProvider.java

```java
package com.showguan.chapter07_server.provider;

import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;
import android.util.Log;

import com.showguan.chapter07_server.database.UserDBHelper;

public class UserInfoProvider extends ContentProvider {
    private static String TAG = "Kennem";
    private UserDBHelper dbHelper;
    private static final int USER = 1;
    private static final int USERS = 2;
    private static final UriMatcher URI_MATCHER = new UriMatcher(UriMatcher.NO_MATCH);

    static {
        URI_MATCHER.addURI(UserInfoContent.AUTHORITIES, "/user/#", USER);
        URI_MATCHER.addURI(UserInfoContent.AUTHORITIES, "/user", USERS);
    }

    @Override
    public boolean onCreate() {
        // TODO: Implement this to initialize your content provider on startup.
        Log.d(TAG, "ContentProvider onCreate: ");
        dbHelper = UserDBHelper.getInstance(getContext());
        return true;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        if (URI_MATCHER.match(uri) == USERS) {
            SQLiteDatabase db = dbHelper.getWritableDatabase();
            db.insert(UserDBHelper.TABLE_NAME, null, values);
        }
        return uri;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {
        if (URI_MATCHER.match(uri) == USERS) {
            Log.d(TAG, "UserInfoProvider query: ");
            SQLiteDatabase db = dbHelper.getReadableDatabase();
            Cursor cursor = db.query(UserDBHelper.TABLE_NAME, projection, selection, selectionArgs, null, null, null);
            return cursor;
        }
        return null;
    }

    @Override
    public int delete(Uri uri, String selection, String[] selectionArgs) {
        int count = 0;
        switch (URI_MATCHER.match(uri)) {
            case USER:
                String id = uri.getLastPathSegment();
                SQLiteDatabase db1 = dbHelper.getWritableDatabase();
                count = db1.delete(UserDBHelper.TABLE_NAME, "_id = ?", new String[]{id});
                db1.close();
                break;
            case USERS:
                SQLiteDatabase db2 = dbHelper.getWritableDatabase();
                count = db2.delete(UserDBHelper.TABLE_NAME, selection, selectionArgs);
                db2.close();
                break;
        }
        return count;
    }

    @Override
    public String getType(Uri uri) {
        // TODO: Implement this to handle requests for the MIME type of the data
        // at the given URI.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public int update(Uri uri, ContentValues values, String selection,
                      String[] selectionArgs) {
        // TODO: Implement this to handle requests to update one or more rows.
        throw new UnsupportedOperationException("Not yet implemented");
    }

}
```

#### UserInfoContent.java 定义常量

```java
package com.showguan.chapter07_server.provider;

import android.net.Uri;
import android.provider.BaseColumns;

public class UserInfoContent implements BaseColumns {
    public static final String AUTHORITIES = "com.showguan.chapter07_server.provider.UserInfoProvider";

    public static final Uri CONTENT_URI = Uri.parse("content://" + AUTHORITIES + "/user");

    public static final String USER_NAME = "name";
    public static final String USER_AGE = "age";
    public static final String USER_HEIGHT = "height";
    public static final String USER_WEIGHT = "weight";
    public static final String USER_MARRIED = "married";
}
```

#### UserDBHelper.java创建表

```java
package com.showguan.chapter07_server.database;


import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

import androidx.annotation.Nullable;


import java.util.ArrayList;
import java.util.List;

public class UserDBHelper extends SQLiteOpenHelper {

    public static final String DB_NAME = "user.db";
    public static final String TABLE_NAME = "user_info";
    private static final int DB_VERSION = 2;
    private static UserDBHelper mHelper = null;
    private SQLiteDatabase mRDB = null;
    private SQLiteDatabase mWDB = null;

    private UserDBHelper(@Nullable Context context) {
        super(context, DB_NAME, null, DB_VERSION);
    }

    public static UserDBHelper getInstance(Context context) {
        if (mHelper == null) {
            mHelper = new UserDBHelper(context);
        }
        return mHelper;
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        String sql = "CREATE TABLE IF NOT EXISTS " + TABLE_NAME + " (" +
                "_id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL," +
                " name VARCHAR NOT NULL," +
                " age INTEGER NOT NULL," +
                " height LONG NOT NULL," +
                " weight FLOAT NOT NULL," +
                " married INTEGER NOT NULL);";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

### client接受数据端

首先在Manifest中添加如下，表示需要访问的内容提供者的包

![image-20240627214757246](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240627214757246.png)

#### ContentWriteActivity.java 

```java
package com.showguan.chapter07_client;

public class ContentWriteActivity extends AppCompatActivity implements View.OnClickListener {
    private static String TAG = "Kennem";

    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private CheckBox ck_isMarried;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_content_write);

        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_isMarried = findViewById(R.id.ck_isMarried);
        findViewById(R.id.btn_read).setOnClickListener(this);
        findViewById(R.id.btn_write).setOnClickListener(this);
        findViewById(R.id.btn_delete).setOnClickListener(this);
    }

    @SuppressLint("Range")
    @Override
    public void onClick(View v) {
        if(v.getId() == R.id.btn_write){
            ContentValues values = new ContentValues();
            values.put(UserInfoContent.USER_NAME, et_name.getText().toString());
            values.put(UserInfoContent.USER_AGE, et_age.getText().toString());
            values.put(UserInfoContent.USER_HEIGHT, et_height.getText().toString());
            values.put(UserInfoContent.USER_WEIGHT, et_weight.getText().toString());
            values.put(UserInfoContent.USER_MARRIED, ck_isMarried.isChecked());
            getContentResolver().insert(UserInfoContent.CONTENT_URI, values);
            ToastUtil.show(this, "保存成功！");
        } else if (v.getId() == R.id.btn_read) {
            Cursor cursor = getContentResolver().query(UserInfoContent.CONTENT_URI, null, null, null, null);
            if (cursor != null) {
                while(cursor.moveToNext()){
                    User info = new User();
                    info.setId(cursor.getInt(cursor.getColumnIndex(UserInfoContent._ID)));
                    info.setAge(cursor.getInt(cursor.getColumnIndex(UserInfoContent.USER_AGE)));
                    info.setName(cursor.getString(cursor.getColumnIndex(UserInfoContent.USER_NAME)));
                    info.setHeight(cursor.getFloat(cursor.getColumnIndex(UserInfoContent.USER_HEIGHT)));
                    info.setWeight(cursor.getFloat(cursor.getColumnIndex(UserInfoContent.USER_WEIGHT)));
                    info.setMarried(cursor.getInt(cursor.getColumnIndex(UserInfoContent.USER_MARRIED)) == 1);
                    Log.d(TAG, info.toString());
                }
                cursor.close();
            }
        } else if (v.getId() == R.id.btn_delete) {
            // 根据id删除单条
//            Uri uri = ContentUris.withAppendedId(UserInfoContent.CONTENT_URI, 1);
//            int count = getContentResolver().delete(uri, null, null);

            //根据名称删除多条
            int count = getContentResolver().delete(UserInfoContent.CONTENT_URI, "name = ?", new String[]{et_name.getText().toString()});

            if(count > 0){
                ToastUtil.show(this, "删除成功");
            }else{
                ToastUtil.show(this, "删除失败，不存在名为" + et_name.getText().toString() + "的用户！");
            }

        }
    }
}
```

#### 通过Uri格式删除一个用户

```java
            // 根据id删除单条
            Uri uri = ContentUris.withAppendedId(UserInfoContent.CONTENT_URI, 1);
            int count = getContentResolver().delete(uri, null, null);
```



## 动态申请权限

步骤：

1. 检查App是否开启了指定权限
   - 调用`ContextCompat`的`checkSelfPermission`方法
2. 请求系统弹窗，以便用户选择是否开启权限
   - 调用`ActivityCompat`的`requestPermission`方法，即可命令系统自动弹出权限申请窗口。
3. 判断用户的权限选择结果
   - 重写活动页面的权限请求回调方法`onRequestPermissionResult`， 在该方法内部处理用户的权限选择结果。

### 懒汉式获取权限

![permission_lazy](https://cdn.jsdelivr.net/gh/kennems/blog-image/permission_lazy.gif)

#### 首先添加按钮事件监听：

```java
    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_contacts) {
            PermissionUtil.checkPermission(this, PERMISSIONS_CONTACTS, REQUEST_CODE_CONTACTS);
        } else if (v.getId() == R.id.btn_sms) {
            PermissionUtil.checkPermission(this, PERMISSIONS_SMS, REQUEST_CODE_SMS);
        }
    }
```

#### 在工具类中判断是否有没有获取到的权限，申请获取

```java
    public static boolean checkPermission(Activity act, String[] permissions, int requestCode){
        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
            int check = PackageManager.PERMISSION_GRANTED;
            for (String permission : permissions) {
                check = ContextCompat.checkSelfPermission(act, permission);
                if(check != PackageManager.PERMISSION_GRANTED){
                    break;
                }
            }
            if(check != PackageManager.PERMISSION_GRANTED){
                ActivityCompat.requestPermissions(act, permissions, requestCode);
                return false;
            }
        }
        return true;
    }
```

#### 其中`ActivityCompat.requestPermissions`方法的参数在`onRequestPermissionsResult`中返回

```java
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode) {
            case REQUEST_CODE_CONTACTS:
                for (int grantResult : grantResults) {
                    Log.d(TAG, String.valueOf(grantResult));
                }
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "通讯录权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
            case REQUEST_CODE_SMS:
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "短信收发权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
        }
    }
```

#### PermissionLazyActivity.java

```java
package com.showguan.chapter07_client;

import android.Manifest;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.provider.Settings;
import android.util.Log;
import android.view.View;

import androidx.activity.EdgeToEdge;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.showguan.chapter07_client.util.PermissionUtil;
import com.showguan.chapter07_client.util.ToastUtil;

public class PermissionLazyActivity extends AppCompatActivity implements View.OnClickListener {

    private static String TAG = "Kennem";

    private static final String[] PERMISSIONS_CONTACTS = new String[]{
            android.Manifest.permission.READ_CONTACTS,
            android.Manifest.permission.WRITE_CONTACTS
    };

    private static final String[] PERMISSIONS_SMS = new String[]{
            Manifest.permission.SEND_SMS,
            Manifest.permission.READ_SMS
    };

    private static final int REQUEST_CODE_CONTACTS = 1;
    private static final int REQUEST_CODE_SMS = 2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_lazy);
        findViewById(R.id.btn_contacts).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_contacts) {
            PermissionUtil.checkPermission(this, PERMISSIONS_CONTACTS, REQUEST_CODE_CONTACTS);
        } else if (v.getId() == R.id.btn_sms) {
            PermissionUtil.checkPermission(this, PERMISSIONS_SMS, REQUEST_CODE_SMS);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode) {
            case REQUEST_CODE_CONTACTS:
                for (int grantResult : grantResults) {
                    Log.d(TAG, String.valueOf(grantResult));
                }
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "通讯录权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
            case REQUEST_CODE_SMS:
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "短信收发权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
        }
    }

    private void jumpToSettings() {
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }
}
```

#### 跳转到系统权限设计界面：

```java
    private void jumpToSettings() {
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }

```

### 饿汉式获取权限

![permission_hungry](https://cdn.jsdelivr.net/gh/kennems/blog-image/permission_hungry.gif)

#### PermissionHungryActivity.java

不同于懒汉式，创建时即申请全部权限

```java
package com.showguan.chapter07_client;

import android.Manifest;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.net.Uri;
import android.os.Bundle;
import android.provider.Settings;
import android.util.Log;
import android.view.View;

import androidx.activity.EdgeToEdge;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.showguan.chapter07_client.util.PermissionUtil;
import com.showguan.chapter07_client.util.ToastUtil;


public class PermissionHungryActivity extends AppCompatActivity implements View.OnClickListener {

    private static String TAG = "Kennem";

    private static final String[] PERMISSIONS_ALL = new String[]{
            android.Manifest.permission.READ_CONTACTS,
            android.Manifest.permission.WRITE_CONTACTS,
            android.Manifest.permission.SEND_SMS,
            Manifest.permission.READ_SMS
    };

    private static final int REQUEST_CODE_ALL = 1;
    private static final int REQUEST_CODE_CONTACTS = 2;
    private static final int REQUEST_CODE_SMS = 3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_lazy);
        findViewById(R.id.btn_contacts).setOnClickListener(this);
        findViewById(R.id.btn_sms).setOnClickListener(this);
        PermissionUtil.checkPermission(this, PERMISSIONS_ALL, REQUEST_CODE_ALL);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_contacts) {
            PermissionUtil.checkPermission(this, new String[]{PERMISSIONS_ALL[0], PERMISSIONS_ALL[1]}, REQUEST_CODE_CONTACTS);
        } else if (v.getId() == R.id.btn_sms) {
            PermissionUtil.checkPermission(this, new String[]{PERMISSIONS_ALL[2], PERMISSIONS_ALL[3]}, REQUEST_CODE_SMS);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode) {
            case REQUEST_CODE_ALL:
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "所有权限获取成功！");
                } else {
                    for (int i = 0; i < grantResults.length; i++) {
                        if (grantResults[i] != PackageManager.PERMISSION_GRANTED) {
                            switch (permissions[i]) {
                                case Manifest.permission.READ_CONTACTS:
                                case Manifest.permission.WRITE_CONTACTS:
                                    ToastUtil.show(this, "获取通讯录读写权限失败");
                                    jumpToSettings();
                                    return;
                                case Manifest.permission.SEND_SMS:
                                case Manifest.permission.READ_SMS:
                                    ToastUtil.show(this, "获取短信读写权限失败");
                                    jumpToSettings();
                                    return;
                            }
                        }

                    }
                }
                break;
            case REQUEST_CODE_CONTACTS:
                for (int grantResult : grantResults) {
                    Log.d(TAG, String.valueOf(grantResult));
                }
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "通讯录权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
            case REQUEST_CODE_SMS:
                if (PermissionUtil.checkGranted(grantResults)) {
                    Log.d(TAG, "短信收发权限获取成功");
                } else {
                    ToastUtil.show(this, "获取权限失败!");
                    jumpToSettings();
                }
                break;
        }
    }

    private void jumpToSettings() {
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        intent.setData(Uri.fromParts("package", getPackageName(), null));
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
    }

}
```

## 添加联系人

添加用户到通讯录，读取所有联系人信息到logcat中。

![image-20240628172028755](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240628172028755.png)

```java
package com.showguan.chapter07_client;

public class ContactAddActivity extends AppCompatActivity implements View.OnClickListener {

    private static String TAG = "Kennem";

    private TextView et_name;
    private TextView et_phone;
    private TextView et_email;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_contact_add);

        et_name = findViewById(R.id.et_name);
        et_phone = findViewById(R.id.et_phone);
        et_email = findViewById(R.id.et_email);
        findViewById(R.id.btn_read).setOnClickListener(this);
        findViewById(R.id.btn_write).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btn_write) {
            // 创建一个联系人对象
            Contact contact = new Contact();
            contact.name = et_name.getText().toString();
            contact.phone = et_phone.getText().toString();
            contact.email = et_email.getText().toString();
            //addContacts(getContentResolver(), contact);

            //批处理方式，每一次操作都是一个ContentProviderOperation， 构建一个操作集合，然后一次性执行
            // 好处：要么全部成功，要么全部失败，保证了事务的一致性
            addFullContacts(getContentResolver(), contact);
        } else if (v.getId() == R.id.btn_read) {
            readPhoneContact(getContentResolver());
        }
    }

    @SuppressLint("Range") // 禁用范围警告，因为我们手动处理列索引
    private void readPhoneContact(ContentResolver resolver) {
        // 查询RawContacts表，获取所有联系人的_raw_contact_id
        Cursor cursor = resolver.query(
                ContactsContract.RawContacts.CONTENT_URI,
                new String[]{ContactsContract.RawContacts._ID}, // 只查询_raw_contact_id列
                null, // 无筛选条件
                null, // 无筛选条件参数
                null // 无排序
        );

        // 遍历查询结果
        while (cursor.moveToNext()) {
            // 获取当前记录的raw_contact_id
            int rawContactId = cursor.getInt(0);

            // 构建用于查询联系人的Data URI
            Uri uri = Uri.parse("content://com.android.contacts/contacts/" + rawContactId + "/data");

            // 查询Data表，获取与当前联系人相关的所有数据
            Cursor dataCursor = resolver.query(
                    uri,
                    new String[]{Contacts.Data.MIMETYPE, Contacts.Data.DATA1, Contacts.Data.DATA2}, // 查询MIME类型和数据列
                    null, // 无筛选条件
                    null, // 无筛选条件参数
                    null // 无排序
            );

            // 创建一个Contact对象用于存储查询结果
            Contact contact = new Contact();

            // 遍历查询结果
            while (dataCursor.moveToNext()) {
                // 获取当前记录的DATA1列的值
                String data1 = dataCursor.getString(dataCursor.getColumnIndex(Contacts.Data.DATA1));
                // 获取当前记录的MIMETYPE列的值
                String mimeType = dataCursor.getString(dataCursor.getColumnIndex(Contacts.Data.MIMETYPE));

                // 根据MIME类型，将数据存储到相应的Contact属性中
                switch (mimeType) {
                    case CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE:
                        // 如果MIME类型是StructuredName，则将data1赋值给contact的name属性
                        contact.name = data1;
                        break;
                    case CommonDataKinds.Phone.CONTENT_ITEM_TYPE:
                        // 如果MIME类型是Phone，则将data1赋值给contact的phone属性
                        contact.phone = data1;
                        break;
                    case CommonDataKinds.Email.CONTENT_ITEM_TYPE:
                        // 如果MIME类型是Email，则将data1赋值给contact的email属性
                        contact.email = data1;
                        break;
                }
            }

            // 关闭Data表的查询结果游标
            dataCursor.close();

            // 如果contact的name属性不为空，则输出联系人姓名到日志
            if (contact.name != null) {
                Log.d(TAG, contact.name);
            }
        }

        // 关闭RawContacts表的查询结果游标
        cursor.close();
    }


    // 往手机通讯录一次性添加一个联系人信息（包括主记录，姓名，电话号码，电子邮箱）
    // 事务性，要么全部成功，要么全部失败
    private void addFullContacts(ContentResolver contentResolver, Contact contact) {
        // 创建一个插入联系人主记录的内容操作器
        ContentProviderOperation op_main = ContentProviderOperation
                // 设置操作类型为插入
                .newInsert(ContactsContract.RawContacts.CONTENT_URI)
                // 设置插入的ACCOUNT_NAME为null，表示使用默认账户
                .withValue(ContactsContract.RawContacts.ACCOUNT_NAME, null)
                // 构建操作
                .build();

        // 创建一个插入联系人姓名的内容操作器
        ContentProviderOperation op_name = ContentProviderOperation
                // 设置操作类型为插入
                .newInsert(ContactsContract.Data.CONTENT_URI)
                // 设置引用的RAW_CONTACT_ID为前一个操作生成的ID
                .withValueBackReference(Contacts.Data.RAW_CONTACT_ID, 0)
                // 设置数据的MIME类型为StructuredName（联系人的名字）
                .withValue(Contacts.Data.MIMETYPE, CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE)
                // 设置联系人的名字
                .withValue(Contacts.Data.DATA1, contact.name)
                // 构建操作
                .build();

        // 创建一个插入联系人电话号码的内容操作器
        ContentProviderOperation op_phone = ContentProviderOperation
                // 设置操作类型为插入
                .newInsert(ContactsContract.Data.CONTENT_URI)
                // 设置引用的RAW_CONTACT_ID为前一个操作生成的ID
                .withValueBackReference(Contacts.Data.RAW_CONTACT_ID, 0)
                // 设置数据的MIME类型为Phone（联系人的电话）
                .withValue(Contacts.Data.MIMETYPE, CommonDataKinds.Phone.CONTENT_ITEM_TYPE)
                // 设置联系人的电话号码
                .withValue(Contacts.Data.DATA1, contact.phone)
                // 设置电话号码的类型为Mobile（手机）
                .withValue(Contacts.Data.DATA2, CommonDataKinds.Phone.TYPE_MOBILE)
                // 构建操作
                .build();

        // 创建一个插入联系人电子邮箱的内容操作器
        ContentProviderOperation op_email = ContentProviderOperation
                // 设置操作类型为插入
                .newInsert(ContactsContract.Data.CONTENT_URI)
                // 设置引用的RAW_CONTACT_ID为前一个操作生成的ID
                .withValueBackReference(Contacts.Data.RAW_CONTACT_ID, 0)
                // 设置数据的MIME类型为Email（联系人的邮箱）
                .withValue(Contacts.Data.MIMETYPE, CommonDataKinds.Email.CONTENT_ITEM_TYPE)
                // 设置联系人的电子邮箱地址
                .withValue(Contacts.Data.DATA1, contact.email)
                // 设置电子邮箱的类型为Mobile（手机邮箱）
                .withValue(Contacts.Data.DATA2, CommonDataKinds.Email.TYPE_MOBILE)
                // 构建操作
                .build();

        // 创建一个操作列表，用于批量执行所有操作
        ArrayList<ContentProviderOperation> operations = new ArrayList<>();
        // 将插入主记录的操作添加到操作列表
        operations.add(op_main);
        // 将插入姓名的操作添加到操作列表
        operations.add(op_name);
        // 将插入电话号码的操作添加到操作列表
        operations.add(op_phone);
        // 将插入电子邮箱的操作添加到操作列表
        operations.add(op_email);

        try {
            // 执行批量操作，将所有数据插入联系人数据库
            contentResolver.applyBatch(ContactsContract.AUTHORITY, operations);
        } catch (OperationApplicationException e) {
            // 捕获OperationApplicationException异常并抛出RuntimeException
            throw new RuntimeException(e);
        } catch (RemoteException e) {
            // 捕获RemoteException异常并抛出RuntimeException
            throw new RuntimeException(e);
        }
    }


    private void addContacts(ContentResolver resolver, Contact contact) {
        // 创建一个ContentValues对象，用于存储新联系人数据
        ContentValues values = new ContentValues();
        // 插入一个空的RawContact记录，返回该记录的URI
        Uri uri = resolver.insert(ContactsContract.RawContacts.CONTENT_URI, values);
        // 解析出新插入记录的ID
        long rawContactId = ContentUris.parseId(uri);

        // 创建一个ContentValues对象，用于存储联系人的名字数据
        ContentValues name = new ContentValues();
        // 设置RAW_CONTACT_ID，表示该数据属于哪个联系人
        name.put(Contacts.Data.RAW_CONTACT_ID, rawContactId);
        // 设置数据的MIME类型为StructuredName（联系人的名字）
        name.put(Contacts.Data.MIMETYPE, CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE);
        // 设置联系人的名字
        name.put(Contacts.Data.DATA1, contact.name);

        // 将联系人的名字数据插入到Data表中
        resolver.insert(ContactsContract.Data.CONTENT_URI, name);


        // 创建一个ContentValues对象，用于存储联系人的电话号码数据
        ContentValues phone = new ContentValues();
        // 设置RAW_CONTACT_ID，表示该数据属于哪个联系人
        phone.put(Contacts.Data.RAW_CONTACT_ID, rawContactId);
        // 设置数据的MIME类型为Phone（联系人的电话）
        phone.put(Contacts.Data.MIMETYPE, CommonDataKinds.Phone.CONTENT_ITEM_TYPE);
        // 设置联系人的电话号码
        phone.put(Contacts.Data.DATA1, contact.phone);
        // 设置电话号码的类型为Mobile（手机）
        phone.put(Contacts.Data.DATA2, CommonDataKinds.Phone.TYPE_MOBILE);
        // 将联系人的电话号码数据插入到Data表中
        resolver.insert(ContactsContract.Data.CONTENT_URI, phone);

        // 创建一个ContentValues对象，用于存储联系人的邮箱数据
        ContentValues email = new ContentValues();
        // 设置RAW_CONTACT_ID，表示该数据属于哪个联系人
        email.put(Contacts.Data.RAW_CONTACT_ID, rawContactId);
        // 设置数据的MIME类型为Email（联系人的邮箱）
        email.put(Contacts.Data.MIMETYPE, CommonDataKinds.Email.CONTENT_ITEM_TYPE);
        // 设置联系人的邮箱地址（此处错误，应该是contact.email，而不是contact.phone）
        email.put(Contacts.Data.DATA1, contact.email); // 纠正了原来的错误
        // 设置邮箱地址的类型为Work（工作邮箱）
        email.put(Contacts.Data.DATA2, CommonDataKinds.Email.TYPE_WORK);
        // 将联系人的邮箱数据插入到Data表中
        resolver.insert(ContactsContract.Data.CONTENT_URI, email);
    }


    private void addContact(ContentResolver resolver, Contact contact) {
        ContentValues values = new ContentValues();
        Uri uri = resolver.insert(ContactsContract.RawContacts.CONTENT_URI, values);
        long rawDataId = ContentUris.parseId(uri);
    }
}
```

## 发送短信

![sendSMS](https://cdn.jsdelivr.net/gh/kennems/blog-image/sendSMS.gif)

### Java

```java
package com.showguan.chapter07_client; // 定义包名

public class SentMmsActivity extends AppCompatActivity implements View.OnClickListener { // 定义SentMmsActivity类，继承AppCompatActivity，实现View.OnClickListener接口

    private static String TAG = "Kennem"; // 定义TAG用于日志

    private ActivityResultLauncher<Intent> mResultLauncher; // 定义ActivityResultLauncher变量
    private EditText et_phone_number; // 定义EditText变量用于电话号码输入
    private EditText et_title; // 定义EditText变量用于标题输入
    private EditText et_desc; // 定义EditText变量用于描述输入
    private Uri picUri; // 定义Uri变量用于存储图片URI

    @Override
    protected void onCreate(Bundle savedInstanceState) { // onCreate方法，Activity创建时调用
        super.onCreate(savedInstanceState); // 调用父类onCreate方法
        setContentView(R.layout.activity_sent_mms); // 设置布局文件

        ImageView iv_add_pic = findViewById(R.id.iv_add_pic); // 获取ImageView用于添加图片
        findViewById(R.id.btn_send).setOnClickListener(this); // 设置发送按钮的点击监听
        iv_add_pic.setOnClickListener(this); // 设置添加图片按钮的点击监听

        et_phone_number = findViewById(R.id.et_phone_number); // 获取EditText用于电话号码输入
        et_title = findViewById(R.id.et_title); // 获取EditText用于标题输入
        et_desc = findViewById(R.id.et_desc); // 获取EditText用于描述输入

        mResultLauncher = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> { // 注册Activity结果启动器
            if(result.getResultCode() == RESULT_OK){ // 检查结果码是否为RESULT_OK
                Intent intent = result.getData(); // 获取结果数据
                picUri = intent.getData(); // 获取图片URI
                if(picUri != null){ // 如果URI不为空
                    iv_add_pic.setImageURI(picUri); // 设置ImageView显示图片
                    Log.d(TAG, picUri.toString()); // 打印图片URI到日志
                }
            }
        });
    }

    @Override
    public void onClick(View v) { // onClick方法，处理点击事件
        if(v.getId() == R.id.iv_add_pic){ // 如果点击的是添加图片按钮
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT); // 创建Intent用于选择内容
            intent.setType("image/*"); // 设置选择类型为图片
            mResultLauncher.launch(intent); // 启动选择图片的Activity
        } else if (v.getId() == R.id.btn_send) { // 如果点击的是发送按钮
            sendMms(et_phone_number.getText().toString(), // 调用sendMms方法发送MMS
                    et_title.getText().toString(), // 获取标题文本
                    et_desc.getText().toString()); // 获取描述文本
        }
    }

    private void sendMms(String phone, String title, String desc) { // sendMms方法，用于发送MMS
        Intent intent = new Intent(Intent.ACTION_SEND); // 创建发送Intent
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK); // 添加新任务标志
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); // 添加URI读权限标志
        intent.putExtra("address", phone); // 设置电话号码
        intent.putExtra("subject", title); // 设置标题
        intent.putExtra("sms_body", desc); // 设置描述
        intent.putExtra(Intent.EXTRA_STREAM, picUri); // 添加图片URI
        intent.setType("image/*"); // 设置类型为图片
        startActivity(intent); // 启动发送MMS的Activity
        ToastUtil.show(this, "从在弹窗中选择信息或短信应用"); // 显示提示信息
    }
}
```

## FileProvider指定获取图片路径并发送短信

![FileProviderSMS](https://cdn.jsdelivr.net/gh/kennems/blog-image/FileProviderSMS.gif)

```java
package com.showguan.chapter07_client; // 定义包名

public class ProvideMmsActivity extends AppCompatActivity { // 定义ProvideMmsActivity类，继承AppCompatActivity

    private static String TAG = "Kennem"; // 定义TAG用于日志

    private List<ImageInfo> mImageList = new ArrayList<>(); // 定义用于存储图片信息的列表
    private GridLayout gl_appendix; // 定义GridLayout用于显示图片

    private static final String[] PERMISSION_STORAGE = new String[]{ // 定义存储权限数组
            Manifest.permission.READ_EXTERNAL_STORAGE,
    };

    private static final int PERMISSION_STORAGE_CODE = 1; // 定义存储权限请求码
    private EditText et_phone_number; // 定义EditText用于输入电话号码
    private EditText et_message; // 定义EditText用于输入消息
    private EditText et_title; // 定义EditText用于输入标题

    @Override
    protected void onCreate(Bundle savedInstanceState) { // onCreate方法，Activity创建时调用
        super.onCreate(savedInstanceState); // 调用父类onCreate方法
        setContentView(R.layout.activity_provide_mms); // 设置布局文件
        gl_appendix = findViewById(R.id.gl_appendix); // 获取GridLayout用于显示图片

        et_title = findViewById(R.id.et_title); // 获取EditText用于输入标题
        et_message = findViewById(R.id.et_message); // 获取EditText用于输入消息
        et_phone_number = findViewById(R.id.et_phone_number); // 获取EditText用于输入电话号码

        MediaScannerConnection.scanFile( // 调用MediaScannerConnection扫描文件
                this,
                new String[]{Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).toString()}, // 扫描下载目录
                null,
                null
        );

        if (PermissionUtil.checkPermission(this, PERMISSION_STORAGE, PERMISSION_STORAGE_CODE)) { // 检查存储权限
            loadImageList(); // 加载图片列表
            showImageGrid(); // 显示图片网格
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) { // 权限请求结果回调
        super.onRequestPermissionsResult(requestCode, permissions, grantResults); // 调用父类方法
        if (requestCode == PERMISSION_STORAGE_CODE) { // 判断请求码是否为存储权限
            if (PermissionUtil.checkGranted(grantResults)) { // 检查权限是否被授予
                loadImageList(); // 加载图片列表
                showImageGrid(); // 显示图片网格
            }
        }
    }

    private void showImageGrid() { // 显示图片网格的方法
        gl_appendix.removeAllViews(); // 移除所有视图
        for (ImageInfo image : mImageList) { // 遍历图片列表
            ImageView imageView = new ImageView(this); // 创建ImageView
            Bitmap bitmap = BitmapFactory.decodeFile(image.path); // 解码图片文件
            imageView.setImageBitmap(bitmap); // 设置图片
            imageView.setScaleType(ImageView.ScaleType.FIT_CENTER); // 设置缩放类型

            int px = Utils.dip2px(this, 110); // 将110dp转换为px
            ViewGroup.LayoutParams params = new ViewGroup.LayoutParams(px, px); // 创建布局参数
            imageView.setLayoutParams(params); // 设置布局参数

            int padding = Utils.dip2px(this, 5); // 将5dp转换为px
            imageView.setPadding(padding, padding, padding, padding); // 设置内边距
            imageView.setOnClickListener(v -> { // 设置点击监听器
                sendMms(et_phone_number.getText().toString(), // 获取输入的电话号码
                        et_title.getText().toString(), // 获取输入的标题
                        et_message.getText().toString(), // 获取输入的消息
                        image.path); // 获取图片路径
            });
            gl_appendix.addView(imageView); // 将ImageView添加到GridLayout
        }
    }

    private void sendMms(String phone, String title, String desc, String path) { // 发送MMS的方法
        Uri uri = Uri.parse(path); // 将路径解析为Uri
        // 兼容Android 7.0 把访问文件的Uri方式改为FileProvider
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) { // 判断是否为Android 7.0及以上
            uri = FileProvider.getUriForFile(this, getString(R.string.file_Provider), new File(path)); // 使用FileProvider获取Uri
            Log.d(TAG, String.format("FileProvider Uri : %s", uri.toString())); // 打印FileProvider的Uri
        }
        Intent intent = new Intent(Intent.ACTION_SEND); // 创建发送Intent
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK); // 添加新任务标志
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION); // 添加读取URI权限标志
        intent.putExtra("address", phone); // 设置电话号码
        intent.putExtra("subject", title); // 设置标题
        intent.putExtra("sms_body", desc); // 设置消息内容
        intent.putExtra(Intent.EXTRA_STREAM, uri); // 添加图片Uri
        intent.setType("image/*"); // 设置类型为图片
        startActivity(intent); // 启动Activity
        ToastUtil.show(this, "从在弹窗中选择信息或短信应用"); // 显示提示信息
    }

    @SuppressLint("Range")
    private void loadImageList() { // 加载图片列表的方法
        String[] columns = new String[]{ // 定义要查询的列
                MediaStore.Images.Media._ID,
                MediaStore.Images.Media.TITLE,
                MediaStore.Images.Media.SIZE,
                MediaStore.Images.Media.DATA
        };
        Cursor cursor = getContentResolver().query( // 查询图片媒体库
                MediaStore.Images.Media.EXTERNAL_CONTENT_URI, // 查询外部图片URI
                columns, // 查询的列
                "_size < 307200", // 查询条件：大小小于300KB
                null, // 查询参数
                "_size DESC"); // 排序方式：按大小降序

        int count = 0; // 定义计数器
        if (cursor != null) { // 如果游标不为空
            while (cursor.moveToNext() && count < 6) { // 遍历查询结果，最多6个
                ImageInfo image = new ImageInfo(); // 创建ImageInfo对象
                image.id = cursor.getLong(cursor.getColumnIndex(MediaStore.Images.Media._ID)); // 获取图片ID
                image.name = cursor.getString(cursor.getColumnIndex(MediaStore.Images.Media.TITLE)); // 获取图片标题
                image.size = cursor.getLong(cursor.getColumnIndex(MediaStore.Images.Media.SIZE)); // 获取图片大小
                image.path = cursor.getString(cursor.getColumnIndex(MediaStore.Images.Media.DATA)); // 获取图片路径
                if (FileUtil.checkFileUri(this, image.path)) { // 检查文件URI是否有效
                    count += 1; // 增加计数
                    mImageList.add(image); // 将图片信息添加到列表
                }
                Log.d(TAG, image.toString()); // 打印图片信息
            }
        }
    }
}

```

### xml

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="对方号码："
            android:textColor="@color/black"
            android:textSize="17sp" />

        <EditText
            android:id="@+id/et_phone_number"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_margin="3dp"
            android:layout_weight="1"
            android:background="@drawable/edittext_selector"
            android:inputType="number"
            android:textColor="@color/black"
            android:textSize="17sp"
            android:text="10086"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="彩信标题："
            android:textColor="@color/black"
            android:textSize="17sp" />

        <EditText
            android:id="@+id/et_title"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_margin="3dp"
            android:layout_weight="1"
            android:background="@drawable/edittext_selector"
            android:textColor="@color/black"
            android:textSize="17sp"
            android:text="Hallo"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="彩信内容："
            android:textColor="@color/black"
            android:textSize="17sp" />

        <EditText
            android:id="@+id/et_message"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_margin="3dp"
            android:layout_weight="1"
            android:background="@drawable/edittext_selector"
            android:gravity="left|top"
            android:textColor="@color/black"
            android:textSize="17sp"
            android:text="test"/>

    </LinearLayout>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="30dp"
        android:text="点击下方图片开始发送彩信"
        android:textColor="@color/black"
        android:textSize="17sp" />

    <GridLayout
        andddroid:id="@+id/gl_appendix"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="3" />

</LinearLayout>
```

## FileProvider 安装apk

![ApkProvider](https://cdn.jsdelivr.net/gh/kennems/blog-image/ApkProvider.gif)

### Java

```java
package com.showguan.chapter07_client;

public class ProviderApkActivity extends AppCompatActivity implements View.OnClickListener {

    private static final String TAG = "Kennem";

    // 所需权限列表
    private static final String[] PERMISSIONS = new String[]{
            Manifest.permission.REQUEST_INSTALL_PACKAGES,
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE
    };

    // 请求码，用于权限请求回调标识
    private static final int REQUEST_CODE = 1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_provider_apk);
        // 设置按钮点击监听
        findViewById(R.id.btn_install).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        // 检查权限并安装APK
        checkAndInstall();
    }

    /**
     * 检查权限并安装APK
     */
    private void checkAndInstall() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
            Log.d(TAG, "android 11 + ");
            // 检查是否具有管理所有文件的权限
            if (!Environment.isExternalStorageManager()) {
                Log.d(TAG, "checkisExternalStorageManager");
                // 跳转到设置页面请求权限
                Intent intent = new Intent(Settings.ACTION_MANAGE_APP_ALL_FILES_ACCESS_PERMISSION);
                intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                intent.setData(Uri.fromParts("package", getPackageName(), null));
                startActivity(intent);
            } else {
                // 权限已授予，
                // 安装APK
                installApk();
            }
        } else {
            // 检查所需权限是否已全部授予
            if (PermissionUtil.checkPermission(this, PERMISSIONS, REQUEST_CODE)) {
                // 权限已授予，安装APK
                installApk();
            }
        }
    }

    /**
     * 安装APK文件
     */
    private void installApk() {
        // 获取APK文件的路径
        String apkPath = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOWNLOADS).toString() + "/chapter06-release.apk";
        Log.d(TAG, "apkPath:" + apkPath);
        // 获取应用包管理器
        PackageManager pm = getPackageManager();
        // 获取APK文件的包信息
        PackageInfo pi = pm.getPackageArchiveInfo(apkPath, PackageManager.GET_ACTIVITIES);
        if (pi == null) {
            // 如果包信息为空，显示文件损坏提示
            ToastUtil.show(this, "安装文件已经损坏!");
            return;
        }
        // 获取APK文件的Uri
        Uri uri = Uri.parse(apkPath);
        // 兼容Android 7.0及以上版本，通过FileProvider获取Uri
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            // 通过FileProvider获取文件的Uri
            uri = FileProvider.getUriForFile(this, getString(R.string.file_provider), new File(apkPath));
            Log.d("Kennem", String.format("new uri:%s", uri.toString()));
        }
        // 创建Intent以启动APK安装程序
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
        // 设置Uri的数据类型为APK文件
        intent.setDataAndType(uri, "application/vnd.android.package-archive");
        // 启动系统自带的应用安装程序
        startActivity(intent);
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        // 检查权限请求结果
        if (requestCode == REQUEST_CODE && PermissionUtil.checkGranted(grantResults)) {
            // 权限已授予，安装APK
            installApk();
        }
    }
}

```

### 

