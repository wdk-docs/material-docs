# 主题化您的自定义组件

为了使用Angular Material的工具设计自己的组件，组件的样式必须使用Sass来定义。

## 使用`@mixin`自动应用主题

### 为什么使用`@mixin`

使用`@mixin`函数的好处是，当你改变你的主题时，每个使用它的文件都会自动更新。
使用不同的主题参数调用`@mixin`可以在应用程序或组件中使用多个主题。

### How to use `@mixin`

我们可以通过在其主题文件中添加一个`@mixin`函数来更好地为我们的自定义组件添加主题，然后调用此函数来应用主题。

所有你需要的是在custom-component-theme.scss中创建一个`@mixin`函数

```scss
// 导入自定义主题并提取部分内容所需的所有工具
@import '~@angular/material/theming';

// 定义一个接受主题并输出组件颜色样式的mixin。
@mixin candy-carousel-theme($theme) {
  // 定义一个接受主题并输出组件颜色样式的mixin。
  $primary: map-get($theme, primary);
  $accent: map-get($theme, accent);

  // 定义一个接受主题并输出组件颜色样式的mixin。
  .candy-carousel {
    background-color: mat-color($primary);
    border-color: mat-color($accent, A400);
  }
}
```

现在你只需调用`@mixin`函数来应用主题：

```scss
// 导入预先构建的主题
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';
// 导入您的自定义输入主题文件，以便您可以调用自定义输入主题功能
@import 'app/candy-carousel/candy-carousel-theme.scss';
// 使用预构建主题中的$theme变量可以调用主题函数
@include candy-carousel-theme($theme);
```

有关主题功能的更多详细信息，请参阅[源代码中的注释](https://github.com/angular/material2/blob/master/src/lib/core/theming/_theming.scss).

### 使用`@mixin`的最佳实践

当使用`@mixin`时，主题文件应该只包含受传入主题影响的定义。

所有不受主题影响的样式应放置在`candy-carousel.scss`文件中。
这个文件应该包含所有不受大小，转换等主题影响的内容......

受该主题影响的样式应放置在一个单独的主题文件中，名称为_candy-carousel-theme.scss，该文件的名称前应该有一个`_`。
这个文件应该包含负责将主题应用到组件的`@mixin`函数。

## 使用调色板中的颜色

您可以使用`@angular/material/theming`中的主题函数和材料选项板变量。
您可以使用`mat-color`功能从调色板中提取特定的颜色。 例如：

```scss
// 导入主题功能
@import '~@angular/material/theming';
// 导入您的自定义它们
@import 'src/unicorn-app-theme.scss';

// 根据需要使用垫颜色从调色板中提取单个颜色。
// 色调可以是标准值之一（500，A400等）， 三种预先配置的色调之一（默认，较轻，较暗）， 或前面提到的任何前缀`-contrast`。
// 例如，`较暗对比`色调给出浅色以与`较暗`色相对比。
// 请注意，使用带`-contrast`修饰符的数字色调时需要使用引号。
// 可用的调色板： https://www.google.com/design/spec/style/color.html
.candy-carousel {
  background-color: mat-color($candy-app-primary);
  border-color: mat-color($candy-app-accent, A400);
  color: mat-color($candy-app-primary, '100-contrast');
}
```