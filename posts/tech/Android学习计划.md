# Android学习计划

6.24 - 6.30 学完Android基础，复习完Java基础，JavaWeb基础， 要做到极其熟练才行

7.1 - 7.5 把小米的笔记完完全全背下来，不能有半点差错

7.6 - 7. 20 小米训练营， 上课尽量回答问题， 最后的机会了， 不能浪费



## 常见BUG

### 常见的空指针异常

```java
    private void showGoods() {
        int screenWidth = getResources().getDisplayMetrics().widthPixels;
        LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(screenWidth / 2, LinearLayout.LayoutParams.WRAP_CONTENT);

        List<GoodsInfo> list = mDBHelper.queryAllGoods();

        for (GoodsInfo info : list) {
            View view = LayoutInflater.from(this).inflate(R.layout.item_goods, null);
            ImageView iv_thumb = view.findViewById(R.id.iv_thumb);
            TextView tv_name = view.findViewById(R.id.tv_name);
            TextView tv_price = view.findViewById(R.id.tv_price);
            Log.d(TAG, info.picPath);
            iv_thumb.setImageURI(Uri.parse(info.picPath));
            tv_name.setText(info.name);
            tv_price.setText(String.valueOf(info.price));
            gl_channel.addView(view, layoutParams);
        }
    }
```

![image-20240625173022223](https://cdn.jsdelivr.net/gh/kennems/blog-image/image-20240625173022223.png)

从新加载的视图中找到某个控件，一定要加对应的视图名称，这里是`view.findViewById()`





### App Inspector 不显示数据库问题：

```java
//        bookDatabase = Room.databaseBuilder(this, BookDatabase.class, "book")
//                .fallbackToDestructiveMigration()
//                .allowMainThreadQueries()
//                .build();
```

`.allowMainThreadQueries()`会干扰**app Inspector**