Android开发 MVP + RxJava + Retrofit + Dagger2的简单实现
-------------

# 简介:
> 本篇文章主要介绍 MVP 结合 Rxjava + Retrofit 与 Dagger2 的简单实现.

# 目录:
[1.为什么要使用 Dagger2](#1)

[2.简单实现](#2)


# <span id = "1">**1.为什么要使用 Dagger2**</span>

### 介绍:

&ensp;&ensp;Dagger2 是什么不赘述:

- [Dagger2 介绍](https://www.jianshu.com/p/2cd491f0da01)
> Dagger2是一个依赖注入框架。


### 优点:

- 便于简化代码
- 便于复杂依赖关系的解耦.
- 便于对象的管理.

# <span id = "2">**2.简单实现**</span>


## 添加配置

> project 的 build.gradle 添加配置

```

buildscript {
    
    dependencies {

        classpath'com.neenbedankt.gradle.plugins:android-apt:1.8'
        ...

    }
}




```



> app 的 build.gradle 添加配置


```


    implementation 'cn.finalteam:okhttpfinal:2.0.7'

    implementation 'io.reactivex:rxjava:1.2.0'
    implementation 'io.reactivex:rxandroid:1.2.1'
    implementation 'com.squareup.retrofit2:retrofit:2.1.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.1.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava:2.1.0'

    compile 'com.google.dagger:dagger:2.7'
    annotationProcessor 'com.google.dagger:dagger-compiler:2.7'

```


## 编写 Dagger2 的依赖注入工具类与接口



```

/**
 * NetTaskModule 的Component
 *
 * @author 神经大条蕾弟
 * @date 2019/07/28 16:42
 */
@Singleton
@Component(modules =NetTaskModule.class)
public interface NetTaskComponent {
    NetTask getNetTask();
}



```




```

/**
 * Dagger2 的依赖注入工厂类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/28 16:37
 */

@Module
public class NetTaskModule {

    @Singleton
    @Provides
    NetTask provideIpInfoTask(){
        return new IpInfoTask();
    }

}




```



```


/**
 * 为了方便管理 Component, 创建 FragmentScoped 注解
 *
 * @author 神经大条蕾弟
 * @date 2019/07/28 16:59
 */
@Documented
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface FragmentScoped {

}



```



```


/**
 * 用于提供 IpInfoContract.View 的类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/28 17:03
 */
@Module
public class IpInfoModule {
    private IpInfoContract.View mView;
    public IpInfoModule(IpInfoContract.View mView){
        this.mView = mView;
    }
    @Provides
    IpInfoContract.View provideIpInfoContract(){
        return mView;
    }
}



```


```


/**
 * IpInfoComponent 接口
 *
 * @author 神经大条蕾弟
 * @date 2019/07/28 17:11
 */
@FragmentScoped
@Component(dependencies = NetTaskComponent.class, modules = IpInfoModule.class)
public interface IpInfoComponent {
    void inject(IpInfoActivity ipInfoActivity);
}



```



## 实现Model


##### 实体类

```

/**
 * 实体类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/15 00:35
 */

public class IpData {
    private String country;
    private String area;
    private String city;

    public String getArea() {
        return area;
    }

    public void setArea(String area) {
        this.area = area;
    }

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }
}



```



```

/**
 * IP信息实体类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:23
 */

public class IpInfo {
    private int code;
    private IpData data;

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public IpData getData() {
        return data;
    }

    public void setData(IpData data) {
        this.data = data;
    }

}



```



##### 接口

> 定义访问网络的接口, getIpInfo 方法返回 Observable 类型是为了支持 RxJava

```


/**
 * 定义 Retrofit 用于访问网络的接口
 *
 * @author 神经大条蕾弟
 * @date 2019/07/21 15:07
 */

public interface IpService {
    @FormUrlEncoded
    @POST("getIpInfo.php")
    Observable<IpInfo> getIpInfo(@Field("ip") String ip);
}




```


> 返回的 Subscription 也是为了支持 RxJava

```

/**
 * 获取网络数据的接口类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:26
 */

public interface NetTask<T> {
    Subscription execute (T data, LoadTasksCallBack callBack);
}



```



##### 回调


```

/**
 * 网络访问的回调
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:28
 */

public interface LoadTasksCallBack<T> {
    void onSuccess(T t);
    void onStart();
    void onFailed();
    void onFinish();
}




```


##### 获取的数据的实现类


> IpInfoTask 的变化主要是使用了 RxJava 和 Retrofit 替代 OkHttpFinal 来访问网络, 通过调用 createRetrofit() 方法来创建和配置 Retrofit. 在 executr 方法中使用 RxJava 结合 Retrofit 来访问网络, 并返回 Subscription.

```

/**
 * NetTask 的实现类以获取数据
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:30
 */

public class IpInfoTask implements NetTask<String> {

    private static IpInfoTask INSTANCE = null;
    private static final String HOST = "http://ip.taobao.com/service/getIpInfo.php";

    private Retrofit retrofit;

    private LoadTasksCallBack loadTasksCallBack;

    private IpInfoTask(){
        createRetrofit();
    }

    private void createRetrofit() {
        retrofit = new Retrofit.Builder().
                baseUrl(HOST).
                addConverterFactory(GsonConverterFactory.create()).
                addCallAdapterFactory(RxJavaCallAdapterFactory.create()).
                build();
    }

    public static IpInfoTask getInstance(){
        if (INSTANCE == null){
            INSTANCE = new IpInfoTask();
        }
        return INSTANCE;
    }

    @Override
    public Subscription execute(String ip, final LoadTasksCallBack loadTasksCallBack) {

        IpService ipService = retrofit.create(IpService.class);
        Subscription subscription = ipService.getIpInfo(ip).subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Subscriber<IpInfo>() {
                    @Override
                    public void onStart() {
                        loadTasksCallBack.onStart();
                    }

                    @Override
                    public void onCompleted() {
                        loadTasksCallBack.onFinish();
                    }

                    @Override
                    public void onError(Throwable e) {
                        loadTasksCallBack.onFailed();
                    }

                    @Override
                    public void onNext(IpInfo ipInfo) {
                        loadTasksCallBack.onSuccess(ipInfo);
                    }
                });

            return subscription;

    }
}




```



## 实现Presenter


##### 接口

> 为了能方便管理 RxJava 的订阅和取消订阅, 定义 base presenter

```

/**
 * 定义 base presenter
 *
 * @author 神经大条蕾弟
 * @date 2019/07/21 15:36
 */

public interface BasePresenter {

    void subscribe();
    void unsubscribe();

}



```



```

/**
 * 契约接口: 主要用于存放相同业务的 Presenter 和 View 的接口, 便于查找和维护
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:44
 */

public interface IpInfoContract {

    interface Presenter extends BasePresenter{
        void getIpInfo(String ip);
    }

    interface View extends BaseView<Presenter>{
        void setIpInfo(IpInfo ipInfo);
        void showLoading();
        void hideLoading();
        void showError();
        boolean isActive();
    }

}



```


```


/**
 * View 的基础类, 给View 绑定 Presenter
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:48
 */

public interface BaseView<T> {
    void setPresenter(T presenter);
}



```


##### presenter 实现类

```


/**
 * Presenter
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:55
 */

public class IpInfoPresenter implements IpInfoContract.Presenter, LoadTasksCallBack<IpInfo> {

    private NetTask netTask;
    private IpInfoContract.View addTaskView;
    private CompositeSubscription mSubscriptions;
    private Subscription subscription;

    @Inject
    public IpInfoPresenter (IpInfoContract.View addTaskView, NetTask netTask){
        this.netTask = netTask;
        this.addTaskView = addTaskView;
        mSubscriptions = new CompositeSubscription();
    }

    @Inject
    void setPresenter(){
        addTaskView.setPresenter(this);
    }

    @Override
    public void getIpInfo(String ip) {
        subscription = netTask.execute(ip, this);
        subscribe();
    }

    @Override
    public void onSuccess(IpInfo ipInfo) {
        if (addTaskView.isActive()){
            addTaskView.setIpInfo(ipInfo);
        }
    }

    @Override
    public void onStart() {
        if (addTaskView.isActive()){
            addTaskView.showLoading();
        }
    }

    @Override
    public void onFailed() {
        if (addTaskView.isActive()){
            addTaskView.showError();
            addTaskView.hideLoading();
        }
    }

    @Override
    public void onFinish() {
        if (addTaskView.isActive()){
            addTaskView.hideLoading();
        }
    }

    @Override
    public void subscribe() {
        if (subscription != null){
            mSubscriptions.add(subscription);
        }
    }

    @Override
    public void unsubscribe() {
        if (mSubscriptions != null && mSubscriptions.hasSubscriptions()){
            mSubscriptions.unsubscribe();
        }
    }
}





```


## 实现 View

##### Fragment

```

/**
 * View 层碎片
 * @author 神经大条蕾弟
 * @date   2019/7/15 0015 0:13
 */
public class IpInfoFragment extends Fragment implements IpInfoContract.View {

    private TextView tv_country;
    private TextView tv_area;
    private TextView tv_city;
    private Button bt_ipinfo;
    private Dialog mDialog;
    private IpInfoContract.Presenter mPresenter;

    public static IpInfoFragment newInstance(){
        return new IpInfoFragment();
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View root = inflater.inflate(R.layout.fragment_ip_info, container, false);
        tv_country = (TextView) root.findViewById(R.id.tv_country);
        tv_area = (TextView) root.findViewById(R.id.tv_area);
        tv_city = (TextView) root.findViewById(R.id.tv_city);
        bt_ipinfo = (Button) root.findViewById(R.id.btn_ipinf);

        return root;
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        mDialog = new ProgressDialog(getActivity());
        mDialog.setTitle("获取数据中");
        bt_ipinfo.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mPresenter.getIpInfo("39.155.184.147");
            }
        });
    }

    @Override
    public void setPresenter(IpInfoContract.Presenter presenter) {
        mPresenter = presenter;
    }

    @Override
    public void setIpInfo(IpInfo ipInfo) {
        if (ipInfo != null && ipInfo.getData() != null){
            IpData ipData = ipInfo.getData();
            tv_country.setText(ipData.getCountry());
            tv_area.setText(ipData.getArea());
            tv_city.setText(ipData.getCity());
        }
    }

    @Override
    public void showLoading() {
        mDialog.show();
    }

    @Override
    public void hideLoading() {
        if (mDialog.isShowing()) {
            mDialog.dismiss();
        }
    }

    @Override
    public void showError() {
        Toast.makeText(getActivity().getApplicationContext(), "网络出错", Toast.LENGTH_SHORT).show();
    }

    @Override
    public boolean isActive() {
        return isAdded();
    }

    @Override
    public void onPause() {
        super.onPause();
        mPresenter.unsubscribe();
    }


}




```



##### Activity

```

public class IpInfoActivity extends AppCompatActivity {

    @Inject
    private IpInfoPresenter ipInfoPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_ip_info);

        IpInfoFragment ipInfoFragment = (IpInfoFragment) getSupportFragmentManager().findFragmentById(R.id.contentFrame);

        if (ipInfoFragment == null){
            ipInfoFragment = ipInfoFragment.newInstance();
            ActivityUtils.addFragmentToActivity(getSupportFragmentManager(), ipInfoFragment, R.id.contentFrame);
        }

//        IpInfoTask ipInfoTask = IpInfoTask.getInstance();
//        ipInfoPresenter = new IpInfoPresenter(ipInfoFragment, ipInfoTask);
//        ipInfoFragment.setPresenter(ipInfoPresenter);

        DaggerIpInfoComponent.builder().ipInfoModule(new IpInfoModule(ipInfoFragment))
                .netTaskComponent(((MvpApplication) getApplication()).getTasksRepositoryComponent())
                .build()
                .inject(this);

    }
}



```


##### 工具类

```


/**
 * 加载碎片工具类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/15 00:48
 */

public class ActivityUtils {

    public static void addFragmentToActivity(FragmentManager fragmentManager, Fragment fragment, int frameId){
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.add(frameId, fragment);
        transaction.commit();
    }

}



```


##### Application

```


/**
 * 自定义Application
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:20
 */
public class MvpApplication extends Application {

    private NetTaskComponent netTaskComponent;

    @Override
    public void onCreate() {
        super.onCreate();

        netTaskComponent = DaggerNetTaskComponent.builder().netTaskModule(new NetTaskModule()).build();

    }

    public NetTaskComponent getTasksRepositoryComponent(){
        return netTaskComponent;
    }

}



```


