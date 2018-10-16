---
layout: post
title: 关于 UIAutomator2.0 的使用
tags: [UIAutomator2.0]
excerpt_separator: <!--more-->
---
每天完成五条发现者的任务是在太烦，所以用 UI 自动测试框架完成
<!--more-->
### 学习资料
- 慕课网课程：涵盖工程搭建、API 基本使用。入门不二之选。
> 参考：[【慕课网】Android UiAutomator 2.0 入门实战](https://www.imooc.com/learn/986)
- UI Automator Viewer：界面分析工具。一键得到当前截图界面的所有元素信息，包括应用包名、元素种类、res-id 等等，为定位元素做准备。
> 参考：[UI Automator Viewer 的使用](https://blog.csdn.net/daihuimaozideren/article/details/78226810)
- 更多知识点
> 参考：[UIAutomator2.0详解（UIDevice篇---- 序）](https://blog.csdn.net/daihuimaozideren/article/details/78352736)

### 常见误区：应用上下文
在我开始使用 UIAutomator2.0 尝试操作应用控件的时候，我犯了一个错误。首先我打开应用，然后运行测试。此时我无论使用 By.res/By.clazz 等等方法搜索我想要的控件，都是报空指针异常。

后来我发觉无论我在运行测试之前打开何种 App，运行测试的“上下文”为手机桌面，所以通过 By 方法是在桌面中搜索（当时搜索 TextView 并打印其 Text 发现是时间与运营商信息便发觉这点）。因此如果需要操作其他应用的元素，需要通过 UIAutomator2.0 代码打开目标应用。

代码打开应用示例：
![](https://i.imgur.com/O8v7RH8.png)
> 参考：[uiautomator 怎么启动指定应用?——知乎用户谢宏浩的回答](https://www.zhihu.com/question/28886583/answer/171476746)

使用上述代码打开应用时，就可以正常使用 By 方法搜索页面中的目标元素。
> 获取 APP_PACKAGE_NAME 应用包名：使用 UI Automator Viewer 随意截取应用任意界面，在 Node Detail 中 package 即为应用包名。

### 其他
关于等待：真正完美的等待一般是（1）等待该动作结束后在执行下一个动作（2）等待控件状态变更为期望状态时执行动作等等。但为了代码编写简单可直接使用Thread.sleep() 方法。这当然不是什么好主意，但因为我需要完成的目标比较简单用此方法也没什么大碍，但如果需要严格意义的测试，等待需要认真考虑。

完成动作的方式：很多操作有两种实现方式。例如点击操作，你可以指定坐标点在此坐标点处模拟点击，或者通过 findObject 方法得到 UIObject 对象再调用其 click 方法。我更喜欢执行坐标点执行操作，因为这样不受限于应用上下文而且更加直接。如果使用第二种方法，一是要小心如果你的 By 方法设置的不合适，findObject 方法会抛出异常（找不到相应元素），二是如果没有进入到合适的上下文中，也会抛出异常。但这种方法更加通用，因为一些页面的元素位置并不固定，因此需要根据其特定条件查找获取其引用。

因此如果目标元素位置固定，通过坐标点实现操作最为容易，其余的情况则需要通过 findObject 方法获取到 UIObject 对象再进一步操作。如果不想因异常打断测试，可以使用 try catch 包裹，在 catch 中进行异常处理。如果出现异常的情况不是什么太大问题，刷新页面或者直接翻页就能解决的，直接抓住异常处理即可，否则应该抛出异常中断测试寻找问题出现的原因。

### 脱离 PC 环境运行 UIAutomator2.0
还没有测试过，但这里有一篇文章阐述做法与原理，可以一读。
> 参考：[uiautomator2.0+脱离PC运行（apk启动uiautomator2.0+）的实现方案](https://blog.csdn.net/cxq234843654/article/details/52605441)