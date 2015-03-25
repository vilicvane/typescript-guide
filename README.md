# TypeScript 指南

随着 Web 应用的项目规模逐步扩大, JavaScript 需要更强大的工具 - 类型系统 - 来满足更庞大的开发需求, TypeScript 应运而生.

### 更大的规模

TypeScript 提供了类, 模块以及接口来帮助我们创建健壮的组件. 这些特性在开发时能提高代码的可靠性, 而最终会编译为单纯的 JavaScript. TypeScript 让我们可以在应用模块间定义接口, 也可以方便地查看现有 JavaScript 类库的信息.

### 始于 JavaScript, 终于 JavaScript

TypeScript 始于数百万 JavaScript 开发者如今就熟知的语法和语义. 它可以使用现有丰富的 JavaScript 类库, 也可以被其他 JavaScript 代码使用.

TypeScript 会编译为可读, 简洁的 JavaScript. 这使得 TypeScript 可以被应用到任何浏览器, Node.js, 或者任何兼容 ES3 的环境中.

### 为大型应用准备的强大工具

类型系统让 TypeScript 开发者得以使用高效的开发工具和实践: 静态类型检查, 基于符号的导航, 自动完成, 以及代码重构.

这些类型信息是可选的, 类型推断机制使得很少的标注就能为静态类型检查提供很大的帮助.

## 指南引索

指南从 [快速上手 TypeScript](入门指南/快速上手.md) 开始, 根据不同用户的接受程度会在其中给出不同的阅读建议.

- 入门指南
 - [快速上手 TypeScript](入门指南/快速上手.md)
 - [TypeScript 特性举例](入门指南/特性举例.md)
 - [在 TypeScript 中使用 JavaScript 类库](入门指南/使用JS类库.md)
 - [开发环境](入门指南/开发环境.md)
- 最佳实践
- 相关资源

## 是否应该使用 TypeScript?

关注 TypeScript 的同学里, 有一部分是对 Web 开发感兴趣, 但并没有 JavaScript 编程经验, 甚至没有其他语言编程经验的同学. 我并不建议这部分同学现在就使用 TypeScript, 如果是团队中打算使用 TypeScript, 个人建议应至少有一个精通 JavaScript 的成员.

还有一部分是对 Web 前端感兴趣的后端工程师, 尤其是使用 C# 的工程师, 但即便拥有较多的编程经验, 依旧建议先熟悉原生 JavaScript, 再进行 TypeScript 的学习和开发.

我认为 TypeScript 并不能单纯地作为一门新语言来理解. **TypeScript 是 JavaScript 的超集, 而其中的 "Type" 是 TypeScript 为 JavaScript 打造的工具.** 要用好这一门工具, 必须先理解这门工具服务的语言.