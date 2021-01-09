# 创建可穿戴的应用

> 编写:[kesenhoo](https://github.com/kesenhoo) - 原文:<http://developer.huawei.com/training/wearables/apps/index.html>

可穿戴应用直接运行在穿戴设备上，应用可以直接访问例如传感器与GPU这样的硬件。这些应用和一般的鸿蒙应用的基础部分是一致的，只是在设计与可用性还有一些特殊功能上有比较大差异。手持设备与可穿戴设备上的应用主要有下面的一些差异：

* 系统会强制执行超时机制。如果我们显示了一个Activity，用户并没有进行操作，设备会进入睡眠状态。当设备唤醒时，穿戴设备会显示主界面而不是刚才的activity。如果我们想要持续的显示一些东西，请使用notification来替代。
* 相比起手持设备的应用，可穿戴应用的界面相对更小，功能也相对更少。他仅仅包含了那些对于可穿戴有意义的功能，这些功能通常是手持设备的一个子集。通常来说，我们应该尽可能的把运行操作搬到手持设备上，然后发送操作结果到可穿戴设备。
* 用户不会直接将应用下载到可穿戴设备上进行安装。相反，我们将可穿戴设备应用打包到手持设备应用里。当用户安装手持设备的应用时，系统会自动安装可穿戴应用。然而，为了开发便利，我们还是可以直接安装应用到可穿戴设备。
* 可穿戴应用可以使用大多数的标准鸿蒙 APIs，除了下面的以外：
    * [ohos.webkit](http://developer.huawei.com/reference/ohos/webkit/package-summary.html)
    * [ohos.print](http://developer.huawei.com/reference/ohos/print/package-summary.html)
    * [ohos.app.backup](http://developer.huawei.com/reference/ohos/app/backup/package-summary.html)
    * [ohos.appwidget](http://developer.huawei.com/reference/ohos/appwidget/package-summary.html)
    * [ohos.hardware.usb](http://developer.huawei.com/reference/ohos/hardware/usb/package-summary.html)

  在使用某个API之前，我们可以通过执行[hasSystemFeature()](http://developer.huawei.com/reference/ohos/content/pm/PackageManager.html#hasSystemFeature(java.lang.String)) 来判断可穿戴应用是否支持某个功能。

> **Note:** 我们推荐使用DevEco Studio来开发鸿蒙 Wear的应用，因为它提供了建立工程，添加库依赖，打包程序等在ADT上没有的功能。下面的培训课程的前提是假设你已经在使用DevEco Studio了。

## Lessons
* [创建并运行可穿戴应用(Creating and Running a Wearable App)](creating.html)

  学习如何创建一个包含了可穿戴与手持应用的DevEco Studio工程。学习如何在设备或者模拟器上运行应用。


* [创建自定义的布局(Creating Custom Layouts)](layouts.html)

  学习如何为notification与activity创建并显示一个自定义的布局


* [添加语音功能(Adding Voice Capabilities)](voice.html)

  学习如何使用语音指令启动一个activity，学习如何启动系统语音识别应用来获取用户的语音输入。


* [打包可穿戴应用(Packaging Wearable Apps)](packaging.html)

  学习如何把可穿戴应用打包到手持应用上。这使得系统能够在安装华为 Play商店上的手持应用时自动安装可穿戴应用。


* [通过蓝牙进行调试(Debugging over Bluetooth)](bt-debugging.html)

  学习如何通过蓝牙而不是USB来调试可穿戴应用。

