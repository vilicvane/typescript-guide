# 枚举

枚举类型的值默认从 0 开始:

```ts
enum State {
    stateOne,
    stateTwo
}

console.log(State.stateOne); // logs `0`.
```

但可以为其指定具体的值:

```ts
enum ErrorCode {
    accessDenied = 1000,
    incorrectPassword, // 1001
    noSuchUser // 1002
    
    databaseError = 2000,
    queryFailed, // 2001
    insertionFailed // 2002
}
```

某些情况还可以结合位运算来标记功能开关:

```ts
enum Options {
    featureOne = 1,
    featureTwo = 1 << 1,
    featureThree = 1 << 2
}

var enabledOptions = Options.featureOne | Options.featureTwo;

if (enabledOptions & Options.featureOne) {
    console.log('do feature one');
}
```

在 TypeScript 1.4 中, 出于性能考虑, 在原有枚举特性的基础上加入了 `const enum`:

```ts
const enum ASTType {
    program,
    expression,
    operator
}

var typeProgram = ASTType.program;
```

**默认配置下**, 编译后将只有一行输出:

```js
var typeProgram = 0 /* program */;
```

请读者自行对比加与不加 const, 编译结果的区别.

除了性能考虑之外, 使用 `const enum` 也可以避免由于模块互相引用带来的与之相关的问题.
