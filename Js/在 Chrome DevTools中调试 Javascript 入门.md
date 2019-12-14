# 在 Chrome DevTools中调试 Javascript 入门

## 第1步：重现错误
找到一系列可一致出现重复错误的操作始终是调试的第一步

1  打开如下网页`https://googlechrome.github.io/devtools-samples/debug-js/get-started`
2 在Number1文本框中输入5
3 在Number2文本框中输入1
4 点击 `Add Number1 and Number2`.按钮下方的标签显示 `5+1=51`.结果因改为`6`.这就是你需要修正的错误.

![](assets/20191213104153194_1159548855.png =800x)


## 第2步:熟悉 Source 面板界面
DevTools可为CSS 分析页面加载性能和监控网络请求等不同的任务提供许多不同的工具.你可以在 `Sources`面板中调试`JavaScript`

1 通过按 `Command+Option+I `(Mac) 或 `Control+Shift+I`（Windows、Linux），打开 `DevTools`。 此快捷方式可打开 `Console` 面板
![](assets/20191213104634207_909579545.png =800x)

2.点击`Sources`标签
![](assets/20191213104722753_1802340484.png =800x)

Sources 界面包含3个部分:
![](assets/20191213104820217_525476626.png =800x)

Sources 面板界面的3个部分
1.`File Navigator`窗格.此处列出页面请求的每个文件.
2.`Code Edito`r 窗格.在`File Navigator` 窗格中选择文件后,此处会显示该文件内容.
3.`JavaScript Debugging` 窗格。 检查页面 `JavaScript` 的各种工具。 如果 DevTools 窗口布局较宽，此窗格会显示在` Code Editor `窗格右侧


## 第3步:使用断点暂停代码
调试这种问题的常用方法是将多个 console.log() 语句插入代码，以便在执行脚本时检查相关值。 例如：
```javascript
function updateLabel() {
  var addend1 = getNumber1();
  console.log('addend1:', addend1);
  var addend2 = getNumber2();
  console.log('addend2:', addend2);
  var sum = addend1 + addend2;
  console.log('sum:', sum);
  label.textContent = addend1 + ' + ' + addend2 + ' = ' + sum;
}
```
虽然 `console.log()` 方法可以完成任务，但断点可以更快完成此任务。 断点可让您在执行代码的过程中暂停代码，并在此时及时检查所有相关值。 与 `console.log()` 方法相比，断点具有一些优势：
- 使用 `console.log()`，您需要手动打开源代码，查找相关代码，插入 `console.log()` 语句，然后重新加载此页面，才能在控制台中看到这些消息。 使用断点，无需了解代码结构即可暂停相关代码。
- 在 `console.log()` 语句中，您需要明确指定要检查的每个值。 使用断点，DevTools 会在暂停时及时显示所有变量值。 有时在您不知道的情况下，有些变量会影响您的代码。

简言之,与`console.log()`方法相比,断点可以帮助您更快的查找和修正错误.

如果退一步思考应用的运作方式，您可以根据经验推测出，使用与 `Add Number 1 and Number 2` 按钮关联的 `click` 事件侦听器时计算的和不正确 (5 + 1 = 51)。 因此，您可能需要在` click` 侦听器运行时暂停代码。 `Event Listener Breakpoints` 可让您完成此任务：

- 在 `JavaScript Debugging` 窗格中，点击 `Event Listener Breakpoints` 以展开该部分。 DevTools 会显示 `Animation` 和 `Clipboard` 等可展开的事件类别列表。
- 在 `Mouse` 事件类别旁，点击 `Expand Expand`图标。 DevTools 会显示 `click` 和 `mousedown` 等鼠标事件列表。 每个事件旁都有一个复选框。
- 勾选 `click` 复选框。 DevTools 现在经过设置可以在任何 click 事件侦听器运行时自动暂停。

![](assets/20191213105806990_1400569072.png =800x)

- 返回至演示页面，再次点击` Add Number 1 and Number 2`。 DevTools 会暂停演示并在 Sources 面板中突出显示一行代码。 DevTools 应在此代码行暂停：
```
 function onClick() {
```

如果是在其他代码行暂停，请按 `Resume Script Execution` 继续执行脚本， {:.cdt-inl} 直到在正确的代码行暂停为止。

```
注：如果是在其他代码行暂停，可使用浏览器扩展程序在您访问的每一页上注册一个 click 事件侦听器。 使用扩展程序的 click 侦听器可执行暂停操作。 如果是采用无痕模式进行隐私浏览，将会停用所有扩展程序，而且您会发现您每次都在正确的代码行暂停。
```

Event Listener Breakpoints 只是 DevTools 提供的多种断点类型之一。 您需要记住所有不同的类型，因为每种类型最终都可帮助您尽快调试不同情景的问题。 如需了解使用每种类型的时机和方式，请参阅使用[断点暂停代码](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints?hl=zh-cn)。


## 第4步:单步调试
一个常见的错误原因是脚本执行顺序有误。 可以通过单步调试代码一次一行地检查代码执行情况，准确找到执行顺序异常之处。 立即尝试：
![](assets/20191213110531962_1990532716.png =800x)

这就是单步调试代码的基本思路。 如果看一下 `get-started.js` 中的代码，您会发现错误多半出在 updateLabel() 函数的某处。 您可以使用另一种断点来暂停较接近极可能出错位置的代码，而不是单步调试每一行代码


## 第５步：设置代码行断点

代码行断点是最常见的断点类型。 如果您想在执行到某一行代码时暂停，请使用代码行断点：
![](assets/20191213110714365_875589811.png =800x)

![](assets/20191213110751788_118885242.png =800x)


## 第６步：检查变量值
`addend1`、`addend2` 和` sum` 的值疑似有问题。 这些值位于引号中，这意味着它们是字符串。 这个假设有助于说明错误的原因。 现在可以收集更多信息。 DevTools 可提供许多用于检查变量值的工具。

### 方法１：scope窗口
在某代码行暂停时，`Scope` 窗格会显示当前定义的局部和全局变量，以及各变量值。 其中还会显示闭包变量（如果适用）。 双击变量值可进行编辑。 如果不在任何代码行暂停，则 `Scope` 窗格为空。

![](assets/20191213111509679_628565043.png =800x)

### 方法2:监视表达式
`Watch Expressions` 标签可让您监视变量值随时间变化的情况。 顾名思义，监视表达式不仅限于监视变量。 您可以将任何有效的 `JavaScript` 表达式存储在监视表达式中。 立即尝试：
- 点击 `Watch` 标签。
- 点击 `Add Expression` 添加表达式。
- 输入 `typeof sum`。
- 按 Enter 键。 DevTools 会显示 `typeof sum: "string"`。 冒号右侧的值就是监视表达式的结果。
![](assets/20191213111736908_197879380.png =800x)

正如猜想，sum 的求值结果本应是数字，而实际结果却是字符串。 现在已确定这就是错误的原因。


### 方法3:控制台
除了查看 console.log() 消息以外，您还可以使用控制台对任意 JavaScript 语句求值。 对于调试，您可以使用控制台测试错误的潜在解决方法。 立即尝试：

如果您尚未打开 `Console` 抽屉式导航栏，请按` Escape` 将其打开。 该导航栏将在 `DevTools `窗口底部打开。
在 Console 中，输入 `parseInt(addend1) + parseInt(addend2)`。 此语句有效，因为您会在特定代码行暂停，其中 `addend1` 和 `addend2 `在范围内。
按 Enter 键。 DevTools 对语句求值并打印输出 6，即您预计演示页面会产生的结果。

![](assets/20191213112056031_1994130516.png =800x)


## 第7步:应用修正的方法

您已找到修正错误的方法。 接下来就是尝试通过编辑代码并重新运行演示来使用修正方法。 您不必离开 DevTools 就能应用修正。 您可以直接在 DevTools UI 内编辑 JavaScript 代码。 立即尝试：

![](assets/20191213112237590_197457216.png =800x)

```
Note: 此工作流程只能修正在浏览器中运行的代码， 不能为访问您页面的所有用户修正代码。 为此，您需要修正自己服务器上的代码。
```

## 后续步骤
恭喜！现在您已了解如何在调试 JavaScript 时充分使用 Chrome DevTools。 本教程介绍的工具和方法有助于您节省大量时间。

本教程仅介绍两种设置断点的方法。 DevTools 还提许多其他方法，其中包括：

- 仅在满足您指定的条件时触发的条件断点。
- 发生已捕获或未捕获异常时触发的断点。
- 当请求的网址与您提供的子字符串匹配时触发的 XHR 断点。

如需了解使用每种类型的时机和方式，请参阅[使用断点暂停代码](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints?hl=zh-cn)
有几个代码单步执行控件未在本教程中予以说明。 如需了解详情，请参阅[单步执行时越过代码行](https://developers.google.com/web/tools/chrome-devtools/javascript/reference?hl=zh-cn#stepping)