# 角料的双向性

## 为您的应用程序设置文本方向

[`dir`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/dir)属性通常应用于页面的`<html>`或`<body>`元素。
但是，它可以用于Angular应用程序中的任何元素，以将文本方向应用于页面的较小子集。

所有的角料组件都会自动反映其容器的LTR/RTL方向。

## 在你自己的组件中读取文本方向

`@angular/cdk/bidi`提供了一个`Directionality`注入，可以被应用程序中的任何组件使用。
要使用它，您必须从`@angular/cdk/bidi`中导入`BidiModule`。

`Directionality`提供了两个有用的属性：

* `value`: 当前的文本方向，无论是`ltr`还是`rtl`。
* `change`: 每当文本方向改变时都会发出一个`Observable`。 请注意，这只能捕获Angular应用程序上下文中`dir`属性的更改。它不会对`<html>`和`<body>`上的`dir`进行修改，因为它们被认为是静态的。

### 例

```ts
@Component({ /* ... */ })
export class MyCustomComponent {
  private dir: Direction;

  constructor(directionality: Directionality) {
    this.dir = directionality.value;

    directionality.change.subscribe(() => {
       this.dir = directionality.value;
    });
  }
}
```