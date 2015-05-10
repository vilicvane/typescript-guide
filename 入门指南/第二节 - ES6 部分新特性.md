# 第二节 - ES6 部分新特性

## let/const 语句

https://github.com/Microsoft/TypeScript/pull/904

`let` 和 `var` 相似, 都可以用来声明变量, 不同的是 `var` 声明的变量作用域是 `function`, `let` 则是 `block`.

```typescript
var i = 0;

for (let i = 0; i < 10; i++) {
    console.log('let-for:', i);
}

{
    let i = '...';
    console.log('let-block:', i);
}

console.log('var:', i);
```

`const` 的作用域和 `let` 一致, 用于声明常量.

## for...of 循环

用于遍历数组中的元素.

```typescript
var arr = [1, 2, 3];

for (let n of arr) {
    console.log(n);
}
```

