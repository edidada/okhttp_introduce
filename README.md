# okhttp_introduce

okhttp_v3.9.1

OpenSSLSocketImpl
https://stackoverflow.com/questions/30178294/android-custom-socket-deriving-from-opensslsocketimpl-not-sslsocket

DefaultSocketFactory
https://www.cnblogs.com/maxudong/p/8385018.html

https
RealConnection 
establishProtocol()
client hello


Android平台OKHttp库介绍

[郭孝星okhttp源码解析参考网页](https://juejin.im/post/5a704ed05188255a8817f4c9)

Call类图

![Call类图](https://github.com/edidada/okhttp_introduce/blob/master/call.jpg "Call类图")

插值器类图
![插值器类图](https://github.com/edidada/okhttp_introduce/blob/master/interceptor.jpg "插值器类图")

```java
public class MainActivity extends AppCompatActivity {

    OkHttpClient client;
    private static final int base = 0;
    private static final int Network = base +1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ConnectionSpec spec = new ConnectionSpec.Builder(ConnectionSpec.MODERN_TLS)
                .tlsVersions(TlsVersion.TLS_1_2)
                .cipherSuites(
                       // CipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                       // CipherSuite.TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,
                        CipherSuite.TLS_DHE_RSA_WITH_AES_128_GCM_SHA256)
                .build();

        client = new OkHttpClient.Builder()
                .connectionSpecs(Collections.singletonList(spec))
                .build();

        final Handler h = new Handler(){
            @Override
            public void handleMessage(Message msg) {
                if(msg == null){
                    return;
                }
                super.handleMessage(msg);
                switch (msg.what) {
                    case Network:
                        ;System.out.println(msg.obj);
                }
            }
        };
        findViewById(R.id.bt_startHttps).setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View view) {
                new Thread(new Runnable(){
                    @Override
                    public void run() {
                        try {
                            String s = runs("https://www.edidada.cn/");
                            Message m = new Message();
                            m.what = Network;
                            m.obj = s;
                            h.sendMessage(m);
                        }catch (Exception e){
                            e.printStackTrace();
                        }
                    }
                }).start();
            }
        });

    }

    String runs(String url) throws IOException {
        Request request = new Request.Builder()
                .url(url)
                .build();

        Response response = client.newCall(request).execute();
        RealConnection r;
        return response.body().string();
    }
    CipherSuite s;
}
```

```shell
04-09 15:59:12.458 3002-3045/com.example.okhttpstest W/System.err: javax.net.ssl.SSLHandshakeException: Handshake failed
04-09 15:59:12.458 3002-3045/com.example.okhttpstest W/System.err:     at com.android.org.conscrypt.OpenSSLSocketImpl.startHandshake(OpenSSLSocketImpl.java:396)
04-09 15:59:12.458 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.RealConnection.connectTls(RealConnection.java:299)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.RealConnection.establishProtocol(RealConnection.java:268)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.RealConnection.connect(RealConnection.java:160)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.StreamAllocation.findConnection(StreamAllocation.java:256)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.StreamAllocation.findHealthyConnection(StreamAllocation.java:134)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.StreamAllocation.newStream(StreamAllocation.java:113)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.connection.ConnectInterceptor.intercept(ConnectInterceptor.java:42)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:147)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:121)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.cache.CacheInterceptor.intercept(CacheInterceptor.java:93)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:147)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:121)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.BridgeInterceptor.intercept(BridgeInterceptor.java:93)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:147)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RetryAndFollowUpInterceptor.intercept(RetryAndFollowUpInterceptor.java:125)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:147)
04-09 15:59:12.459 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.java:121)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.RealCall.getResponseWithInterceptorChain(RealCall.java:200)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at okhttp3.RealCall.execute(RealCall.java:77)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at com.example.okhttpstest.MainActivity.runs(MainActivity.java:82)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at com.example.okhttpstest.MainActivity$2$1.run(MainActivity.java:62)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at java.lang.Thread.run(Thread.java:818)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err: Caused by: javax.net.ssl.SSLProtocolException: SSL handshake terminated: ssl=0xae7a0f00: Failure in SSL library, usually a protocol error
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err: error:100c5410:SSL routines:ssl3_read_bytes:SSLV3_ALERT_HANDSHAKE_FAILURE (external/boringssl/src/ssl/s3_pkt.c:972 0xae4944e0:0x00000001)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err: error:100c009f:SSL routines:ssl3_get_server_hello:HANDSHAKE_FAILURE_ON_CLIENT_HELLO (external/boringssl/src/ssl/s3_clnt.c:750 0xaac45d97:0x00000000)
04-09 15:59:12.460 3002-3045/com.example.okhttpstest W/System.err:     at com.android.org.conscrypt.NativeCrypto.SSL_do_handshake(Native Method)
04-09 15:59:12.461 3002-3045/com.example.okhttpstest W/System.err:     at com.android.org.conscrypt.OpenSSLSocketImpl.startHandshake(OpenSSLSocketImpl.java:324)
04-09 15:59:12.461 3002-3045/com.example.okhttpstest W/System.err: 	... 22 more
```