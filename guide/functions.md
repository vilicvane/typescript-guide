# 函数

## 类型标注

基本用法:

```ts
function foo(str: string, num: number): void {
    console.log(str, num);
}

function bar(date: Date): string {
    return date.getFullYear() + '-' + (date.getMonth() + 1);
}
```

可选参数:

```ts
function foo(str: string, num?: number): void {
    console.log(str, num || 0);
}

function bar(str: string, num = 10, bool = true): void {
    console.log(str, num, bool);
}
```

可变长度的参数:

```ts
function foo(num: number, ...args: string[]): string[] {
    return args.slice(num);
}
```

## 函数重载

很多时候我们需要一个函数对于不同的参数响应不同的功能, 这时我们可以为函数添加多个签名来告知调用者多种可能的组合.

但需要注意的是, 这种标记并不会影响函数的行为, 对于不同参数的判断依然得代码自己的逻辑.

```ts
function foo(): string;
function foo(str: string): number;
function foo(str?: string): any {
    if (typeof str === 'string') {
        return str.length;
    } else {
        return 'empty';
    }
}
```

注意这里只有最后一行 `function foo(str?: string): any` 对函数内的实现有影响, 而对于函数外对该函数的调用, 只有前两行会作为类型检查的依据. 并且最后一行的类型必须兼容前面两行的签名:

- `str?: string` 是没有或有一个类型为 `string` 的参数.
- `any` 既可以是 `string` 也可以是 `number`.

## 延伸

函数签名中的类型标注还可以结合一些 ES6 的特性, 比如[解构 (Destructuring)](features/destructuring.md), 读者可以尝试一些自己认为合理的形式.

---

<div style="position: absolute;">[< 基本类型](basic-types.md)</div>

<div style="text-align: right;">[类与接口 >](classes-and-interfaces.md)</div>