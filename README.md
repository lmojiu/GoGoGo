# 📍 影梭 (修复编译版)

> 一个基于 Android 调试 API + 百度地图 SDK 实现的安卓定位修改工具，支持摇杆自由移动。
> **本项目为原版 GoGoGo 的修复/构建版本，重点解决了新版 Android Studio 环境下的编译配置、百度地图 Key 鉴权及搜索失效等问题。**

---

## 🛠️ 项目功能
* **虚拟定位**：修改系统地理位置（免 Root）。
* **摇杆控制**：通过屏幕摇杆模拟移动。
* **位置搜索**：集成百度地图 POI 检索，支持地点搜索。
* **历史记录**：自动记录历史定位信息。

---

## 🚀 快速开始 / 编译指南

**如果你想在本地构建此项目，请按照以下步骤配置。**

### 1. 确认包名 (ApplicationId)
百度地图 SDK 的鉴权强依赖于包名。请打开 `app/build.gradle` 文件，在 `defaultConfig` 闭包中确认你的包名：

```groovy
defaultConfig {
    // 默认为 com.zcshou.gogogo，你可以修改为自己的包名
    // 注意：此处修改后，百度控制台申请 Key 时也要填写相同的包名
    applicationId "com.zcshou.gogogo"   
    // ...
}
```

### 2. 生成签名证书 (SHA1)
为了通过百度地图的安全校验，你需要生成一个 `.jks` 签名文件。

1.  **创建密钥库**：
    * 在 Android Studio 菜单栏选择：`Build` -> `Generate Signed Bundle / APK` -> `APK` -> `Create new...`。
    * 按照提示填写密码、别名等信息，生成 `.jks` 文件（建议保存在项目根目录的 `keystore` 文件夹下）。

2.  **获取 SHA1 指纹**：
    * **方法 A (推荐)**：在 Android Studio 右侧点击 `Gradle` -> `Tasks` -> `android` -> `signingReport`，在控制台查看 SHA1。
    * **方法 B (命令行)**：
        ```bash
        keytool -list -v -keystore <你的签名文件路径.jks>
        ```
    * **复制输出中的 SHA1 值**（如 `32:2F:E6:...`）。

### 3. 申请百度地图 API Key (AK)
1.  登录 [百度地图开放平台控制台](https://lbsyun.baidu.com/apiconsole/center#/home)。
2.  进入 **应用管理** -> **我的应用** -> **创建应用**。
3.  **应用类型**：选择 `Android SDK`。
4.  **填写鉴权信息**：
    * **发布版 SHA1**：填入上一步获取的 SHA1。
    * **开发版 SHA1**：如果是本地调试，填入和发布版一样的 SHA1 即可。
    * **包名**：填入第一步中的 `applicationId` (例如 `com.zcshou.gogogo`)。
5.  **⚠️ 关键设置 (必做)**：
    * 创建应用成功后，点击该应用的 **“设置”** -> **“启用服务”**。
    * **务必勾选所有服务**（尤其是“检索服务”、“LBS云检索”、“地点输入提示”），否则 App 内搜索地点会无反应。

### 4. 配置项目 Gradle
回到 Android Studio，打开 `app/build.gradle` 文件，将你申请到的 Key 和签名信息填入配置。

```groovy
android {
    // ... 其他配置

    // 1. 配置签名信息
    signingConfigs {
        debug {
            storeFile file('../keystore/key.jks') // 指向你的 .jks 文件路径
            storePassword '你的密码'
            keyAlias '你的别名'
            keyPassword '你的密码'
        }
        release {
            storeFile file('../keystore/key.jks') // 指向你的 .jks 文件路径
            storePassword '你的密码'
            keyAlias '你的别名'
            keyPassword '你的密码'
        }
    }

    defaultConfig {
        // ... 其他配置
        // 填入百度地图 Key (AK)
        def myAK = "在此处粘贴你的百度地图Key"
        
    }
}
```
