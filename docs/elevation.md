# 海拔

Angular Material的高程类别和混合类型允许您沿z轴添加元素之间的分隔。
所有材料设计元素都有静止的高程。
另外，有些元素可能会因用户交互而改变其高程。
[材料设计规范说明](https://material.io/guidelines/material-design/elevation-shadows.html)了如何最好地使用高程。

Angular Material提供了两种控制元素高程的方法：预定义的CSS类和mixin。

## 预定义的CSS类

向元素添加高程的最简单方法是简单地添加一个预定义的CSS类mat-elevation-z＃，其中`＃`是所需的高程数0-24。
动态高程可以通过切换高程等级来实现：

```html
<div [class.mat-elevation-z2]="!isActive" [class.mat-elevation-z8]="isActive"></div>
```

<!-- example(elevation-overview) -->

## Mixins

也可以通过`mat-elevation`混合在CSS中添加海拔，该混合采用数字0-24表示元素的高程。
为了使用mixin，你必须导入`〜@angular/material/theming`：

```scss
@import '~@angular/material/theming';

.my-class {
  @include mat-elevation(2);
}
```

为了方便起见，您可以使用`mat-elevation-transition` mixin在高程更改时添加转换：

```scss
@import '~@angular/material/theming';

.my-class {
  @include mat-elevation-transition;
  @include mat-elevation(2);

  &:active {
    @include mat-elevation(8);
  }
}
```