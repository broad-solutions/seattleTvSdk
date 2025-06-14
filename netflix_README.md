#seattleTvSdk 广告SDK 对接文档
欢迎使用Cloud Infinitegroup开发的广告SDK！本文档将指导您如何在您的应用程序中集成SeattleSdk的广告功能。

## 1. 概述

SeattleSdk广告SDK提供了简单而强大的方式在您OTT盒子的应用程序中显示广告。您可以通过以下步骤将广告集成到您的应用中：

- 添加 SeattleSdk广告SDK 依赖
- 初始化广告SDK
- 请求广告
- 显示广告

## 2. 添加依赖

要开始使用 SeattleSdk广告SDK，首先需要在您的应用程序中添加SDK 的依赖。请将以下依赖添加到您的
build.gradle 文件中：

    dependencies {
            implementation 'cn.coolplay:seattle_tv_sdk:1.3.2.7.2'
    }

maven配置: 项目根目录下settings.gradle

    pluginManagement {
         repositories {
            google()
            mavenCentral()
         }
    }
    dependencyResolutionManagement {
         repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
              repositories {
                  google()
                  mavenCentral()
                  maven {
                      url = uri("https://github.com/broad-solutions/seattleTvSdk/raw/main")
                  }
              }
    }

## 3. 初始化 SDK

在您的应用程序中的合适位置初始化SeattleSdk广告SDK。

- 在使用SDK前，请让贵司的商务先向我们商务代表申请client_id,client_secret。

```
      (在application 中)
      private fun initCoolPlay() {
        Thread {
            CoolPlaySdk.init(
                CoolPlayBuilder(
                    this,
                    "填写client_id",
                    "填写client_secret",
                    deviceID = DeviceUtil.mac,//获取设备ID
                    isDebug = true, //是否是debug模式 打开log
                    testMode = true,//是否是测试模式
                    initStatusCallBack = { i: Int, s: String? ->
                        "initSdk==$s".print()
            
                    },
                ),
            )
        }.start()
    }
```

##4. 代码集成:

```
   private val adRequestBuilder by lazy {
        SplashRequest(
            adContainer,  //传入广告容器
            adsLoadListener = CoolPlayAdListener( //传入广告监听
                "splash",
                adListener = object : CoolPlayAdCallback {
                    //成功
                    override fun onComplete() {
                        "onComplete".print("Loading")
                        destroy()
                        goMain.invoke()
                    }
                     //失败
                    override fun onError() {
                        "onError".print("Loading")
                        destroy()
                        goMain.invoke()
                    }
                },
            )
        )
    } 
   //开始加载广告
  CoolPlaySdk.loadAd(adRequestBuilder)
  
  //消亡
   fun destroy() {
      adRequestBuilder.release()
   }

```

## 5. 广告类型和用途说明

- Splash广告(视频版)：视频广告，适用于App启动时，在Splash里展现广告内容，此时不需要传入视频链接。
- Banner广告(视频版)：视频广告，适用于APP进入主界面后，可以在任何位置显示广告，此时不需要传入视频链接。
- Section广告(视频版)：视频广告，适用于APP进入主界面后，一般支持视频播放过程中的前、中和后贴广告，所以此时必须传入视频链接，一般适用于播放影视作品时。（暂不支持）

## 6. 混淆

```
-keep class cn.coolplay.** { *; }
# 保留 Media3 UI 和 Media3 ExoPlayer 类和方法
-keep class androidx.media3.** { *; }
```




