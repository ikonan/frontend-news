# 译：2021年启动TypeScript项目

这是一份使用现代工具在 2021 年启动 TypeScript 项目的指南。
● TypeScript 4
● 可选使用 esbuild 为浏览器（和 Node.js）打包
● 使用 typescript-eslint 进行代码检查（tslint 已弃用）
● 使用 Jest（和 ts-jest）进行测试
● 发布包到 npm
● 持续集成（GitHub Actions / GitLab CI）
● 使用 TypeDoc 自动生成 API 文档
你可以使用示例仓库（而不是本指南中的手动设置）：
git clone https://github.com/metachris/typescript-boilerplate.git
基础项目设置
基础设置包括四个步骤：
1. 创建项目和源代码目录
2. 创建 package.json
3. 获取.gitignore、tsconfig.json、.eslintrc.js
4. 安装 TypeScript 和依赖项
注意：本指南使用 yarn，但如果你更喜欢 npm，它有类似的命令。
# 创建项目文件夹
mkdir my-project
cd my-project

# 创建源代码文件夹和文件
mkdir src
touch src/main.ts src/main.test.ts src/cli.ts

# 创建package.json
yarn init

# 安装TypeScript、代码检查器和Jest
yarn add -D typescript @types/node ts-node
yarn add -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
yarn add -D jest ts-jest @types/jest

# 获取.gitignore
wget https://raw.githubusercontent.com/metachris/typescript-boilerplate/master/.gitignore

# 获取带有一些默认值的tsconfig.json（根据需要调整）
wget https://raw.githubusercontent.com/metachris/typescript-boilerplate/master/tsconfig.json

# 或者你可以创建一个全新的tsconfig.json（带有详细的文档字符串）
tsc --init

# 获取.eslintrc.js
wget https://raw.githubusercontent.com/metachris/typescript-boilerplate/master/.eslintrc.js

# 获取jest.config.json，以便ts-jest无需单独的TypeScript编译步骤即可运行测试
wget https://raw.githubusercontent.com/metachris/typescript-boilerplate/master/jest.config.js

# 创建git仓库并进行第一次提交
git init
git add .
git commit -am "initial commit"
使用 src/cli.ts 编写从命令行运行的代码。这样，main.ts 中的代码可以被包含而无需运行入口点代码，并允许更轻松的跨目标构建和代码分支（例如 Node.js 和浏览器）。
在 package.json 中添加脚本：
{
  "scripts": {
    "cli": "ts-node src/cli.ts",
    "test": "jest",
    "lint": "eslint src/ --ext .js,.jsx,.ts,.tsx",
    "build": "tsc -p tsconfig.json",
    "clean": "rm -rf dist build",
    "ts-node": "ts-node"
  }
}
现在你可以运行 yarn cli、yarn test、yarn lint、yarn build 和 yarn ts-node <文件名>。
💡 在 Visual Studio Code 中，你可以使用构建和测试任务通过键盘快捷键启动脚本。在命令面板中选择"Configure Default Build Task"和"Configure Default Test Task"（请参阅 VS Code 文档）。
使用 Jest 进行测试
你可以这样编写 Jest 测试：
import { greet } from "./main";

test("the data is peanut butter", () => {
  expect(1).toBe(1);
});

test("greeting", () => {
  expect(greet("Foo")).toBe("Hello Foo");
});
使用 yarn test 运行测试，无需单独的编译步骤。
● 另请参阅 Jest 文档
● 查看其他现代测试运行器，如 ava、uvu 和 tape
esbuild
esbuild 是一个极其快速的 JavaScript 打包工具，
也可以编译大部分 TypeScript 代码。你可以使用它为 Node.js 和浏览器打包。esbuild 仍然相对年轻，正在积极开发中；另请参阅 GitHub 上的 esbuild。
为什么除了 tsc 之外还要使用 esbuild？TypeScript 编译器不能很好地为浏览器打包（开发人员通常会使用额外的打包工具，如 webpack、parcel 或 rollup），而且速度相当慢。
安装 esbuild：
yarn add -D esbuild
为 Node.js 打包
除了 tsc（TypeScript 编译器）之外，你还可以使用 esbuild 为 Node.js 打包代码，如下所示：
# 编译和打包
yarn esbuild src/cli.ts --bundle --platform=node --outfile=dist/esbuild/cli.js

# 同样，但进行压缩和生成源映射
yarn esbuild src/cli.ts --bundle --platform=node --minify --sourcemap=external --outfile=dist/esbuild/cli.js

# 运行打包后的输出
node dist/esbuild/cli.js
在 esbuild 文档中阅读更多关于 esbuild 选项的信息。
注意：
● 使用 esbuild 构建时，你可以使用--watch 选项在文件更改时重新构建
● esbuild 目前不支持构建.d.ts 声明文件（另请参阅此问题）。你需要使用 tsc 构建这些文件。
● 示例仓库在 package.json 中包含了 esbuild 命令作为脚本
构建浏览器兼容模块
你可以使用 esbuild、webpack、parcel 等打包工具生成浏览器兼容模块。
本指南使用 esbuild：
# 为浏览器打包
yarn esbuild src/browser.ts --bundle --outfile=dist/esbuild/browser.js

# 同样，但进行压缩和生成源映射
yarn esbuild src/browser.ts --bundle --minify --sourcemap=external --outfile=dist/esbuild/browser.js
browser.ts 中的代码在浏览器中加载后会被执行。
esbuild 有一个--global-name=xyz 标志，用于将入口点的导出存储在全局变量中。另请参阅 esbuild 的"Global name"文档。
访问 DOM 属性（window、document）
当代码在浏览器中加载时，你可以访问 window 和 document。你可能想使用它将代码的一部分附加到 window 对象上。
在 tsconfig.json 中，将 DOM 添加到库列表中：
"lib": ["ES6", "DOM"]
创建 src/browser.ts 作为浏览器构建的入口点。你可以在那里像这样将自定义属性附加到 window 上：
// 导入函数
import { greet } from './main'

// 使其在window对象上可访问
(window as any).greet = greet
现在使用 esbuild 打包：
yarn esbuild src/browser.ts --bundle --outfile=dist/esbuild/browser.js
使用这样的简单网站测试结果：browser-test.html
● 示例仓库在 package.json 中包含了 esbuild 命令作为脚本
● 如果你更喜欢使用 webpack，请查看这个 webpack.config.js 以获取灵感
● 与其将 window 转换为 any，你可能想正确扩展 Window 接口（请参阅此处）
发布到 npm
让我们将最新代码发布到 npm，以便在 Node.js 和浏览器中使用。
npm 和 yarn 会忽略.gitignore 中的文件。由于 dist 在其中，我们需要使用自定义的.npmignore 覆盖 npm 的忽略设置：
wget https://raw.githubusercontent.com/metachris/micropython-ctl/master/.npmignore
创建构建并运行 yarn publish：
# 使用tsc和esbuild构建
yarn build-all

# 更新版本并发布到npm
yarn publish
build-all 使用 tsc 构建项目以获取类型定义文件，并使用 esbuild 为 Node.js 和浏览器构建。
运行 yarn publish 后，项目/新版本将在 npm 上发布。🎉
例如，这个样板项目的 npm 包：
● https://www.npmjs.com/package/typescript-boilerplate-2021
从 Node.js 使用
你可以使用 npm 安装该模块：
npm install typescript-boilerplate-2021

# 或者使用yarn
yarn add typescript-boilerplate-2021
在自定义代码中使用该模块：
import { greet } from "typescript-boilerplate-2021";

greet("World");
从浏览器使用
有几个 CDN 会自动分发 npm 项目，如 jsDelivr、cdnjs、unpkg.com 或 skypack。
无需任何手动干预，你可以像这样访问 jsDelivr 上的包：
● https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021
● https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021/
● https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021@0.3.0
● https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021@0.3.0/package.json
● https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021@0.3.0/dist/esbuild/browser.js
你可以像这样从 HTML 引用打包文件：
<script src="https://cdn.jsdelivr.net/npm/typescript-boilerplate-2021@0.3.0"></script>

使用这样的简单网站测试结果：browser-test.html
持续集成
你可能希望在每次代码推送时运行测试和代码检查。此外，你可能还希望通过 CI 构建和部署文档。
GitHub Actions
请参阅 GitHub Actions 文档。创建文件.github/workflows/lint-and-test.yml：
name: Lint and test

on: [push, pull_request]

jobs:
  lint_and_test:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        nodejs: [10, 12, 14]

    steps:
      - uses: actions/checkout@v2

      # https://github.com/actions/setup-node
      - uses: actions/setup-node@v2-beta
        with:
          node-version: ${{ matrix.nodejs }}

      - run: yarn install
      - run: yarn test
      - run: yarn lint
      - run: yarn build-all
在多个操作系统中测试
如果你想在各种操作系统（Windows、Linux、macOS）上验证你的构建/包，你可以为作业设置这样的矩阵：
jobs:
  default-version:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [macos-latest, windows-latest, ubuntu-latest]

    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v2-beta
      with:
        node-version: 12
    ...
GitLab CI
请参阅 GitLab CI 文档。创建文件.gitlab-ci.yml：
image: node:12

cache:
  paths:
    - node_modules/

stages:
  - test

lint-and-test:
  stage: test
  script:
    - yarn install
    - yarn test
    - yarn lint
    - yarn build-all
使用 TypeDoc 生成 API 文档
你可以使用 TypeDoc 从 TypeScript 源文件自动生成 API 文档，TypeDoc 基于 JSDoc 语法构建。生成的文档可以通过 CI 发布到 GitHub/GitLab 页面。
● 安装 TypeDoc：yarn add -D typedoc
● 将 docs 脚本添加到 package.json："typedoc --entryPoints src/main.ts"
文档字符串如下所示：
/**
 * This comment _supports_ [Markdown](https://marked.js.org/)
 */
export class DocumentMe {}
使用 yarn docs 生成文档。生成的 HTML 保存在 docs/中。
你可以使用 CI 自动将文档发布到 GitHub 或 GitLab 页面：
● GitHub pages：.github/workflows/deploy-gh-pages.yml
● GitLab pages：.gitlab-ci.yml
例如，这是示例项目的文档：https://metachris.github.io/typescript-boilerplate/
