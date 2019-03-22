# Android 中的数据存储

## 目录
- 一、数据存储的方式
- 二、greenDAO
- 三、参考

## 一、数据存储的方式
> 在 Android 系统中主要的存储方式分为以下五种

1. SharePreferences （配置共享）：轻量级的数据存储机制，以键值对的形式将基本的数据类型存储在私有的 SharePreferences 目录（/data/data/<包名>/shared_perfs/）中。
2. File I/O （文件存储）：通过字节流的操作来完成，能直接对二进制数据进行处理。
3. SQLite （数据库）：通过 SQLiteOpenHelper 来封装入口，然后调用 SQLiteDataBase 进行操作，SQLiteConnection 中包含许多 Native 方法，通过 JNI 与 SQLite3 进行交互。
4. ContentProvider （内容提供者）：将数据以类似数据库中表的方式暴露给别的应用程序，以实现共享数据。
5. 网络存储：使用 HTTP 协议或者 Socket 通信作为传输方式，HTTP 的底层也是通过短时间的 Socket 通信来实现的传输。


> 根据业务选着适当的存储方式需要考虑三个纬度

1. 安全
`SQLite -> SharePreferences -> ContentProvider -> File I/O -> 网络存储`
2. 效率
`SQLite -> SharePreferences = File I/O -> ContentProvider -> 网络存储`
3. 量极
`网络存储 -> File I/O -> ContentProvider = SQLite -> SharePreferences`

|存储方式|安全|效率|量级|
|:--:|:--:|:--:|:--:|
|网络存储|丢包、拦截问题|写入和读取网络环境速度|无限大|
|File I/O|sd 内存|存储大型数据文件|sd 卡内存|
|SQLite|App 内|管理数据处理最高效，不用于保存大数据文件|数据表格|
|ContentProvider|App 外标识符分辨|跨 App 传输数据，速度取决于存储数据的获取的类型和大小|取决于存储体|
|SharePreferences|App 内|配置存储，并非专门用于数据持久化存储|配置 XML|

## 二、greenDAO
> greenDAO 是一个对象关系映射 （ORM） 的框架，是目前众多 ORM 数据库中最稳定、速度最快、编写体验最好的框架，并支持 RxJava。

- [项目地址](https://github.com/greenrobot/greenDAO)
- [官方文档文档](http://greenrobot.org/greendao/documentation/)

**特点**
- 坚如磐石：greenDAO 自2011年以来一直存在，并被无数着名的应用程序使用
- 超级简单：简洁直接的 API，带有注释的V3
- 小：库的大小 <150K，它只是普通的 Java jar（没有CPU依赖的本机部分）
- 快速：可能是 Android 上最快的 ORM，由智能代码生成驱动
- 安全和富有表现力的查询 API：QueryBuilder 使用属性常量来避免拼写错误
- 强大的连接：跨实体查询，甚至是复杂关系的链接
- 灵活的属性类型：使用自定义类或枚举来表示实体中的数据
- 加密：支持 SQLCipher 加密数据库

**使用方法**

1. 引入框架

`build.gradle` 文件
```
buildscript {
    repositories {
        jcenter()
        mavenCentral() // add repository
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
}
```

`app/build.gradle` 文件
```
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin

dependencies {
    implementation 'org.greenrobot:greendao:3.2.2' // add library
}
```

2. 设置实体类

在 src 文件夹中，创建实体类 `Note.java`
```
@Entity(indexes = {
        @Index(value = "text, date DESC", unique = true)
})
public class Note {

    @Id
    private Long id;

    @NotNull
    private String text;
    private Date date;
}
```

在 Android Studio 中使用 Build> Make project。这会触发 greenDAO 生成 DAO 类,生成后的代码如下。
```
@Entity(indexes = {
        @Index(value = "text, date DESC", unique = true)
})
public class Note {

    @Id
    private Long id;

    @NotNull
    private String text;
    private Date date;

    @Generated(hash = 1501873839)
    public Note(Long id, @NotNull String text, Date date) {
        this.id = id;
        this.text = text;
        this.date = date;
    }

    @Generated(hash = 1272611929)
    public Note() {
    }

    public Long getId() {
        return this.id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getText() {
        return this.text;
    }

    public void setText(String text) {
        this.text = text;
    }

    public Date getDate() {
        return this.date;
    }

    public void setDate(Date date) {
        this.date = date;
    }

}
```

3. 在自定义的 `Application` 初始化

```
public class App extends Application {

    private DaoSession daoSession;

    @Override
    public void onCreate() {
        super.onCreate();

        // regular SQLite database
        DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this, "notes-db");
        Database db = helper.getWritableDb();

        // encrypted SQLCipher database
        // note: you need to add SQLCipher to your dependencies, check the build.gradle file
        // DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this, "notes-db-encrypted");
        // Database db = helper.getEncryptedWritableDb("encryption-key");

        daoSession = new DaoMaster(db).newSession();
    }

    public DaoSession getDaoSession() {
        return daoSession;
    }
}
```

4. 相应的 `onCreate` 里取得其应引用对象

```
    private NoteDao noteDao;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        DaoSession daoSession = ((App) getApplication()).getDaoSession();
        noteDao = daoSession.getNoteDao();
    }
```

5. 查找全部

```
    private TextView tvShow;
    private EditText editTextNote;
    private NoteDao noteDao;
    private Query<Note> notesQuery;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvShow = findViewById(R.id.tv_show);
        editTextNote = findViewById(R.id.editTextNote);

        DaoSession daoSession = ((App) getApplication()).getDaoSession();
        noteDao = daoSession.getNoteDao();
    }

    /**
     * 查找全部
     *
     * @param view
     */
    public void OnSelectClick(View view) {
        notesQuery = noteDao.queryBuilder().orderAsc(NoteDao.Properties.Text).build();
        List<Note> notes = notesQuery.list();
        tvShow.setText("");
        for (Note note : notes) {
            tvShow.append("id:" + note.getId() + " text:" + note.getText() + " data：" + note.getDate() + "\n");
        }
    }
```

![](https://upload-images.jianshu.io/upload_images/3304008-970532e35d9a6395.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 插入数据

```
        Note note = new Note();
        note.setText(editTextNote.getText().toString());
        note.setDate(new Date());
        noteDao.insert(note);
        editTextNote.setText("");
```

7. 删除数据

```
        Long noteId = Long.valueOf(editTextNote.getText().toString());
        noteDao.deleteByKey(noteId);
```

8. 例子源码

[TestGreenDAO](https://github.com/Demons96/TestPrograms/tree/master/TestGreenDAO)

## 三、参考

- Android 组件化架构-第二章-2.5数据存储
- [greenDAO 项目地址](https://github.com/greenrobot/greenDAO)
- [greenDAO 官方文档文档](http://greenrobot.org/greendao/documentation/)
