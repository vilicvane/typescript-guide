# 模块

## 命名空间 (Namespace)

命名空间在 TypeScript 1.5 以前叫做内部模块 (Internal Module), 1.5 开始已经有新的关键字 `namespace` 来代替之前使用的 `module`.

### 基本的使用

```ts
// 嵌套:
namespace Utils {
    // 闭包中的变量:
    var hashed = 'abcdefg1234567'
    
    // 导出嵌套的模块:
    export namespace Crypto {
        // 导出函数:
        export function md5(): string {
            return hashed;
        }
    }
}

var fakeMd5 = Utils.Crypto.md5();

// 多级:
namespace Utils.XHR {
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

命名空间主要是用在单个文件或者能够在同一个作用域内执行的多个文件中, 好处是无需大量地 `import`, 但需要自己处理文件之间的依赖关系 (顺序). 在前端常见的用法是通过多个 `<script>` 标签依次引入多个脚本文件, 或者直接编译为一个文件.

## 外部模块 (External Modules)

Node.js, CommonJS, AMD, ES6 相关模块都是典型的外部模块. 以 Node.js 为例, 引入 `path` 模块:

```ts
import * as Path from 'path';
import { join, relative } from 'path';

var jointPathA = Path.join('parent', 'child');
var jointPathB = join('parent', 'child');
```

TypeScript 支持将文件编译为多种模块系统对应的格式, 截止 1.5 包括 AMD, CommonJS, UMD, SystemJS 和 ES6 原生模块.

详情请参考 [tsconfig.json 的配置](./tsconfig.md).

### 编写模块

```ts
// 文件 lib.ts

export function add(a: number, b: number): number {
    return a + b;
}

export function subtract(a: number, b: number): number {
    return a - b;
}

// 默认导出项
export default var str = 'abc';
```

引入 lib.ts:

```ts
import { add, subtract } from './lib';

add(1, 2);
subtract(3, 4);

// 引入默认导出项 `add`:
import theStr from './lib';
// theStr === 'abc'

// 混合引入:
import str, { add, subtract } from './lib';

// 以包为命名空间引入:
import * as Lib from './lib';
```

---

&lt; [类与接口](classes-and-interfaces.md) | [x](x.md) &gt;
