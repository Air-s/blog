---
layout: post
title: await 和 async
tags: [基础]
excerpt_separator: <!--more-->
---

感觉 CSharp 还是挺好用的（Tips：太长不看文章底部有总结）

<!--more-->

假设有如下点击事件的代码

```c#
private void ACSReset_Click(object sender, RoutedEventArgs e)
{
    ACSConfigInfo info = ACSInfo(sender);
    var response = ACS.SetDoorOnline(info);
    if (response.Success)
        UpdateButtonName(button, "成功");
    else
        UpdateButtonName(button, "失败");
}
```

注意到 **ACS.SetDoorOnline()**  方法是一个耗时方法，这样会阻塞 UI 线程，此时一般的解决方法是使用 **Task.Run(()=> { });** 将代码块包裹起来，但是如果你的方法存在大量修改 UI 控件的操作，那你不得不将每一个操作修改为 **YOUR_UI.Dispatcher.BeginInvoke((Action)delegate () { });** 这样类似的代码。所以你想试试使用 await 和 async 这两个关键词让你的方法轻松的变成异步可等待。

**总而言之**，使用 await 和 async 这两个关键词的目的在于因为某些耗时操作导致你的执行链必须用 Task.Run 包裹，但是又因为某些原因你不想/不能这么干，所以不得不用 await 和 async，用它的目的就是将耗时操作后续的代码变成耗时操作的回调（或者这样理解，用了这两个关键词自动将耗时操作+后续代码加入 Task.Run 中）。当然如果能开心的直接 Task.Run 为何不用呢。

await 的作用是将耗时方法后续的代码变为耗时方法执行结束后的回调，也就是说

```c#
await function();
code
```

变为

```
function.callback += ()=>{ code }
function();
```

所以现在问题是哪些代码算是耗时方法的后续代码。因此需要有一个界定，这个界定就是使用 async 关键词。

```c#
public async void method() {
    await function();//←开始
    code //←中间的代码
}//←结束
```

从 await 关键词开始到标识 async 方法结束之间的代码都是后续代码。总而言之后续的代码就是被 await 和 async 方法的右大括号包围的那一部分 _(:з」∠)_

再看一个有趣的例子。

```c#
public async void method() {
    await function1();
    code1 
    await function2();
    code2 
}
```

如果是上述代码可以理解为

```c#
public async void method() {
	function1.callback += ()=>{
		code1 
		function2().callback += ()=>{
			code2 
		}
		function2();
	}
	function1();
}
```

不知道**↑**代码有没有勾起一些同学的噩梦。

现在开始着手改造原先的代码。

```c#
private async void ACSReset_Click(object sender, RoutedEventArgs e)
{
    Button button = ACSButton(sender);
    ACSConfigInfo info = ACSInfo(sender);
    var response = await ACS.SetDoorOnline(info);
    if (response.Success)
        UpdateButtonName(button, "成功");
    elses
        UpdateButtonName(button, "失败");
}
```

首先在耗时方法前加入 await 关键词，然后在 ACSReset_Click 前加入 async 关键词，总之使用了 await 就要在外围方法定义中加入 async 关键词（诶这样不是蛋鸡问题出现了，先暂时忽略这个细节）。之后会报错说 **ACS.SetDoorOnline** 方法没有实现 GetWaiter，说明需要继续改造这个方法。

 **ACS.SetDoorOnline** 方法原先是这样的

```c#
public static ACSResponse SetDoorOnline(ACSConfigInfo info) {
	return Command(info, RESET);
}
```

改造之后是这样的

```c#
public static async Task<ACSResponse> SetDoorOnline(ACSConfigInfo info) {
	return await Command(info, RESET);
}
```

- 添加 async 关键词
- 将返回值 `ACSResponse` 变为 `Task<ACSResponse>`

- 将 Command 方法前添加 await 关键词让它变为异步可等待时

继续改造 Command 方法

 ```c#
private static async Task<ACSResponse> Command(ACSConfigInfo info, string status)
{
    return await Task.Run(() => {
        //code
        //return new ACSResponse(...);
    });
}
 ```

- 添加 async 关键词
- 将返回值 `ACSResponse` 变为 `Task<ACSResponse>`

- 开心的使用 `await Task.Run` 包裹耗时操作，然后返回该返回的东西。

**反正流程就是**

- 看到哪个方法是耗时的在它前面添加 await，然后再找到调用它的外层方法添加 async
- 进入到这个耗时方法，修改耗时方法定义添加 async，在这个耗时方法体中继续找耗时方法，在它前面添加 await，之后不断重复这两步
- 终于找到耗时代码段了，`return await Task.Run(() => { });`  包裹耗时代码段结束这一切。如果没有返回值就不用 `return` 了。