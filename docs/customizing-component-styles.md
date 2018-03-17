# 自定义角度材质组件样式

## 造型概念

在定制角度材质组件的样式时，请记住3个问题：

1. 你的风格是封装的吗？
2. 你的风格比默认更具体吗？
3. 组件是组件的子组件，还是存在于DOM中的其他位置？

### 查看封装

默认情况下，Angular组件样式的作用域会影响组件的视图。
这意味着您编写的样式将影响组件模板中的所有元素。
它们不会影响模板中其他组件的子元素。
你可以在[Angular documentation](https://angular.io/guide/component-styles#view-encapsulation)中阅读更多关于视图封装的内容。
您也可以在Angular博客上看看[_Angular中的CSS状态_](https://blog.angular.io/the-state-of-css-in-angular-4a52d4bd2700)。

### 选择者特异性

每个CSS声明都具有基于所使用的选择器的类型和数量的特定等级。
更具体的样式将优先于不太特定的样式。
Angular Material使用组件的最小特定选择器，以便轻松覆盖它们。
您可以阅读更多关于特异性以及如何计算[MDN web文档](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity).

### 组件位置

一些Angular Material组件，特别是基于叠加层的组件，如MatDialog，MatSnackbar等，不作为组件的子项存在。
通常它们被注入DOM中的其他地方。
记住这一点很重要，因为即使使用高特异性和阴影穿孔选择器也不会针对不是您的组件的直接子项的元素。
全局风格建议用于定位这些组件。

## 造型叠加组件

基于覆盖的组件具有可用于定位覆盖窗格的`panelClass`属性（或类似）。
例如，要从对话框中删除填充：

```scss
// 在您的主题设置后将其添加到您的全局样式表中
.myapp-no-padding-dialog .mat-dialog-container {
  padding: 0;
}
```

```ts
this.dialog.open(MyDialogComponent, {panelClass: 'myapp-no-padding-dialog'})
```

由于您将样式添加到全局样式表中，因此适当地对其进行适当的范围是一种很好的做法。
尝试用您的应用程序名称或“自定义”为选择器添加前缀。
还要注意`mat-dialog-container`的填充默认是通过一个特殊性为1的选择器添加的。
自定义样式具有2的特异性，所以它们将始终优先。

## 造型其他组件

如果组件的视图封装处于打开状态（默认），则组件样式只会影响模板中的顶级子组件。
除非您执行以下任一操作，否则属于子组件的HTML元素不能作为组件样式的目标：

- 将覆盖样式添加到全局样式表中。确定选择器的范围，以便它仅影响您需要的特定元素。
- 关闭组件上的视图封装。如果你这样做，一定要适当的范围，否则你可能会偶然在你的应用程序的其他地方结束其他组件。
- 使用不推荐使用的阴影穿孔后代组合器强制样式应用于所有子元素。在[Angular文档](https://angular.io/guide/component-styles#deprecated-deep--and-ng-deep)中​​详细了解这个弃用的解决方案.