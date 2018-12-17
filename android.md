# 签名工具
>在Android Studio中点击菜单 Build->Generate signed apk... 打包签名过程中,

>可以看到两种签名选项 V1(Jar Signature)  V2(Full APK Signature),
刚开始升级AS看到这个懵了,既然是APK Signature,就放心偷懒选了V2,结果安装失败？？？无奈,只能查资料...

>从Android 7.0开始, 谷歌增加新签名方案 V2 Scheme (APK Signature);
但Android 7.0以下版本, 只能用旧签名方案 V1 scheme (JAR signing)

>V1签名:
    来自JDK(jarsigner), 对zip压缩包的每个文件进行验证, 签名后还能对压缩包修改(移动/重新压缩文件)
    对V1签名的apk/jar解压,在META-INF存放签名文件(MANIFEST.MF, CERT.SF, CERT.RSA), 
    其中MANIFEST.MF文件保存所有文件的SHA1指纹(除了META-INF文件), 由此可知: V1签名是对压缩包中单个文件签名验证
    
>V2签名:
    来自Google(apksigner), 对zip压缩包的整个文件验证, 签名后不能修改压缩包(包括zipalign),
    对V2签名的apk解压,没有发现签名文件,重新压缩后V2签名就失效, 由此可知: V2签名是对整个APK签名验证
    
    V2签名优点很明显:
        签名更安全(不能修改压缩包)
        签名验证时间更短(不需要解压验证),因而安装速度加快

>注意: apksigner工具默认同时使用V1和V2签名,以兼容Android 7.0以下版本


***
## 签名生成
1.方法一(jarsigner,只支持V1签名)
    进入JDK/bin, 输入命令
    jarsigner -keystore 密钥库名 xxx.apk 密钥别名
    
    从JDK7开始, jarsigner默认算法是SHA256, 但Android 4.2以下不支持该算法,
    所以需要修改算法, 添加参数 -digestalg SHA1 -sigalg SHA1withRSA
    jarsigner -keystore 密钥库名 -digestalg SHA1 -sigalg SHA1withRSA xxx.apk 密钥别名
    
    参数:
        -digestalg  摘要算法
        -sigalg     签名算法
    
    例如:
        用JDK7及以上jarsigner签名,不支持Android 4.2 以下
        jarsigner -keystore debug.keystore MyApp.apk androiddebugkey
        
        用JDK7及以上jarsigner签名,兼容Android 4.2 以下            
        jarsigner -keystore debug.keystore -digestalg SHA1 -sigalg SHA1withRSA MyApp.apk androiddebugkey
                
2.方法二(apksigner,默认同时使用V1和V2签名)
    进入Android SDK/build-tools/SDK版本, 输入命令
    apksigner sign --ks 密钥库名 --ks-key-alias 密钥别名 xxx.apk
    
    若密钥库中有多个密钥对,则必须指定密钥别名
    apksigner sign --ks 密钥库名 --ks-key-alias 密钥别名 xxx.apk
    
    禁用V2签名
    apksigner sign --v2-signing-enabled false --ks 密钥库名 xxx.apk
                    
    参数:
        --ks-key-alias       密钥别名,若密钥库有一个密钥对,则可省略,反之必选
        --v1-signing-enabled 是否开启V1签名,默认开启
        --v2-signing-enabled 是否开启V2签名,默认开启
    
    例如:
        在debug.keystore密钥库只有一个密钥对
        apksigner sign --ks debug.keystore MyApp.apk
        
        在debug.keystore密钥库中有多个密钥对,所以必须指定密钥别名
        apksigner sign --ks debug.keystore --ks-key-alias androiddebugkey MyApp.apk

***
## 获取sha1
> keytool -list -v -keystore debug.keystore