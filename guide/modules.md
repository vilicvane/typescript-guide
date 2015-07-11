# 模块

## 内部模块 (Internal Modules)

内部模块更像一个具有闭包的命名空间, 在 TypeScript 1.5 中也会有新的关键字 `namespace` 来代替现在使用的 `module`.

不过例子中我们将依然沿用 `module` 关键字.

### 基本的使用

```ts
// 嵌套:
module Utils {
    // 闭包中的变量:
    var hashed = 'abcdefg1234567'
    
    // 导出嵌套的模块:
    export module Crypto {
        // 导出函数:
        export function md5(): string {
            return hashed;
        }
    }
}

var fakeMd5 = Utils.Crypto.md5();

// 多级:
module Utils.XHR {
    // 除了到处函数与变量外, 还可以导出类型.
    export interface Result {
        xhr: XMLHttpRequest;
        protocol: string;
    }
    
    export function request(url: string, data?: any): Result {
        return {
            xhr: undefined,
            protocol: 'http'
        };
    }
}
```

内部模块主要是用在单个文件或者能够在同一个作用域内执行的多个文件中, 好处是无需大量地 `import`/`require`, 但需要自己处理文件之间的依赖关系 (顺序). 在前端常见的用法是通过多个 `<script>` 标签依次引入多个脚本文件, 或者直接编译为一个文件.

## 外部模块 (External Modules)



---

&lt; [类与接口](classes-and-interfaces.md) | [x](x.md) &gt;
