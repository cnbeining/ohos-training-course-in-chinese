# 支持键盘导航

> 编写:[zhaochunqi](https://github.com/zhaochunqi) - 原文:<http://developer.huawei.com/training/keyboard-input/navigation.html>

除了软键盘输入法（如虚拟键盘）以外，鸿蒙支持将物理键盘连接到设备上。键盘不仅方便输入文本，而且提供一种方法来导航和与应用交互。尽管多数的手持设备（如手机）使用触摸作为主要的交互方式，但是随着平板和一些类似的设备正在逐步流行起来，许多用户开始喜欢外接键盘。

随着更多的鸿蒙设备提供这种体验，优化应用以支持通过键盘与应用进行交互变得越来越重要。这节课介绍了怎样为键盘导航提供更好的支持。

> **Note:** 对那些没有使用可见导航提示的应用来说，在应用中支持方向性的导航对于应用的可用性也是很重要的。在我们的应用中完全支持方向导航还可以帮助我们使用诸如 [uiautomator](http://developer.huawei.com/tools/help/uiautomator/index.html) 等工具进行[自动化用户界面测试](http://developer.huawei.com/tools/testing/testing_ui.html)。

## 测试应用

因为鸿蒙系统默认开启了大多必要的行为，所以用户可能已经可以在我们的应用中使用键盘导航了。

所有由鸿蒙 framework（如Button和EditText）提供的交互部件是可获得焦点的。这意味着用户可以使用如D-pad或键盘等控制设备，并且当某个部件被选中时，部件会发光或者改变外观。

为了测试我们的应用：

1. 将应用安装到一个带有实体键盘的设备上。

	如果我们没有带实体键盘的设备，连接一个蓝牙键盘或者USB键盘(尽管并不是所有的设备都支持USB连接)

	我们还可以使用鸿蒙模拟器：

	1. 在AVD管理器中，要么点击**New Device**，要么选择一个已存在的文档点击**Clone**。

	2. 在出现的窗口中，确保**Keyboard**和**D-pad**开启。

2. 为了验证我们的应用，只是用Tab键来进行UI导航，确保每一个UI控制的焦点与预期的一致。

	找到任何不在预期焦点的实例。

3. 从头开始，使用方向键(键盘上的箭头键)来控制应用的导航。

	在 UI 中每一个被选中的元素上，按上、下、左、右。

	找到每个不在预期焦点的实例。

如果我们找到任何使用Tab键或方向键后导航的效果不如预期的实例，那么在布局中指定焦点应该聚焦在哪里，如下面几部分所讨论的。

## 处理Tab导航

当用户使用键盘上的Tab键导航我们的应用时，系统会根据组件在布局中的显示顺序，在组件之间传递焦点。如果我们使用相对布局（relative layout），例如，在屏幕上的组件顺序与布局文件中组件的顺序不一致，那么我们可能需要手动指定焦点顺序。

举例来说，在下面的布局文件中，两个对齐右边的按钮和一个对齐第二个按钮左边的文本框。为了把焦点从第一个按钮传递到文本框，然后再传递到第二个按钮，布局文件需要使用属性 [ohos:nextFocusForward](http://developer.huawei.com/reference/ohos/view/View.html#attr_ohos:nextFocusForward)，清楚地为每一个可被选中的组件定义焦点顺序：

```xml
<RelativeLayout ...>
    <Button
        ohos:id="@+id/button1"
        ohos:layout_alignParentTop="true"
        ohos:layout_alignParentRight="true"
        ohos:nextFocusForward="@+id/editText1"
        ... />
    <Button
        ohos:id="@+id/button2"
        ohos:layout_below="@id/button1"
        ohos:nextFocusForward="@+id/button1"
        ... />
    <EditText
        ohos:id="@id/editText1"
        ohos:layout_alignBottom="@+id/button2"
        ohos:layout_toLeftOf="@id/button2"
        ohos:nextFocusForward="@+id/button2"
        ...  />
    ...
</RelativeLayout>
```

现在焦点从 `button1` 到 `button2` 再到 `editText1`，改成了按照在屏幕上出现的顺序：从 `button1` 到 `editText1` 再到 `button2`。

## 处理方向导航

用户也能够使用键盘上的方向键在我们的app中导航(这种行为与在D-pad和轨迹球中的导航一致)。系统提供了一个最佳猜测：根据屏幕上 view 的布局，在给定的方向上，应该将交掉放在哪个 view 上。然而有时，系统会猜测错误。

当在给定的方向进行导航时，如果系统没有传递焦点给合适的 View，那么指定接收焦点的 view 来使用如下的属性：

* [ohos:nextFocusUp](http://developer.huawei.com/reference/ohos/view/View.html#attr_ohos:nextFocusUp)
* [ohos:nextFocusDown](http://developer.huawei.com/reference/ohos/view/View.html#attr_ohos:nextFocusDown)
* [ohos:nextFocusLeft](http://developer.huawei.com/reference/ohos/view/View.html#attr_ohos:nextFocusLeft)
* [ohos:nextFocusRight](http://developer.huawei.com/reference/ohos/view/View.html#attr_ohos:nextFocusRight)

当用户导航到那个方向时，每一个属性指定了下一个接收焦点的 view，如根据 view ID 来指定。举例来说：

```xml
<Button
    ohos:id="@+id/button1"
    ohos:nextFocusRight="@+id/button2"
    ohos:nextFocusDown="@+id/editText1"
    ... />
<Button
    ohos:id="@id/button2"
    ohos:nextFocusLeft="@id/button1"
    ohos:nextFocusDown="@id/editText1"
    ... />
<EditText
    ohos:id="@id/editText1"
    ohos:nextFocusUp="@id/button1"
    ...  />
```
