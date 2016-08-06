## AngularJs 指令实践指南（译）
* 出处：[sitepoint](https://www.sitepoint.com/practical-guide-angularjs-directives)

指令（Directives）是所有AngularJs应用最重要的组件。尽管AngularJS自带了丰富的指令，但你会经常需要为应用程序创建特定的指令。这篇教程将概述自定义指令，以及介绍在实际项目中如何使用指令。教程最后，我会指导你通过Angular指令来创建一个简单的记事本APP。
　　
## 概述

一个指令引入一种新的语法。指令作为DOM元素的标记，赋予元素特殊的行为。比如，静态HTML无法知道如何创建并展示一个日期选择器控件。为了让HTML能够识别这个新的（注：创建日期选择器控件）语法，我们就需要一条指令，这条指令会通过某种方法创建一个支持选择日期的元素。下面我们会了解这是如何实现的。

如果你之前写过一个Angular应用，那么你肯定用到过指令，无论你是否意识到。你可能已经用过简单的指令，比如<code>ng-model</code>，<code>ng-repeat</code>，<code>ng-show</code>，等等。所有这些指令都会赋予DOM元素特殊的行为。比如，<code>ng-repeat</code>重复一个特定的元素，而<code>ng-show</code>会有条件的显示一个元素。如果你想让一个元素支持拖拽，你也要创建一个指令来实现它。指令的基本思想很简单，即通过对元素绑定事件监听或者改变DOM，使你的HTML成为真正交互式的。

## jQuery  视角

试想一下如何使用jQuery创建一个日期选择器。首先在HTML中添加一个普通的输入框，然后通过jQuery调用<code>$(element).datePicker()</code>，来将它转变成一个日期选择器。但是，仔细想一下。当一个设计人员查看HTML时，他/她能立即猜到这个输入框实际的作用吗？这只是一个输入框还是一个日期选择器？你不得不查看jQuery代码来确定。Angular的方法是使用指令来扩展HTML。所以，一个日期选择器指令看起来就像这样：

    <date-picker></date-picker>

或者也可以像这样：

    <input type='text' date-picker/>
