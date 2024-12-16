---
title : 'Android（3）'
date : 2024-06-21T22:30:13+08:00
lastmod: 2024-06-21T22:20:13+08:00
description : "Android学习笔记" 
image : img/cat.jpg
draft : false    
categories : ["Android学习笔记"]
tags : ["Android"]
# password : leetcode
---

# Android（3）

## 数据存储

### 共享参数的用法

- `SharedPreferences` 是 Android的一个**轻量级存储工具**，采用的存储结构是`key-value`的键值对方式
- 共享参数的存储介质是符合XML规范的配置文件。保存路径是：`/data/data/应用包名/shared_prefs/文件名.xml`

共享参数主要适用的场合：

- **简单且孤立的数据**。若是复杂且相互间有关的数据，则要保存在数据库中。
- **文本形式的数据**。若是二进制数据，则要保存在文件中。
- **需要持久化存储的数据**。在App退出后再次启动时，之前保存的数据仍然有效。

实际开发中，共享参数经常存储的数据有App的个性化配置信息，用户使用App的行为信息，临时需要保存的片段信息。

### ShareWriteActivity.java

```java
package com.showguan.chapter06;

public class ShareWriteActivity extends AppCompatActivity implements View.OnClickListener {

    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private SharedPreferences preferences;
    private CheckBox ck_isMarried;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_share_write);
        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_isMarried = findViewById(R.id.ck_isMarried);

        findViewById(R.id.btn_save).setOnClickListener(this);

        preferences = getSharedPreferences("config", Context.MODE_PRIVATE);

        reload();

    }

    private void reload() {
        String name = preferences.getString("name", null);
        if(name != null){
            et_name.setText(name);
        }
        int age = preferences.getInt("age", 0);
        if(age != 0){
            et_age.setText(String.valueOf(age));
        }
        Float height = preferences.getFloat("height", 0.0F);
        if(height != 0.0F){
            et_height.setText(height.toString());
        }
        Float weight = preferences.getFloat("weight", 0.0F);
        if(weight != 0.0F){
            et_weight.setText(weight.toString());
        }

        Boolean isMarried = preferences.getBoolean("married", false);
        ck_isMarried.setChecked(isMarried);

    }

    @Override
    public void onClick(View v) {
        String name = et_name.getText().toString();
        String age = et_age.getText().toString();
        String height = et_height.getText().toString();
        String weight = et_weight.getText().toString();

        SharedPreferences.Editor editor = preferences.edit();
        editor.putString("name", name);
        editor.putInt("age", Integer.valueOf(age));
        editor.putFloat("height", Float.valueOf(height));
        editor.putFloat("weight", Float.valueOf(weight));
        editor.putBoolean("married", ck_isMarried.isChecked());
        editor.commit();

    }
}
```

### 实现保存密码

![savePassword](https://cdn.jsdelivr.net/gh/kennems/blog-image/savePassword.gif)

#### Java

用数据库存储

```java
package com.showguan.chapter06;

import java.util.Random;

public class LoginSQLliteActivity extends AppCompatActivity implements RadioGroup.OnCheckedChangeListener, View.OnClickListener, View.OnFocusChangeListener {

    // 定义界面中的控件
    private TextView login_passFun;
    private EditText login_input;
    private TextView login_input_option;
    private View remember_password_layout;
    private RadioButton rb_phonecode;
    private RadioButton rb_password;
    private CheckBox ck_save_password;
    private EditText et_phone;
    private Button login;
    private String mPassword = "123456"; // 默认密码
    private String mVerifyCode; // 验证码

    // ActivityResultLauncher用于处理从FindPasswordActivity返回的结果
    private ActivityResultLauncher<Intent> register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
        @Override
        public void onActivityResult(ActivityResult o) {
            Intent intent = o.getData();
            if (intent != null && o.getResultCode() == Activity.RESULT_OK) {
                mPassword = intent.getStringExtra("new_password"); // 获取新密码
            }
        }
    });
    private SharedPreferences preferences;
    private LoginDBHelper mHelper;

    @Override
    protected void onStart() {
        super.onStart();
        mHelper = LoginDBHelper.getInstance(this); // 获取数据库帮助类实例
        mHelper.openReadLink(); // 打开数据库读取连接
        mHelper.openWriteLink(); // 打开数据库写入连接
        reload(); // 重新加载用户信息
    }

    @Override
    protected void onStop() {
        super.onStop();
        mHelper.closeLink(); // 关闭数据库连接
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login_main); // 设置活动布局

        // 初始化控件
        RadioGroup rg_login = findViewById(R.id.rg_login);
        login_passFun = findViewById(R.id.login_passFun);
        login_input = findViewById(R.id.login_input);
        login_input_option = findViewById(R.id.login_input_option);
        remember_password_layout = findViewById(R.id.remember_password_layout);
        rb_password = findViewById(R.id.rb_password);
        rb_phonecode = findViewById(R.id.rb_phonecode);
        et_phone = findViewById(R.id.et_phone);
        ck_save_password = findViewById(R.id.ck_save_password);
        login = findViewById(R.id.login);

        // 设置监听器
        rg_login.setOnCheckedChangeListener(this);
        et_phone.addTextChangedListener(new HideTextWatcher(et_phone, 11));
        login_input.addTextChangedListener(new HideTextWatcher(login_input, 6));
        login_input.setOnFocusChangeListener(this);
        login_input_option.setOnClickListener(this);
        login.setOnClickListener(this);

        // 获取SharedPreferences对象
        preferences = getSharedPreferences("config", Context.MODE_PRIVATE);
    }

    // 重新加载用户信息
    private void reload() {
        LoginInfo info = mHelper.queryTop(); // 查询最新的用户信息
        if (info != null && info.isRemember()) {
            et_phone.setText(info.getPhone());
            login_input.setText(info.getPassword());
            ck_save_password.setChecked(true);
        }
    }

    // 监听RadioGroup选中状态变化
    @Override
    public void onCheckedChanged(RadioGroup group, int checkedId) {
        if (checkedId == R.id.rb_password) {
            login_passFun.setText(R.string.login_password);
            login_input.setHint(R.string.password_hint);
            login_input_option.setText(R.string.forget_password);
            remember_password_layout.setVisibility(View.VISIBLE);
        } else if (checkedId == R.id.rb_phonecode) {
            login_passFun.setText(R.string.login_phonecode);
            login_input.setHint(R.string.code_hint);
            login_input_option.setText(R.string.get_code);
            remember_password_layout.setVisibility(View.GONE);
        }
    }

    // 监听点击事件
    @Override
    public void onClick(View v) {
        String phoneNum = et_phone.getText().toString();
        if (phoneNum.length() < 11) {
            Toast.makeText(this, "您输入的手机号不足11位，请重新输入", Toast.LENGTH_SHORT).show();
            return;
        }
        if (v.getId() == R.id.login_input_option) {
            if (rb_password.isChecked()) {
                Intent intent = new Intent(this, FindPasswordActivity.class);
                intent.putExtra("phone", phoneNum);
                register.launch(intent); // 启动FindPasswordActivity
            } else if (rb_phonecode.isChecked()) {
                mVerifyCode = String.format("%06d", new Random().nextInt(999999)); // 生成随机验证码
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.setTitle("手机验证码");
                builder.setMessage("手机号" + phoneNum + ", 本次验证码是" + mVerifyCode);
                builder.setPositiveButton("好的", null);
                AlertDialog dialog = builder.create();
                dialog.show();
            }
        } else if (v.getId() == R.id.login) {
            if (rb_password.isChecked()) {
                if (!mPassword.equals(login_input.getText().toString())) {
                    Toast.makeText(this, "用户名或密码错误，请检查输入：", Toast.LENGTH_SHORT).show();
                    return;
                }
                loginSuccess();
            } else if (rb_phonecode.isChecked()) {
                if (mVerifyCode == null || !mVerifyCode.equals(login_input.getText().toString())) {
                    Toast.makeText(this, "验证码错误，请检查输入：", Toast.LENGTH_SHORT).show();
                    return;
                }
                loginSuccess();
            }
        }
    }

    // 登录成功处理
    private void loginSuccess() {
        String desc = String.format("你的手机号是%s, 恭喜你登录成功，点击确认返回上一个页面", et_phone.getText().toString());
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setTitle("登录成功");
        builder.setMessage(desc);
        builder.setPositiveButton("确认返回", (dialog, which) -> {
            finish(); // 结束当前活动
        });
        builder.setNegativeButton("我再看看吧", null);
        AlertDialog dialog = builder.create();
        dialog.show();

        if (rb_password.isChecked() && ck_save_password.isChecked()) {
            SharedPreferences.Editor editor = preferences.edit();
            editor.putString("account", et_phone.getText().toString());
            editor.putString("password", login_input.getText().toString());
            editor.putBoolean("is_save_password", true);
            editor.commit(); // 提交保存
        }
        LoginInfo info = new LoginInfo(et_phone.getText().toString(), login_input.getText().toString(), ck_save_password.isChecked());
        mHelper.insert(info); // 将用户信息插入数据库
    }

    // 监听控件焦点变化
    @Override
    public void onFocusChange(View v, boolean hasFocus) {
        if (v.getId() == R.id.login_input && hasFocus) {
            LoginInfo info = mHelper.queryByPhone(et_phone.getText().toString());
            if (info != null) {
                login_input.setText(info.getPassword());
                ck_save_password.setChecked(true);
            } else {
                login_input.setText("");
                ck_save_password.setChecked(false);
            }
        }
    }

    // 自定义TextWatcher类，用于隐藏输入法
    class HideTextWatcher implements TextWatcher {
        private EditText mView;
        private int maxLength;

        public HideTextWatcher(EditText editText, int maxLength) {
            this.mView = editText;
            this.maxLength = maxLength;
        }

        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {
            // 在文本改变前执行的操作
        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {
            // 在文本改变时执行的操作
        }

        @Override
        public void afterTextChanged(Editable s) {
            // 在文本改变后执行的操作
            if (s.toString().length() == maxLength) {
                ViewUtil.hideOneInputMethod(LoginSQLliteActivity.this, mView);
            }
        }
    }
}

```



### 数据库管理器SQLiteDatabase

- SQLiteDatabase是SQLite的数据库管理类，它提供了托干操作数据表的API， 常用的方法有3类：
- 管理类，用户数据库层面的操作
  - `openDatabase`：打开指定路径的数据库
  - `isOpen`：判断数据库是否已打开
  - `close`：关闭数据库
  - `getVersion`：获取数据库的版本号
  - `setVersion`：设置数据库的版本号

### SQLiteDatabase实现保存密码

效果和SharePreference一致，但是是保存在数据库中。

#### `LoginDBHelper.java`

```java
package com.showguan.chapter06.database;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;

import androidx.annotation.Nullable;

import com.showguan.chapter06.enity.LoginInfo;
import com.showguan.chapter06.enity.User;

import java.util.ArrayList;
import java.util.List;

public class LoginDBHelper extends SQLiteOpenHelper {

    private static final String DB_NAME = "login.db";
    private static final String TABLE_NAME = "login_info";
    private static final int DB_VERSION = 1;
    private static LoginDBHelper mHelper = null;
    private SQLiteDatabase mRDB = null;
    private SQLiteDatabase mWDB = null;

    private LoginDBHelper(@Nullable Context context) {
        super(context, DB_NAME, null, DB_VERSION);
    }

    public static LoginDBHelper getInstance(Context context) {
        if (mHelper == null) {
            mHelper = new LoginDBHelper(context);
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
        String sql = "CREATE TABLE IF NOT EXISTS " + TABLE_NAME + " (" +
                "_id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL," +
                " phone VARCHAR NOT NULL," +
                " password VARCHAR NOT NULL," +
                " remember INTEGER NOT NULL);";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        // 数据库版本更新时使用
        String sql = "ALTER TABLE " + TABLE_NAME + " ADD COLUMN phone VARCHAR;";
        db.execSQL(sql);
        sql = "ALTER TABLE " + TABLE_NAME + " ADD COLUMN password VARCHAR;";
        db.execSQL(sql);
    }

    public void save(LoginInfo info){
        try {
            mWDB.beginTransaction();
            delete(info);
            insert(info);
            mWDB.setTransactionSuccessful();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            mWDB.endTransaction();
        }
    }

    public long delete(LoginInfo info){
        return mWDB.delete(TABLE_NAME, "phone=?", new String[]{
                info.getPhone()
        });
    }

    public long insert(LoginInfo info) {
        ContentValues values = new ContentValues();
        values.put("phone", info.getPhone());
        values.put("password", info.getPassword());
        values.put("remember", info.isRemember());

        // 返回行号
        return mWDB.insert(TABLE_NAME, null, values);
    }

    public long deleteByName(String name) {
        return mWDB.delete(TABLE_NAME, "name = ?", new String[]{
                name
        });
    }

    public List<User> queryAll() {
        List<User> list = new ArrayList<>();
        Cursor cursor = mRDB.query(TABLE_NAME, null, null, null, null, null, null);
        while (cursor.moveToNext()) {
            User user = new User();
            user.setId(cursor.getInt(0));
            user.setName(cursor.getString(1));
            user.setAge(cursor.getInt(2));
            user.setHeight(cursor.getFloat(3));
            user.setWeight(cursor.getFloat(4));
            user.setMarried(cursor.getInt(5) == 1 ? true : false);
            list.add(user);
        }
        return list;
    }

    public LoginInfo queryTop() {
        LoginInfo info = null;
        String sql = "SELECT * FROM " + TABLE_NAME + " WHERE remember = 1 ORDER BY _id DESC LIMIT 1";
        Cursor cursor = null;

        try {
            cursor = mRDB.rawQuery(sql, null);
            if (cursor != null && cursor.moveToNext()) {
                info = new LoginInfo();
                int phoneIndex = cursor.getColumnIndex("phone");
                int passwordIndex = cursor.getColumnIndex("password");
                int rememberIndex = cursor.getColumnIndex("remember");

                if (phoneIndex != -1) {
                    info.setPhone(cursor.getString(phoneIndex));
                }
                if (passwordIndex != -1) {
                    info.setPassword(cursor.getString(passwordIndex));
                }
                if (rememberIndex != -1) {
                    info.setRemember(cursor.getInt(rememberIndex) == 1);
                }
            }
        } finally {
            if (cursor != null) {
                cursor.close();
            }
        }
        return info;
    }

    public LoginInfo queryByPhone(String phone) {
        LoginInfo info = null;
        String sql = "SELECT * FROM " + TABLE_NAME;
        Cursor cursor = mRDB.query(TABLE_NAME, null, "phone=? and remember=1", new String[]{phone}, null, null, null);

        try {
            if (cursor != null && cursor.moveToNext()) {
                info = new LoginInfo();
                int phoneIndex = cursor.getColumnIndex("phone");
                int passwordIndex = cursor.getColumnIndex("password");
                int rememberIndex = cursor.getColumnIndex("remember");

                if (phoneIndex != -1) {
                    info.setPhone(cursor.getString(phoneIndex));
                }
                if (passwordIndex != -1) {
                    info.setPassword(cursor.getString(passwordIndex));
                }
                if (rememberIndex != -1) {
                    info.setRemember(cursor.getInt(rememberIndex) == 1);
                }
            }
        } finally {
            if (cursor != null) {
                cursor.close();
            }
        }
        return info;
    }

}

```

#### 实体类`LoginInfo.java`

```java
package com.showguan.chapter06.enity;

public class LoginInfo {
    private String phone;
    private String password;
    private boolean remember;

    public LoginInfo(String phone, String password, boolean remember) {
        this.phone = phone;
        this.password = password;
        this.remember = remember;
    }

    public LoginInfo() {
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public boolean isRemember() {
        return remember;
    }

    public void setRemember(boolean remember) {
        this.remember = remember;
    }

    @Override
    public String toString() {
        return "LoginInfo{" +
                "phone='" + phone + '\'' +
                ", password='" + password + '\'' +
                ", remember=" + remember +
                '}';
    }
}

```

#### `LoginSQLliteActivity.java`

```java
package com.showguan.chapter06;

public class LoginSQLliteActivity extends AppCompatActivity implements RadioGroup.OnCheckedChangeListener, View.OnClickListener, View.OnFocusChangeListener {

    // UI元素
    private TextView login_passFun;
    private EditText login_input;
    private TextView login_input_option;
    private View remember_password_layout;
    private RadioButton rb_phonecode;
    private RadioButton rb_password;
    private CheckBox ck_save_password;
    private EditText et_phone;
    private Button login;

    // 密码和验证码
    private String mPassword = "123456";
    private String mVerifyCode;

    // ActivityResultLauncher 用于启动另一个Activity并接收结果
    private ActivityResultLauncher<Intent> register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
        @Override
        public void onActivityResult(ActivityResult o) {
            Intent intent = o.getData();
            if (intent != null && o.getResultCode() == Activity.RESULT_OK) {
                // 获取从FindPasswordActivity返回的新密码
                mPassword = intent.getStringExtra("new_password");
            }
        }
    });

    // SharedPreferences 用于存储数据
    private SharedPreferences preferences;
    // SQLite数据库助手
    private LoginDBHelper mHelper;

    // Activity生命周期方法，在Activity启动时调用
    @Override
    protected void onStart() {
        super.onStart();
        // 打开数据库读写连接
        mHelper = LoginDBHelper.getInstance(this);
        mHelper.openReadLink();
        mHelper.openWriteLink();
        // 重新加载登录信息
        reload();
    }

    // Activity生命周期方法，在Activity停止时调用
    @Override
    protected void onStop() {
        super.onStop();
        // 关闭数据库连接
        mHelper.closeLink();
    }

    // Activity创建时调用
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置Activity的布局文件
        setContentView(R.layout.activity_login_main);

        // 绑定UI元素
        RadioGroup rg_login = findViewById(R.id.rg_login);
        login_passFun = findViewById(R.id.login_passFun);
        login_input = findViewById(R.id.login_input);
        login_input_option = findViewById(R.id.login_input_option);
        remember_password_layout = findViewById(R.id.remember_password_layout);
        rb_password = findViewById(R.id.rb_password);
        rb_phonecode = findViewById(R.id.rb_phonecode);
        et_phone = findViewById(R.id.et_phone);
        ck_save_password = findViewById(R.id.ck_save_password);
        login = findViewById(R.id.login);

        // 设置事件监听
        rg_login.setOnCheckedChangeListener(this);
        et_phone.addTextChangedListener(new HideTextWatcher(et_phone, 11));
        login_input.addTextChangedListener(new HideTextWatcher(login_input, 6));
        login_input.setOnFocusChangeListener(this);
        login_input_option.setOnClickListener(this);
        login.setOnClickListener(this);

        // 初始化SharedPreferences
        preferences = getSharedPreferences("config", Context.MODE_PRIVATE);
    }

    // 重新加载登录信息
    private void reload() {
        LoginInfo info = mHelper.queryTop();
        if (info != null && info.isRemember()) {
            // 如果有保存的登录信息，填充到UI元素中
            et_phone.setText(info.getPhone());
            login_input.setText(info.getPassword());
            ck_save_password.setChecked(true);
        }
    }

    // RadioGroup选项变化时调用
    @Override
    public void onCheckedChanged(RadioGroup group, int checkedId) {
        if (checkedId == R.id.rb_password) {
            // 选择密码登录
            login_passFun.setText(R.string.login_password);
            login_input.setHint(R.string.password_hint);
            login_input_option.setText(R.string.forget_password);
            remember_password_layout.setVisibility(View.VISIBLE);
        } else if (checkedId == R.id.rb_phonecode) {
            // 选择验证码登录
            login_passFun.setText(R.string.login_phonecode);
            login_input.setHint(R.string.code_hint);
            login_input_option.setText(R.string.get_code);
            remember_password_layout.setVisibility(View.GONE);
        }
    }

    // 点击事件处理
    @Override
    public void onClick(View v) {
        String phoneNum = et_phone.getText().toString();
        if (phoneNum.length() < 11) {
            // 手机号不足11位，提示用户
            Toast.makeText(this, "您输入的手机号不足11位，请重新输入", Toast.LENGTH_SHORT).show();
            return;
        }
        if (v.getId() == R.id.login_input_option) {
            if (rb_password.isChecked()) {
                // 找回密码
                Intent intent = new Intent(this, FindPasswordActivity.class);
                intent.putExtra("phone", phoneNum);
                register.launch(intent);
            } else if (rb_phonecode.isChecked()) {
                // 获取验证码
                mVerifyCode = String.format("%06d", new Random().nextInt(999999));
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.setTitle("手机验证码");
                builder.setMessage("手机号" + phoneNum + ", 本次验证码是" + mVerifyCode);
                builder.setPositiveButton("好的", null);
                AlertDialog dialog = builder.create();
                dialog.show();
            }
        } else if (v.getId() == R.id.login) {
            if (rb_password.isChecked()) {
                // 验证密码登录
                if (!mPassword.equals(login_input.getText().toString())) {
                    Toast.makeText(this, "用户名或密码错误，请检查输入：", Toast.LENGTH_SHORT).show();
                    return;
                }
                loginSuccess();
            } else if (rb_phonecode.isChecked()) {
                // 验证验证码登录
                if (mVerifyCode == null || !mVerifyCode.equals(login_input.getText().toString())) {
                    Toast.makeText(this, "验证码错误，请检查输入：", Toast.LENGTH_SHORT).show();
                    return;
                }
                loginSuccess();
            }
        }
    }

    // 登录成功处理
    private void loginSuccess() {
        String desc = String.format("你的手机号是%s, 恭喜你登录成功，点击确认返回上一个页面", et_phone.getText().toString());

        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setTitle("登录成功");
        builder.setMessage(desc);
        builder.setPositiveButton("确认返回", (dialog, which) -> {
            finish();
        });
        builder.setNegativeButton("我再看看吧", null);
        AlertDialog dialog = builder.create();
        dialog.show();

        if (rb_password.isChecked() && ck_save_password.isChecked()) {
            // 保存密码到SharedPreferences
            SharedPreferences.Editor editor = preferences.edit();
            editor.putString("account", et_phone.getText().toString());
            editor.putString("password", login_input.getText().toString());
            editor.putBoolean("is_save_password", true);
            editor.commit();
        }
        if (rb_password.isChecked()) {
            // 保存登录信息到SQLite
            LoginInfo info = new LoginInfo(et_phone.getText().toString(), login_input.getText().toString(), ck_save_password.isChecked());
            mHelper.insert(info);
        }
    }

    // 焦点变化处理
    @Override
    public void onFocusChange(View v, boolean hasFocus) {
        if (v.getId() == R.id.login_input && hasFocus) {
            // 输入框获得焦点时，从数据库中查询对应的密码
            LoginInfo info = mHelper.queryByPhone(et_phone.getText().toString());
            if (info != null) {
                login_input.setText(info.getPassword());
                ck_save_password.setChecked(true);
            } else {
                login_input.setText("");
                ck_save_password.setChecked(false);
            }
        }
    }

    // 自定义TextWatcher类，用于监听文本变化
    class HideTextWatcher implements TextWatcher {
        private EditText mView;
        private int maxLength;

        public HideTextWatcher(EditText editText, int i) {
            this.mView = editText;
            this.maxLength = i;
        }

        @Override
        public void beforeTextChanged(CharSequence s, int start, int count, int after) {

        }

        @Override
        public void onTextChanged(CharSequence s, int start, int before, int count) {

        }

        @Override
        public void afterTextChanged(Editable s) {
            if (s.toString().length() == maxLength) {
                // 当文本长度达到最大值时隐藏输入法
                ViewUtil.hideOneInputMethod(LoginSQLliteActivity.this, mView);
            }
        }
    }
}

```



### 存储卡存储

私有存储空间与公共存储空间

![image-20240623232852884](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240623232852884.png)

![image-20240623232948558](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240623232948558.png)

#### XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">


    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="10dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="姓名: "
            android:textSize="@dimen/common_font_size"/>

        <EditText
            android:id="@+id/et_name"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/edittext_selector"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="10dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="年龄: "
            android:textSize="@dimen/common_font_size"/>

        <EditText
            android:id="@+id/et_age"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/edittext_selector"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="10dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="身高: "
            android:textSize="@dimen/common_font_size"/>

        <EditText
            android:id="@+id/et_height"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/edittext_selector"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="10dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="体重: "
            android:textSize="@dimen/common_font_size"/>

        <EditText
            android:id="@+id/et_weight"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/edittext_selector"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <CheckBox
            android:id="@+id/ck_isMarried"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="已婚"
            android:textSize="@dimen/common_small_font_size"/>

    </LinearLayout>

    <Button
        android:id="@+id/btn_write"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="保存"/>

    <Button
        android:id="@+id/btn_read"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="读取"/>

    <TextView
        android:id="@+id/tv_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />

</LinearLayout>
```

#### Java

```java
package com.showguan.chapter06;

public class FileWriteActivity extends AppCompatActivity implements View.OnClickListener {

    // 定义界面中的控件
    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private SharedPreferences preferences;
    private CheckBox ck_isMarried;
    private String path;
    private TextView tv_content;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 设置活动布局
        setContentView(R.layout.activity_file_write);

        // 初始化控件
        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_isMarried = findViewById(R.id.ck_isMarried);
        tv_content = findViewById(R.id.tv_content);

        // 设置按钮点击事件监听器
        findViewById(R.id.btn_write).setOnClickListener(this);
        findViewById(R.id.btn_read).setOnClickListener(this);

        // 获取SharedPreferences对象，模式为私有
        preferences = getSharedPreferences("config", Context.MODE_PRIVATE);
    }

    @Override
    public void onClick(View v) {
        if(v.getId() == R.id.btn_write){
            // 获取输入框中的文本内容
            String name = et_name.getText().toString();
            String age = et_age.getText().toString();
            String height = et_height.getText().toString();
            String weight = et_weight.getText().toString();

            // 拼接字符串内容
            StringBuilder sb = new StringBuilder();
            sb.append("姓名:").append(name);
            sb.append("\n年龄:").append(age);
            sb.append("\n身高:").append(height);
            sb.append("\n体重:").append(weight);
            sb.append("\n婚否:").append(ck_isMarried.isChecked());

            String directory;
            String fileName = System.currentTimeMillis() + ".txt";

            // 使用内部存储空间，卸载应用后文件将被删除
            directory = getFilesDir().toString();
            path = directory + File.separatorChar + fileName;
            
            // 打印路径日志
            Log.d("Kennem", path);
            
            // 保存文本内容到文件
            FileUtil.saveText(path, sb.toString());
            
            // 显示保存成功的Toast消息
            Toast.makeText(this, "保存成功", Toast.LENGTH_SHORT).show();
        } else if (v.getId() == R.id.btn_read) {
            // 从文件读取内容并显示到TextView
            tv_content.setText(FileUtil.openText(path));
        }
    }
}
```

### 存储卡保存图片

![image-20240624224319416](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240624224319416.png)

![savePic](https://cdn.jsdelivr.net/gh/kennems/blog-image/savePic.gif)

实际保存的文件路径：`/sdcard/Android/data/com.showguan.chapter06/files/Download/1719307975140.jpg`

![image-20240625173432559](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240625173432559.png)

#### Java

```java
package com.showguan.chapter06;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.util.Log;
import android.view.View;
import android.widget.ImageView;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.showguan.chapter06.util.FileUtil;

import java.io.File;

public class ImageWriteActivity extends AppCompatActivity implements View.OnClickListener {

    private ImageView iv_pic;
    private String path;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_image_write);
        findViewById(R.id.btn_read).setOnClickListener(this);
        findViewById(R.id.btn_save).setOnClickListener(this);
        iv_pic = findViewById(R.id.iv_pic);
    }

    @Override
    public void onClick(View v) {
        if(v.getId() == R.id.btn_save){
            String directory = getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString();
            String fileName = System.currentTimeMillis() + ".jpg";
            path = directory + File.separatorChar + fileName;
            Log.d("Kennem", path);
            Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.cat2);
            FileUtil.saveImage(path, bitmap);
            Toast.makeText(this, "保存成功", Toast.LENGTH_SHORT).show();
        } else if (v.getId() == R.id.btn_read) {
            // 使用FileUtil工具类从指定路径读取图片文件并设置到ImageView中显示
            // 注意：这里假设FileUtil类中有一个名为readImage的方法，可以根据路径返回Bitmap对象。
            // 由于具体的FileUtil类实现不明确，这段代码是假设性的示例。

            // 方法一：使用FileUtil工具类读取图片文件
            // Bitmap bitmap = FileUtil.readImage(path); // 假设FileUtil类有这个方法
            // iv_pic.setImageBitmap(bitmap); // 将读取的Bitmap对象设置到ImageView中显示

            // 方法二：直接使用BitmapFactory解码指定路径的图片文件为Bitmap对象
            // Bitmap bitmap = BitmapFactory.decodeFile(path); // 使用BitmapFactory解码文件
            // iv_pic.setImageBitmap(bitmap); // 将解码得到的Bitmap对象设置到ImageView中显示

            // 方法三：使用Uri对象加载指定路径的图片文件到ImageView中显示
            iv_pic.setImageURI(Uri.parse(path)); // 将指定路径的图片文件通过Uri加载并显示到ImageView

        }
    }
}
```

#### FileUtil

```java
package com.showguan.chapter06.util;

public class FileUtil {
    // 保存文本到指定路径
    public static void saveText(String path, String txt) {
        // 注意，这里需要确保os被正确关闭之后才能保存文件
        try (BufferedWriter os = new BufferedWriter(new FileWriter(path))) {
            os.write(txt);
            Log.d("Kennem", "保存成功");
            Log.d("Kennem", "保存的内容" + txt);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //从指定路径的文本文件中读取为字符串
    public static String openText(String path){
        BufferedReader is = null;
        StringBuilder sb = new StringBuilder();
        try{
            is = new BufferedReader(new FileReader(path));
            String line = null;
            while((line = is.readLine()) != null){
                sb.append(line);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            if(is != null){
                try{
                    is.close();
                }catch (Exception e){
                    e.printStackTrace();
                }
            }
        }

        return sb.toString();
    }

    public static void saveImage(String path, Bitmap bitmap) {
        try(FileOutputStream fos = new FileOutputStream(path)){
            bitmap.compress(Bitmap.CompressFormat.JPEG, 100, fos);
        }catch (Exception e){
            e.printStackTrace();
        }
    }

    public static Bitmap readImage(String path) {
        Bitmap bitmap = null;
        try(FileInputStream fis = new FileInputStream(path))
        {
            bitmap = BitmapFactory.decodeStream(fis);
        }catch (Exception e){
            e.printStackTrace();
        }
        return bitmap;
    }
}
```

### Application 声明周期

首先自定义一个Application

```java
package com.showguan.chapter06;

import android.app.Application;
import android.content.res.Configuration;
import android.util.Log;

import androidx.annotation.NonNull;

import java.util.HashMap;

public class MyApplication extends Application {

    private static MyApplication mApp;

    public HashMap<String, String> infoMap = new HashMap<>();


    public static MyApplication getInstance(){
        return mApp;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d("Kennem", "MyApplication onCreate: ");

    }

    @Override
    public void onTerminate() {
        super.onTerminate();
        Log.d("Kennem", "MyApplication onTerminate: ");
    }


    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d("Kennem", "MyApplication onConfigurationChanged: ");
    }
}
```

2、修改manifest文件

![image-20240624180723031](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240624180723031.png)

3、Application声明周期

![image-20240624180900919](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240624180900919.png)

Application在Activity之前创建， 并且旋转屏幕时会调用`onConfigurationChanged`方法，并会重新创建Activity。

### Application全局变量

数和在`Application`中保存的全局变量主要有下面三种数据：

- 会频繁读取的信息，如用户名，手机号等。
- 不方便由意图传递的数据，例如位图对象，非字符串类型的集合对象等
- 容易因频繁分配内存二导致内存泄漏的对象，如Handler对象等。

通过在MyApplication中定义全局变量`infoMap`, 通过单例类获取app对象，实现在infoMap中， 即内存中存储数据。

#### MyAppliaction.java

```java
package com.showguan.chapter06;

import android.app.Application;
import android.content.res.Configuration;
import android.util.Log;

import androidx.annotation.NonNull;

import java.util.HashMap;

public class MyApplication extends Application {

    private static MyApplication mApp = new MyApplication();

    public HashMap<String, String> infoMap = new HashMap<>();


    public static MyApplication getInstance(){
        return mApp;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d("Kennem", "MyApplication onCreate: ");

    }

    @Override
    public void onTerminate() {
        super.onTerminate();
        Log.d("Kennem", "MyApplication onTerminate: ");
    }


    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d("Kennem", "MyApplication onConfigurationChanged: ");
    }
}

```

#### AppWriteActivity.java

```java
package com.showguan.chapter06;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.widget.CheckBox;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import com.showguan.chapter06.util.ToastUtil;

public class AppWriteActivity extends AppCompatActivity implements View.OnClickListener {


    private EditText et_name;
    private EditText et_age;
    private EditText et_height;
    private EditText et_weight;
    private SharedPreferences preferences;
    private CheckBox ck_isMarried;
    private MyApplication app;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_app_write);

        et_name = findViewById(R.id.et_name);
        et_age = findViewById(R.id.et_age);
        et_height = findViewById(R.id.et_height);
        et_weight = findViewById(R.id.et_weight);
        ck_isMarried = findViewById(R.id.ck_isMarried);

        findViewById(R.id.btn_save).setOnClickListener(this);

        app = MyApplication.getInstance();
        reload();
    }

    private void reload() {
        String name = app.infoMap.get("name");
        if (name == null) {
            return;
        }
        String age = app.infoMap.get("age");
        String height = app.infoMap.get("height");
        String weight = app.infoMap.get("weight");
        String married = app.infoMap.get("married");
        et_name.setText(name);
        et_age.setText(age);
        et_height.setText(height);
        et_weight.setText(weight);
        ck_isMarried.setChecked(married == "是");
    }

    //
    @Override
    public void onClick(View v) {
        String name = et_name.getText().toString();
        String age = et_age.getText().toString();
        String height = et_height.getText().toString();
        String weight = et_weight.getText().toString();


        app.infoMap.put("name", name);
        app.infoMap.put("age", age);
        app.infoMap.put("height", height);
        app.infoMap.put("weight", weight);
        app.infoMap.put("married", ck_isMarried.isChecked() ? "是" : "否");
        ToastUtil.show(this, "保存成功");
    }
}
```

### Room框架的导入

Room时谷歌公司推出的数据库处理框架，该框架同样基于SQLite, 但它通过注解技术极大简化了数据库操作，减少了原来相当一部分编码工作量。

- 在使用Room之前，要先修改模块的build.gradle文件，往dependencies节点添加下面的配置，表示导入指定版本的Room库

```build.gradle
dependencies {
    implementation 'androidx.room:room-runtime:2.6.1'
    annotationProcessor 'androidx.room:room-compiler:2.6.1'
}
```

#### 1、创建实体类 `BookInfo.java`

```java
package com.showguan.chapter06.enity;

import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity
public class BookInfo {
    @PrimaryKey(autoGenerate = true)
    private int id;
    private String name;
    private String author;
    private String publish;
    private double price;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public String getPublish() {
        return publish;
    }

    public void setPublish(String publish) {
        this.publish = publish;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "BookInfo{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", author='" + author + '\'' +
                ", publish='" + publish + '\'' +
                ", price=" + price +
                '}';
    }
}
```

#### 2、创建Dao数据访问对象

```java
package com.showguan.chapter06.dao;

import androidx.room.Dao;
import androidx.room.Delete;
import androidx.room.Insert;
import androidx.room.Query;
import androidx.room.Update;

import com.showguan.chapter06.enity.BookInfo;

import java.util.List;

@Dao
public interface BookDao {
    @Insert
    void insert(BookInfo... bookInfos);

    @Delete
    void delete(BookInfo... bookInfos);

    @Query("DELETE FROM BookInfo")
    void deleteAll();

    @Update
    int update(BookInfo... bookInfos);

    @Query("SELECT * FROM BookInfo")
    List<BookInfo> queryAll();

    @Query("SELECT * FROM BookInfo WHERE name = :name ORDER BY id DESC LIMIT 1")
    BookInfo queryByName(String name);
}

```

#### 3、创建 `BookDatabase`  连接实体类和Dao

```java
package com.showguan.chapter06.database;

import androidx.room.Database;
import androidx.room.RoomDatabase;

import com.showguan.chapter06.dao.BookDao;
import com.showguan.chapter06.enity.BookInfo;

@Database(entities = {BookInfo.class}, version = 1, exportSchema = true)
public abstract class BookDatabase extends RoomDatabase{

     public abstract BookDao bookDao();

}
```

#### 4、在`MyApplication`中构建并返回数据库对象

```java
package com.showguan.chapter06;

import android.app.Application;
import android.content.res.Configuration;
import android.util.Log;

import androidx.annotation.NonNull;
import androidx.room.DatabaseConfiguration;
import androidx.room.InvalidationTracker;
import androidx.room.Room;
import androidx.sqlite.db.SupportSQLiteOpenHelper;

import com.showguan.chapter06.dao.BookDao;
import com.showguan.chapter06.database.BookDatabase;

import java.util.HashMap;

public class MyApplication extends Application {

    private static MyApplication mApp = new MyApplication();

    public HashMap<String, String> infoMap = new HashMap<>();

    private static BookDatabase bookDatabase;

    public static MyApplication getInstance(){
        return mApp;
    }

    @Override
    public void onCreate() {
        super.onCreate();

        Log.d("Kennem", "MyApplication onCreate: ");

        bookDatabase = Room.databaseBuilder(this, BookDatabase.class, "book")
                .addMigrations() // 允许迁移数据库（发生数据库变更时，Room默认删除原数据库再创建新数据库， 这样原来的数据会消失。
                .allowMainThreadQueries() //允许再主线程中操作数据库（Room默认不能在主线程中操作数据库）
                .build();

    }

    @Override
    public void onTerminate() {
        super.onTerminate();
        Log.d("Kennem", "MyApplication onTerminate: ");
    }


    @Override
    public void onConfigurationChanged(@NonNull Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d("Kennem", "MyApplication onConfigurationChanged: ");
    }

    public BookDatabase getBookDatabase(){
        return bookDatabase;
    }
}

```

#### 5、在使用时只需要用`BookDao`调用相应的方法即可

```java
package com.showguan.chapter06;

import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import com.showguan.chapter06.dao.BookDao;
import com.showguan.chapter06.enity.BookInfo;
import com.showguan.chapter06.util.ToastUtil;

import java.util.List;

public class RoomWriteActivity extends AppCompatActivity implements View.OnClickListener {
    private String TAG = "Kennem";

    private EditText et_name;
    private EditText et_author;
    private EditText et_publish;
    private EditText et_price;
    private BookDao bookDao;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_room_write);

        et_name = findViewById(R.id.et_name);
        et_author = findViewById(R.id.et_author);
        et_publish = findViewById(R.id.et_publish);
        et_price = findViewById(R.id.et_price);


        findViewById(R.id.btn_add).setOnClickListener(this);
        findViewById(R.id.btn_delete).setOnClickListener(this);
        findViewById(R.id.btn_update).setOnClickListener(this);
        findViewById(R.id.btn_select).setOnClickListener(this);
        findViewById(R.id.btn_deleteAll).setOnClickListener(this);

        bookDao = MyApplication.getInstance().getBookDatabase().bookDao();
    }

    @Override
    public void onClick(View v) {
        String name = null;
        String author = null;
        String publish = null;
        Double price = 0.0;
        if (!et_name.getText().toString().equals("")) {
            name = et_name.getText().toString();
        }
        if (!et_author.getText().toString().equals("")) {
            author = et_author.getText().toString();
        }
        if (!et_publish.getText().toString().equals("")) {
            publish = et_publish.getText().toString();
        }
        if (!et_price.getText().toString().equals("")) {
//            Log.d(TAG, et_price.getText().toString());
            price = Double.valueOf(et_price.getText().toString());
        }

        if (v.getId() == R.id.btn_add) {
            BookInfo bookInfo = new BookInfo();
            bookInfo.setName(name);
            bookInfo.setAuthor(author);
            bookInfo.setPublish(publish);
            bookInfo.setPrice(price);
            bookDao.insert(bookInfo);
            ToastUtil.show(this, "保存成功！");
        } else if (v.getId() == R.id.btn_delete) {
            BookInfo b = new BookInfo();
            int id = bookDao.queryByName(name).getId();
            b.setId(id);

            bookDao.delete(b);
        } else if (v.getId() == R.id.btn_update) {
            BookInfo b3 = new BookInfo();
            BookInfo b4 = bookDao.queryByName(name);
            b3.setId(b4.getId());
            b3.setName(name);
            b3.setAuthor(author);
            b3.setPublish(publish);
            b3.setPrice(price);
            bookDao.update(b3);
        } else if (v.getId() == R.id.btn_select) {
            List<BookInfo> bookInfos = bookDao.queryAll();
            for (BookInfo bookInfo : bookInfos) {
                Log.d(TAG, bookInfo.toString());
            }
        } else if (v.getId() == R.id.btn_deleteAll) {
            bookDao.deleteAll();
        }

    }
}
```

## 案例-实现购物车

