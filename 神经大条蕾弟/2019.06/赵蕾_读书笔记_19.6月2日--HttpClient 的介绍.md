HttpClient 的介绍
-------------

# 简介:
> 本篇文章主要讲解, HttpClient 的简单使用.

# 目录:
[1.什么是 HttpClient ?](#1)

[2.HttpClient 的简单运用](#2)

[3.HttpClient 引入连接池](#3)


# <span id = "1">**1.什么是 HttpClient ?**</span>

### HttpClient 介绍:

&ensp;&ensp;HttpClient 是不是非常眼熟呢? 这个 HttpClient 在 `okhttp` 和 `Retrofit` 里面都有用到, 在 Android 端使用的非常广泛.

> HttpClient 是 Apache 公司的网络请求框架. HttpClient 常用与服务端和客户端.


### 引入依赖:


```

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile "org.apache.httpcomponents:httpclient:4.5.2"
	...
}


```


# <span id = "2">**2.HttpClient 的简单运用**</span>

### 例子:


`HttpClientsUtil`
```

package com.orange.rxjavahttpclient.httpClient;

import android.util.Log;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import java.io.IOException;

import static android.content.ContentValues.TAG;

/**
 * HttpClients例子
 *
 * @author 神经大条蕾弟
 * @date 2019/06/02 15:37
 */

public class HttpClientsUtil {

    HttpEntity entity = null;

    public HttpClientsUtil(){
        try {
            String url = "http://www.163.com";

            

            HttpGet get = new HttpGet(url);

            CloseableHttpResponse response = client.execute(get);

            //服务器返回码
            int statusCode = response.getStatusLine().getStatusCode();
            Log.e(TAG, statusCode + "");

            //服务器响应成功
            if (statusCode == 200){
                //服务器返回内容
                String respStr = null;
                entity = response.getEntity();
                if (entity != null) {
                    respStr = EntityUtils.toString(entity, "UTF-8");
                }
                Log.e(TAG, respStr);
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (entity != null){
                //释放资源
                try {
                    EntityUtils.consume(entity);
                } catch (IOException e){
                    e.printStackTrace();
                }
            }
        }
    }

}


```



> HttpClient 提供了 CloseableHttpClient 实例的工厂方法. 


```

CloseableHttpClient client = HttpClients.createDefault();


```



# <span id = "3">**3.HttpClient 引入连接池**</span>


> 注释写的很清楚了, 不赘述.



`TestHttpClientWithPool`


```


package com.orange.rxjavahttpclient.httpClient;

import android.util.Log;

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

import static android.content.ContentValues.TAG;

/**
 * HttpClient连接池
 *
 * @author 神经大条蕾弟
 * @date 2019/06/02 15:54
 */

public class TestHttpClientWithPool {

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

    public TestHttpClientWithPool(){
        httpGet("http://www.163.com", 6000);
    }

    /**
     * 获取Http客户端连接对象
     *
     * @param timeOut 超时时间
     * @return http 客户端连接对象
     */
    public static CloseableHttpClient getHttpClient(int timeOut) {
        //创建Http请求配置参数
        RequestConfig requestConfig = RequestConfig.custom()
                //获取连接超时时间
                .setConnectionRequestTimeout(timeOut)
                //请求超时时间
                .setConnectTimeout(timeOut)
                //响应超时时间
                .setSocketTimeout(timeOut)
                .build();

        //创建HttpClient
        return HttpClients.custom()
                //请求相关的超时信息设置到客户端
                .setDefaultRequestConfig(requestConfig)
                //把请求重试设置到连接客户端
                .setRetryHandler(new RetryHandler())
                //配置连接池管理对象
                .setConnectionManager(connManager)
                .build();
    }

    public static String httpGet(String url, int timeOut) {
        String msg = null;

        //获取客户端连接对象
        CloseableHttpClient httpClient = getHttpClient(timeOut);
        //创建Get请求对象
        HttpGet httpGet = new HttpGet(url);

        CloseableHttpResponse response = null;

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


}



```



`RetryHandler`
```

package com.orange.rxjavahttpclient.httpClient;

import org.apache.http.HttpEntity;
import org.apache.http.HttpEntityEnclosingRequest;
import org.apache.http.HttpRequest;
import org.apache.http.NoHttpResponseException;
import org.apache.http.client.HttpRequestRetryHandler;
import org.apache.http.client.protocol.HttpClientContext;
import org.apache.http.conn.ConnectTimeoutException;
import org.apache.http.protocol.HttpContext;

import java.io.IOException;
import java.io.InterruptedIOException;
import java.net.UnknownHostException;

import javax.net.ssl.SSLException;
import javax.net.ssl.SSLHandshakeException;

/**
 * 是否重连
 *
 * @author 神经大条蕾弟
 * @date 2019/06/02 16:28
 */

public class RetryHandler implements HttpRequestRetryHandler {

    @Override
    public boolean retryRequest(IOException exception, int executionCount, HttpContext context) {

        if (executionCount >= 3){//如果已经重试了3次, 就放弃
            return false;
        }

        if (exception instanceof NoHttpResponseException){//如果服务器丢掉了连接, 那么就重试
            return true;
        }

        if (exception instanceof SSLHandshakeException){//不要重试SSL握手异常
            return false;
        }

        if (exception instanceof InterruptedIOException){//超时
            return true;
        }

        if (exception instanceof UnknownHostException){//目标服务器不可达
            return false;
        }

        if (exception instanceof ConnectTimeoutException){//连接被拒绝
            return false;
        }

        if (exception instanceof SSLException){//ssl握手异常
            return false;
        }

        HttpClientContext clientContext = HttpClientContext.adapt(context);
        HttpRequest request = clientContext.getRequest();

        //如果请求是幂等的, 就再次尝试
        if (!(request instanceof HttpEntityEnclosingRequest)){
            return true;
        }

        return false;
    }

}



```