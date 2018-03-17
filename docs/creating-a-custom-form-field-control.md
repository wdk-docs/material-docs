# 创建一个自定义表单字段控件

可以创建可以在`<mat-form-field>`内使用的自定义表单域控件。
如果您需要创建一个与表单字段共享许多常见行为的组件，但是会添加一些额外的逻辑，这会非常有用。

例如，在本指南中，我们将学习如何创建用于输入美国电话号码的自定义输入，并将其与`<mat-form-field>`结合使用。
以下是我们将在本指南结束时构建的内容：

<!-- example(form-field-custom-control) -->

为了学习如何构建自定义表单字段控件，让我们从我们希望在表单字段中工作的简单输入组件开始。
例如，一个电话号码输入，将数字的各个部分分割成自己的输入。
(注意：这不是一个强大的组件，只是我们学习的起点。)

```ts
class MyTel {
  constructor(public area: string, public exchange: string, public subscriber: string) {}
}

@Component({
  selector: 'my-tel-input',
  template: `
    <div [formGroup]="parts">
      <input class="area" formControlName="area" size="3">
      <span>&ndash;</span>
      <input class="exchange" formControlName="exchange" size="3">
      <span>&ndash;</span>
      <input class="subscriber" formControlName="subscriber" size="4">
    </div>
  `,
  styles: [`
    div {
      display: flex;
    }
    input {
      border: none;
      background: none;
      padding: 0;
      outline: none;
      font: inherit;
      text-align: center;
    }
  `],
})
class MyTelInput {
  parts: FormGroup;

  @Input()
  get value(): MyTel | null {
    let n = this.parts.value;
    if (n.area.length == 3 && n.exchange.length == 3 && n.subscriber.length == 4) {
      return new MyTel(n.area, n.exchange, n.subscriber);
    }
    return null;
  }
  set value(tel: MyTel | null) {
    tel = tel || new MyTel('', '', '');
    this.parts.setValue({area: tel.area, exchange: tel.exchange, subscriber: tel.subscriber});
  }

  constructor(fb: FormBuilder) {
    this.parts =  fb.group({
      'area': '',
      'exchange': '',
      'subscriber': '',
    });
  }
}
```

## 提供我们的组件作为MatFormFieldControl

第一步是提供我们的新组件作为`MatFormFieldControl`接口的实现，`<mat-form-field>`知道如何使用。
为此，我们将让我们的类实现`MatFormFieldControl`。
由于这是一个通用接口，因此我们需要包含一个类型参数，用于指示我们的控件将使用的数据类型，在本例中为`MyTel`。
然后，我们在组件中添加一个提供程序，以便表单字段能够将其注入为`MatFormFieldControl`。

```ts
@Component({
  ...
  providers: [{provide: MatFormFieldControl, useExisting: MyTelInput}],
})
class MyTelInput implements MatFormFieldControl<MyTel> {
  ...
}
```

这设置了我们的组件，因此它可以使用`<mat-form-field>`，但现在我们需要实现我们刚刚实现的接口所声明的各种方法和属性。
要了解更多关于MatFormFieldControl接口的信息，请参阅表单字段[API文档](https://material.angular.io/components/form-field/api).

## 实现MatFormFieldControl的方法和属性

### `value`

该属性允许某人设置或获取我们控制的价值。
当我们实现`MatFormFieldControl`时，它的类型应该与我们用于类型参数的类型相同。
由于我们的组件已经有一个值属性，所以我们不需要为这个做任何事情。

### `stateChanges`

因为`<mat-form-field>`使用`OnPush`变化检测策略，
我们需要让它知道什么时候在表单字段控件中发生什么事情，这可能需要表单域来运行更改检测。
我们通过`stateChanges`属性来做到这一点。
到目前为止，表单字段唯一需要了解的是值的变化。
当发生这种情况时，我们需要发出stateChanges流，并且当我们继续清除这些属性时，我们可能会发现更多需要发出的地方。
当我们的组件被销毁时，我们也应该确保完成`stateChanges`。

```ts
stateChanges = new Subject<void>();

set value(tel: MyTel | null) {
  ...
  this.stateChanges.next();
}

ngOnDestroy() {
  this.stateChanges.complete();
}
```

### `id`

该属性应该返回组件模板中的一个元素的ID，我们希望`<mat-form-field>`将它的所有标签和提示关联起来。
在这种情况下，我们将使用主机元素并为其生成一个唯一的ID。

```ts
static nextId = 0;

@HostBinding() id = `my-tel-input-${MyTelInput.nextId++}`;
```

### `placeholder`

这个属性允许我们告诉`<mat-form-field>`用作占位符。
在这个例子中，我们将做`matInput`和`<mat-select>`，并允许用户通过`@Input（）`指定它。
由于占位符的值可能会随着时间而改变，因此我们需要确保在占位符更改时通过在`stateChanges`流上发出来在父窗体字段中触发更改检测。

```ts
@Input()
get placeholder() {
  return this._placeholder;
}
set placeholder(plh) {
  this._placeholder = plh;
  this.stateChanges.next();
}
private _placeholder: string;
```

### `ngControl`

该属性允许表单字段控件指定绑定到此组件的`@ angular / forms`控件。
由于我们没有将组件设置为`ControlValueAccessor`，因此我们只会在组件中将其设置为`null`。

```ts
ngControl: NgControl = null;
```

很可能你会想要实现`ControlValueAccessor`，这样你的组件就可以使用`formControl`和`ngModel`。
如果你实现了`ControlValueAccessor`，你将需要获得一个对你的控件关联的`NgControl`的引用，并将其公开。

简单的方法是将它作为公共属性添加到您的构造函数中，并让依赖注入来处理它：

```ts
constructor(
  ...,
  @Optional() @Self() public ngControl: NgControl,
  ...,
) { }
```

请注意，如果你的组件实现了`ControlValueAccessor`，它可能已经被设置为提供`NG_VALUE_ACCESSOR`（在组件的装饰器的`providers`部分，或者可能在模块声明中）。
如果是这样，你可能会得到一个无法实例化循环依赖性错误。

要解决这个问题，请移除`NG_VALUE_ACCESSOR`提供者，直接设置值访问器：

```ts
constructor(
  ...,
  @Optional() @Self() public ngControl: NgControl,
  ...,
) {
  // Setting the value accessor directly (instead of using
  // the providers) to avoid running into a circular import.
  if (this.ngControl != null) { this.ngControl.valueAccessor = this; }
}
```

有关`ControlValueAccessor`的更多信息，请参阅[API文档](https://angular.io/api/forms/ControlValueAccessor).

### `focused`

此属性指示是否应将表单字段控件视为处于聚焦状态。
当它处于聚焦状态时，表单域将以纯色下划线显示。
对于我们的组件而言，如果任何部分输入都集中在一起，我们想要考虑它的重点。
我们可以使用`@ angular / cdk`中的FocusMonitor来轻松检查。
我们还需要记住发出`stateChanges`流，以便发生变化检测。

```ts
focused = false;

constructor(fb: FormBuilder, private fm: FocusMonitor, private elRef: ElementRef) {
  ...
  fm.monitor(elRef.nativeElement, true).subscribe(origin => {
    this.focused = !!origin;
    this.stateChanges.next();
  });
}

ngOnDestroy() {
  ...
  this.fm.stopMonitoring(this.elRef.nativeElement);
}
```

### `empty`

该属性指示表单字段控件是否为空。
对于我们的控制，如果所有零件都是空的，我们会认为它是空的。

```ts
get empty() {
  let n = this.parts.value;
  return !n.area && !n.exchange && !n.subscriber;
}
```

### `shouldLabelFloat`

该属性用于指示标签是否应处于浮动位置。
我们将使用与matInput相同的逻辑，并在输入为焦点或非空时悬浮占位符。
由于占位符在不处于浮动状态时将与我们的控件重叠，所以当它不是浮动时，我们应该隐藏`-`字符。

```ts
@HostBinding('class.floating')
get shouldLabelFloat() {
  return this.focused || !this.empty;
}
```

```css
span {
  opacity: 0;
  transition: opacity 200ms;
}
:host.floating span {
  opacity: 1;
}
```

### `required`

该属性用于指示是否需要输入。
`<mat-form-field>`使用此信息向占位符添加所需的指示符。
同样，如果所需的状态发生变化，我们将确保运行变更检测。

```ts
@Input()
get required() {
  return this._required;
}
set required(req) {
  this._required = coerceBooleanProperty(req);
  this.stateChanges.next();
}
private _required = false;
```

### `disabled`

同样，如果所需的状态发生变化，我们将确保运行变更检测。
除了向表单字段报告正确的状态外，我们还需要在构成组件的各个输入上设置禁用状态。

```ts
@Input()
get disabled() {
  return this._disabled;
}
set disabled(dis) {
  this._disabled = coerceBooleanProperty(dis);
  this.stateChanges.next();
}
private _disabled = false;
```

```html
<input class="area" formControlName="area" size="3" [disabled]="disabled">
<span>&ndash;</span>
<input class="exchange" formControlName="exchange" size="3" [disabled]="disabled">
<span>&ndash;</span>
<input class="subscriber" formControlName="subscriber" size="4" [disabled]="disabled">
```

### `errorState`

该属性指示相关的`NgControl`是否处于错误状态。
由于在这个例子中我们没有使用`NgControl`，所以除了将其设置为`false`之外，我们不需要做任何事情。

```ts
errorState = false;
```

### `controlType`

该属性允许我们为表单字段中的控件类型指定一个唯一的字符串。
`<mat-form-field>`会根据这个类型添加一个额外的类，这个类可以很容易地将特殊的样式应用到包含特定类型控件的`<mat-form-field>`中。
在这个例子中，我们将使用`my-tel-input`作为我们的控制类型，这将导致表单字段添加`mat-form-field-my-tel-input`类。

```ts
controlType = 'my-tel-input';
```

### `setDescribedByIds(ids: string[])`

`<mat-form-field>`使用该方法来指定应该用于组件的`aria-describedby`属性的ID。
该方法有一个参数，ID列表，我们只需要将给定的ID应用到我们的主机元素。

```ts
@HostBinding('attr.aria-describedby') describedBy = '';

setDescribedByIds(ids: string[]) {
  this.describedBy = ids.join(' ');
}
```

### `onContainerClick(event: MouseEvent)`

该方法将在单击表单域时调用。
它允许你的组件钩入并处理它的点击，但它想要的。
该方法有一个参数，点击的`MouseEvent`。
在我们的例子中，如果用户不想单击一个`<input>`，那么我们只关注第一个`<input>`。

```ts
onContainerClick(event: MouseEvent) {
  if ((event.target as Element).tagName.toLowerCase() != 'input') {
    this.elRef.nativeElement.querySelector('input').focus();
  }
}
```

## 试试吧

现在我们已经完全实现了界面，我们已经准备好尝试我们的组件了！
我们所需要做的就是将它放在一个`<mat-form-field>`中

```html
<mat-form-field>
  <my-tel-input></my-tel-input>
</mat-form-field>
```

我们还获得了所有带有`<mat-form-field>`的特性，例如浮动占位符，前缀，后缀，提示和错误 (如果我们给了表单字段一个`NgControl`并且正确地报告错误状态).

```html
<mat-form-field>
  <my-tel-input placeholder="Phone number" required></my-tel-input>
  <mat-icon matPrefix>电话</mat-icon>
  <mat-hint>包括区号</mat-hint>
</mat-form-field>
```