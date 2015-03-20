# 在 TypeScript 中使用 JavaScript 类库

TypeScript 设计的目标之一就是重用现有的 JavaScript 类库, 而它实现这一点则是通过 *.d.ts 声明文件, 为原有 JS 文件和现有 TS 项目搭桥. (除此之外, 我个人也经常使用 TS 的相关语法为 JS 库写简单的文档.)

目前在项目 [DefinitelyTyped](http://definitelytyped.org/) 中, 已经有由大家提供的常用类库的 TypeScript 声明文件, 我们可以使用 Visual Studio 的 NuGet 包管理工具或者 [tsd](http://definitelytyped.org/tsd/) 命令行工具轻松获取.

在 Visual Studio 中, 可以在右上搜索框中直接键入 "[包名].typescript", 如 "jquery.typescript", 在候选框中选择搜索 NuGet 包, 安装对应的包即可. 但是 NTVS (Node.js Tools for Visual Studio) 新建的 NodeJS 项目还无法安装这些包, 可以使用 tsd 命令行工具进行管理.

## Visual Studio NuGet 添加声明文件注意事项

1. Visual Studio 安装 NuGet 包后, 声明文件不一定会马上生效. 这时可以在解决方案管理器里对应的项目下, 切换 "Show All Files" 的状态, 以使声明生效.
2. Visual Studio 中是以 TS 文件是否在项目中为编译的依据, 而非 TS 文件是否在项目目录中. 也就是说, "Show All Files" 关闭时看不到的 TS 文件是不会被编译的. 同样, 这些文件中的声明文件也不会起作用 (被引用的除外).
3. 如果使用 tsd 来管理声明文件, 注意将 `typings/tsd.d.ts` 文件加入项目.