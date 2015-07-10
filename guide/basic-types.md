TypeScript 在 2012 年 10 月首次公开, 经过近一年多的发展, 于 2014 年 4 月的 Build 大会上发布了 1.0 版. 相较于目前的版本, TypeScript 1.0 还缺少了很多特性. 但多番思考后, 编者认为或许从 1.0 出发, 能让读者更自然地接受/融入到 TypeScript 中来.

---

# 基本类型

## 原始值类型

TypeScript 中与原始值对应的特殊类型, 包括 `boolean`, `number`, `string`:

```ts
var bool: boolean = true;
var num: number = 123;
var str: string = 'hello';
```

表示任意类型的 `any`:

```ts
var foo: any = 123;
foo = 'thank you';
```

以及表示 "空" 的 `void`, 最常见的是用于标注函数无返回值:

```ts
function bar(): void {
    alert('thank you very much');
}
```

## 数组 (Array)

TypeScript 中可以用两种方式来表示一个数组:

```ts
var arrA: string[] = ['hello', 'thank you'];
var arrB: Array<string> = ['thank you very much'];
```

其中 `Array<string>` 这种形式稍后我们还会看到很多, 也是 TypeScript 类型系统中非常重要的一部分.

## 枚举 (Enum)

有时我们需要用数字或者字符串来描述一个过程的多个状态, 或者一个接口的多个选项, 这时使用枚举类型则是不错的选择:

```ts
enum State {
    created,
    started,
    ended,
    disposed
}

var currentState = State.created;

switch (currentState) {
    case State.created:
        console.log('created');
        break;
    case State.started:
        console.log('started');
        break;
    case State.ended:
    case State.disposed:
        console.log('ended or disposed');
        break;
    default:
        throw new Error('Invalid state');
}
```

更多用法请参考 [枚举](features/enum.md).

---

&lt; [目录](README.md) | [函数](functions.md) &gt;
