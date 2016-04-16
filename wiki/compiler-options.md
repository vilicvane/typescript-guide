# 编译选项

由 [vilicvane](https://github.com/vilic) 译自 [TypeScript Wiki](https://github.com/Microsoft/TypeScript-Handbook/blob/2e0403a535722521b100764615004ea468e9429b/pages/Compiler%20Options.md).

选项 | 简写 | 描述
-------|-----------|------------
`--declaration` | `-d` | 生成对应的 '.d.ts' 文件.
`--help` | `-h` | 打印帮助信息.
`--version` | `-v` | 打印编译器版本.
`--module` | `-m` | 指定模块代码生成: 'commonjs', 'amd', 'system', 'umd', 或者 'es2015'. 只有 'amd' 和 'system' 可以和 `--outFile` 一起使用. 当便以目标为 ES5 或者更低时不能使用值 'es2015'.
`--project` | `-p` | 编译给定目录中的项目. 该目录中需要有一个 `tsconfig.json` 文件用于直接编译. 参考 [tsconfig.json](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json) 文档了解更多.
`--target` | `-t` | 指定 ECMAScript 目标版本: 'ES3' (默认), 'ES5', or 'ES6'<sup>[1]</sup>
`--watch` | `-w` | 使用监听模式执行编译. 监视输入文件并在发生变化时触发重新编译.
`--charset` | | 输入文件的字符集.
`--diagnostics` | | 输出诊断信息.
`--emitBOM` | | 在输出文件的开头添加 UTF-8 字节顺序标记 (BOM).
`--emitDecoratorMetadata`<sup>[1]</sup> | | 为被源码中被装饰的声明输出设计类型元数据. 见 [issue #2577](https://github.com/Microsoft/TypeScript/issues/2577) 了解详情.
`--experimentalDecorators` | | 启用对 ES7 装饰器的试验性支持.
`--inlineSourceMap` | | 将 source map 包含在输出文件中, 而不输出单独的文件.
`--inlineSources` | | 将源代码和 source map 输出到同一个文件中. 需要 `--inlineSourceMap` 或 `--sourceMap` 被启用.
`--isolatedModules` | | 对于未解析的文件无条件输出导入项.
`--jsx` | | 支持 '.tsx' 文件中的 JSX: 'React' 或 'Preserve'. 参考 [JSX](https://github.com/Microsoft/TypeScript/wiki/JSX).
`--reactNamespace` | | 指定使用 'react' 作为 JSX 输出设置时用于调用 `createElement` 和 `__spread` 的对象.
`--listFiles` | | 输出编译时的文件部分名称.
`--locale` | | 错误信息的地域, 比如 en-us.
`--mapRoot` | | 指定调试器定位 map 文件的位置, 而不使用生成代码的位置. 当运行时 .map 文件和对应的 .js 文件不在同一位置时需要指定该位置. 指定的位置会被内嵌到 source map 中告诉调试器 map 文件所在的位置.
`--moduleResolution` | | 决定模块被解析的方式. 可以是用于 Node.js/io.js 解析风格风格的 'node', 或者 'classic' (默认).
`--newLine` | | 输出文件时使用指定的换行符: 'CRLF' (dos) 或 'LF' (unix).
`--noEmit` | | 不进行文件输出.
`--noEmitOnError` | | 在有任何错误时都不输出文件.
`--noEmitHelpers` | | 不在编译结果中生成自定义的像 `__extends` 的辅助函数.
`--noImplicitAny` | | 在表达式或者声明包含隐式的 'any' 类型是报错.
`--noLib` | | 不引入默认的库文件 (lib.d.ts).
`--noResolve` | | 在被编译的文件列表中不列出由三斜线引用的和导入的目标文件.
`--skipDefaultLibCheck` | | 跳过默认库文件检查.
`--out` | | 已弃用. 请使用 `--outFile` 代替.
`--outDir` | | 将文件输出到指定文件夹.
`--outFile` | | 将输出内容合并为单个文件. 合并的顺序由在命令行中传给编译器的列表与三斜线引用及导入决定. [查看输出文件顺序文档了解更多](https://github.com/Microsoft/TypeScript/wiki/FAQ#how-do-i-control-file-ordering-in-combined-output---out-).
`--preserveConstEnums` | | 在生成的代码中保留常量枚举声明. 查看[常量枚举文档](https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#94-constant-enum-declarations) 了解更多.
`--pretty`<sup>[1]</sup> | | 使用颜色和上下文丰富报错和信息.
`--removeComments` | | 移除除以 `/*!` 开头的版权头信息以外的所有注释.
`--rootDir` | | 指定输入文件的根目录. 仅用于和 `--outDir` 一起控制输出文件的目录结构.
`--sourceMap` | | 生成对应的 '.map' 文件.
`--sourceRoot` | | 指定 debugger 定位 TypeScript 文件的位置 (而不是源文件地址). 当源文件在运行时与设计时在不同位置时可以使用此选项. 指定的位置将被嵌入 source map 来告知 debugger 去哪里寻找这些文件.
`--stripInternal`<sup>[1]</sup> | | 不输出有 `/** @internal */` JSDoc 标注代码的声明.
`--suppressExcessPropertyErrors`<sup>[1]</sup> | | 不检查对象字面量多余的属性.
`--suppressImplicitAnyIndexErrors` | | 忽略没有索引签名对象的 `--noImplicitAny` 错误. 见 [issue #1232](https://github.com/Microsoft/TypeScript/issues/1232#issuecomment-64510362) 了解更多.
`--allowUnusedLabels` | | 不对未使用的标签报错.
`--noImplicitReturns` | | 当函数中有代码路径没有返回值时报错.
`--noFallthroughCasesInSwitch` | | 对 switch 语句中贯穿的 case 报错.
`--allowUnreachableCode` | | 不对无法到达的语句报错.
`--forceConsistentCasingInFileNames` | | 禁止对同一个文件大小写不一致的引用.
`--allowSyntheticDefaultImports` | | 允许对没有默认输出的模块进行默认导入. 这不会影响代码输出, 仅用于进行类型检查.
`--allowJs` | | 允许编译 JavaScript 文件.
`--noImplicitUseStrict` | | 在输出的模块中不增加 `"use strict"` 指令.

<sup>[1]</sup> 这些选项是试验性的.

## 相关信息
 - 了解 tsconfig.json 见 [tsconfig.json](https://github.com/Microsoft/TypeScript/wiki/tsconfig.json)
 - 了解在 MSBuild 中设置编译器选项见 [Setting Compiler Options in MSBuild projects](https://github.com/Microsoft/TypeScript/wiki/Setting-Compiler-Options-in-MSBuild-projects)
