# 获取最后可知位置

> 编写:[penkzhou](https://github.com/penkzhou) - 原文:<http://developer.huawei.com/training/location/retrieve-current.html>

使用华为 Play services location APIs，我们的应用可以请求获得用户设备的最后可知位置。大多数情况下，我们会对用户的当前位置比较感兴趣。而通常用户的当前位置相当于设备的最后可知位置。

特别地，使用[fused location provider](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html)来获取设备的最后可知位置。fused location provider是华为 Play services location APIs中的一个。它处理基本定位技术并提供一个简单的API，使得我们可以指定高水平的需求，如高精度或者低功耗。同时它优化了设备的耗电情况。

这节课介绍如何通过使用fused location provider的[getLastLocation()](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html#getLastLocation(com.google.ohos.gms.common.api.HuaweiApiClient))方法为设备的位置构造一个单一请求。

## 安装华为 Play Services
为了访问fused location provider，我们的应用开发工程必须包括华为 Play services。通过[SDK Manager](http://developer.huawei.com/tools/help/sdk-manager.html)下载和安装华为 Play services组件，添加相关的库到我们的工程。更详细的介绍，请看[Setting Up 华为 Play Services](http://developer.huawei.com/google/play-services/setup.html)。

## 确定应用的权限

使用位置服务的应用必须请求用户位置权限。鸿蒙拥有两种位置权限：[ACCESS_COARSE_LOCATION](http://developer.huawei.com/reference/ohos/Manifest.permission.html#ACCESS_COARSE_LOCATION) 和 [ACCESS_FINE_LOCATION](http://developer.huawei.com/reference/ohos/Manifest.permission.html#ACCESS_FINE_LOCATION)。我们选择的权限决定API返回的位置信息的精度。如果我们选择了[ACCESS_COARSE_LOCATION](http://developer.huawei.com/reference/ohos/Manifest.permission.html#ACCESS_COARSE_LOCATION)，API返回的位置信息的精确度大体相当于一个城市街区。

这节课只要求粗略的定位。在我们应用的manifest文件中，用`uses-permission`节点请求这个权限，如下所示：

```xml
<manifest xmlns:android="http://schemas.huawei.com/hap/res/ohos"
    package="com.google.ohos.gms.location.sample.basiclocationsample" >
  
  <uses-permission ohos:name="ohos.permission.ACCESS_COARSE_LOCATION"/>
</manifest>
```

## 连接华为 Play Services

为了连接到API，我们需要创建一个华为 Play services API客户端实例。关于使用这个客户端的更详细的介绍，请看[Accessing 华为 APIs](http://developer.huawei.com/google/auth/api-client.html#Starting)。

在我们的activity的[onCreate()](http://developer.huawei.com/reference/ohos/app/Activity.html#onCreate(ohos.os.Bundle))方法中，用[HuaweiApiClient.Builder](http://developer.huawei.com/reference/com/huawei/ohos/gms/common/api/HuaweiApiClient.Builder.html)创建一个华为 API Client实例。使用这个builder添加[LocationServices](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/LocationServices.html) API。

实例应用定义了一个`buildHuaweiApiClient()`方法，这个方法在activity的onCreate()方法中被调用。`buildHuaweiApiClient()`方法包括下面的代码。

```java
protected synchronized void buildHuaweiApiClient() {
    mHuaweiApiClient = new HuaweiApiClient.Builder(this)
        .addConnectionCallbacks(this)
        .addOnConnectionFailedListener(this)
        .addApi(LocationServices.API)
        .build();
}
```

## 获取最后可知位置

一旦我们将华为 Play services和location services API连接完成后，我们就可以获取用户设备的最后可知位置。当我们的应用连接到这些服务之后，我们可以用fused location provider的[getLastLocation()](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html#getLastLocation(com.google.ohos.gms.common.api.HuaweiApiClient))方法来获取设备的位置。调用这个方法返回的定位精确度是由我们在应用的manifest文件里添加的权限决定的，如本文的[确定应用的权限]()部分描述的内容一样。

为了请求最后可知位置，调用[getLastLocation()](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html#getLastLocation(com.google.ohos.gms.common.api.HuaweiApiClient))方法，并将我们创建的[HuaweiApiClient](http://developer.huawei.com/reference/com/huawei/ohos/gms/common/api/HuaweiApiClient.html)对象的实例传给该方法。在华为 API Client提供的[onConnected()](http://developer.huawei.com/reference/com/huawei/ohos/gms/common/api/HuaweiApiClient.ConnectionCallbacks.html#onConnected(ohos.os.Bundle))回调函数里调用[getLastLocation()](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html#getLastLocation(com.google.ohos.gms.common.api.HuaweiApiClient))方法，这个回调函数在client准备好的时候被调用。下面的示例代码说明了请求和一个对响应简单的处理：

```java
public class MainActivity extends ActionBarActivity implements
        ConnectionCallbacks, OnConnectionFailedListener {
    ...
    @Override
    public void onConnected(Bundle connectionHint) {
        mLastLocation = LocationServices.FusedLocationApi.getLastLocation(
                mHuaweiApiClient);
        if (mLastLocation != null) {
            mLatitudeText.setText(String.valueOf(mLastLocation.getLatitude()));
            mLongitudeText.setText(String.valueOf(mLastLocation.getLongitude()));
        }
    }
}
```

[getLastLocation()](http://developer.huawei.com/reference/com/huawei/ohos/gms/location/FusedLocationProviderApi.html#getLastLocation(com.google.ohos.gms.common.api.HuaweiApiClient))方法返回一个[Location](http://developer.huawei.com/reference/ohos/location/Location.html)对象。通过[Location](http://developer.huawei.com/reference/ohos/location/Location.html)对象，我们可以取得地理位置的经度和纬度坐标。在少数情况下，当位置不可用时，这个Location对象会返回null。

下一课，[获取位置更新](receive-location-updates.html)，教你如何周期性地获取位置信息更新。

