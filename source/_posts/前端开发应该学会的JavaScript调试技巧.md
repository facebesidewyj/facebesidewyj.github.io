---
title: 前端开发应该学会的JavaScript调试Bug技巧
date: 2018-12-09 21:24:46
categories: JS实战
tags:
  - JavaScript
description: JavaScript调试技巧
---

熟练使用 Chrome DevTools 来调试 JS 代码是每个前端的必备技能，大多数新手前端工程师喜欢用 console.log()打印相关上下文信息来调试代码。

**console.log()调试带来的问题**：

1. 使用 console.log()打印调试比较繁琐，而且不能很快定位出问题点，缺乏效率。
2. 需要多次手写代码，一不小心忘记删除也会造成线上代码不美观。

# 第一步：复现 Bug

调试 bug 的第一步就是要多次复现 Bug，如果不是稳定复现的 Bug，解决起来将会非常麻烦，只能靠经验推测。

所以在遇到 Bug 时，要尽可能需要多次重现 Bug，减少不必要的步骤。

我们通过一个测试的 Demo 来逐步修复 Bug：

- [Demo 地址](https://googlechrome.github.io/devtools-samples/debug-js/get-started)
- Demo 中输入 1 和 2，结果为 12，这明显是一个需要修复的 Bug

# 第二步：使用 Chrome DevTools 的断点暂停代码执行

Chrome DevTools 可以在代码运行期间通过设置断点来暂停代码，并且在暂停区间可以看到当前上下文中变量的值。

1. 打开 Chrome 控制面板
2. 点击**Sources**标签
3. 点击**Event Listener Breakpoints**，这是一个事件监听断点选择列表
4. 展开在**Mouse**项
5. 选中**click**复选框

![](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_cd5ea0ea-09fc-44d4-8a76-ee80e84165db.png)

6. 这时，再次点击按钮“Add Number 1 and Number 2”，代码会暂停运行。

> 选择“click”时，Chrome DevTools 会在所有 click 事件上设置基于事件的断点。click 任何节点，并且该节点具有 click 事件时，DevTools 会自动暂停到该节点的 click 处理程序的第一行。

# 第三步：逐步调试代码，发现问题

逐步调试代码可以让我们清楚代码运行情况，找出代码所在的问题点。

- 在**Sources**标签中，点击**Step into next function call**

![](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_6f6ca455-08d2-4dda-b2a5-fb6911228f64.png)

这个按钮能让你逐步查看 onClick() 函数的运行，每次运行一行。

- 点击**Step over next function call**按钮

  ![](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_6f6ca455-08d2-4dda-b2a5-fb6911228f64.png)

它会让 DevTools 执行 inputsAreEmpty() 而不进入该函数的代码。

# 第四步：设置行断点

代码行断点是我们在开发中经常用的一种断点。它可以让代码暂停在指定的一行。

- 查看 updateLabel() 中的最后一行代码，如下所示：

```text
label.textContent = addend1 + ' + ' + addend2 + ' = ' + sum;
```

这行代码显然是问题所在，在这行设置行断点。

- 点击**Resume script execution**按钮

![img](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_bd761f19-048e-40e1-8506-a0e71f36e3a2.png)

这段代码会持续执行，直到问题所在的断点行停止。

- 查看 updateLabel() 中已执行的代码行。DevTools 会显示 addend1，addend2 和 sum 的值。

sum 的值明显问题，求值结果应该是数字，但这里却是字符串。这就是造成 bug 的原因。

![](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_50ae6605-ee58-458f-ab6c-52262c8df337.png)

# 第五步：检查变量值

造成 Bug 的一个常见原因就是变量或函数产生了异常值。

DevTools 上有个替代 console.log() 的工具叫 **Watch Expressions**，使用 **Watch Expressions** 能够监控变量值随着时间的变化。**Watch Expressions** 并不仅仅能监控变量，你可以将任何有效的 JavaScript 表达式存储在 **Watch Expressions** 中。

- 在 DevTools 上的**Sources**面板上点击**Watch**，展开这部分。
- 点击**Add Expression**

![img](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_5cca3485-41e2-41e0-ac37-671580e63872.png)

- 输入 `typeof sum`
- 按 Enter 键，DevTools 会显示 `typeof sum: "string"`。

![img](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_803f22d6-0575-404f-912f-15e9c9205cd9.png)

这里可以看到，sum 被处理成了字符串。这就是 Bug 的来源。

DevTools 中另一个能取代 console.log() 的工具是 **Console**，可以使用 **Console** 对任意 JavaScript 语句求值。

- 按下 Esc 可以打开 **Console**，显示在 DevTools 窗口底部。
- 在 **Console** 中输入`parseInt(addend1) + parseInt(addend2)`
- 按 Enter 键，DevTools 会对语句求值，输出结果。

![img](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_0b266779-82f4-4a80-a97d-8ebf23008647.png)

# 第六步：修复问题

确定 Bug 问题所在之后，剩下就是编辑代码，尝试修复，并重新运行 Demo。直接在 DevTools 中就可以修复代码，步骤如下：

1. 在 DevTools 的**Sources**面板的代码编辑器中，用 `var sum = parseInt(addend1) + parseInt(addend2)` 替换 `var sum = addend1 + addend2`。

2. 按 Command+S（Mac）或 Ctrl+S（Windows，Linux）保存，代码被修改。

3. 点击 **Deactivate breakpoints**

![img](./../images/0_hyuaGwUfClHRIpuk.png)

它变为蓝色表示正处于活动状态。如果这样设置，DevTools 会忽略你设置的任何断点。

4. 点击**Resume script execution**

![img](./../images/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_bd761f19-048e-40e1-8506-a0e71f36e3a2.png)

5. 在项目中修改对应的代码，Bug 解决了。

> **参考资料：**  **https://codeburst.io/learn-how-to-debug-javascript-with-chrome-devtools-9514c58479db**
