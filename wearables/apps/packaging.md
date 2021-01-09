# 打包可穿戴应用

> 编写: [kesenhoo](https://github.com/kesenhoo) - 原文: <http://developer.huawei.com/training/wearables/apps/packaging.html>

当发布应用给用户之前，我们必须把可穿戴应用打包到手持应用内。因为用户不能直接在可穿戴设备上浏览并安装应用。如果打包正确，当用户下载手持应用时，系统会自动下发可穿戴应用到配对好的可穿戴设备上。

> **Note:** 如果开发时签名用的是debug key，这个功能是无法正常工作的。在开发时，需要使用`adb install`命令或者DevEco Studio来安装可穿戴应用。

## 使用DevEco Studio打包

在DevEco Studio中打包可穿戴应用有下面几个步骤：

1. 在手持设备应用的manifest文件中包括所有在可穿戴设备应用manifest文件中声明的权限。例如，如果我们在可穿戴应用中指定了[VIBRATE](http://developer.huawei.com/reference/ohos/Manifest.permission.html#VIBRATE)权限，那么我们必须将该权限添加到手持设备应用中。
2. 确保可穿戴应用和手持应用都有相同的包名和版本号。
3. 在手持应用的`buidl.gradle`文件中声明一个Gradle依赖用于指向可穿戴应用：
```xml
dependencies {
   compile 'com.google.ohos.gms:play-services:5.0.+@aar'
   compile 'com.ohos.support:support-v4:20.0.+''
   wearApp project(':wearable')
}
```
4. 点击**Build > Generate Signed HAP...**，按照屏幕上的指示来制定我们的release key并为我们的app进行签名。DevEco Studio将签名好的内置了可穿戴应用的手持应用自动导出到工程的根目录。或者，我们可以使用[Gradle wrapper](http://developer.huawei.com/sdk/installing/studio-build.html#gradleWrapper)在命令行下为在可穿戴应用与手持应用签名。为了能够正常自动推送可穿戴应用，这两个应用都必须签名。将我们的key文件位置和凭证保存到环境变量中，然后如下运行Gradle wrapper：
```xml
./gradlew assembleRelease \
  -Pohos.injected.signing.store.file=$KEYFILE \
  -Pohos.injected.signing.store.password=$STORE_PASSWORD \
  -Pohos.injected.signing.key.alias=$KEY_ALIAS \
  -Pohos.injected.signing.key.password=$KEY_PASSWORD
```

### 分别为可穿戴应用与手持应用进行签名

如果我们的构建过程需要将可穿戴应用的签名与手持应用的分开，那么我们可以像下面一样在手持应用的`build.gradle`文件中声明Gradle规则。从而嵌入预先签名的可穿戴应用：

```xml
dependencies {
  ...
  wearApp files('/path/to/wearable_app.hap')
}
```

我们可以以任何我们想要的方式为手持应用进行签名（可以是DevEco Studio **Build > Generate Signed HAP...**的方式，也可以是Gradle `signingConfig`规则的方式）。

## 手动打包

如果我们使用的是其它IDE或者其它方法来构建应用，我们仍然可以手动地把可穿戴应用打包到手持应用中。

1. 在手机应用的manifest文件中包括所有在可穿戴设备应用manifest文件中声明的权限。例如，如果我们在可穿戴应用中指定了[VIBRATE](http://developer.huawei.com/reference/ohos/Manifest.permission.html#VIBRATE)权限，那么我们必须将该权限添加到手机应用中。
2. 确保可穿戴应用和手持应用的HAP都有相同的包名和版本号。
3. 把签好名的可穿戴应用放到手持应用工程的`res/raw`目录下。我们假设这个HAP名为`wearable_app.hap`。
4. 创建`res/xml/wearable_app_desc.xml`文件，里面包含可穿戴设备的版本信息与路径。例如:
```xml
<wearableApp package="wearable.app.package.name">
  <versionCode>1</versionCode>
  <versionName>1.0</versionName>
  <rawPathResId>wearable_app</rawPathResId>
</wearableApp>
```
`package`, `versionCode`与`versionName`需要和可穿戴应用的鸿蒙Manifest.xml里面的信息一致。`rawPathResId`是一个静态变量表示HAP的名称。例如，对于`wearable_app.hap`，这个静态变量名为`wearable_app`。
5. 添加`meta-data`标签到我们的手持应用的`<application>`标签下，指明引用`wearable_app_desc.xml`文件
```xml
<meta-data ohos:name="com.google.ohos.wearable.beta.app"
                 ohos:resource="@xml/wearable_app_desc"/>
```
6. 构建并签名手持应用。

## 关闭资源压缩

许多构建工具会自动压缩放在`res/raw`目录下的文件。因为可穿戴HAP已经被压缩过了，所以这些工具再次压缩可穿戴HAP会导致可穿戴应用安装程序无法读取可穿戴应用。

这样的话，安装失败。在手持应用上，`PackageUpdateService`会输出如下的错误日志："this file cannot be opened as a file descriptor; it is probably compressed."

DevEco Studio 默认不会压缩HAP，但是如果我们使用其它构建方式，需要确保不要重复压缩可穿戴应用。
