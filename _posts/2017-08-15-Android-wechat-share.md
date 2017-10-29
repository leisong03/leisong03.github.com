---
layout: post
title:  " Android wechat 分享"
date:   2017-08-15 19:00:00 +0800
categories: Android 
tags: Android 
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍`Android App`中接入`wechat`分享。

## `wechat`分享

### 微信开放平台

1.1 在微信开放平台申请成为开发者 [微信开放平台](https://open.weixin.qq.com/)

2.2 创建移动应用 -> 创建成功以后（七天之内） 

3.3 应用签名和包名，使用微信提供的签名生成工具获取应用签名和包名。[签名生成工具](https://open.weixin.qq.com/)

**注：App 要是release版本, debug版本不能正常分享**


### `wechat`分享开放

1.1 项目build.gradle 添加


```java

dependencies {
     compile 'com.tencent.mm.opensdk:wechat-sdk-android-with-mta:+'
}

```

**注：以往的libammsdk.jar 不建议使用，因为libammsdk.jar中的包名com.tencent.mm.sdk已更改为com.tencent.mm.opensdk。所以可能会出现冲突**

2.2 AndroidManifest.xml 配置

```java

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

```

3.3 在申请的包名创建子包wxapi包。

新建 WXEntryActivity,主要处理微信返回的数据

```java

/**
 * Created by chenjy on 2017/10/12.
 */

public class WXEntryActivity extends AppCompatActivity implements IWXAPIEventHandler {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_wxentry);
        WXActivity.api.handleIntent(getIntent(), this);
    }

    @Override
    public void onResp(BaseResp resp) { //在这个方法中处理微信传回的数据
        //形参resp 有下面两个个属性比较重要
        //1.resp.errCode
        //2.resp.transaction则是在分享数据的时候手动指定的字符创,用来分辨是那次分享(参照4.中req.transaction)
        switch (resp.errCode) { //根据需要的情况进行处理
            case BaseResp.ErrCode.ERR_OK:
                //正确返回
                break;
            case BaseResp.ErrCode.ERR_USER_CANCEL:
                //用户取消
                break;
            case BaseResp.ErrCode.ERR_AUTH_DENIED:
                //认证被否决
                break;
            case BaseResp.ErrCode.ERR_SENT_FAILED:
                //发送失败
                break;
            case BaseResp.ErrCode.ERR_UNSUPPORT:
                //不支持错误
                break;
            case BaseResp.ErrCode.ERR_COMM:
                //一般错误
                break;
            default:
                //其他不可名状的情况
                break;
        }
    }

    @Override
    public void onReq(BaseReq req) {
    }
}

``` 

4.4 创建一个 WXActivity 用于发送请求

```java

/**
 * Created by chenjy on 2017/10/12.
 */

public class WXActivity extends AppCompatActivity {

    public static IWXAPI api;//这个对象是专门用来向微信发送数据的一个重要接口,使用强引用持有,所有的信息发送都是基于这个对象的

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.share_popup);
        ButterKnife.inject(this);
        registerWeChat(this);
    }

    @OnClick({R.id.weichat,R.id.weichat_friend})
    void onActionViewClick(View view){
        Intent intent = null;
        switch (view.getId()){
            case R.id.weichat:
                sharePicByFile(true);
                break;
            case R.id.weichat_friend:
                sharePicByFile(false);
                break;

        }
    }

    public void registerWeChat(Context context) {   //向微信注册app
        api = WXAPIFactory.createWXAPI(context, WXConstants.APP_ID, true);
        api.registerApp(WXConstants.APP_ID);
    }

    /**
     * @param isShareFriend true 分享到朋友，false分享到朋友圈
     */
    public void sharePicByFile(boolean isShareFriend) {

        if (!api.isWXAppInstalled()) {
            Toast.makeText(WXActivity.this,"您还未安装微信", Toast.LENGTH_SHORT);
            return;
        }

        //创建一个WXWebPageObject对象，用于封装要发送的Url
        WXWebpageObject webpage = new WXWebpageObject();
        webpage.webpageUrl = "https://open.weixin.qq.com/";
        //创建一个WXMediaMessage对象
        WXMediaMessage msg = new WXMediaMessage(webpage);
        msg.title = "微信分享！";
        msg.description = "我在使用微信分享，快来加入我吧！";

        SendMessageToWX.Req req = new SendMessageToWX.Req();
        req.transaction = buildTransaction("webpage");//transaction字段用于唯一标识一个请求，这个必须有，否则会出错
        req.message = msg;

        req.scene = isShareFriend ? SendMessageToWX.Req.WXSceneSession : SendMessageToWX.Req.WXSceneTimeline;

        api.sendReq(req);
    }
    private String buildTransaction(final String type) {
        return (type == null) ? String.valueOf(System.currentTimeMillis()) : type + System.currentTimeMillis();
    }

}


```

5.5 配置AndroidManifest.xml

```java

 <activity android:name=".WXActivity"/>
 <activity android:name=".wxapi.WXEntryActivity"
            android:launchMode="singleTop"
            android:exported="true"/>   //注意这个属性一定是true,不然微信不能调用

			
```
			
![output](http://wx2.sinaimg.cn/mw690/c584f169ly1fksetdx8y5j206h0303yb.jpg)

其他分享示例可以参照，[其他格式示例](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317340&token=&lang=zh_CN)









