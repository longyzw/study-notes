# uni-app离线打包

## 一、安卓离线打包

当前SDK版本：Android-SDK@3.2.3.81011_20210826，如果与hbuilderX版本不一致要及时更新SDK

### 1）、打包（不含原生插件）

##### 1. 下载uni-app官方SDK

##### 2. 导入原生工程

- 打开android studio 点击file -> new -> import project -> 导入SDK下HBuilder-Integrate-AS文件夹

##### 3. 生成签名证书

   - 点击菜单Build->Generate Signed Bundle/APK

   - 选择APK，Android App Bundle是传到谷歌应用市场用的新格式。点击Next进入下一步

   - 点击Create new…，进入创建keystore签名文件对话框

   - 填写信息

     <img src=".\..\images\uni-app\android_jks.png" style="zoom: 50%;" />

   - 签名路径放到HBuilder-Integrate-AS下的simpleDemo文件夹下

##### 4. 查看jks证书文件内容

- keytool -v -list -keystore 证书名.jks

##### 5. hbuilderX生成基础应用

##### 6. 配置离线appkey

- 进入uni-app开发者后台，点击当前新建项目，配置离线appkey

##### 7. 填写包名

- 平台勾选android，包名填写入：com.video.test

##### 8. 查看签名

- Android 证书签名SHA1填写第4步查看的内容对应的SHA1值，然后保存

##### 9. 生成本地资源

- hbuilderX点击当前项目->点击菜单'发行'->原生APP-本地打包->生成本地打包APP资源

##### 10. 配置原生工程

- 复制本地打包资源到安卓工程->simpleDemo\src\main\assets\apps下

   - 修改AndroidManifest.xml中包名和appkey

    ```java
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.video.test">
        <provider
        android:authorities="com.video.test.dc.fileprovider">
        <meta-data
        android:name="dcloud_appkey"
        android:value="5bf93963f14dc8a45bece2421330f49a" />
    ```

   - 修改assets\data\dcloud_control.xml中appid和debug调试开启（打包基座为true，正式包false）

    ```java
    <hbuilder debug="true" syncDebug="true">
        <apps>
        	<app appid="__UNI__25DE602" appver=""/>
        </apps>
    </hbuilder>
    ```

   - 修改build.gradle中配置项

    ```java
    defaultConfig {
        applicationId "com.video.test"
        minSdkVersion 21
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
        multiDexEnabled true
    }
    signingConfigs {
        config {
            keyAlias 'test'
            keyPassword '123456'
            storeFile file('test.jks')
            storePassword '123456'
            v1SigningEnabled true
            v2SigningEnabled true
        }
    }
    ```
   - 复制SDK中debug-server-release.aar到simpleDemo\libs文件夹下
   
   - 配置完成后点击build运行

### 2）、打包（含原生插件）

##### 1. 插件市场下载原生插件

- 解压下载的插件

##### 2.  配置插件

- 复制包含android及ios的整个文件夹到uni-app项目根目录下的nativeplugins目录下，并在manifest.json的APP原生插件配置中选择本地插件，勾选上当前的插件，在APP权限中勾选对应插件需要的权限
- 复制解压插件android目录下的.aar文件到安卓工程libs目录下
- 在AndroidManifest.xml中添加对应插件需要的权限，没有则不添加

##### 3. 打包

同离线打包一样

### 3)、注意事项

##### 1. 添加http请求支持

- 在app-> src -> main -> res目录下新增xml文件network_security_config.xml
- 在AndroidManifest.xml文件中的application中新增android:networkSecurityConfig="@xml/network_security_config"

### 4)、App模块配置

需要在原生工程中配置

##### 1.SQLite

- 将官方提供的SDK -> libs目录下的 sqlite-release.aar 复制到项目/libs下
- 在build.gradle中添加以下代码，如已添加，请继续下一步
```java
repositories {
    flatDir {
    	dirs 'libs' //指明包的目录是libs
    }
}
```
- 打开项目/src/main/assets/data/dcloud_properties.xml文件，添加配置：`<feature name="Sqlite" value="io.dcloud.feature.sqlite.DataBaseFeature" />`

##### 2.videoplayer

- 将官方提供的SDK -> libs目录下的 media-release.aar，weex_videoplayer-release.aar 复制到项目/libs下
- 在dcloud_properties.xml中添加配置：`<feature name="VideoPlayer" value="io.dcloud.media.MediaFeatureImpl"/>`



## 二、IOS离线打包