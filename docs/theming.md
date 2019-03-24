# 主题化您的**角料**应用程序

## 什么是主题

**主题**是将应用于角料组件的一组颜色。库的主题化方法基于[物料设计规范的指导][1].

在**角料**中，通过组合多个调色板来创建主题. 特别是，一个主题包括:

* 主调色板: 所有屏幕和组件中使用最广泛的颜色。
* 调色板: 用于浮动动作按钮和交互式元素的颜色。
* 警告调色板: 用于传达错误状态的颜色。
* 前景调色板: 文本和图标的颜色。
* 背景调色板: 用于元素背景的颜色。

在角料中, 所有主题样式均在构建时生成_statically_，以便您的应用程序不必在启动时花费周期来生成主题样式.

[1]: https://material.google.com/style/color.html#color-color-palette

## 使用预先构建的主题

**角料**预装了几个预先构建的主题`css`文件。
这些主题文件还包括核心的所有样式 (所有组件通用的样式), 所以你只需要在你的应用中包含一个用于**角料**的css文件.

您可以从`@angular/material/prebuilt-themes`直接将主题文件包含到您的应用程序

可用的预建主题:

* `deeppurple-amber.css`
* `indigo-pink.css`
* `pink-bluegrey.css`
* `purple-green.css`

如果您使用的是**角令**, 这与在`styles.css`文件中包含一行一样简单:

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';
```

或者，您可以直接引用该文件。这看起来像:

```html
<link href="node_modules/@angular/material/prebuilt-themes/indigo-pink.css" rel="stylesheet">
```

实际路径将取决于您的服务器设置。

您还可以将该文件与应用程序的其余CSS进行连接。

最后, 如果你的应用的内容没有放在`mat-sidenav-container`元素中, 你需要将`mat-app-background`类添加到你的包装器元素中 (例如 `body`).
这可确保正确的主题背景应用于您的页面。

## 定义自定义主题

当你想要更多的自定义比预先建立的主题提供, 你可以创建你自己的主题文件.

自定义主题文件有两件事:

1. 导入`mat-core()`sass mixin。 这包括多个组件使用的所有常见样式。 **这应该只在你的应用程序中包含一次。** 如果这个mixin包含多次, 您的应用程序将以这些常见样式的多个副本结束.
2. 将主题数据结构定义为多个调色板的组合。 可以使用`mat-light-theme`函数或`mat-dark-theme`函数创建此对象。 这个函数的输出然后传递给`angular-material-theme`混合，它将输出主题的所有相应样式。

一个典型的主题文件将如下所示：

```scss
@import '~@angular/material/theming';
// 加上您应用中其他组件的导入。

// 包含**角料**的常见样式。 我们在这里包含这个，这样你只需要在你的应用中加载一个**角料**的css文件。
// 确保你只包含这个mixin一次！
@include mat-core();

// 使用`palette.scss`中提供的Material Design面板（上面导入）为主题定义调色板。
// 对于每个调色板，您可以选择指定默认，较亮和较暗的色调。
// 可用的调色板： https://www.google.com/design/spec/style/color.html

$candy-app-primary: mat-palette($mat-indigo);
$candy-app-accent:  mat-palette($mat-pink, A200, A100, A400);

// 警告调色板是可选的（默认为红色）。
$candy-app-warn:    mat-palette($mat-red);

// 创建主题对象（包含所有调色板的Sass地图）。
$candy-app-theme: mat-light-theme($candy-app-primary, $candy-app-accent, $candy-app-warn);

// 包括核心和应用程序中使用的每个组件的主题样式。
// 或者，您可以为您正在使用的每个组件导入和@include主题混合。
@include angular-material-theme($candy-app-theme);
```

你只需要这个单一的Sass文件;您不需要使用Sass来设计应用程序的其余部分。

!!! warning

    如果您正在使用**角令**, 支持编译Sass到CSS是内置的; 你只需要在`angular-cli.json`中的``styles'`列表中添加一个新的条目来指向主题文件(例如, `unicorn-app-theme.scss`).

如果你不使用**角令**， 您可以使用任何现有的Sass工具来构建文件(如gulp-sass 要么 grunt-sass). 最简单的方法是使用`node-sass` CLI; 你只需运行:

```bash
node-sass src/unicorn-app-theme.scss dist/unicorn-app-theme.css
```

然后将输出文件包含在index.html中。

主题文件不应该导入到其他SCSS文件中。
这会导致重复样式被写入您的CSS输出。
如果您想在其他SCSS文件中使用主题定义对象（例如`$ candy-app-theme`）
那么主题对象的定义应该被分解成它自己的文件，
与包含`mat-core`和`angular-material-theme` mixin分开。

主题文件可以与应用程序的其余部分进行连接和缩小。

请注意，如果您将生成的主题文件包含在Angular组件的`styleUrls`中，
这些样式将受制于该组件的[视图封装](https://angular.io/docs/ts/latest/guide/component-styles.html#!#view-encapsulation).

### 多个主题

您可以通过多次包含`angular-material-theme`混合来为应用程序创建多个主题, 每个包含都由一个额外的CSS类进行门控.

请记住，只有一次只包含`@ mat-core`混入;
它不应该包含在每个主题中。

### 定义多个主题的示例

```scss
@import '~@angular/material/theming';
// 加上您应用中其他组件的导入。

// 包含角度材质的常用样式。
// 我们在这里包含这个，这样你只需要在你的应用中加载一个用于**角料**的css文件。
// **确保你只包含这个mixin一次!**
@include mat-core();

// 定义默认主题（与上面的示例相同）.
$candy-app-primary: mat-palette($mat-indigo);
$candy-app-accent:  mat-palette($mat-pink, A200, A100, A400);
$candy-app-theme:   mat-light-theme($candy-app-primary, $candy-app-accent);

// 包括默认主题样式.
@include angular-material-theme($candy-app-theme);


// 定义一个备用的黑暗主题.
$dark-primary: mat-palette($mat-blue-grey);
$dark-accent:  mat-palette($mat-amber, A200, A100, A400);
$dark-warn:    mat-palette($mat-deep-orange);
$dark-theme:   mat-dark-theme($dark-primary, $dark-accent, $dark-warn);

// 用CSS类在块内包含替代主题样式。
// 无论你想要什么，你都可以创建这个CSS类。,在这个例子中，
// 具有`.unicorn-dark-theme`的元素内部的任何组件都将受到此替代黑暗主题而不是默认主题的影响。
.unicorn-dark-theme {
  @include angular-material-theme($dark-theme);
}
```

在上面的例子中，具有`unicorn-dark-theme`类的父类中的任何组件都将使用黑色主题，
在上面的例子中，具有`unicorn-dark-theme`类的父类中的任何组件都将使用黑色主题，

你可以用这种方式包括尽可能多的主题。
你也可以在不同的文件中使用@ @包含`angular-material-theme`，然后根据最终用户的交互延迟加载它们（如何根据你的应用程序延迟加载CSS资源）。

然而，重要的是要记住`mat-core` mixin应该只包含_once_。

### 多个主题和基于覆盖的组件

由于某些组件（例如菜单，选择，对话等）位于全局覆盖容器的内部，
需要额外的步骤才能让这些组件受到主题的CSS类选择器的影响(`.unicorn-dark-theme`在上面的例子中).

为此，您可以将相应的类添加到全局覆盖容器。
对于上面的例子，这看起来像：

```ts
import {OverlayContainer} from '@angular/cdk/overlay';

@NgModule({
  // ...
})
export class UnicornCandyAppModule {
  constructor(overlayContainer: OverlayContainer) {
    overlayContainer.getContainerElement().classList.add('unicorn-dark-theme');
  }
}
```

## 只主题化某些组件

`angular-material-theme` mixin将为库中的[所有组件输出样式](https://github.com/angular/material2/blob/master/src/lib/core/theming/_all-theme.scss).
如果您只使用组件的子集（或者如果您想更改特定组件的主题），您可以包含特定于组件的主题混合。
您还需要包含`mat-core-theme` mixin，其中包含常见行为的主题特定样式(如涟漪).

```scss
@import '~@angular/material/theming';
// 加上您应用中其他组件的导入。

// 包含角度材质的常用样式。
// 我们在这里包含这个，这样你只需要在你的应用中加载一个用于**角料**的css文件。
// **确保你只包含这个mixin一次！**
@include mat-core();

// 定义主题。
$candy-app-primary: mat-palette($mat-indigo);
$candy-app-accent:  mat-palette($mat-pink, A200, A100, A400);
$candy-app-theme:   mat-light-theme($candy-app-primary, $candy-app-accent);

// 仅包含指定组件的主题样式。
@include mat-core-theme($candy-app-theme);
@include mat-button-theme($candy-app-theme);
@include mat-checkbox-theme($candy-app-theme);
```

## 主题化你自己的组件

有关自己组件的更多细节, 请参阅[主题化组件](./theming-your-components.md).

## 未来的工作

* 一旦CSS变量（自定义属性）在我们支持的所有浏览器中都可用，我们将探索如何利用它们使主题变得更简单。
* 随着发展的继续，将增加更多预建主题。