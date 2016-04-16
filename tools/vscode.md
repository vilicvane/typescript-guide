# Visual Studio Code TypeScript 指南

Visual Studio Code (以下简称 VSCode) 是一款的免费, 开源, 跨平台的开发工具. 定位是轻量级的 IDE, 以键盘操作为中心, 使用 JSON 作为配置文件格式.
VSCode 内置 TypeScript 支持, 并且通过 TypeScript Salsa 为 JavaScript 开发也提供了强大的体验.

访问 https://code.visualstudio.com 下载安装最新版本.

## 快速上手

### 安装编译器

1. 安装 [Node.js](https://nodejs.org).
2. 通过 Node.js 自带的 `npm` 包管理工具安装 TypeScript 编译器 `tsc` (包名为 `typescript`) 及 TypeScript 声明管理工具 `tsd`.

    ```shell
    npm install --global typescript
    npm install --global tsd
    ```

> 未来 `tsd` 将会被新的工具 [typings](https://github.com/typings/typings) 替代, 但就目前来讲 `typings` 并不算好用.

### 新建 TypeScript 项目

1. 新建项目文件夹 `ts-test` 并在 VSCode 中打开.
2. 在文件夹根目录新建文件 `tsconfig.json`, 键入 `compilerOptions` 后, VSCode 会自动提示一个代码片段, 按 `Tab` 键将插入如下配置:

    ```json
    {
        "compilerOptions": {
            "module": "commonjs"
        },
        "exclude": [
            "node_modules"
        ]
    }
    ```

   在该配置模板的基础上, 在 `compilerOptions` 中新增一项 `target`, 值为 `"es5"`.
3. 新建文件 `test.ts`, 输入以下内容并保存:

    ```ts
    function test(str: string): void {
        console.log(str);
    }

    test('Hello, VSCode!');
    ```

4. 按下 `F1` 或 `Ctrl/Cmd+Shift+P`, 输入 `build`, 选中 "Tasks: Run Build Task". VSCode 将会提示 "No task runner configured", 点击 "Configure Task Runner" 进行配置. VSCode 会给出一个任务模板列表, 其中有两项关于 TypeScript 构建的配置, 一项为普通编译, 一项为增量编译 (监视模式). 比如我们可以选择增量编译的模板, VS Code 会生成如下 `tasks.json` 文件:

    ```json
    {
        "version": "0.1.0",
        "command": "tsc",
        "isShellCommand": true,
        "args": ["-w", "-p", "."],
        "showOutput": "silent",
        "isWatching": true,
        "problemMatcher": "$tsc-watch"
    }
    ```

5. 再次按上一步的方法再次执行构建任务, 或者也可以使用快捷键 `Ctrl/Cmd+Shift+B`. 如果一切顺利, 编译完成后项目目录下回出现编译后的 `test.js` 文件:

    ```js
    function test(str) {
        console.log(str);
    }
    test('Hello, VSCode!');
    ```

这样一个简单的 TypeScript 项目流程就搞定了, 在命令行中输入 `node test.js` 即可执行编译后的 JavaScript 文件. 如果配置构建任务时启用了监视模式, 当源文件改动时, TypeScript 编译器会自动进行增量编译. 在某些情况下 (如删除了某个 .ts 文件), VS Code 会自动重启任务, 使项目能正常编译.

### 项目结构

## 推荐配置

