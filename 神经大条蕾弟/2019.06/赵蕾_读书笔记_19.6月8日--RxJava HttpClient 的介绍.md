使用 RxJava 对 HttpClient 的运用
-------------

# 简介:
> 本篇文章主要讲解, HttpClient 在 RxJava 的简单使用.

# 目录:
[1.为什么要使用 RxJava](#1)

[2.HttpClient 的简单运用](#2)

[3.HttpClient 引入连接池](#3)


# <span id = "1">**1. 为什么要使用 RxJava**</span>

### HttpClient 介绍:

&ensp;&ensp;在网络框架中结合 RxJava 是很常见的一种情况. 上一篇文章我们介绍了 HttpClient 的作用以及简单的使用方式.
> 使用 Rxjava 用 create 操作符创建一个 Observable, 然后再 map 操作符中完成 HttpGet 请求, 把 CloseableHttpResponse 给观察者进行消费, 如果网络请求成功, 则打印服务器返回的 html 内容.


# <span id = "2">**2.HttpClient 的简单运用**</span>

### 例子:


`RxJavaClient`
```

import android.util.Log;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import io.reactivex.Maybe;
import io.reactivex.MaybeEmitter;
import io.reactivex.MaybeOnSubscribe;
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;

import static android.content.ContentValues.TAG;

/**
 * 使用RxJava 重构 HttpClient
 *
 * @author 神经大条蕾弟
 * @date 2019/06/08 17:56
 */

public class RxJavaClient {


    RxJavaClient(){
        request();
        requestMaybe();
    }

    private void requestMaybe() {
        Maybe.create(new MaybeOnSubscribe<String>() {
            @Override
            public void subscribe(MaybeEmitter<String> emitter) throws Exception {
                String url = "http://www.168.com";
                emitter.onSuccess(url);
            }
        }).map(new Function<String, CloseableHttpResponse>() {
            @Override
            public CloseableHttpResponse apply(String s) throws Exception {
                CloseableHttpClient client = HttpClients.createDefault();
                HttpGet get = new HttpGet(s);
                return client.execute(get);
            }
        }).subscribe(new Consumer<CloseableHttpResponse>() {
            @Override
            public void accept(CloseableHttpResponse response) throws Exception {

                //服务器返回码
                int statusCode = response.getStatusLine().getStatusCode();
                Log.e(TAG, "statusCode" + statusCode);
                HttpEntity entity = response.getEntity();

                //服务器返回内容
                String respStr = null;

                if (entity != null) {
                    respStr = EntityUtils.toString(entity, "UTF-8");
                }

                Log.e(TAG, respStr);

                //释放资源
                EntityUtils.consume(entity);

            }
        });
    }

    private void request() {
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {
                String url = "http://www.163.com";
                emitter.onNext(url);
            }
        }).map(new Function<String, CloseableHttpResponse>() {
            @Override
            public CloseableHttpResponse apply(String s) throws Exception {
                CloseableHttpClient client = HttpClients.createDefault();
                HttpGet get = new HttpGet(s);
                return client.execute(get);
            }
        }).subscribe(new Consumer<CloseableHttpResponse>() {
            @Override
            public void accept(CloseableHttpResponse response) throws Exception {
                //服务器返回码
                int statusCode = response.getStatusLine().getStatusCode();
                Log.e(TAG, "statusCode: " + statusCode);

                HttpEntity entity = response.getEntity();

                //服务器返回内容
                String respStr = null;

                if (respStr != null) {
                    respStr = EntityUtils.toString(entity, "UTF-8");
                }

                Log.e(TAG, respStr);


                //释放资源
                EntityUtils.consume(entity);

            }
        });
    }


}


```






# <span id = "3">**3.HttpClient 引入连接池**</span>




`TestHttpClientWithPoolAndMaybe`


```


import org.apache.http.HttpEntity;
import org.apache.http.ParseException;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

import io.reactivex.Maybe;
import io.reactivex.MaybeEmitter;
import io.reactivex.MaybeOnSubscribe;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;

import static android.content.ContentValues.TAG;

/**
 * 使用连接池的 Rxjava HttpClient
 *
 * @author 神经大条蕾弟
 * @date 2019/06/08 20:05
 */

public class TestHttpClientWithPoolAndMaybe {

    /**
     * 全局连接池对象
     */
    private static final PoolingHttpClientConnectionManager connManager = new PoolingHttpClientConnectionManager();

    /**
     * 静态代码块配置连接池信息
     */
    static {
        //设置最大连接数
        connManager.setMaxTotal(200);
        //设置每个连接的路由数
        connManager.setDefaultMaxPerRoute(20);
    }

    TestHttpClientWithPoolAndMaybe(){
        httpGet("http://www.163.com", 6000)
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        Log.e(TAG, s);
                    }
                });
    }

    public static CloseableHttpClient getHttpClient(int timeOut){

        //创建 Http 请求配置参数
        RequestConfig requestConfig = RequestConfig.custom()
                //获取连接超时时间
                .setConnectionRequestTimeout(timeOut)
                //请求超时时间
                .setConnectTimeout(timeOut)
                //响应超时时间
                .setSocketTimeout(timeOut)
                .build();

        //创建 HttpClient
        return HttpClients.custom()
                //把请求相关的超时信息设置到连接客户端
                .setDefaultRequestConfig(requestConfig)
                //请求重试设置到连接客户端
                .setRetryHandler(new RetryHandler())
                //配置连接池管理对象
                .setConnectionManager(connManager)
                .build();

    }

    public static Maybe<String> httpGet(final String url, final int timeOut){

        return Maybe.create(new MaybeOnSubscribe<String>() {
            @Override
            public void subscribe(MaybeEmitter<String> emitter) throws Exception {
                emitter.onSuccess(url);
            }
        }).map(new Function<String, String>() {
            @Override
            public String apply(String s) throws Exception {

                //获取客户端连接对象
                CloseableHttpClient httpClient = getHttpClient(timeOut);
                //创建Get请求对象
                HttpGet httpGet = new HttpGet(url);

                CloseableHttpResponse response = null;
                String msg = null;
                try {
                    //执行请求
                    response = httpClient.execute(httpGet);
                    //获取响应实体
                    HttpEntity entity = response.getEntity();
                    //获取响应信息
                    msg = EntityUtils.toString(entity, "UTF-8");
                } catch (ClientProtocolException e) {
                    Log.e(TAG, "协议错误");
                    e.printStackTrace();
                } catch (ParseException e) {
                    Log.e(TAG, "解析错误");
                    e.printStackTrace();
                } catch (IOException e) {
                    Log.e(TAG, "I/O错误");
                    e.printStackTrace();
                } finally {
                    if (response != null) {
                        try{
                            EntityUtils.consume(response.getEntity());
                            response.close();
                        } catch (IOException e){
                            Log.e(TAG, "释放链接错误" );
                        }
                    }
                }

                return msg;

            }
        });

    }

}




```

