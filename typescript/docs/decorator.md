# 装饰器（Decorator）

> 注意：本文档涉及实验性的阶段2装饰器实现。自Typescript 5.0起，支持阶段3装饰器
> See: [Decorators in Typescript5.0](https://devblogs.microsoft.com/typescript/announcing-typescript-5-0/#decorators)

## 介绍

随着TypeScript和ES6中引入了类，现在存在一些需要额外功能来支持注释或修改类和类成员的场景。装饰器提供了一种在类声明和成员上添加注释和元编程语法的方式。

> 进一步阅读（阶段2）: [A Complete Guide to TypeScript Decorators](https://saul-mirone.github.io/a-complete-guide-to-typescript-decorator/)

要启用对装饰器的实验性支持，您必须在命令行或您的 tsconfig.json 中启用 experimentalDecorators 编译器选项:

**Command Line**:

```shell
tsc --target ES5 --experimentalDecorators
```

```json
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true
  }
}
```

## Decorators 装饰器

A _Decorator_ is a special kind of declaration that can be attached to a [class declaration] , [accessor] , [property] , or [parameter] .
Decorators use the form `@expression`, where `expression` must evaluate to a function that will be called at runtime with information about the decorated declaration.
装饰器是一种特殊的声明，可以附加到类声明、方法、访问器、属性或参数上。装饰器使用的形式是 `@expression` ，其中 `expression` 必须评估为一个在运行时将被调用的函数，该函数会提供有关被装饰声明的信息。
For example, given the decorator `@sealed` we might write the `sealed` function as follows:
例如，给定装饰器 `@sealed` ，我们可以将 `sealed` 函数写成如下形式：

```ts
function sealed(target) {
  // do something with 'target' ...
}
```

## Decorator Factories 装饰器工厂

If we want to customize how a decorator is applied to a declaration, we can write a decorator factory.
A _Decorator Factory_ is simply a function that returns the expression that will be called by the decorator at runtime.
如果我们想要自定义装饰器应用于声明的方式，我们可以编写一个装饰器工厂。装饰器工厂只是一个函数，它返回在运行时由装饰器调用的表达式。

We can write a decorator factory in the following fashion:
我们可以按照以下方式编写一个装饰器工厂

```ts
function color(value: string) {
  // this is the decorator factory, it sets up
  // the returned decorator function
  return function (target) {
    // this is the decorator
    // do something with 'target' and 'value'...
  };
}
```

## Decorator Composition 装饰器组合

Multiple decorators can be applied to a declaration, for example on a single line:
可以对一个声明应用多个装饰器，例如在一行上：

```ts
// @experimentalDecorators
// @noErrors
function f() {}
function g() {}
// ---cut---
@f @g x
```

On multiple lines:

```ts
// @experimentalDecorators
// @noErrors
function f() {}
function g() {}
// ---cut---
@f
@g
x
```

When multiple decorators apply to a single declaration, their evaluation is similar to [function composition in mathematics](https://wikipedia.org/wiki/Function_composition). In this model, when composing functions _f_ and _g_, the resulting composite (_f_ ∘ _g_)(_x_) is equivalent to _f_(_g_(_x_)).
当多个装饰器应用于单个声明时，它们的评估类似于数学中的函数组合。在这个模型中，当组合函数 f 和 g 时，得到的复合函数 (f ∘ g)(x) 等同于 f(g(x))。
As such, the following steps are performed when evaluating multiple decorators on a single declaration in TypeScript:
在TypeScript中，当对单个声明进行多个装饰器的评估时，执行以下步骤：

1. The expressions for each decorator are evaluated top-to-bottom.每个装饰器的表达式从上到下进行评估。
2. The results are then called as functions from bottom-to-top.结果从下到上被称为函数。

If we were to use [decorator factories] , we can observe this evaluation order with the following example:
如果我们使用装饰器工厂，我们可以通过以下示例观察到这个评估顺序

<!-- prettier-ignore -->

```ts
// @experimentalDecorators
function first() {
  console.log("first(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("first(): called");
  };
}

function second() {
  console.log("second(): factory evaluated");
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("second(): called");
  };
}

class ExampleClass {
  @first()
  @second()
  method() {}
}
```

Which would print this output to the console:
这将在控制台上打印出以下输出：

```shell
first(): factory evaluated
second(): factory evaluated
second(): called
first(): called
```

## Decorator Evaluation 装饰器评估

There is a well defined order to how decorators applied to various declarations inside of a class are applied:
在类内部应用装饰器的各种声明有明确定义的顺序

1. _Parameter Decorators_, followed by _Method_, _Accessor_, or _Property Decorators_ are applied for each instance member.参数装饰器，紧随其后的是方法、访问器或属性装饰器，适用于每个实例成员。
2. _Parameter Decorators_, followed by _Method_, _Accessor_, or _Property Decorators_ are applied for each static member.参数装饰器，紧随其后的是方法、访问器或属性装饰器，适用于每个静态成员。
3. _Parameter Decorators_ are applied for the constructor.参数装饰器应用于构造函数。
4. _Class Decorators_ are applied for the class.类装饰器适用于类。

## Class Decorators 类装饰器

A _Class Decorator_ is declared just before a class declaration.
The class decorator is applied to the constructor of the class and can be used to observe, modify, or replace a class definition.
A class decorator cannot be used in a declaration file, or in any other ambient context (such as on a `declare` class).
类装饰器在类声明之前声明。类装饰器应用于类的构造函数，并可用于观察、修改或替换类定义。类装饰器不能在声明文件中使用，也不能在任何其他环境上下文中使用（例如在类上）。

The expression for the class decorator will be called as a function at runtime, with the constructor of the decorated class as its only argument.
类装饰器的表达式将在运行时作为函数调用，其唯一参数为被装饰类的构造函数。

If the class decorator returns a value, it will replace the class declaration with the provided constructor function.
如果类装饰器返回一个值，它将用提供的构造函数替换类声明。

> NOTE&nbsp; Should you choose to return a new constructor function, you must take care to maintain the original prototype.
> The logic that applies decorators at runtime will **not** do this for you.
> 注意：如果你选择返回一个新的构造函数，你必须小心地保持原始的原型。在运行时应用装饰器的逻辑不会为你做这个。
> The following is an example of a class decorator (`@sealed`) applied to a `BugReport` class:
> 以下是应用于一个 `BugReport` 类的类装饰器（ `@sealed` ）的示例

```ts
// @experimentalDecorators
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}
// ---cut---
@sealed
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}
```

We can define the `@sealed` decorator using the following function declaration:
我们可以使用以下函数声明来定义 `@sealed` 装饰器：

```ts
function sealed(constructor: Function) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}
```

When `@sealed` is executed, it will seal both the constructor and its prototype, and will therefore prevent any further functionality from being added to or removed from this class during runtime by accessing `BugReport.prototype` or by defining properties on `BugReport` itself (note that ES2015 classes are really just syntactic sugar to prototype-based constructor functions). This decorator does **not** prevent classes from sub-classing `BugReport`.
当执行 `@sealed` 时，它将封装构造函数和其原型，因此在运行时通过访问 `BugReport.prototype` 或在 `BugReport` 上定义属性将阻止对该类的进一步功能的添加或删除（请注意，ES2015类实际上只是基于原型的构造函数的语法糖）。这个装饰器不会阻止类从 `BugReport` 进行子类化。
Next we have an example of how to override the constructor to set new defaults.
接下来我们有一个示例，展示如何重写构造函数以设置新的默认值。

<!-- prettier-ignore -->

```ts
// @errors: 2339
// @experimentalDecorators
function reportableClassDecorator<T extends { new (...args: any[]): {} }>(constructor: T) {
  return class extends constructor {
    reportingURL = "http://www...";
  };
}

@reportableClassDecorator
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }
}

const bug = new BugReport("Needs dark mode");
console.log(bug.title); // Prints "Needs dark mode"
console.log(bug.type); // Prints "report"

// Note that the decorator _does not_ change the TypeScript type
// and so the new property `reportingURL` is not known
// to the type system:
bug.reportingURL;
```

## Method Decorators 方法装饰器

A _Method Decorator_ is declared just before a method declaration.
The decorator is applied to the _Property Descriptor_ for the method, and can be used to observe, modify, or replace a method definition.
A method decorator cannot be used in a declaration file, on an overload, or in any other ambient context (such as in a `declare` class).
方法装饰器是在方法声明之前声明的。装饰器应用于方法的属性描述符，并可用于观察、修改或替换方法定义。方法装饰器不能在声明文件、重载或任何其他环境上下文中使用（例如在类中）。
The expression for the method decorator will be called as a function at runtime, with the following three arguments:
方法装饰器的表达式将在运行时作为函数调用，带有以下三个参数：

1. Either the constructor function of the class for a static member, or the prototype of the class for an instance member.无论是静态成员的类的构造函数，还是实例成员的类的原型。
2. The name of the member.成员的姓名。
3. The _Property Descriptor_ for the member.成员的属性描述符。

> NOTE&emsp; The _Property Descriptor_ will be `undefined` if your script target is less than `ES5`.注意：如果您的脚本目标低于 `ES5` ，则属性描述符将为 `undefined` 。

If the method decorator returns a value, it will be used as the _Property Descriptor_ for the method.如果方法装饰器返回一个值，它将被用作该方法的属性描述符。

> NOTE&emsp; The return value is ignored if your script target is less than `ES5`.注意：如果您的脚本目标小于 `ES5` ，则忽略返回值。

The following is an example of a method decorator (`@enumerable`) applied to a method on the `Greeter` class:
以下是应用于 `Greeter` 类上的方法装饰器（ `@enumerable` ）的示例

<!-- prettier-ignore -->

```ts
// @experimentalDecorators
function enumerable(value: boolean) {
  return function (target: any,propertyKey: string,descriptor: PropertyDescriptor) {
    descriptor.enumerable = value;
  };
}
// ---cut---
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message;
  }

  @enumerable(false)
  greet() {
    return "Hello, " + this.greeting;
  }
}
```

We can define the `@enumerable` decorator using the following function declaration:
我们可以使用以下函数声明来定义 `@enumerable` 装饰器：

<!-- prettier-ignore -->

```ts
function enumerable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.enumerable = value;
  };
}
```

The `@enumerable(false)` decorator here is a [decorator factory] 
When the `@enumerable(false)` decorator is called, it modifies the `enumerable` property of the property descriptor.
这里的 `@enumerable(false)` 装饰器是一个装饰器工厂。当调用 `@enumerable(false)` 装饰器时，它会修改属性描述符的 `enumerable` 属性。

## Accessor Decorators 访问器装饰器

An _Accessor Decorator_ is declared just before an accessor declaration.
The accessor decorator is applied to the _Property Descriptor_ for the accessor and can be used to observe, modify, or replace an accessor's definitions.
An accessor decorator cannot be used in a declaration file, or in any other ambient context (such as in a `declare` class).
一个访问器装饰器在访问器声明之前声明。访问器装饰器应用于访问器的属性描述符，并可用于观察、修改或替换访问器的定义。访问器装饰器不能在声明文件中使用，也不能在任何其他环境上下文中使用（例如在类中）。

> NOTE&emsp; TypeScript disallows decorating both the `get` and `set` accessor for a single member.
> Instead, all decorators for the member must be applied to the first accessor specified in document order.
> This is because decorators apply to a _Property Descriptor_, which combines both the `get` and `set` accessor, not each declaration separately.
> 注意：TypeScript不允许为同一个成员同时装饰 get 和 set 访问器。相反，所有成员的装饰器必须应用于文档顺序中指定的第一个访问器。这是因为装饰器应用于属性描述符，它结合了 get 和 set 访问器，而不是每个声明分别应用。

The expression for the accessor decorator will be called as a function at runtime, with the following three arguments:
访问器装饰器的表达式将在运行时作为函数调用，带有以下三个参数：
1. Either the constructor function of the class for a static member, or the prototype of the class for an instance member.无论是静态成员的类的构造函数，还是实例成员的类的原型。
2. The name of the member.成员的姓名。
3. The _Property Descriptor_ for the member.成员的属性描述符。

> NOTE&emsp; The _Property Descriptor_ will be `undefined` if your script target is less than `ES5`.注意：如果您的脚本目标低于 ES5 ，则属性描述符将为 undefined 。

If the accessor decorator returns a value, it will be used as the _Property Descriptor_ for the member.如果访问器装饰器返回一个值，它将被用作成员的属性描述符。

> NOTE&emsp; The return value is ignored if your script target is less than `ES5`.注意：如果您的脚本目标小于 ES5 ，则忽略返回值。

The following is an example of an accessor decorator (`@configurable`) applied to a member of the `Point` class:
以下是应用于 Point 类成员的访问器装饰器（ @configurable ）的示例
```ts
// @experimentalDecorators
function configurable(value: boolean) {
  return function (
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    descriptor.configurable = value;
  };
}
// ---cut---
class Point {
  private _x: number;
  private _y: number;
  constructor(x: number, y: number) {
    this._x = x;
    this._y = y;
  }

  @configurable(false)
  get x() {
    return this._x;
  }

  @configurable(false)
  get y() {
    return this._y;
  }
}
```

We can define the `@configurable` decorator using the following function declaration:
我们可以使用以下函数声明来定义 @configurable 装饰器：
<!-- prettier-ignore -->

```ts
function configurable(value: boolean) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    descriptor.configurable = value;
  };
}
```

## Property Decorators 属性装饰器

A _Property Decorator_ is declared just before a property declaration.
A property decorator cannot be used in a declaration file, or in any other ambient context (such as in a `declare` class).属性装饰器在属性声明之前声明。属性装饰器不能在声明文件中使用，也不能在任何其他环境上下文中使用（例如在类中）。

The expression for the property decorator will be called as a function at runtime, with the following two arguments:属性装饰器的表达式将在运行时作为函数调用，带有以下两个参数：

1. Either the constructor function of the class for a static member, or the prototype of the class for an instance member.无论是静态成员的类的构造函数，还是实例成员的类的原型。
2. The name of the member.成员的姓名。

> NOTE&emsp; A _Property Descriptor_ is not provided as an argument to a property decorator due to how property decorators are initialized in TypeScript.
> This is because there is currently no mechanism to describe an instance property when defining members of a prototype, and no way to observe or modify the initializer for a property. 
> 由于TypeScript中属性装饰器的初始化方式，不会将属性描述符作为参数提供给属性装饰器。这是因为目前没有机制来描述原型成员的实例属性，也没有办法观察或修改属性的初始化器。
> The return value is ignored too. As such, a property decorator can only be used to observe that a property of a specific name has been declared for a class.
>返回值也被忽略。因此，属性装饰器只能用于观察某个类声明了特定名称的属性。

We can use this information to record metadata about the property, as in the following example:
我们可以使用这些信息来记录有关该属性的元数据，如下面的示例所示：
```ts
class Greeter {
  @format("Hello, %s")
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    let formatString = getFormat(this, "greeting");
    return formatString.replace("%s", this.greeting);
  }
}
```

We can then define the `@format` decorator and `getFormat` functions using the following function declarations:
我们可以使用以下函数声明来定义 @format 装饰器和 getFormat 函数
```ts
import "reflect-metadata";

const formatMetadataKey = Symbol("format");

function format(formatString: string) {
  return Reflect.metadata(formatMetadataKey, formatString);
}

function getFormat(target: any, propertyKey: string) {
  return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}
```

The `@format("Hello, %s")` decorator here is a [decorator factory] 
When `@format("Hello, %s")` is called, it adds a metadata entry for the property using the `Reflect.metadata` function from the `reflect-metadata` library.
When `getFormat` is called, it reads the metadata value for the format.
这里的 @format("Hello, %s") 装饰器是一个装饰器工厂。当调用 @format("Hello, %s") 时，它使用 reflect-metadata 库中的 Reflect.metadata 函数为属性添加元数据条目。当调用 getFormat 时，它读取格式的元数据值。

> NOTE&emsp; This example requires the `reflect-metadata` library.
> See [Metadata] for more information about the `reflect-metadata` library.
> 注意：此示例需要 reflect-metadata 库。有关 reflect-metadata 库的更多信息，请参阅元数据。

## Parameter Decorators 参数装饰器

A _Parameter Decorator_ is declared just before a parameter declaration.
The parameter decorator is applied to the function for a class constructor or method declaration.
A parameter decorator cannot be used in a declaration file, an overload, or in any other ambient context (such as in a `declare` class).
参数装饰器是在参数声明之前声明的。参数装饰器应用于类构造函数或方法声明的函数。参数装饰器不能在声明文件、重载或任何其他环境上下文中使用（例如在类中）。
The expression for the parameter decorator will be called as a function at runtime, with the following three arguments:
参数装饰器的表达式将在运行时作为函数调用，带有以下三个参数：
1. Either the constructor function of the class for a static member, or the prototype of the class for an instance member.无论是静态成员的类的构造函数，还是实例成员的类的原型。
2. The name of the member.成员的姓名。
3. The ordinal index of the parameter in the function's parameter list.函数参数列表中参数的序数索引。

> NOTE&emsp; A parameter decorator can only be used to observe that a parameter has been declared on a method.注意：参数装饰器只能用于观察方法中的参数是否已声明。

The return value of the parameter decorator is ignored.
参数装饰器的返回值被忽略。
The following is an example of a parameter decorator (`@required`) applied to parameter of a member of the `BugReport` class:
以下是应用于 BugReport 类成员参数的参数装饰器（ @required ）的示例：
<!-- prettier-ignore -->

```ts
// @experimentalDecorators
function validate(target: any, propertyName: string, descriptor: TypedPropertyDescriptor<any>) {}
function required(target: Object, propertyKey: string | symbol, parameterIndex: number) {}
// ---cut---
class BugReport {
  type = "report";
  title: string;

  constructor(t: string) {
    this.title = t;
  }

  @validate
  print(@required verbose: boolean) {
    if (verbose) {
      return `type: ${this.type}\ntitle: ${this.title}`;
    } else {
     return this.title; 
    }
  }
}
```

We can then define the `@required` and `@validate` decorators using the following function declarations:
我们可以使用以下函数声明来定义 @required 和 @validate 修饰符：
<!-- prettier-ignore -->

```ts
// @experimentalDecorators
// @emitDecoratorMetadata
import "reflect-metadata";
const requiredMetadataKey = Symbol("required");

function required(target: Object, propertyKey: string | symbol, parameterIndex: number) {
  let existingRequiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyKey) || [];
  existingRequiredParameters.push(parameterIndex);
  Reflect.defineMetadata( requiredMetadataKey, existingRequiredParameters, target, propertyKey);
}

function validate(target: any, propertyName: string, descriptor: TypedPropertyDescriptor<Function>) {
  let method = descriptor.value!;

  descriptor.value = function () {
    let requiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyName);
    if (requiredParameters) {
      for (let parameterIndex of requiredParameters) {
        if (parameterIndex >= arguments.length || arguments[parameterIndex] === undefined) {
          throw new Error("Missing required argument.");
        }
      }
    }
    return method.apply(this, arguments);
  };
}
```

The `@required` decorator adds a metadata entry that marks the parameter as required.
The `@validate` decorator then wraps the existing `print` method in a function that validates the arguments before invoking the original method.
@required 装饰器添加了一个元数据条目，将参数标记为必需。然后， @validate 装饰器将现有的 print 方法包装在一个函数中，在调用原始方法之前验证参数。

> NOTE&emsp; This example requires the `reflect-metadata` library.
> See [Metadata] for more information about the `reflect-metadata` library.
> 注意：此示例需要 reflect-metadata 库。有关 reflect-metadata 库的更多信息，请参阅元数据。

## Metadata 元数据

Some examples use the `reflect-metadata` library which adds a polyfill for an [experimental metadata API](https://github.com/rbuckton/ReflectDecorators).
This library is not yet part of the ECMAScript (JavaScript) standard.
However, once decorators are officially adopted as part of the ECMAScript standard these extensions will be proposed for adoption.
一些示例使用 reflect-metadata 库，该库为实验性元数据API添加了一个polyfill。该库目前尚未成为ECMAScript（JavaScript）标准的一部分。然而，一旦装饰器正式成为ECMAScript标准的一部分，这些扩展将被提议采纳。
You can install this library via npm:
您可以通过npm安装此库
```shell
npm i reflect-metadata --save
```

TypeScript includes experimental support for emitting certain types of metadata for declarations that have decorators.
To enable this experimental support, you must set the [`emitDecoratorMetadata`] compiler option either on the command line or in your `tsconfig.json`:
TypeScript对于具有装饰器的声明，包含了对某些类型的元数据的实验性支持。要启用这个实验性支持，你必须在命令行或者你的 tsconfig.json 中设置 emitDecoratorMetadata 编译器选项。

**Command Line**:

```shell
tsc --target ES5 --experimentalDecorators --emitDecoratorMetadata
```

**tsconfig.json**:

```json
{
  "compilerOptions": {
    "target": "ES5",
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

When enabled, as long as the `reflect-metadata` library has been imported, additional design-time type information will be exposed at runtime.
启用后，只要已导入 reflect-metadata 库，额外的设计时类型信息将在运行时暴露出来。
We can see this in action in the following example:
我们可以在以下示例中看到这一点的实际应用：
<!-- prettier-ignore -->

```ts
// @emitDecoratorMetadata
// @experimentalDecorators
// @strictPropertyInitialization: false
import "reflect-metadata";

class Point {
  constructor(public x: number, public y: number) {}
}

class Line {
  private _start: Point;
  private _end: Point;

  @validate
  set start(value: Point) {
    this._start = value;
  }

  get start() {
    return this._start;
  }

  @validate
  set end(value: Point) {
    this._end = value;
  }

  get end() {
    return this._end;
  }
}

function validate<T>(target: any, propertyKey: string, descriptor: TypedPropertyDescriptor<T>) {
  let set = descriptor.set!;
  
  descriptor.set = function (value: T) {
    let type = Reflect.getMetadata("design:type", target, propertyKey);

    if (!(value instanceof type)) {
      throw new TypeError(`Invalid type, got ${typeof value} not ${type.name}.`);
    }

    set.call(this, value);
  };
}

const line = new Line()
line.start = new Point(0, 0)

// @ts-ignore
// line.end = {}

// Fails at runtime with:
// > Invalid type, got object not Point
```

The TypeScript compiler will inject design-time type information using the `@Reflect.metadata` decorator.
You could consider it the equivalent of the following TypeScript:
TypeScript编译器将使用装饰器注入设计时类型信息。您可以将其视为以下TypeScript的等效形式：

```ts
class Line {
  private _start: Point;
  private _end: Point;

  @validate
  @Reflect.metadata("design:type", Point)
  set start(value: Point) {
    this._start = value;
  }
  get start() {
    return this._start;
  }

  @validate
  @Reflect.metadata("design:type", Point)
  set end(value: Point) {
    this._end = value;
  }
  get end() {
    return this._end;
  }
}
```

> NOTE&emsp; Decorator metadata is an experimental feature and may introduce breaking changes in future releases.
注意：装饰器元数据是一个实验性功能，可能在未来的版本中引入破坏性变化。
