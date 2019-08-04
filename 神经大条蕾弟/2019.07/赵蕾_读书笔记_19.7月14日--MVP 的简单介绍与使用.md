MVP 的简单介绍与使用
-------------

# 简介:
> 本篇文章主要介绍 MVP(Model-View-Presenter). 使用 Java 语言.

# 目录:
[1.什么是 MVP](#1)

[2.MVP 特点](#2)

[3.MVP 简单使用](#3)


# <span id = "1">**1.什么是 MVP**</span>

### 角色:


![MVP 架构图示](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3454418372,1896139183&fm=26&gp=0.jpg)



&ensp;&ensp;MVP(Model-View-Presenter) 是 MVC 的演化版本, MVP 的角色定义如下:

- Model:
> 主要提供数据的存储功能.

- View:
> 负责处理用户事件和视图部分的展示.

- Presenter:
> 作为 View 和 Model 之间的桥梁.



# <span id = "2">**2.MVP 特点**</span>


1. Presenter 完全将 Model 和 View 进行了分离, 主要的程序逻辑在 Presenter里实现. 

2. Presenter 与具体的 View 是没有直接关联的, 而是通过定义好的接口进行交互, 从而使的在变更 View 时可以保持 Presenter 的不变, 这点符合面向接口的特点.

3. View 应该只有简单的 Set/Get 方法, 以及用户输入和设置界面显示的内容, 除此之外就不应该有更多内容. 绝不允许 View 直接访问 Model, 这就是其中与 MVC 的不同之处.


# <span id = "3">**3.MVP 简单使用**</span>


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


```

/**
 * 获取网络数据的接口类
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:26
 */

public interface NetTask<T> {
    void execute (T data, LoadTasksCallBack callBack);
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

    private LoadTasksCallBack loadTasksCallBack;

    private IpInfoTask(){

    }

    public static IpInfoTask getInstance(){
        if (INSTANCE == null){
            INSTANCE = new IpInfoTask();
        }
        return INSTANCE;
    }

    @Override
    public void execute(String ip, final LoadTasksCallBack loadTasksCallBack) {

        RequestParams requestParams = new RequestParams();
        requestParams.addFormDataPart("ip", ip);
        HttpRequest.post(HOST, requestParams, new BaseHttpRequestCallback<IpInfo>(){
            @Override
            public void onStart() {
                super.onStart();
                loadTasksCallBack.onStart();
            }

            @Override
            protected void onSuccess(IpInfo ipInfo) {
                super.onSuccess(ipInfo);
                loadTasksCallBack.onSuccess(ipInfo);
            }

            @Override
            public void onFinish() {
                super.onFinish();
                loadTasksCallBack.onFinish();
            }

            @Override
            public void onFailure(int errorCode, String msg) {
                super.onFailure(errorCode, msg);
                loadTasksCallBack.onFailed();
            }
        });

    }
}



```



## 实现Presenter


##### 接口


```

/**
 * 契约接口: 主要用于存放相同业务的 Presenter 和 View 的接口, 便于查找和维护
 *
 * @author 神经大条蕾弟
 * @date 2019/07/14 23:44
 */

public interface IpInfoContract {

    interface Presenter{
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

    public IpInfoPresenter (IpInfoContract.View addTaskView, NetTask netTask){
        this.netTask = netTask;
        this.addTaskView = addTaskView;
    }

    @Override
    public void getIpInfo(String ip) {
        netTask.execute(ip, this);
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
}




```



##### Activity

```

public class IpInfoActivity extends AppCompatActivity {

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

        IpInfoTask ipInfoTask = IpInfoTask.getInstance();
        ipInfoPresenter = new IpInfoPresenter(ipInfoFragment, ipInfoTask);
        ipInfoFragment.setPresenter(ipInfoPresenter);
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