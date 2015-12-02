# TypeScript 新增特性一览

由 [vilicvane](https://github.com/vilic) 译自 [TypeScript Wiki](https://github.com/Microsoft/TypeScript/wiki/What's-new-in-TypeScript/6534258ff7c52dc4d6578a3928cdabbc89370a4c).

## TypeScript 1.8 (尚未正式发布)

### `tsc` 错误信息更美观

我们理解大量单色的输出并不直观. 颜色可以帮助识别信息的始末, 这些视觉上的线索在处理复杂的错误信息时非常重要.

通过传递 `--pretty` 命令行选项, TypeScript 会给出更丰富的输出, 包含错误发生的上下文.

![展示在 ConEmu 中美化之后的错误信息](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/images/new-in-typescript/pretty01.png)

### `--project` (`-p`) 选项现在接受任意文件路径

`--project` 命令行选项过去只接受包含了 `tsconfig.json` 文件的文件夹. 考虑到不同的构建场景, 应该允许 `--project` 指向任何兼容的 JSON 文件. 比如说, 一个用户可能会希望为 Node 5 编译 CommonJS 的 ES 2015, 为浏览器编译 AMD 的 ES5. 现在少了这项限制, 用户可以更容易地直接使用 `tsc` 管理不同的构建目标, 无需再通过一些奇怪的方式, 比如将多个 `tsconfig.json` 文件放在不同的目录中.

### 支持输出到 IPC 驱动的文件

TypeScript 1.8 允许用户将 `--outFile` 参数和一些特殊的文件系统对象一起使用, 比如命名的管道 (pipe), 设备 (devices) 等.

举个例子, 在很多与 Unix 相似的系统上, 标准输出流可以通过文件 `/dev/stdout` 访问.

```sh
tsc foo.ts --outFile /dev/stdout
```

这一特性也允许输出给其他命令.

比如说, 我们可以输出生成的 JavaScript 给一个像 [pretty-js](https://www.npmjs.com/package/pretty-js) 这样的格式美化工具:

```sh
tsc foo.ts --outFile /dev/stdout | pretty-js
```

### 合并 `AMD` 或 `System` 模块到一个输出文件的选项

在使用 `--module amd` 或者 `--module system` 时指定 `--outFile` 将会合并编译的所有模块为一个包含了多个模块闭包的输出文件.

### 新的编译器选项: `--allowSyntheticDefaultExports`

指定 `--allowSyntheticDefaultExports` 则表示模块加载器会对于导入的 `.ts` 或 `.d.ts` 文件进行某种约定的默认引入. 我们会认为默认成员是导入模块的 `export=` 成员或者整个模块 (不应该出现这种情况). System 模块会默认开启该选项.

## TypeScript 1.7

### 支持 `async`/`await` 编译到 ES6 (Node v4+)

TypeScript 目前在已经原生支持 ES6 generator 的引擎 (比如 Node v4 及以上版本) 上支持异步函数. 异步函数前置 `async` 关键字; `await` 会暂停执行, 直到一个异步函数执行后返回的 promise 被 fulfill 后获得它的值.

#### 例子

在下面的例子中, 输入的内容将会延时 200 毫秒逐个打印:

```ts
"use strict";

// printDelayed 返回值是一个 'Promise<void>'
async function printDelayed(elements: string[]) {
    for (const element of elements) {
        await delay(200);
        console.log(element);
    }
}

async function delay(milliseconds: number) {
    return new Promise<void>(resolve => {
        setTimeout(resolve, milliseconds);
    });
}

printDelayed(["Hello", "beautiful", "asynchronous", "world"]).then(() => {
    console.log();
    console.log("打印每一个内容!");
});
```

查看 [Async Functions](http://blogs.msdn.com/b/typescript/archive/2015/11/03/what-about-async-await.aspx) 一文了解更多.

### 支持同时使用 `--target ES6` 和 `--module`

TypeScript 1.7 将 `ES6` 添加到了 `--module` 选项支持的选项的列表, 当编译到 `ES6` 时允许指定模块类型. 这让使用具体运行时中你需要的特性更加灵活.

#### 例子

```json
{
    "compilerOptions": { 
        "module": "amd",
        "target": "es6"
    }
}
```

### `this` 类型

在方法中返回当前对象 (也就是 `this`) 是一种创建链式 API 的常见方式. 比如, 考虑下面的 `BasicCalculator` 模块:

```ts
export default class BasicCalculator {
    public constructor(protected value: number = 0) { }

    public currentValue(): number {
        return this.value;
    }

    public add(operand: number) {
        this.value += operand;
        return this;
    }

    public subtract(operand: number) {
        this.value -= operand;
        return this;
    }

    public multiply(operand: number) {
        this.value *= operand;
        return this;
    }

    public divide(operand: number) {
        this.value /= operand;
        return this;
    }
}
```

使用者可以这样表述 `2 * 5 + 1`:

```ts
import calc from "./BasicCalculator";

let v = new calc(2)
    .multiply(5)
    .add(1)
    .currentValue();
```

这使得这么一种优雅的编码方式成为可能; 然而, 对于想要去继承 `BasicCalculator` 的类来说有一个问题. 想象使用者可能需要编写一个 `ScientificCalculator`:

```ts
import BasicCalculator from "./BasicCalculator";

export default class ScientificCalculator extends BasicCalculator {
    public constructor(value = 0) {
        super(value);
    }

    public square() {
        this.value = this.value ** 2;
        return this;
    }

    public sin() {
        this.value = Math.sin(this.value);
        return this;
    }
}
```

因为 `BasicCalculator` 的方法返回了 `this`, TypeScript 过去推断的类型是 `BasicCalculator`, 如果在 `ScientificCalculator` 的实例上调用属于 `BasicCalculator` 的方法, 类型系统不能很好地处理.

举例来说:

```ts
import calc from "./ScientificCalculator";

let v = new calc(0.5)
    .square()
    .divide(2)
    .sin()    // Error: 'BasicCalculator' 没有 'sin' 方法.
    .currentValue();
``` 

这已经不再是问题 - TypeScript 现在在类的实例方法中, 会将 `this` 推断为一个特殊的叫做 `this` 的类型. `this` 类型也就写作 `this`, 可以大致理解为 "方法调用时点左边的类型".

`this` 类型在描述一些使用了 mixin 风格继承的库 (比如 Ember.js) 的交叉类型:

```ts
interface MyType {
    extend<T>(other: T): this & T;
}
```

### ES7 幂运算符

TypeScript 1.7 支持将在 ES7/ES2016 中增加的[幂运算符](https://github.com/rwaldron/exponentiation-operator): `**` 和 `**=`. 这些运算符会被转换为 ES3/ES5 中的 `Math.pow`.

#### 举例

```ts
var x = 2 ** 3;
var y = 10;
y **= 2;
var z =  -(4 ** 3);
```

会生成下面的 JavaScript:

```ts
var x = Math.pow(2, 3);
var y = 10;
y = Math.pow(y, 2);
var z = -(Math.pow(4, 3));
```

### 改进对象字面量解构的检查

TypeScript 1.7 使对象和数组字面量解构初始值的检查更加直观和自然.

当一个对象字面量通过与之对应的对象解构绑定推断类型时:

- 对象解构绑定中有默认值的属性对于对象字面量来说可选.
- 对象解构绑定中的属性如果在对象字面量中没有匹配的值, 则该属性必须有默认值, 并且会被添加到对象字面量的类型中.
- 对象字面量中的属性必须在对象解构绑定中存在.

当一个数组字面量通过与之对应的数组解构绑定推断类型时:

- 数组解构绑定中的元素如果在数组字面量中没有匹配的值, 则该元素必须有默认值, 并且会被添加到数组字面量的类型中.

#### 举例

```ts
// f1 的类型为 (arg?: { x?: number, y?: number }) => void
function f1({ x = 0, y = 0 } = {}) { }

// And can be called as:
f1();
f1({});
f1({ x: 1 });
f1({ y: 1 });
f1({ x: 1, y: 1 });

// f2 的类型为 (arg?: (x: number, y?: number) => void
function f2({ x, y = 0 } = { x: 0 }) { }

f2();
f2({});        // 错误, x 非可选
f2({ x: 1 });
f2({ y: 1 });  // 错误, x 非可选
f2({ x: 1, y: 1 });
```

### 装饰器 (decorators) 支持的编译目标版本增加 ES3

装饰器现在可以编译到 ES3. TypeScript 1.7 在 `__decorate` 函数中移除了 ES5 中增加的 `reduceRight`. 相关改动也内联了对 `Object.getOwnPropertyDescriptor` 和 `Object.defineProperty` 的调用, 并向后兼容, 使 ES5 的输出可以消除前面提到的 `Object` 方法的重复<sup>[1]</sup>.

## TypeScript 1.6

### JSX 支持

JSX 是一种可嵌入的类似 XML 的语法. 它将最终被转换为合法的 JavaScript, 但转换的语义和具体实现有关. JSX 随着 React 流行起来, 也出现在其他应用中. TypeScript 1.6 支持 JavaScript 文件中 JSX 的嵌入, 类型检查, 以及直接编译为 JavaScript 的选项.

#### 新的 `.tsx` 文件扩展名和 `as` 运算符

TypeScript 1.6 引入了新的 `.tsx` 文件扩展名. 这一扩展名一方面允许 TypeScript 文件中的 JSX 语法, 一方面将 `as` 运算符作为默认的类型转换方式 (避免 JSX 表达式和 TypeScript 前置类型转换运算符之间的歧义). 比如:

```ts
var x = <any> foo; 
// 与如下等价:
var x = foo as any;
```

#### 使用 React

使用 React 及 JSX 支持, 你需要使用 [React 类型声明](https://github.com/borisyankov/DefinitelyTyped/tree/master/react). 这些类型定义了 `JSX` 命名空间, 以便 TypeScript 能正确地检查 React 的 JSX 表达式. 比如:

```ts
/// <reference path="react.d.ts" />

interface Props {  
  name: string;
}

class MyComponent extends React.Component<Props, {}> {  
  render() {
    return <span>{this.props.foo}</span>
  }
}

<MyComponent name="bar" />; // 没问题 
<MyComponent name={0} />; // 错误, `name` 不是一个数字  
```

#### 使用其他 JSX 框架

JSX 元素的名称和属性是根据 `JSX` 命名空间来检验的. 请查看 [JSX](https://github.com/Microsoft/TypeScript/wiki/JSX) 页面了解如何为自己的框架定义 `JSX` 命名空间.

#### 编译输出

TypeScript 支持两种 `JSX` 模式: `preserve` (保留) 和 `react`.

- `preserve` 模式将会在输出中保留 JSX 表达式, 使之后的转换步骤可以处理. *并且输出的文件扩展名为 `.jsx`.*
- `react` 模式将会生成 `React.createElement`, 不再需要再通过 JSX 转换即可运行, 输出的文件扩展名为 `.js`.

查看 [JSX](https://github.com/Microsoft/TypeScript/wiki/JSX) 页面了解更多 JSX 在 TypeScript 中的使用.

### 交叉类型 (intersection types)

TypeScript 1.6 引入了交叉类型作为联合类型 (union types) 逻辑上的补充. 联合类型 `A | B` 表示一个类型为 `A` 或 `B` 的实体, 而交叉类型 `A & B` 表示一个类型同时为 `A` 或 `B` 的实体.

#### 例子

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U> {};
    for (let id in first) {
        result[id] = first[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            result[id] = second[id];
        }
    }
    return result;
}

var x = extend({ a: "hello" }, { b: 42 });
var s = x.a;
var n = x.b;
```

```ts
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
interface A { a: string }
interface B { b: string }
interface C { c: string }

var abc: A & B & C;
abc.a = "hello";
abc.b = "hello";
abc.c = "hello";
```

查看 [issue #1256](https://github.com/Microsoft/TypeScript/issues/1256) 了解更多.

### 本地类型声明

本地的类, 接口, 枚举和类型别名现在可以在函数声明中出现. 本地类型为块级作用域, 与 `let` 和 `const` 声明的变量类似. 比如说:

```ts
function f() {
    if (true) {
        interface T { x: number }
        let v: T;
        v.x = 5;
    }
    else {
        interface T { x: string }
        let v: T;
        v.x = "hello";
    }
}
```

推导出的函数返回值类型可能在函数内部声明的. 调用函数的地方无法引用到这样的本地类型, 但是它当然能从类型结构上匹配. 比如:

```ts
interface Point {
    x: number;
    y: number;
}

function getPointFactory(x: number, y: number) {
    class P {
        x = x;
        y = y;
    }
    return P;
}

var PointZero = getPointFactory(0, 0);
var PointOne = getPointFactory(1, 1);
var p1 = new PointZero();
var p2 = new PointZero();
var p3 = new PointOne();
```

本地的类型可以引用类型参数, 本地的类和接口本身即可能是泛型. 比如:

```ts
function f3() {
    function f<X, Y>(x: X, y: Y) {
        class C {
            public x = x;
            public y = y;
        }
        return C;
    }
    let C = f(10, "hello");
    let v = new C();
    let x = v.x;  // number
    let y = v.y;  // string
}
```

### 类表达式

TypeScript 1.6 增加了对 ES6 类表达式的支持. 在一个类表达式中, 类的名称是可选的, 如果指明, 作用域仅限于类表达式本身. 这和函数表达式可选的名称类似. 在类表达式外无法引用其实例类型, 但是自然也能够从类型结构上匹配. 比如:

```ts
let Point = class {
    constructor(public x: number, public y: number) { }
    public length() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
};
var p = new Point(3, 4);  // p has anonymous class type
console.log(p.length());
```

### 继承表达式

TypeScript 1.6 增加了对类继承任意值为一个构造函数的表达式的支持. 这样一来内建的类型也可以在类的声明中被继承.

`extends` 语句过去需要指定一个类型引用, 现在接受一个可选类型参数的表达式. 表达式的类型必须为有至少一个构造函数签名的构造函数, 并且需要和 `extends` 语句中类型参数数量一致. 匹配的构造函数签名的返回值类型是类实例类型继承的基类型. 如此一来, 这使得普通的类和与类相似的表达式可以在 `extends` 语句中使用.

一些例子:

```ts
// 继承内建类

class MyArray extends Array<number> { }
class MyError extends Error { }

// 继承表达式类

class ThingA {
    getGreeting() { return "Hello from A"; }
}

class ThingB {
    getGreeting() { return "Hello from B"; }
}

interface Greeter {
    getGreeting(): string;
}

interface GreeterConstructor {
    new (): Greeter;
}

function getGreeterBase(): GreeterConstructor {
    return Math.random() >= 0.5 ? ThingA : ThingB;
}

class Test extends getGreeterBase() {
    sayHello() {
        console.log(this.getGreeting());
    }
}
```

### `abstract` (抽象的) 类和方法

TypeScript 1.6 为类和它们的方法增加了 `abstract` 关键字. 一个抽象类允许没有被实现的方法, 并且不能被构造.

#### 例子

```ts
abstract class Base {
    abstract getThing(): string;
    getOtherThing() { return 'hello'; }
}

let x = new Base(); // 错误, 'Base' 是抽象的

// 错误, 必须也为抽象类, 或者实现 'getThing' 方法
class Derived1 extends Base { }

class Derived2 extends Base {
    getThing() { return 'hello'; }
    foo() { 
        super.getThing();// 错误: 不能调用 'super' 的抽象方法
    } 
}

var x = new Derived2(); // 正确
var y: Base = new Derived2(); // 同样正确
y.getThing(); // 正确
y.getOtherThing(); // 正确
```

### 泛型别名

TypeScript 1.6 中, 类型别名支持泛型. 比如:

```ts
type Lazy<T> = T | (() => T);

var s: Lazy<string>;
s = "eager";
s = () => "lazy";

interface Tuple<A, B> {
    a: A;
    b: B;
}

type Pair<T> = Tuple<T, T>;
```

### 更严格的对象字面量赋值检查

为了能发现多余或者错误拼写的属性, TypeScript 1.6 使用了更严格的对象字面量检查. 确切地说, 在将一个新的对象字面量赋值给一个变量, 或者传递给类型非空的参数时, 如果对象字面量的属性在目标类型中不存在, 则会视为错误.

#### 例子

```ts
var x: { foo: number };
x = { foo: 1, baz: 2 };  // 错误, 多余的属性 `baz`

var y: { foo: number, bar?: number };
y = { foo: 1, baz: 2 };  // 错误, 多余或者拼错的属性 `baz`
```

一个类型可以通过包含一个索引签名来现实指明未出现在类型中的属性是被允许的.

```ts
var x: { foo: number, [x: string]: any };
x = { foo: 1, baz: 2 };  // 现在 `baz` 匹配了索引签名
```

### ES6 生成器 (generators)

TypeScript 1.6 添加了对于 ES6 输出的生成器支持.

一个生成器函数可以有返回值类型标注, 就像普通的函数. 标注表示生成器函数返回的生成器的类型. 这里有个例子:

```ts
function *g(): Iterable<string> {
    for (var i = 0; i < 100; i++) {
        yield ""; // string 可以赋值给 string
    }
    yield * otherStringGenerator(); // otherStringGenerator 必须可遍历, 并且元素类型需要可赋值给 string
}
```

没有标注类型的生成器函数会有自动推演的类型. 在下面的例子中, 类型会由 yield 语句推演出来:

```ts
function *g() {
    for (var i = 0; i < 100; i++) {
        yield ""; // 推导出 string
    }
    yield * otherStringGenerator(); // 推导出 otherStringGenerator 的元素类型
}
```

### 对 `async` (异步) 函数的试验性支持

TypeScript 1.6 增加了编译到 ES6 时对 `async` 函数试验性的支持. 异步函数会执行一个异步的操作, 在等待的同时不会阻塞程序的正常运行. 这是通过与 ES6 兼容的 `Promise` 实现完成的, 并且会将函数体转换为支持在等待的异步操作完成时继续的形式.

由 `async` 标记的函数或方法被称作_异步函数_. 这个标记告诉了编译器该函数体需要被转换, 关键字 _await_ 则应该被当做一个一元运算符, 而不是标示符. 一个_异步函数_必须返回类型与 `Promise` 兼容的值. 返回值类型的推断只能在有一个全局的, 与 ES6 兼容的 `Promise` 类型时使用.

#### 例子

```ts
var p: Promise<number> = /* ... */;  
async function fn(): Promise<number> {  
  var i = await p; // 暂停执行知道 'p' 得到结果. 'i' 的类型为 "number"
  return 1 + i;  
}  

var a = async (): Promise<number> => 1 + await p; // 暂停执行.  
var a = async () => 1 + await p; // 暂停执行. 使用 --target ES6 选项编译时返回值类型被推断为 "Promise<number>"
var fe = async function(): Promise<number> {  
  var i = await p; // 暂停执行知道 'p' 得到结果. 'i' 的类型为 "number"
  return 1 + i;  
}  

class C {  
  async m(): Promise<number> {  
    var i = await p; // 暂停执行知道 'p' 得到结果. 'i' 的类型为 "number"
    return 1 + i;  
  }  

  async get p(): Promise<number> {  
    var i = await p; // 暂停执行知道 'p' 得到结果. 'i' 的类型为 "number"
    return 1 + i;  
  }  
}
```

### 每天发布新版本

由于并不算严格意义上的语言变化<sup>[2]</sup>, 每天的新版本可以使用如下命令安装获得:

```sh
npm install -g typescript@next
```

### 对模块解析逻辑的调整

从 1.6 开始, TypeScript 编译器对于 "commonjs" 的模块解析会使用一套不同的规则. 这些[规则](https://github.com/Microsoft/TypeScript/issues/2338) 尝试模仿 Node 查找模块的过程. 这就意味着 node 模块可以包含它的类型信息, 并且 TypeScript 编译器可以找到这些信息. 不过用户可以通过使用 `--moduleResolution` 命令行选项覆盖模块解析规则. 支持的值有:

- 'classic' - TypeScript 1.6 以前的编译器使用的模块解析规则
- 'node' - 与 node 相似的模块解析

### 合并外围类和接口的声明

外围类的实例类型可以通过接口声明来扩展. 类构造函数对象不会被修改. 比如说:

```ts
declare class Foo {
    public x : number;
}

interface Foo {
    y : string;
}

function bar(foo : Foo)  {
    foo.x = 1; // 没问题, 在类 Foo 中有声明
    foo.y = "1"; // 没问题, 在接口 Foo 中有声明
}
```

### 用户定义的类型收窄函数

TypeScript 1.6 增加了一个新的在 `if` 语句中收窄变量类型的方式, 作为对 `typeof` 和 `instanceof` 的补充. 用户定义的类型收窄函数的返回值类型标注形式为 `x is T`, 这里 `x` 是函数声明中的形参, `T` 是任何类型. 当一个用户定义的类型收窄函数在 `if` 语句中被传入某个变量执行时, 该变量的类型会被收窄到 `T`.

#### 例子

```ts
function isCat(a: any): a is Cat {
  return a.name === 'kitty';
}

var x: Cat | Dog;
if(isCat(x)) {
  x.meow(); // 那么, x 在这个代码块内是 Cat 类型
}
```

### `tsconfig.json` 对 `exclude` 属性的支持

一个没有写明 `files` 属性的 `tsconfig.json` 文件 (默认会引用所有子目录下的 *.ts 文件) 现在可以包含一个 `exclude` 属性, 指定需要在编译中排除的文件或者目录列表. `exclude` 属性必须是一个字符串数组, 其中每一个元素指定对应的一个文件或者文件夹名称对于 `tsconfig.json` 文件所在位置的相对路径. 举例来说:

```json
{
    "compilerOptions": {
        "out": "test.js"
    },
    "exclude": [
        "node_modules",
        "test.ts",
        "utils/t2.ts"
    ]
}
```

`exclude` 列表不支持通配符. 仅仅可以是文件或者目录的列表.

### `--init` 命令行选项

在一个目录中执行 `tsc --init` 可以在该目录中创建一个包含了默认值的 `tsconfig.json`. 可以通过一并传递其他选项来生成初始的 `tsconfig.json`. 

## TypeScript 1.5

### ES6 模块

TypeScript 1.5 支持 ECMAScript 6 (ES6) 模块. ES6 模块可以看做之前 TypeScript 的外部模块换上了新的语法: ES6 模块是分开加载的源文件, 这些文件还可能引入其他模块, 并且导出部分供外部可访问. ES6 模块新增了几种导入和导出声明. 我们建议使用 TypeScript 开发的库和应用能够更新到新的语法, 但不做强制要求. 新的 ES6 模块语法和 TypeScript 原来的内部和外部模块结构同时被支持, 如果需要也可以混合使用.

#### 导出声明

作为 TypeScript 已有的 `export` 前缀支持, 模块成员也可以使用单独导出的声明导出, 如果需要, `as` 语句可以指定不同的导出名称.

```ts
interface Stream { ... }
function writeToStream(stream: Stream, data: string) { ... }
export { Stream, writeToStream as write };  // writeToStream 导出为 write
```

引入声明也可以使用 `as` 语句来指定一个不同的导入名称. 比如:

```ts
import { read, write, standardOutput as stdout } from "./inout";
var s = read(stdout);
write(stdout, s);
```

作为单独导入的候选项, 命名空间导入可以导入整个模块:

```ts
import * as io from "./inout";
var s = io.read(io.standardOutput);
io.write(io.standardOutput, s);
```

### 重新导出

使用 `from` 语句一个模块可以复制指定模块的导出项到当前模块, 而无需创建本地名称.

```ts
export { read, write, standardOutput as stdout } from "./inout";
```

`export *` 可以用来重新导出另一个模块的所有导出项. 在创建一个聚合了其他几个模块导出项的模块时很方便.

```ts
export function transform(s: string): string { ... }
export * from "./mod1";
export * from "./mod2";
```

#### 默认导出项

一个 export default 声明表示一个表达式是这个模块的默认导出项.

```ts
export default class Greeter {
    sayHello() {
        console.log("Greetings!");
    }
}
```

对应的可以使用默认导入:

```ts
import Greeter from "./greeter";
var g = new Greeter();
g.sayHello();
```

#### 无导入加载

"无导入加载" 可以被用来加载某些只需要其副作用的模块.

```ts
import "./polyfills";
```

了解更多关于模块的信息, 请参见 [ES6 模块支持规范](https://github.com/Microsoft/TypeScript/issues/2242).

### 声明与赋值的解构

TypeScript 1.5 添加了对 ES6 解构声明与赋值的支持.

#### 解构

解构声明会引入一个或多个命名变量, 并且初始化它们的值为对象的属性或者数组的元素对应的值.

比如说, 下面的例子声明了变量 `x`, `y` 和 `z`, 并且分别将它们的值初始化为 `getSomeObject().x`, `getSomeObject().x` 和 `getSomeObject().x`:

```ts
var { x, y, z } = getSomeObject();
```

解构声明也可以用于从数组中得到值.

```ts
var [x, y, z = 10] = getSomeArray();
```

相似的, 解构可以用在函数的参数声明中:

```ts
function drawText({ text = "", location: [x, y] = [0, 0], bold = false }) {  
    // 画出文本
}

// 以一个对象字面量为参数调用 drawText
var item = { text: "someText", location: [1,2,3], style: "italics" };
drawText(item);
```

#### 赋值

解构也可以被用于普通的赋值表达式. 举例来讲, 交换两个变量的值可以被写作一个解构赋值:

```ts
var x = 1;  
var y = 2;  
[x, y] = [y, x];
```

### `namespace` (命名空间) 关键字

过去 TypeScript 中 `module` 关键字既可以定义 "内部模块", 也可以定义 "外部模块"; 这让刚刚接触 TypeScript 的开发者有些困惑. "内部模块" 的概念更接近于大部分人眼中的命名空间; 而 "外部模块" 对于 JS 来讲, 现在也就是模块了.

> 注意: 之前定义内部模块的语法依然被支持.

**之前**:

```ts
module Math {
    export function add(x, y) { ... }
}
```

**之后**:

```ts
namespace Math {
    export function add(x, y) { ... }
}
```

### `let` 和 `const` 的支持

ES6 的 `let` 和 `const` 声明现在支持编译到 ES3 和 ES5. 

#### Const

```ts
const MAX = 100;

++MAX; // 错误: 自增/减运算符不能用于一个常量
```

#### 块级作用域

```ts
if (true) {
  let a = 4;
  // 使用变量 a
}
else {
  let a = "string";
  // 使用变量 a
}

alert(a); // 错误: 变量 a 在当前作用域未定义
```

### `for...of` 的支持

TypeScript 1.5 增加了 ES6 `for...of` 循环编译到 ES3/ES5 时对数组的支持, 以及编译到 ES6 时对满足 `Iterator` 接口的全面支持.

#### 例子:

TypeScript 编译器会转译 `for...of` 数组到具有语义的 ES3/ES5 JavaScript (如果被设置为编译到这些版本).

```ts
for (var v of expr) { }
```

会输出为:

```js
for (var _i = 0, _a = expr; _i < _a.length; _i++) {
    var v = _a[_i];
}
```

### 装饰器

> TypeScript 装饰器是局域 [ES7 装饰器](https://github.com/wycats/javascript-decorators) 提案的. 

一个装饰器是:

- 一个表达式
- 并且值为一个函数
- 接受 `target`, `name`, 以及属性描述对象作为参数
- 可选返回一个会被应用到目标对象的属性描述对象

> 了解更多, 请参见 [装饰器](https://github.com/Microsoft/TypeScript/issues/2249) 提案.

#### 例子:

装饰器 `readonly` 和 `enumerable(false)` 会在属性 `method` 添加到类 `C` 上之前被应用. 这使得装饰器可以修改其实现, 具体到这个例子, 设置了 `descriptor` 为 `writable: false` 以及 `enumerable: false`.

```ts
class C {
  @readonly
  @enumerable(false)
  method() { }
}

function readonly(target, key, descriptor) {
    descriptor.writable = false;
}

function enumerable(value) {
  return function (target, key, descriptor) {
     descriptor.enumerable = value;
  }
}
```

### 计算属性

使用动态的属性初始化一个对象可能会很麻烦. 参考下面的例子:

```ts
type NeighborMap = { [name: string]: Node };
type Node = { name: string; neighbors: NeighborMap;}

function makeNode(name: string, initialNeighbor: Node): Node {
    var neighbors: NeighborMap = {};
    neighbors[initialNeighbor.name] = initialNeighbor;
    return { name: name, neighbors: neighbors };
}
```

这里我们需要创建一个包含了 neighbor-map 的变量, 便于我们初始化它. 使用 TypeScript 1.5, 我们可以让编译器来干重活:

```ts
function makeNode(name: string, initialNeighbor: Node): Node {
    return {
        name: name,
        neighbors: {
            [initialNeighbor.name]: initialNeighbor
        }
    }
}
```

### 指出 `UMD` 和 `System` 模块输出

作为 `AMD` 和 `CommonJS` 模块加载器的补充, TypeScript 现在支持输出为 `UMD` ([Universal Module Definition](https://github.com/umdjs/umd)) 和 [`System`](https://github.com/systemjs/systemjs) 模块的格式.

**用法**:

> tsc --module umd

以及

> tsc --module system


### Unicode 字符串码位转义

ES6 中允许用户使用单个转义表示一个 Unicode 码位.

举个例子, 考虑我们需要转义一个包含了字符 '𠮷' 的字符串. 在 UTF-16/USC2 中, '𠮷' 被表示为一个代理对, 意思就是它被编码为一对 16 位值的代码单元, 具体来说是 `0xD842` 和 `0xDFB7`. 之前这意味着你必须将该码位转义为 `"\uD842\uDFB7"`. 这样做有一个重要的问题, 就事很难讲两个独立的字符同一个代理对区分开来.

通过 ES6 的码位转义, 你可以在字符串或模板字符串中清晰地通过一个转义表示一个确切的字符: `"\u{20bb7}"`. TypeScript 在编译到 ES3/ES5 时会将该字符串输出为 `"\uD842\uDFB7"`.

### 标签模板字符串编译到 ES3/ES5

TypeScript 1.4 中, 我们添加了模板字符串编译到所有 ES 版本的支持, 并且支持标签模板字符串编译到 ES6. 得益于 [@ivogabe](https://github.com/ivogabe) 的大量付出, 我们填补了标签模板字符串对编译到 ES3/ES5 的支持.

当编译到 ES3/ES5 时, 下面的代码:

```ts
function oddRawStrings(strs: TemplateStringsArray, n1, n2) {
    return strs.raw.filter((raw, index) => index % 2 === 1);
}

oddRawStrings `Hello \n${123} \t ${456}\n world`
```

会被输出为:

```ts
function oddRawStrings(strs, n1, n2) {
    return strs.raw.filter(function (raw, index) {
        return index % 2 === 1;
    });
}
(_a = ["Hello \n", " \t ", "\n world"], _a.raw = ["Hello \\n", " \\t ", "\\n world"], oddRawStrings(_a, 123, 456));
var _a;
```

### AMD 可选依赖名称

`/// <amd-dependency path="x" />` 会告诉编译器需要被注入到模块 `require` 方法中的非 TS 模块依赖; 然而在 TS 代码中无法使用这个模块.

新的 `amd-dependency name` 属性允许为 AMD 依赖传递一个可选的名称.

```ts
/// <amd-dependency path="legacy/moduleA" name="moduleA"/>
declare var moduleA:MyType
moduleA.callStuff()
```

生成的 JS 代码:

```ts
define(["require", "exports", "legacy/moduleA"], function (require, exports, moduleA) {
    moduleA.callStuff()
});
```

### 通过 `tsconfig.json` 指示一个项目

通过添加 `tsconfig.json` 到一个目录指明这是一个 TypeScript 项目的根目录. `tsconfig.json` 文件指定了根文件以及编译项目需要的编译器选项. 一个项目可以由以下方式编译:

- 调用 tsc 并不指定输入文件, 此时编译器会从当前目录开始往上级目录寻找 `tsconfig.json` 文件.
- 调用 tsc 并不指定输入文件, 使用 `-project` (或者 `-p`) 命令行选项指定包含了 `tsconfig.json` 文件的目录.

#### 例子:
```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noImplicitAny": true,
        "sourceMap": true,
    }
}
```

参见 [tsconfig.json wiki 页面](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json) 查看更多信息.

### `--rootDir` 命令行选项

选项 `--outDir` 在输出中会保留输入的层级关系. 编译器将所有输入文件共有的最长路径作为根路径; 并且在输出中应用对应的子层级关系.

有的时候这并不是期望的结果, 比如输入 `FolderA\FolderB\1.ts` 和 `FolderA\FolderB\2.ts`, 输出结构会是 `FolderA\FolderB\` 对应的结构. 如果输入中新增 `FolderA\3.ts` 文件, 输出的结构将突然变为 `FolderA\` 对应的结构.

`--rootDir` 指定了会输出对应结构的输入目录, 不再通过计算获得.

### `--noEmitHelpers` 命令行选项

TypeScript 编译器在需要的时候会输出一些像 `__extends` 这样的工具函数. 这些函数会在使用它们的所有文件中输出. 如果你想要聚合所有的工具函数到同一个位置, 或者覆盖默认的行为, 使用 `--noEmitHelpers` 来告知编译器不要输出它们.

### `--newLine` 命令行选项

默认输出的换行符在 Windows 上是 `\r\n`, 在 *nix 上是 `\n`. `--newLine` 命令行标记可以覆盖这个行为, 并指定输出文件中使用的换行符.

### `--inlineSourceMap` and `inlineSources` 命令行选项

`--inlineSourceMap` 将内嵌源文件映射到 `.js` 文件, 而不是在单独的 `.js.map` 文件中. `--inlineSources` 允许进一步将 `.ts` 文件内容包含到输出文件中.

## TypeScript 1.4

### 联合类型

#### 概览

联合类型是描述一个可能是几个类型之一的值的有效方式. 举例来说, 你可能会有一个 API 用于执行一个 `commandline` 为 `string`, `string[]` 或者是返回值为 `string` 的函数的程序. 现在可以这样写:

```ts
interface RunOptions {
   program: string;
   commandline: string[]|string|(() => string);
}
```

对联合类型的赋值非常直观 -- 任何可以赋值给联合类型中任意一个类型的值都可以赋值给这个联合类型:

```ts
var opts: RunOptions = /* ... */;
opts.commandline = '-hello world'; // 没问题
opts.commandline = ['-hello', 'world']; // 没问题
opts.commandline = [42]; // 错误, number 不是 string 或 string[]
```

当从联合类型中读取时, 你可以看到联合类型中各类型共有的属性:

```ts
if(opts.length === 0) { // 没问题, string 和 string[] 都有 'length' 属性
  console.log("it's empty");
}
```

使用类型收窄, 你可以方便的使用具有联合类型的变量:

```ts
function formatCommandline(c: string|string[]) {
    if(typeof c === 'string') {
        return c.trim();
    } else {
        return c.join(' ');
    }
}
```

#### 更严格的泛型

结合联合类型可以表示很多种类型场景, 我们决定让某些泛型调用更加严格. 之前, 以下的代码能出人意料地无错通过编译:

```ts
function equal<T>(lhs: T, rhs: T): boolean {
  return lhs === rhs;
}

// 过去: 无错误
// 现在: 错误, 'string' 和 'number' 间没有最佳共有类型
var e = equal(42, 'hello');
```

而通过联合类型, 你现在可以在函数声明或者调用的时候指明想要的行为:

```ts
// 'choose' 函数的参数类型必须相同
function choose1<T>(a: T, b: T): T { return Math.random() > 0.5 ? a : b }
var a = choose1('hello', 42); // 错误
var b = choose1<string|number>('hello', 42); // 正确

// 'choose' 函数的参数类型不需要相同
function choose2<T, U>(a: T, b: U): T|U { return Math.random() > 0.5 ? a : b }
var c = choose2('bar', 'foo'); // 正确, c: string
var d = choose2('hello', 42); // 正确, d: string|number
```

#### 更好的类型接口

联合类型也允许了数组或者其他地方有更好的类型接口, 以便一个集合中可能有多重类型.

```ts
var x = [1, 'hello']; // x: Array<string|number>
x[0] = 'world'; // 正确
x[0] = false; // 错误, boolean 不是 string 或 number
```

### `let` 声明

在 JavaScript 中, `var` 声明会被 "提升" 到它们所在的作用域. 这可能会导致一些令人疑惑的问题:

```ts
console.log(x); // 本意是在这里写 'y'
/* 当前代码块靠后的位置 */
var x = 'hello';
```

ES6 的关键字 `let` 现在在 TypeScript 中得到支持, 声明变量获得了更直观的块级语义. 一个 `let` 变量只能在它声明之后被引用, 其作用域被限定于它被声明的句法块:

```ts
if(foo) {
    console.log(x); // 错误, 在声明前不能引用 x
    let x = 'hello';
} else {
    console.log(x); // 错误, x 在当前块中没有声明
}
```

`let` 仅在编译到 ECMAScript 6 时被支持 (`--target ES6`).

### `const` 声明

另外一种在 TypeScript 中被支持的新的 ES6 声明类型是 `const`. 一个 `const` 变量不能被赋值, 并且在声明的时候必须被初始化. 这可以用在你声明和初始化后不希望值被改变时:

```ts
const halfPi = Math.PI / 2;
halfPi = 2; // 错误, 不能赋值给一个 `const`
```

`const` 仅在编译到 ECMAScript 6 时被支持 (`--target ES6`).

## 模板字符串

TypeScript 现在支持 ES6 模板字符串. 现在可以方便地在字符串中嵌入任何表达式:

```ts
var name = "TypeScript";
var greeting  = `Hello, ${name}! Your name has ${name.length} characters`;
```

当编译到 ES6 以前的版本时, 字符串会被分解为:

```ts
var name = "TypeScript!";
var greeting = "Hello, " + name + "! Your name has " + name.length + " characters";
```

### 类型收窄

在 JavaScript 中常常用 `typeof` 或者 `instanceof` 在运行时检查一个表达式的类型. TypeScript 现在理解这些条件, 并且在 `if` 语句中会据此改变类型接口.

使用 `typeof` 来检查一个变量:

```ts
var x: any = /* ... */;
if(typeof x === 'string') {
    console.log(x.subtr(1)); // 错误, 'subtr' 在 'string' 上不存在
}
// 这里 x 的类型依然是 any
x.unknown(); // 正确
```

与联合类型和 `else` 一起使用 `typeof`:

```ts
var x: string|HTMLElement = /* ... */;
if(typeof x === 'string') {
    // x 如上所述是一个 string
} else {
    // x 在这里是 HTMLElement
    console.log(x.innerHTML);
}
```

与类和联合类型一起使用 `instanceof`:

```ts
class Dog { woof() { } }
class Cat { meow() { } }
var pet: Dog|Cat = /* ... */;
if(pet instanceof Dog) {
    pet.woof(); // 正确
} else {
    pet.woof(); // 错误
}
```

### 类型别名

现在你可以使用 `type` 关键字为类型定义一个_别名_:

```ts
type PrimitiveArray = Array<string|number|boolean>;
type MyNumber = number;
type NgScope = ng.IScope;
type Callback = () => void;
```

类型别名和它们原来的类型完全相同; 它们仅仅是另一种表述的名称.

### `const enum` (完全内联的枚举)

枚举非常有用, 但有的程序可能并不需要生成的代码, 而简单地将枚举成员的数字值内联能够给这些程序带来一定好处. 新的 `const enum` 声明在类型安全上和 `enum` 一致, 但是编译后会被完全抹去.

```ts
const enum Suit { Clubs, Diamonds, Hearts, Spades }
var d = Suit.Diamonds;
```

编译为:

```js
var d = 1;
```

如果可能 TypeScript 现在会计算枚举的值:

```ts
enum MyFlags {
  None = 0,
  Neat = 1,
  Cool = 2,
  Awesome = 4,
  Best = Neat | Cool | Awesome
}
var b = MyFlags.Best; // 输出 var b = 7;
```

### `--noEmitOnError` 命令行选项

TypeScript 编译器的默认行为会在出现类型错误 (比如, 尝试赋值一个 `string` 给 `number`) 时依然输出 .js 文件. 在构建服务器或者其他只希望有 "干净" 版本的场景可能并不是期望的结果. 新的 `noEmitOnError` 标记会使编译器在有任何错误时不输出 .js 代码.

对于 MSBuild 的项目这是目前的默认设定; 这使 MSBuild 的增量编译变得可行, 输出仅在代码没有问题时产生.

### AMD 模块名称

AMD 模块默认生成是匿名的. 对于一些像打包工具这样的处理输出模块的工具会带来一些问题 (比如 r.js).

新的 `amd-module name` 标签允许传入一个可选的模块名称给编译器:

```ts
//// [amdModule.ts]
///<amd-module name='NamedModule'/>
export class C {
}
```

这会在调用 AMD 的 `define` 方法时传入名称 `NamedModule`:

```ts
//// [amdModule.js]
define("NamedModule", ["require", "exports"], function (require, exports) {
    var C = (function () {
        function C() {
        }
        return C;
    })();
    exports.C = C;
});
```

## TypeScript 1.3

### 受保护成员

在类中新的 `protected` 标示符就像它在其他一些像 C++, C# 与 Java 这样的常见语言中的功能一致. 一个 `protected` (受保护的) 的成员仅在子类或者声明它的类中可见:

```ts
class Thing {
  protected doSomething() { /* ... */ }
}

class MyThing extends Thing {
  public myMethod() {
    // 正确, 可以在子类中访问受保护成员
    this.doSomething();
  }
}
var t = new MyThing();
t.doSomething(); // 错误, 不能在类外调用受保护成员
```

### 元组类型

元组类型可以表示一个数组中部分元素的类型是已知, 但不一定相同的情况. 举例来说, 你可能希望描述一个数组, 在下标 0 处为 `string`, 在 1 处为 `number`:

```ts
// 声明一个元组类型
var x: [string, number];
// 初始化
x = ['hello', 10]; // 正确
// 错误的初始化
x = [10, 'hello']; // 错误
```

当使用已知的下标访问某个元素时, 能够获得正确的类型:

```ts
console.log(x[0].substr(1)); // 正确
console.log(x[1].substr(1)); // 错误, 'number' 类型没有 'substr' 属性
```

注意在 TypeScript 1.4 中, 当访问某个下标不在已知范围内的元素时, 获得的是联合类型:

```ts
x[3] = 'world'; // 正确
console.log(x[5].toString()); // 正确, 'string' 和 'number' 都有 toString 方法
x[6] = true; // 错误, boolean 不是 number 或 string
```

## TypeScript 1.1

### 性能优化

1.1 版编译器大体比之前任何版本快 4 倍. 查看 [这篇文章里令人印象深刻的对比](http://blogs.msdn.com/b/typescript/archive/2014/10/06/announcing-typescript-1-1-ctp.aspx).

### 更好的模块可见规则

TypeScript 现在仅在开启了 `--declaration` 标记时严格要求模块类型的可见性. 对于 Angular 的场景来说非常有用, 比如:

```ts
module MyControllers {
  interface ZooScope extends ng.IScope {
    animals: Animal[];
  }
  export class ZooController {
    // 过去是错误的 (无法暴露 ZooScope), 而现在仅在需要生成 .d.ts 文件时报错
    constructor(public $scope: ZooScope) { }
    /* 更多代码 */
  }
}
```

---

**[1]** 原文为 "The changes also inline calls `Object.getOwnPropertyDescriptor` and `Object.defineProperty` in a backwards-compatible fashion that allows for a to clean up the emit for ES5 and later by removing various repetitive calls to the aforementioned `Object` methods."

**[2]** 原文为 "While not strictly a language change..."
