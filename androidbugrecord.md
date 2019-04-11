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
