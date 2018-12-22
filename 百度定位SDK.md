# 百度定位

1.添加SDK（jar + so

2.配置build.gradle文件
```
sourceSets{
        main{
                 jnilibs.srcDir 'libs'
                 jni.srcDirs = []    //disable automatic ndk-build
         }
}

dependencies {
        compile fileTree(dir:  'libs' , include: ['*.jar'])
        androidTestCompile ('com.android.support.test.espresso: espresso-core: 2.2.2', {
                  exclude group: 'com.android.support',  module: 'support-annotations'
         })
        compile 'com.android.support: appcompat-v7:25.1.0'
        testCompile 'junit: junit: 4.12'
}
```
3.添加AK
>Android定位SDK自v4.0版本起，需要进行AK鉴权。开发者在使用SDK前，需完成AK申请，并在AndroidManifest.xml文件中，正确填写AK。
在Application标签中增加如下代码：
```
<meta-data
    android:name="com.baidu.lbsapi.API_KEY"
    android:value="AK" >
</meta-data>
```

4.添加定位权限


使用定位SDK，需在Application标签中声明service组件，每个App拥有自己单独的定位service，代码如下：
```
<service android:name="com.baidu.location.f" android:enabled="true" android:process=":remote"> </service>
```
除添加service组件外，使用定位SDK还需添加如下权限：
***
```
<!-- 这个权限用于进行网络定位-->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"></uses-permission>
<!-- 这个权限用于访问GPS定位-->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"></uses-permission>
<!-- 用于访问wifi网络信息，wifi信息会用于进行网络定位-->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"></uses-permission>
<!-- 获取运营商信息，用于支持提供运营商信息相关的接口-->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
<!-- 这个权限用于获取wifi的获取权限，wifi信息会用来进行网络定位-->
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"></uses-permission>
<!-- 用于读取手机当前的状态-->
<uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>
<!-- 写入扩展存储，向扩展卡写入数据，用于写入离线定位数据-->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
<!-- 访问网络，网络定位需要上网-->
<uses-permission android:name="android.permission.INTERNET" />
<!-- SD卡读取权限，用户写入离线定位数据-->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
```
>注意 build.gradle中需要添加
```
android{
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
}
```
***
***
# 代码流程
### 1 第一步 获取AK 配置定位SDK环境

### 2 第二步 初始化LocationClient类
请在主线程中声明LocationClient类对象，该对象初始化需传入Context类型参数。推荐使用getApplicationConext()方法获取全进程有效的Context。
核心代码段如下：
```
public LocationClient mLocationClient = null;
private MyLocationListener myListener = new MyLocationListener();
//BDAbstractLocationListener为7.2版本新增的Abstract类型的监听接口
//原有BDLocationListener接口暂时同步保留。具体介绍请参考后文第四步的说明
public void onCreate() {
    mLocationClient = new LocationClient(getApplicationContext());     
    //声明LocationClient类
    mLocationClient.registerLocationListener(myListener);    
    //注册监听函数
}
```
### 3 第三步 配置定位SDK参数

### 4 第四步 实现BDAbstractLocationListener接口

### 5 第五步 获取定位经纬度
最后，只需发起定位，便能够从BDAbstractLocationListener监听接口中获取定位结果信息。核心代码如下：
```
mLocationClient.start();
//mLocationClient为第二步初始化过的LocationClient对象
//调用LocationClient的start()方法，便可发起定位请求
```
start()：启动定位SDK；stop()：关闭定位SDK。调用start()之后只需要等待定位结果自动回调即可。

开发者定位场景如果是单次定位的场景，在收到定位结果之后直接调用stop()函数即可。

如果stop()之后仍然想进行定位，可以再次start()等待定位结果回调即可。

自V7.2版本起，新增LocationClient.reStart()方法，用于在某些特定的异常环境下重启定位。

如果开发者想按照自己逻辑请求定位，可以在start()之后按照自己的逻辑请求LocationClient.requestLocation()函数，会主动触发定位SDK内部定位逻辑，等待定位回调即可。