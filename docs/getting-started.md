# 入门

要获得新的Angular应用程序入门帮助，请查看[Angular CLI](https://cli.angular.io/).

对于现有的应用程序，请按照以下步骤开始使用Angular Material。

## 第1步：安装Angular Material and Angular CDK

对于现有的应用程序，请按照以下步骤开始使用Angular Material。
在以下示例中使用适合您项目的内容。

### NPM

```bash
npm install --save @angular/material @angular/cdk
```

### Yarn

```bash
yarn add @angular/material @angular/cdk
```

### 替代方法：快照构建

快照版本还包含来自主服务器的最新更改。
请注意，此快照构建不应被认为是稳定的，并且可能会在各个发行版之间中断

### NPM - builds

```bash
npm install --save angular/material2-builds angular/cdk-builds
```

### Yarn - builds

```bash
yarn add angular/material2-builds angular/cdk-builds
```

## 第2步：动画

一些Material组件依赖于Angular动画模块，以便能够进行更高级的转换。
如果你想让这些动画在你的应用中运行， 你必须安装`@angular/animations`模块并在你的应用中包含`BrowserAnimationsModule`。

### NPM - animations

```bash
npm install --save @angular/animations
```

### Yarn - animations

```bash
yarn add @angular/animations
```

!!! Note

    `@angular/animations`使用所有浏览器都不支持的WebAnimation API。
    如果您想在这些浏览器中支持Material组件动画, 你将不得不包含一个[polyfill](https://github.com/web-animations/web-animations-js).

```ts
import {BrowserAnimationsModule} from '@angular/platform-browser/animations';

@NgModule({
  ...
  imports: [BrowserAnimationsModule],
  ...
})
export class PizzaPartyAppModule { }
```

如果您不想为项目添加其他依赖项, 你可以使用`NoopAnimationsModule`.

```ts
import {NoopAnimationsModule} from '@angular/platform-browser/animations';

@NgModule({
  ...
  imports: [NoopAnimationsModule],
  ...
})
export class PizzaPartyAppModule { }
```

## 第3步：导入组件模块

为您想要使用的每个组件导入NgModule：

```ts
import {MatButtonModule, MatCheckboxModule} from '@angular/material';

@NgModule({
  ...
  imports: [MatButtonModule, MatCheckboxModule],
  ...
})
export class PizzaPartyAppModule { }
```

或者，您可以创建一个单独的NgModule，导入将在您的应用程序中使用的所有Angular Material组件。
然后，您可以在需要使用组件的任何地方包含此模块。

```ts
import {MatButtonModule, MatCheckboxModule} from '@angular/material';

@NgModule({
  imports: [MatButtonModule, MatCheckboxModule],
  exports: [MatButtonModule, MatCheckboxModule],
})
export class MyOwnCustomMaterialModule { }
```

无论使用哪种方法，请确保导入Angular Material模块_after_ Angular的BrowserModule，因为导入顺序对于NgModules很重要。

## 第4步：包含一个主题

包含主题是将所有核心和主题样式应用到您的应用程序所必需的。

要开始使用预构建的主题，请在您的应用程序中全局添加一个Angular Material的预建主题。
如果您使用的是Angular CLI，可以将其添加到`styles.css`中：

```css
@import "~@angular/material/prebuilt-themes/indigo-pink.css";
```

如果您不使用Angular CLI，则可以通过`index.html`中的`<link>`元素包含预构建主题。

有关主题的更多信息以及有关如何创建自定义主题的说明, 请参阅[主题指南](./theming.md).

## 第5步：手势支持

一些组件(`mat-slide-toggle`, `mat-slider`, `matTooltip`)依靠[HammerJS](http://hammerjs.github.io/)进行手势。
为了获得这些组件的完整功能集，必须将HammerJS加载到应用程序中。

您可以通过[npm](https://www.npmjs.com/package/hammerjs)将HammerJS添加到您的应用程序中, 一个CDN(如那个[Google CDN](https://developers.google.com/speed/libraries/#hammerjs)), 或直接从您的应用程序提供。

要通过npm进行安装，请使用以下命令：

### NPM - hammerjs

```bash
npm install --save hammerjs
```

### Yarn - hammerjs

```bash
yarn add hammerjs
```

安装后，将其导入到应用程序的入口点 (例如 `src/main.ts`).

```ts
import 'hammerjs';
```

## 步骤6（可选）：添加材料图标

如果你想在官方[Material Design Icons](https://material.io/icons/)使用`mat-icon`组件, 在你的`index.html`中加载图标字体。

```html
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
```

有关使用“材质图标”的更多信息, 检查一下[Material Icons Guide](https://google.github.io/material-design-icons/).

请注意，`mat-icon`支持任何字体或svg图标; 使用材料图标是许多选项之一.

## 附录：配置SystemJS

如果您的项目正在使用SystemJS进行模块加载, 您需要在SystemJS配置中添加`@angular/material`和`@angular/cdk`.

`@angular/cdk`包由多个入口点组成。
每个入口点都必须使用SystemJS单独注册。

下面是一个配置示例`@angular/material`, 使用`@angular/cdk/platform`和`@angular/cdk/a11y`:

```js
System.config({
  // 现有的配置选项
  map: {
    // ...
    '@angular/material': 'npm:@angular/material/bundles/material.umd.js',

    // 单独的CDK
    '@angular/cdk/platform': 'npm:@angular/cdk/bundles/cdk-platform.umd.js',
    '@angular/cdk/a11y': 'npm:@angular/cdk/bundles/cdk-a11y.umd.js',
    // ...
  }
});
```

## 示例Angular Material项目

- [material.angular.io](https://material.angular.io) - 我们使用Angular Material构建我们自己的文档！