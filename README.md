# myNotePad
基本要求和附加功能  
## 一.基本功能介绍：
### 1.时间戳



![](https://i.loli.net/2018/06/05/5b156850c1252.png)

### 2.便签搜索（根据标题及内容模糊搜索）

![](https://i.loli.net/2018/06/05/5b1569788e10f.png)






## 二.技术分析：
### 1.时间戳分析：
因为数据库中已有时间字段，所以只需要格式化时间存入即可
NoteEditor.updateNote()函数中修改
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//设置日期格式</br>
        values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, df.format(new Date()));
 ### 2.便签搜索
   private void addSearchView() 
   {
        //给listview添加头部(search)

        View v=View.inflate(this, R.layout.notelistheader,null);
        getListView().addHeaderView(v);
        //给搜索框添加搜索功能
        final EditText et_Search=(EditText)v.findViewById(R.id.et_search);
        et_Search.addTextChangedListener(new TextWatcherForSearch(){
            @Override
            public void onTextChanged(CharSequence charSequence, int i, int i1, int i2) {
                super.onTextChanged(charSequence, i, i1, i2);
                if (charSequence.length()!=0 && et_Search.getText().toString().length()!=0){
                    String str_Search = et_Search.getText().toString();
                    Cursor search_cursor = managedQuery(
                            getIntent().getData(),            // Use the default content URI for the provider.
                            PROJECTION,                       // Return the note ID and title for each note.
                            NotePad.Notes.COLUMN_NAME_TITLE+" like ?",     //模糊查询数据库
                            new String[]{"%"+str_Search+"%"}, //匹配字符串条件                            // No where clause, therefore no where column values.
                            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
                    );
                    adapter.swapCursor(search_cursor);//用搜索结果的cursor刷新listview
    
                }else {
                    if (cursor!=null)//删除搜索框中的text后刷新listview
                    adapter.swapCursor(cursor);//刷新listview
                }
            }
        });
    }
 ### 3.改变背景颜色
    使用底部按钮弹出对话框
      private void setBackgroundTwo(){
        AlertDialog.Builder builder=new AlertDialog.Builder(NotesList.this);
        LayoutInflater inflater=getLayoutInflater();
        View view=inflater.inflate(R.layout.dialogcolor,null);
        LinearLayout linearLayout=(LinearLayout) view.findViewById(R.id.dialoglinear);
        final int[] colorArray={
                Color.parseColor("#707070"),//黑
                Color.parseColor("#fcf9a4"),//黄
                Color.parseColor("#abed65"),//绿
                Color.parseColor("#33a9e1"),//蓝
                Color.parseColor("#070707"),//黑
                Color.parseColor("#1cdaef"),//蓝绿
                Color.parseColor("#fa77ab"),//粉色
        };
        for (int i=0;i<7;i++){//动态创建imageview 用以显示不同颜色
            ImageView imageView=new ImageView(NotesList.this);
            imageView.setLayoutParams(new LinearLayout.LayoutParams(90,120));
            imageView.setBackgroundColor(colorArray[i]);
            final int finalI = i;
            imageView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    NoteAttribute.snoteBackground=colorArray[finalI];
                    getListView().setBackgroundColor(NoteAttribute.snoteBackground);
                    getListView().setBackgroundColor(colorArray[finalI]);
                    appbackground
                            .edit()
                            .putInt(NoteAttribute.NOTEBACKGROUND,colorArray[finalI])
                            .apply();//使用轻量级存储sharepreference异步将颜色存储在本地
                }
            });
            linearLayout.addView(imageView);
        }
        builder.setView(view).create().show();
    
    }
## 三.功能演示
### 1>基本功能
#### 1.notepad的主界面</br>
包含了时间戳

![](https://i.loli.net/2018/06/05/5b1568f942ebc.png)

#### 2.添加便签</br>


![](https://i.loli.net/2018/06/05/5b15693502380.png)

#### 3.点击搜索</br>
添加了搜索功能

![](https://i.loli.net/2018/06/05/5b1569788e10f.png)



### 2>扩展功能（加分点）
#### 1.UI 美化
- 用模块化形状来设计笔记列表，类似于iOS的消息通知，提升了用户使用体验。
- 加入了主题色、背景色调整，用户可以根据自己喜欢的颜色进行背景色设置。
​

![](https://i.loli.net/2018/06/05/5b1569d566927.png)

#### 2.改变背景色
可以根据自己的喜好，设置不同的背景颜色

![](https://i.loli.net/2018/06/05/5b1569ee93370.png)

改变后的界面

![](https://i.loli.net/2018/06/05/5b156a01c604b.png)




