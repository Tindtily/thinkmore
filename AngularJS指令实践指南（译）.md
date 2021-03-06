# AngularJs 指令实践指南(译)
* 出处：[sitepoint](https://www.sitepoint.com/practical-guide-angularjs-directives)

指令（Directives）是所有AngularJs应用最重要的组件。尽管AngularJS自带了丰富的指令，但你会经常需要为应用程序创建特定的指令。这篇教程将概述自定义指令，以及介绍在实际项目中如何使用指令。教程最后，我会指导你通过Angular指令来创建一个简单的记事本APP。
　　
## 概述

一个指令引入一种新的语法。指令作为DOM元素的标记，赋予元素特殊的行为。比如，静态HTML无法知道如何创建并展示一个日期选择器控件。为了让HTML能够识别这个新的（注：创建日期选择器控件）语法，我们就需要一条指令，这条指令会通过某种方法创建一个支持选择日期的元素。下面我们会了解这是如何实现的。

如果你之前写过一个Angular应用，那么你肯定用到过指令，无论你是否意识到。你可能已经用过简单的指令，比如`ng-model`，`ng-repeat`，`ng-show`，等等。所有这些指令都会赋予DOM元素特殊的行为。比如，`ng-repeat`重复一个特定的元素，而`ng-show`会有条件的显示一个元素。如果你想让一个元素支持拖拽，你也要创建一个指令来实现它。指令的基本思想很简单，即通过对元素绑定事件监听或者改变DOM，使你的HTML成为真正交互式的。

## jQuery  视角

试想一下如何使用jQuery创建一个日期选择器。首先在HTML中添加一个普通的输入框，然后通过jQuery调用`$(element).datePicker()`，来将它转变成一个日期选择器。但是，仔细想一下。当一个设计人员查看HTML时，他/她能立即猜到这个输入框实际的作用吗？这只是一个输入框还是一个日期选择器？你不得不查看jQuery代码来确定。Angular的方法是使用指令来扩展HTML。所以，一个日期选择器指令看起来就像这样：

`<date-picker></date-picker>`

或者也可以像这样：

`<input type='text' date-picker/>`

这种创建UI组件的方法直接且清晰。你通过查看元素就可以很容易地知道它是什么。

## 创建自定义指令

一个Angular指令可以有四种表现形式：

1. 作为一个新的HTML元素（`<date-picker></date-picker>`）
2. 作为元素的属性（`<input input='text' date-picker/>`）
3. 作为CSS class（`<input type='text' class='date-picker'/>`）
4. 作为注释（`<!--directive:date-picker-->`）

当然，我们能控制我们的指令在HTML里面的表现形式。现在，让我们看一下一个典型的Angular指令的写法。指令的注册方式与`controller`相同。但是它返回一个拥有指令配置属性的简单的对象（指令定义对象）。下面是一个简单的Hello World指令。

    var app = angular.module('myapp', []);
    app.directive('helloWorld', function() {
        return {
            restrict: 'AE',
            replace: 'true',
            template: '<h3>Hello World</h3>'
        };
    };

上面的代码中，`app.directive()`方法在我们的模块中注册了一个新的指令。方法的第一个参数是指令的名称。第二个参数是返回一个指令定义对象。如果你的指令依赖外部的对象/服务，例如`$rootScope`，`$http`或`$compile`，他们可以被注入到这里。这个指令可以在HTML中这样用：

    <hello-world/>

或者：

    <hello:world/>

或者作为元素属性：

    <div hello-world></div>

或者：

    <div hello:world/>

如果你想遵循HTML5规范，你可以使用前缀来修饰属性名`x-`或`data-`。因此，下面的标签也可以匹配`helloWorld`指令。

    <div data-hello-world></div>

    OR

    <div x-hello-world></div>

## 注意：
当匹配指令是，Angular从元素属性名中剔除前缀`x-`或者`data-`。然后将转换`-`或`:`分隔符转换成驼峰命名法，从而匹配注册的指令。这就是为什么我们在HTML中以`hello-world`的形式使用`helloWorld`指令。

尽管上面的指令仅仅实现了静态文本的显示，但这里面还是有一些有意思的地方值得我们去发掘。在指令定义对象中，我们使用了三个属性来配置。下面我们逐一看一下他们的作用。

* `restrict` – 此属性规定如何在HTML中使用指令（还记得一个指令的四种表现形式吗）。例子中我们设置为：`'AE'`。所以，可以作为新的元素或者一个元素属性来引入指令。如果希望使用class的形式引入指令，我们可以设置`restrict`为`'AEC'`。
* `template` – 此属性规定了指令被Angular编译（compile）和链接（link）后生成的HTML的样子。属性值可以不是一个简单的字符串。`template`可以很复杂，而且经常包含其他指令、表达式（`{{}}`）等等。在大多数情况下你会看到使用`templateUrl`替代`template`。所以，理想情况下你应该把模版放到单独的HTML文件中，然后使用`templateUrl`指向它。
* `replace` – 此属性规定包含指令的HTML元素是否被生成的模板所替代。上述例子中我们用到了`<hello-world></hello-world>`指令，并且设置`replace`为`true`。所以在指令编译完成后，输出的模版会替换掉`<hello-world></hello-world>`。最终输出结果为`<h3>Hello World!</h3>`。如果设置`replace`为`false`（默认值），输出的模板会被插入到引入指令的元素中。

打开这个[plunker](http://plnkr.co/edit/GKI339z2VDdZTOE2bGFP)，右键“Hello World!!”检查元素，可以很直观的看明白。

## `link`函数和Scope

通过指令生成的模板只有在正确的scope中编译才有意义。默认情况下，指令并不会创建一个新的子scope。更多的，它使用父级scope。如果这个指令在一个controller中，那么它会使用这个controller的scope。

为了利用scope，我们可以使用`link`函数。它是由指令定义对象的`link`属性配置的。下面改写下我们的`helloWorld`指令，当用户在输入框输入颜色名字的时候，Hello World文本的背景色自动改变。并且，当用户点击Hello World时，背景颜色恢复到白色。HTML代码如下：

    <body ng-controller="MainCtrl">
        <input type="text" ng-model="color" placeholder="Enter a color"/>
        <hello-world/>
    </body>

修改后的`helloWorld`指令如下：

    app.directive('helloWorld', function(){
        return {
            restrict: 'AE',
            replace: true,
            template: '<p style="background-color:{{color}}">Hello World',
            link: function(scope, elem, attrs){
                elem.bind('click', function(){
                    elem.css('background-color', 'white');
                    scope.$apply(function(){
                        scope.color = 'white';
                    });
                });
                elem.bind('mouseover', function(){
                    elem.css('cursor', 'pointer')
                })
            }
        }
    })

注意指令中的`link`函数，它有三个参数：

* `scope` – 指令所在scope。例子中，指令的scope就是父级controller的scope。
* `elem` – 使用jQLite（jQuery子集）包装的指令所在的DOM元素。如果你在引入Angular之前引入了jQuery，这个对象会使用jQuery包装。所以在DOM操作时就不需要使用`$()`来包装。
* `attrs` –