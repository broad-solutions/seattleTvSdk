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

[//]: # (            implementation 'cn.coolplay:seattle_tv_sdk:1.3.2.6')

            implementation 'cn.coolplay:seattle_tv_sdk:1.3.2.7.2'
            implementation 'cn.coolplay:seattle_tv_sdk_airmobi:1.0.1'
            implementation 'cn.coolplay:adsdk_nie_test:1.0'//测试使用
            implementation 'cn.coolplay:adsdk_nie:1.0'//正式使用
            implementation 'cn.coolplay:adsdk_nie_fire_tv:1.0'// fire_tv
           //okhttp3okHttp4 如果项目中有其他版本的gson和OkHttp，只要api接口一样，可以复用。
            implementation 'com.squareup.okhttp3:okhttp:3.12.12'
            implementation 'com.google.code.gson:gson:2.10.1'

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
                airMobiBuilder = AirMobiBuilder(this)
            )
        }.start()
    }
```

##4. 代码集成:

```
    布局
    <cn.coolplay.sdk.player.ClPlayerView xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:id="@+id/player_view"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      app:buffered_color="@color/white"
      app:controller_layout_id="@layout/layout_drama_controller_holder" //自定义controller 可以不使用
      app:show_buffering="always" />
    
    adRequestBuilder = SectionRequest(
            adView,
            mVideoMute = 2,  //静音 0、全部  1、广告静音 2 全部开启声音
            isVMap = true,   //前中后贴 配置为 true 其他配置为false 默认是false
            adsLoadListener = CoolPlayAdListener("label", commonPrams, adListener = object : CoolPlayAdCallback {
                override fun attachPlayer(context: Context): ClPlayerView {
                    //这里绑定播放器 这里使用将 PlayerView替换为 CpPlayerView 其他跟PlayerView 使用一致
                    return playerView
                }

                override fun onComplete() {
                    super.onComplete()
                    "视屏播放完毕,继续下一集".print("PlayerEpisodeFragment")
                }
            })
        ).apply {
            // 设置加载超时时间
            mLoadVideoTimeout = 15000
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
- Section广告(视频版)：视频广告，适用于APP进入主界面后，一般支持视频播放过程中的前、中和后贴广告，所以此时必须传入视频链接，一般适用于播放影视作品时

## 6. 混淆

```
-keep class cn.coolplay.** { *; }
-keep class com.tcl.ff.component.vastad.**{*;}
-keep class com.thoughtworks.xstream.**{*;}
-keep class tv.danmaku.ijk.** { *; }
```




