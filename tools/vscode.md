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

4. 按下 `F1` 或 `Ctrl/Cmd+Shift+P`, 输入 `build`, 选中 "Tasks: Run Build Task". VSCode 将会提示 "No task runner configured", 点击 "Configure Task Runner" 进行配置. VSCode 打开自动生成的 `.vscode/tasks.json` 文件, 以便我们进行修改. 接下来将其替换为以下配置并保存:

    ```json
    {
        "version": "0.1.0",
        "command": "tsc",
        "isShellCommand": true,
        "showOutput": "silent",
        "args": [],
        "problemMatcher": "$tsc"
    }
    ```

5. 再次按上一步的方法执行构建任务, 或者也可以使用快捷键 `Ctrl/Cmd+Shift+B`. 如果一切顺利, 编译完成后项目目录下回出现编译后的 `test.js` 文件:

    ```js
    function test(str) {
        console.log(str);
    }
    test('Hello, VSCode!');
    ```

这样一个简单的 TypeScript 项目流程就搞定了, 在命令行中输入 `node test.js` 即可执行编译后的 JavaScript 文件.

### 使用增量编译

通常我会习惯在开发中开启增量编译, 当源文件改动时自动编译. 要为默认构建任务开启增量编译:

1. 在 `task.json` 中 `args` 数组里添加一项 `"-w"` (`"--watch"`).
2. 增加一项 `isWatching` 值为 `true`.
3. 将 `problemMatcher` 一项改为 `"$tsc-watch"`.

```json
{
    "version": "0.1.0",
    "command": "tsc",
    "isShellCommand": true,
    "isWatching": true,
    "showOutput": "silent",
    "args": ["-w"],
    "problemMatcher": "$tsc-watch"
}
```

另外建议同时将 `tsconfig.json` 的 `compilerOptions` 中配置 `noEmitOnError` 为 `true`.

```json
{
    "compilerOptions": {
        "module": "commonjs",
        "noEmitOnError": true
    },
    "exclude": [
        "node_modules"
    ]
}
```

### 项目结构

## 推荐配置

