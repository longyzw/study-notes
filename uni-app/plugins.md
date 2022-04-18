# 插件开发

## 安卓插件开发

### 一、准备开发环境

- 下载uni-app最新版SDK并解压
- 安装Java环境，jdk1.8
- 安装Android studio

### 二、开发调试插件

#### 1、导入项目

- 点击Android Studio菜单选项File—>New—>Import Project
- 导入uni插件原生项目 UniPlugin-Hello-AS工程
- 工程切换为Project模式

#### 2、创建插件Library

- 点击Android Studio菜单选项File—>New—>New Module
- 选择Android Library
- 输入 Library名称如：toastplus 点击finish
- 然后在app目录下的build.gradle下引入 toastplus `implementation project(':toastplus')`
- Library在build.gradle配置开发依赖`compileOnly fileTree(dir: '../app/libs', include: ['uniapp-v8-release.aar'])`，如果有其他依赖的插件包也需要引入
- 点击编辑器右上角Sync now进行同步
- modle目录（module/src/main/java/com.xxx.xxx/）下创建ToastPlus类集成UniMoudle
- 书写示例代码
```java
public class ToastPlus extends UniModule {
    JSCallback jsCallback;
    String path = "";

    /**
     * 简单回调示例
     * 传入一个String类型 处理并返回
     */
    @JSMethod(uiThread = true)
    public void processData(String data, JSCallback jsCallback) {
        this.jsCallback = jsCallback;
        if (mWXSDKInstance != null && mWXSDKInstance.getContext() instanceof Activity) {
            path = data + "+(处理完成)";
            //返回处理结果 进行回调
            jsCallback.invoke(path);
        }
    }

    /**
     * 调用原生Toast显示传入内容
     */
    @JSMethod(uiThread = true)
    public void showToast(String message) {
        if (mWXSDKInstance != null && mWXSDKInstance.getContext() instanceof Activity) {
            Toast.makeText(mWXSDKInstance.getContext(), message, Toast.LENGTH_SHORT).show();
        }
    }
}
```
- 在app/dcloud_uniplugins.json文件中添加插件信息
```json
{
    "hooksClass": "",
    "plugins": [
        {
            "type": "module",
            "name": "toastplus",
            "class": "com.megawin.toastplus.ToastPlus"
        }
    ]
}
```

#### 3、创建uni-app项目

- 创建uni-app模板项目，并修改index代码
```vue
<template>
	<view class="content">
		<view class="button1">
			<button @click="showToast(message)">点击显示原生Toast</button>
		</view>
		<view class="button2">
			<button @click="processData(waitprocessdata)">回调的使用</button>
		</view>
	</view>
</template>

<script>
	const ToastPlusModule = uni.requireNativePlugin("toastplus")
	export default {
		data() {
			return {
				message: '我是一条很长的Toast我是一条很长的Toast',
				waitprocessdata: '我是待处理的数据'
			}
		},
		methods: {
			showToast(message) {
				ToastPlusModule.showToast(message)
			},
			processData(data) {
				ToastPlusModule.processData(data, processokdata => {
					ToastPlusModule.showToast(processokdata)
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.content {
		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		text {
			line-height: 50rpx;
		}
	}
</style>
```

#### 4、调试插件

- 点击HbuilderX菜单选项 发行—>原生APP-本地打包—>生成本地打包APP资源
- 打开控制台输出路径，复制UNI开头的文件夹到AndroidStudio app/src/main/assets/apps/目录下
- 修改dcloud_control.xml中的appid字段
- 修改项目中的包名，appkey，打包配置和版本号等
- 点击运行可调试插件，如果运行成功并正确响应调用的事件则插件开发成功

### 三、打包为本地插件

#### 1、本地创建一个插件文件夹 

- 名称可以命名为你的uni账户名-插件名称
- 包含android文件夹（存放编译后的插件包）和package.json文件（插件的配置和说明）

#### 2、配置pakege.json

```json
{
	"name": "ToastPlus",
	"id": "Ly-ToastPlus",
	"version": "1.0.0",
	"description": "测试原生插件开发及使用",
	"_dp_type":"nativeplugin",
	"_dp_nativeplugin":{
		"android": {
			"plugins": [
				{
					"type": "module",
					"name":"ToastPlus",
					"class": "com.megawin.toastplus.ToastPlus"
				}
			],
			"integrateType": "aar" 
		}    
	}
}
```

#### 3、打包插件的aar包

- androidstudio选中右侧gradle选中选择对应插件->Tasks->other->assembleRelease
- aar包输入路径为插件目录\build\outputs\aar\插件名称.aar
- 复制aar包到android目录下

### 四、调试本地插件

- 将整理好的文件夹放入nativeplugins文件夹下
- 然后修改index.vue中引入的插件名和配置的一致
- 配置manifest.json->App原生插件配置->本地插件->选择刚才配置的插件
- 进行云打包或离线打包，如果正常使用则代表插件开发完成

### 五、发布到插件市场

填写对应的插件信息提交发布

## IOS插件开发

