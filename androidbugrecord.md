android studio 3.0 以上经常遇到以前的项目aapt2 而且解决方法都不相同，这里做一下记录  
1.资源包重复，利用屏蔽部分三方引用逐步排除，再忽略重复资源包  
2.androidx冲突，利用屏蔽部分三方引用逐步排除，将升级到androidx三方引用固定引用版本  
3.突发性aapt2,clean,invalidata caches,以及终极方法，c:\user\.gradle\cache删掉  
4.资源检查不完整，在build 添加
```JAVA
android{
 aaptOptions.cruncherEnabled = false
    aaptOptions.useNewCruncher = false}
```
9.0系统新特性,http无法访问网络,只支持https，并且前台服务也有权限限制，为了保持兼容，以下步骤  
1.build 添加 
```GRADLE
android{useLibrary 'org.apache.http.legacy'}
```
2.在 androidManifest 下
```XML
<!--android 9.0上使用前台服务，需要添加权限-->
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

    <application
        ...
        android:usesCleartextTraffic="true"
        android:networkSecurityConfig="@xml/network_security_config">
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
</application>
```
res下建立xml包文件添加network_security_configxml
```XML
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
```
edittext无法弹出问题 android系统bug 由于透明状态栏 而且没有设置fitsystem = true的原因 归纳为android5497bug   
8.0以上系统 关于解决欢迎页白屏问题 出现Only fullscreen opaque activities can request orientation
设置style
```XML
<item name="android:windowNoTitle">true</item>
<item name="android:windowFullscreen">true</item>
<item name="android:windowIsTranslucent">false</item>
<item name="android:windowDisablePreview">true</item>
```
在做一个二次开发项目的时候始终无法申请write权限，配置都正常，权限框架也换了几个都无法获取，查阅资料也没有解决，只好把文件都保存到app内部文件夹，三方架包有的用存储的也只有下下来改源码，项目也就这样了，随着需求持续开发，最近只好重新查看下问题，偶然翻到一个类似的情况,说是某个三方项目把写入权限版本限制了
```XML
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"  android:maxSdkVersion="18"/>
```
于是就尝试解决办法 终于搞定了
```XML
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" tools:remove="android:maxSdkVersion" />
```

