# 角料排版

## 什么是排版

排版是一种排列方式，使文字在显示时更清晰易读，更具吸引力。
Angular Material的排版基于[材料设计规范][1]中的指导原则，并按字体排列。
每个级别都有一个`font-size`，`line-height`和`font-weight`。
可用的级别是：

* `display-4`, `display-3`, `display-2` and `display-1` - Large, one-off headers, 通常在页面的顶部(e.g. a hero header).
* `headline` - 与`<h1>`标签对应的部分标题。
* `title` - 部分标题对应于`<h2>`标签。
* `subheading-2` - 部分标题对应于`<h3>`标签。
* `subheading-1` - 部分标题对应于`<h4>`标签。
* `body-1` - 基体文本。
* `body-2` - 粗体文本。
* `caption` - 小体和暗示文字。
* `button` - 按钮和锚点。
* `input` - 表单输入字段。

排版级别被收集到用于生成CSS的排版配置中。

## 用法

要开始，您首先包含具有300,400和500重量的`Roboto`字体。
您可以自己托管，也可以从[Google字体][2]中加入：

```html
<link href="https://fonts.googleapis.com/css?family=Roboto:300,400,500" rel="stylesheet">
```

现在，您可以将相应的CSS类添加到您想要样式化的元素中：

```html
<h1 class="mat-display-1">寒鸦爱我的石英大狮身人面像。</h1>
<h2 class="mat-h2">敏捷的棕色狐狸跳过了懒狗。</h2>
```

默认情况下，Angular Material不应用任何全局CSS。
为了更广泛地应用图书馆的印刷风格，您可以利用`mat-typography` CSS类。
这个类将风格所有的后代本地元素。

```html
<!-- 默认情况下，Angular Material不将全局样式应用于本地元素。 -->
<h1>这个标题是无风格的</h1>

<!-- 应用mat-tyography类为本地元素添加样式。 -->
<section class="mat-typography">
  <h1>这个头文件将被样式化</h1>
</section>
```

## 定制

字体定制是Angular Material基于Sass的主题的扩展。
与创建自定义主题类似，您可以创建自定义印刷配置。

```scss
@import '~@angular/material/theming';

// 定义一个覆盖font-family以及`headlines`和`body-1`级别的自定义印刷配置。
$custom-typography: mat-typography-config(
  $font-family: 'Roboto, monospace',
  $headline: mat-typography-level(32px, 48px, 700),
  $body-1: mat-typography-level(16px, 24px, 500)
);
```

如上例所示，通过使用`mat-typography-config`函数来创建印刷配置，该函数提供前面介绍的font-family和一组印刷级别。
每个印刷级别由`mat-typography-level`函数定义，它需要`font-size`，`line-height`和`font-weight`。 ,**注意**`font-family`必须用引号括起来。

一旦创建了自定义印刷术定义，它就可以用于通过不同的Sass mixins生成样式。

```scss
// 覆盖排版CSS类（例如，mat-h​​1，mat-display-1，mat-typography等）。
@include mat-base-typography($custom-typography);

// 替代特定角度材质组件的版式。
@include mat-checkbox-typography($custom-typography);

// 覆盖所有角度材质的版式，包括书写板印刷和所有组件。
@include angular-material-typography($custom-typography);
```

如果您使用Material的主题，您还可以将您的印刷配置传递给`mat-core`混合：

```scss
// Override the typography in the core CSS.
@include mat-core($custom-typography);
```

有关排版功能和默认配置的更多详细信息，请参阅[源代码](https://github.com/angular/material2/blob/master/src/lib/core/typography/_typography.scss).

## 您的自定义CSS中的材质排版

Angular Material包含印刷实用工具mixin和函数，您可以使用它们自定义您自己的组件：

* `mat-font-size($config, $level)` - 根据提供的配置和级别获取`font-size`。
* `mat-font-family($config)` - 根据提供的配置获取`font-family`。
* `mat-line-height($config, $level)` - 根据提供的配置和级别获取`line-height`。
* `mat-font-weight($config, $level)` - 根据提供的配置和级别获取`font-weight`。
* `mat-typography-level-to-styles($config, $level)` - Mixin接受配置对象和印刷级别，并输出一个简短的CSS`font`声明。

```scss
@import '~@angular/material/theming';

// 使用默认版式级别创建配置。
$config: mat-typography-config();

// 只使用材质`font-size`和`font-family`的自定义标题。
.unicorn-header {
  font-size: mat-font-size($config, headline);
  font-family: mat-font-family($config);
}

// 自定义标题，它使用`title`级别的所有印刷样式。
.unicorn-title {
  @include mat-typography-level-to-styles($config, title);
}
```

[1]: https://material.io/guidelines/style/typography.html
[2]: https://fonts.google.com/