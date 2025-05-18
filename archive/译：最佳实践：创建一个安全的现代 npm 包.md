# 最佳实践：创建一个安全的现代 npm 包

技术在不断进步，创建 npm 包的方法也得跟上时代。即使 npm 到 2025 年就有 15 年历史了，但创建 npm 包的方式应该更现代化。要是你觉得自己的方法有点过时，就接着往下看吧。

在这个教程里，我们会一步一步教你用 2025 年的现代最佳实践来创建 npm 包。首先，你会学会怎么创建 npm 包，熟悉把包构建并发布到 npm 注册表的流程。然后，你会知道如何通过设置测试框架、持续集成和部署流水线、安全检查以及自动化的语义版本管理，来打造更可靠、能用于生产环境的 npm 包。学完这个教程，你就能自信地创建现代化且容易维护的 npm 包啦。咱们开始吧！

## 先决条件

1. 了解 Node.js、JavaScript/TypeScript、GitHub 和 GitHub Actions。
2. 有创建 npm 包的开发工具。

## 简单示例 npm 包

先通过一个简单的例子，熟悉创建和发布 npm 包的流程。要是你已经熟悉这些内容，就可以直接跳到[生产就绪的 npm 包](https://snyk.io/blog/best-practices-create-modern-npm-package/#npm-package)部分，这里面有更高级的主题。

### 设置你的项目

你得有个 GitHub 项目才能开始，按下面的步骤来初始化项目。要是你已经有可用的项目，可以跳过下一部分，但一定要仔细看看这部分的第 5 步，关于你的包名称的内容。

- 在 [https://github.com/new](https://github.com/new) 创建一个 GitHub 仓库。
- 把仓库克隆到本地。
  **示例**：`git clone` [https://github.com/snyk-snippets/simple-npm-package.git](https://github.com/snyk-snippets/simple-npm-package.git)
- 打开终端，切换到克隆项目的文件夹。
  **示例**：`cd simple-npm-package`
- 运行 `npm init -y` 来创建 `package.json` 文件。**注意**：如果你克隆了示例仓库，就不用执行这一步。
- 用作用域名称更新 `package.json` 里的 `name` 属性。
  **示例**：`@clarkio/simple-npm-package`。记得用你自己的用户名或组织名称替换 `@clarkio`。
- 为包编写代码（或者直接用 index.js 里的 hello world 示例）。

项目创建好后，就可以接着创建 npm 账户了。

### 设置 npm 账户

为了让别人能用你的 npm 包，你得有个 npm 账户。下面的步骤会教你创建账户（要是你还没有的话），开启双重身份验证（2FA）来提高账户安全性，再把账户连接到本地机器。

1. 在 [https://www.npmjs.com/signup](https://www.npmjs.com/signup) 注册 npm 账户。
2. 为了更安全，在 npm 账户上开启 2FA：[https://docs.npmjs.com/configuring-two-factor-authentication](https://docs.npmjs.com/configuring-two-factor-authentication)。
3. 在终端用 `npm login` 命令登录你的 npm 账户，然后按屏幕上的提示操作。例如：

```javascript
> npm login
npm notice Log in on https://registry.npmjs.org/
Username: clarkio
Password:
Email: (this IS public) <email address>
npm notice Please use the one-time password (OTP) from your authenticator application
Enter one-time password from our authenticator app: <OTP>
Logged in as clarkio on https://registry.npmjs.org/.
```

### 如何发布你的 npm 包

你有了 npm 项目，也注册了 npm 账户，就可以把包发布到 [官方 npm 公共注册表](https://npmjs.org/) 了。下面是发布前检查和正式发布的完整流程：

1\. 在终端运行 `npx pack --dry-run` 预览要发布的包内容：

```bash
> npx pack --dry-run
npm notice Tarball Contents
npm notice 1.1kB LICENSE
npm notice 1.9kB README.md
npm notice 108B index.js
npm notice 700B package.json
npm notice Tarball Details
```

这样能保证你没有漏掉包正常运行需要的源代码文件。另外，养成习惯，别不小心把敏感信息（像包含数据库凭证或 API 密钥的本地配置文件）泄露给公众。

2\. 在终端运行 `npm publish --dry-run` 命令，看看实际执行这个命令会怎样。

```bash
> npm publish --dry-run
npm notice
npm notice 📦@clarkio/simple-npm-package@0.0.1
npm notice === Tarball Contents ===
npm notice 1.1kB LICENSE
npm notice 1.2kB README.md
npm notice 95B index.js
npm notice 690B package.json
npm notice === Tarball Details===
npm notice name: @clarkio/simple-npm-package
npm notice version: 0.0.1
npm notice filename:@clarkio/simple-npm-package-0.0.1.tgz
npm notice package size:1.7 kB
npm notice unpacked size: 3.1 kB
npm notice shasum:40ede3ed630fa8857c0c9b8d4c81664374aa811c
npm notice integrity:sha512-QZCyWZTspkcUXL... ]L60ZKBOOBRLTg==
npm notice total files:4
npm notice
+ @clarkio/simple-npm-package@0.0.1
```

3\. 在终端运行 `npm publish --access=public` 把包实际发布到 npm。**注意**：作用域包（像 @clarkio/modern-npm-package）默认是私有的，所以需要 `--access=public` 选项。如果包没有作用域，而且 `package.json` 里的 `private` 字段没设置成 `true`，那这个包就是公开的。

```bash
> npm publish --access=public
npm notice
npm notice 📦@clarkio/simple-npm-package@0.0.1
npm notice === Tarball Contents ===
npm notice 1.1kB LICENSE
npm notice 1.2kB README.md
npm notice 95B index.js
npm notice 690B package.json
npm notice === Tarball Details===
npm notice name: @clarkio/simple-npm-package
npm notice version: 0.0.1
npm notice filename:@clarkio/simple-npm-package-0.0.1.tgz
npm notice package size:2.1 kB
npm notice unpacked size: 4.1 kB
npm notice shasum:6f335d6254ebb77a5a24ee729650052a69994594
npm notice integrity:sha512-VZ1K1eMFOKeJW[...]7ZjKFVAxLcpdQ==
npm notice total files:4
npm notice
This operation requires a one-time password.
Enter OTP: <OTP>
+ @clarkio/simple-npm-package@0.0.1
```

你已经完成啦！你已经构建并部署了自己的 npm 包。接下来，你会学习怎么创建一个更可靠、能用于生产环境、能被更多人使用的包。

## 生产就绪的 npm 包

前面的示例包有可能在生产环境用，但随着时间推移，需要手动维护。用工具和自动化，再加上合适的测试和安全检查，能减少让包平稳运行的总体工作量。咱们来看看这都涉及什么。

下面的部分会涵盖：

1. 设置你的 `modern-npm-package` 项目。
2. 为 ECMAScript (ESM) 模块格式进行构建。
3. 设置并编写单元测试。
4. 实施安全检查。
5. 自动化版本管理和发布。

要是你看这篇文章的时候没有自己的项目可以用，可以参考这个示例项目：[https://github.com/snyk-snippets/modern-npm-package](https://github.com/snyk-snippets/modern-npm-package)。

### 为 ECMAScript 模块格式进行构建

从 Node.js 12 版本及更高版本开始，ECMAScript 模块格式就得到原生支持了，最新的长期支持版本是 22.x。而且，JavaScript 运行时领域有了更多竞争对手，像 Bun.js 和 Deno，这让现在专注于用 ESM 格式变得更容易。我们会用 TypeScript 让你的 npm 包支持 ESM 格式。

- 首先，创建一个叫 `tsconfig.json` 的 TypeScript 配置文件。这是在使用 ESM 构建你的包时可以用的编译设置。你可以根据项目需求调整这个文件，特别是如果你不用提供的示例项目结构，就得调整 `files` 属性，让它和你的项目结构一致。

```json
{
  "compilerOptions": {
    "lib": ["ES2024", "DOM"],
    "target": "ES2024",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./lib/",
    "declarationDir": "./lib/types",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "checkJs": true,
    "allowJs": true,
    "declaration": true,
    "declarationMap": true,
    "allowSyntheticDefaultImports": true
  },
  "files": ["./src/index.ts"]
}
```

- `lib` 属性告诉 TypeScript 在为项目写代码时该引用哪些类型来提供帮助。
- `target` 属性告诉 TypeScript 该把项目代码编译成哪个 JavaScript 版本。
- `module` 属性告诉 TypeScript 在编译项目代码时该用哪种 JavaScript 模块格式。
- `moduleResolution` 属性帮助 TypeScript 确定怎么引用 “import” 语句。
- `outDir` 和 `declarationDir` 属性告诉 TypeScript 该把编译代码的结果和定义其中使用的类型的文件放在哪儿。

2\. 更新 `package.json` 文件，添加一个 `files` 字段，指向 `lib` 文件夹，这里面包含 TypeScript 为你构建包的结果。
3\. 更新 `package.json` 文件里的 `main` 和 `types` 字段，让它们指向构建/编译后的包的输出。这是默认的备用选项。

```javascript
"types": "./lib/index.d.ts",
"main": "./lib/index.js",
```

4\. 在 `package.json` 文件里添加一个 `files` 字段，指明在 npm 打包你的代码进行发布时该包含哪些文件。

```javascript
"files": [
  "lib/**/*"
],
```

5\. 通过 `package.json` 里的 `scripts` 字段创建命令，用 `tsc` 编译包。这会生成 `lib` 文件夹的源文件。

```javascript
"clean": "del-cli ./lib",
"build": "npm run clean && tsc -p ./tsconfig.json",
"prepack": "npm run build",
```

- `clean` 脚本用来删除之前构建生成的输出文件，这样就能从头开始构建。
- `build` 脚本会清理之前的输出文件，用 TypeScript 编译器把包构建到输出目录。
- `prepack` 脚本由 npm 在打包 npm 包准备发布到注册表之前使用。

6\. 现在你得安装让这些功能正常工作所需的开发依赖项。运行 `npm install -D typescript del-cli`。
7\. 现在你可以在终端运行 `npm run build`，让 TypeScript 构建你的项目，为使用和发布做准备。

这就是用 TypeScript 构建支持 CommonJS 和 ECMAScript 模块格式的 npm 包需要的所有设置。接下来，你会学习怎么针对 npm 包代码设置并运行测试，确保它能产生你想要的结果。

### 设置并添加测试

为了对代码的行为和结果有信心，你得实施一个测试流程。测试会让你在初次写代码时，用和正常顺利路径不一样的方式思考代码的功能。比如，你可以想想怎么让一个函数出错，抛出错误或者产生意外的结果。这样能让你的应用程序更有弹性、更可持续，还能确保添加更多功能时不会出问题。

要是你想深入了解测试，学习最佳实践，一定要看看 Yoni Goldberg 的 [JavaScript 最佳实践仓库](https://github.com/goldbergyoni/javascript-testing-best-practices)。

### 单元测试

要确保你的包按你期望的方式运行，就得针对代码编写测试。你需要一些工具来帮你设置项目，运行单元测试并显示结果。这些工具现在作为 Node.js 的内置模块提供（[自 20.x、18.x 版本以及 16.17.x 版本在实验标志后可用](https://nodejs.org/docs/latest-v22.x/api/test.html#test-runner)）。按下面的步骤为你的 npm 包设置并运行测试：

1. 在终端用下面的命令安装开发依赖项：`npm i -D @types/node`
2. 在项目的根目录下创建一个 `tests` 文件夹。
3. 在 `tests` 文件夹里创建一个 `index.test.ts` 文件。
4. 在 `index.test.ts` 文件里编写单元测试，测试 `index.ts` 里的代码。
   **注意**：你可以参考示例 npm 包仓库作为示例：[https://github.com/snyk-snippets/modern-npm-package](https://github.com/snyk-snippets/modern-npm-package)。
5. 在 `package.json` 文件的 `scripts` 部分添加一个 `test` 属性，把它的值设置为 `node --experimental-strip-types --test`

```json
{
  "scripts": {
    "clean": "del-cli ./lib",
    "build": "npm run clean && tsc -p ./tsconfig.json",
    "prepack": "npm run build",
    "test": "node --experimental-strip-types --test"
  }
}
```

6\. 在项目的根目录下的终端中运行 `npm test` 来执行测试，查看结果：

```bash
> @snyk-labs/modern-npm-package@0.0.0-development test
> node --experimental-strip-types --test

(node:83429) ExperimentalWarning: Type Stripping is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
(node:83430) ExperimentalWarning: Type Stripping is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
▶ NPM Package
 ✔ should be an object (0.754083ms)
 ✔ should have a helloWorld property (0.518958ms)
 ✔ NPM Package (1.754875ms)
▶ Hello World Function
 ✔ should be a function (0.136667ms)
 ✔ should return the hello world message (0.063791ms)
✔ Hello World Function (0.270417ms)
▶ Goodbye Function
 ✔ should be a function (0.13275ms)
 ✔ should return the goodbye message (0.159541ms)
 ✔ Goodbye Function (0.552542ms)
ℹ tests 6
ℹ suites 3
ℹ pass 6
ℹ fail 0
ℹ cancelled 0
ℹ skipped 0
ℹ todo 0
ℹ duration_ms 113.692542
```

### 在流水线中进行测试

你已经有了验证代码行为的测试，就可以在流水线里用它们了。这能确保推送到仓库里的任何更改都不会破坏代码的行为。按下面的步骤在项目流水线里创建一个测试工作流。

1. 为你的仓库创建一个新的 GitHub Action：`https://github.com/<你的账户或组织>/<你的仓库名称>/actions/new`
2. 把工作流重命名为 `tests.yml`
3. 在你的工作流文件里插入下面的 Snyk GitHub Action 脚本：

```yml
name: Tests

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [22.x]

    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

这个 YAML 脚本会检出你最新的代码，安装依赖项，运行 `npm test` 命令来执行测试。它会针对 `node-version` 字段里列出的每个 Node.js 版本执行这些操作，这样你就能确保代码在每个运行时里都能按预期工作。

你现在已经完成了对 npm 包代码进行测试运行和评估的项目设置。不过，你可能会想：“我怎么在另一个项目里 _使用_ 我的 npm 包进行测试呢？” 接下来咱们看看怎么实现。

### 包测试

通过单元测试对 npm 包的代码有信心是一回事，但确保整个 npm 包的使用体验又是另一回事。这要把你的 npm 包作为依赖项引入到另一个项目里，看看它在这个项目里的使用是否如你预期的那样顺利。下面是五种可以进行测试的方法：

1. 通过 `npm pack` 输出进行安装。
2. 通过相对路径进行安装。
3. 通过 `npm link` 进行安装。
4. 通过注册表（例如 [npmjs.com](https://npmjs.com/) 上的 npm 公共注册表）进行安装。
5. 使用 [Verdaccio](https://verdaccio.org/)（一个开源的 npm 私有注册表项目）在持续集成（CI）中运行端到端的包发布和安装步骤。

#### npm pack

这种方法用 `npm pack` 命令把你的 npm 包打包并压缩成一个单独的文件（`<包名>.tgz`）。然后你可以进入想用这个包的项目，通过这个文件来安装它。具体步骤如下：

1. 在你的 npm 包目录下，在终端中运行 `npm pack` 命令。注意它生成的 `.tgz` 文件及其位置。
2. 切换到你想用 npm 包的项目目录。**示例**：`cd /path/to/project`
3. 在 **客户端** 项目目录中，运行 `npm install /path/to/package.tgz`，但要把 `/path/to/package.tgz` 替换成步骤 1 里 `.tgz` 文件的正确路径。
4. 然后你就可以在这个 **客户端** 项目里开始用这个包进行测试了。

这能让你体验到最接近生产环境的 npm 包使用情况。

#### npm link

这种方法用 `npm link` 命令，在尝试把你的包安装到 **客户端** 项目里时，指向你的包目录。具体步骤如下：

1. 在你的 npm 包目录下的终端中运行 `npm link`。
2. 切换到你想用这个 npm 包的项目目录。**示例**：`cd /path/to/project`
3. 在 **客户端** 项目目录下运行 `npm link <你的包名>`。

这会在你的代码引用这个包时，把 **客户端** 项目指向 npm 包目录。这种方法不会让你体验到完全像生产环境那样使用包，但能确保包的功能按预期工作。

#### 相对路径

这种方法利用了你现有的 `npm install` 命令使用知识。它和 `npm link` 类似，但不用学像 `link` 这样的新命令。

1. 在你的 **客户端** 项目目录下的终端中运行 `npm install /path/to/your/package`。

和 `npm link` 方法类似，这能让你在客户端项目里快速测试包的功能，但不能提供完全像生产环境那样的体验。这是因为它指向的是完整的包源代码目录，而 **不是** 你在 npm 注册表里能找到的打包版本。

#### npm 注册表

这种方法利用了 npm 包的公共（或你自己的）注册表。它要发布你的包，然后像安装其他任何 npm 包一样进行安装。

1. 用本文前面说的步骤，通过 `npm publish` 命令发布你的 npm 包。
2. 切换到你想用这个 npm 包的项目目录。**示例**：`cd /path/to/project`
3. 在 **客户端** 项目目录中运行 `npm install <name-of-your-package>`。

此时，你已经构建了支持现代模块格式的包，并通过单元测试和打包测试确保它能按预期运行。接下来，你需要确保 npm 包中不存在安全问题，并防止引入新的安全问题。

## 实施安全检查

就像你不希望自己的项目有安全漏洞一样，你也不希望把漏洞带到别人的项目里。构建一个会在很多其他项目里使用的 npm 包，意味着你要承担更大的责任，确保一切都是安全的。你需要设置安全检查，帮助监控漏洞、发出警报，还能协助缓解漏洞。像 [Snyk](https://snyk.io/product/) 这样的工具就能简化实现这些需求所需的工作。

对于这个示例 npm 包，你用 GitHub 作为源代码管理工具，所以可以利用它的 GitHub Actions 功能，把 Snyk 集成到你的工作流程中。Snyk 有一个 [GitHub Actions](https://github.com/snyk/actions) 参考项目，能帮你快速开始这项工作，还能为你在项目里可能用到的其他编程语言和工具提供示例。

1\. Snyk 是免费的，所以去注册并获取你的 [Snyk API 令牌](https://app.snyk.io/account)。
2\. 在 GitHub 上把你的 Snyk API 令牌添加为仓库密钥：`https://github.com/<你的账户或组织>/<你的仓库名称>/settings/secrets/actions/new`
3\. 为你的仓库创建一个新的 GitHub Action：`https://github.com/<你的账户或组织>/<你的仓库名称>/actions/new`
4\. 把工作流重命名为 `snyk.yml`。
5\. 在你的工作流文件里插入下面的 Snyk Action 脚本：

```yml
name: Snyk Security Check
on: [push, pull_request]
jobs:
  security:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@main
    - name: Run Snyk to check for vulnerabilities
  uses: snyk/actions/node@master
  env:
  SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
```

6\. 提交你的更改。
7\. 验证操作是否成功运行：`https://github.com/<你的账户或组织>/<你的仓库名称>/actions`

设置好后，**每当有人向你的仓库推送代码或打开拉取请求时，都会运行安全检查**，确保不会往包里引入任何漏洞。如果发现问题，操作会失败，还会向你发出找到的安全问题的详细信息警报。接下来，你要自动化 npm 包的版本管理和发布过程。

你是不是想在更改推送到仓库之前就了解安全问题？[为你选择的开发工具安装 Snyk 插件](https://snyk.io/platform/ide-plugins/)。你更喜欢用命令行工具吗？[也把 Snyk 命令行工具添加到你的工具链中](https://snyk.io/platform/snyk-cli/)。这些工具能帮助你在开发过程中检测安全问题，在项目工作流程的早期向你发出警报。

关于当前设置要注意的是，它只用了 [Snyk 开源](https://snyk.io/product/open-source-security-management/)（SCA）产品，没使用 [Snyk 代码](https://snyk.io/product/snyk-code/)（SAST）。Snyk 代码是我们的代码安全产品，你得先通过 Snyk 账户免费启用它，然后把它添加到这里的工作流脚本中，才能充分利用它。要了解更多关于在管道里使用 Snyk 代码的信息，请阅读这篇关于 [使用 GitHub Actions 构建安全管道](https://snyk.io/blog/building-a-secure-pipeline-with-github-actions/) 的文章（这篇文章用 Java 和 Maven，但可以换成 Node.js 和 npm）。

## 自动化版本管理和发布

每当你把更改合并到主分支时，你可能不想每次都手动更新 npm 包的版本并发布它。相反，你会希望把这个过程自动化。要是你还记得本文前面简单 npm 包的示例，你用下面的命令来发布 npm 包：

你还希望遵循语义化版本控制的行业标准，这样你的包的使用者就能理解你发布到注册表的不同版本变更会带来什么影响。

### 什么是语义化版本控制？

语义化版本控制规定版本号由三个占位符组成。第一个是主版本号，第二个是次版本号，最后一个是补丁版本号。要了解更多关于语义化版本控制、版本管理和锁文件的信息，请阅读[什么是 Package Lock JSON 以及锁文件如何与 Yarn 和 NPM 包一起工作](https://snyk.io/blog/what-is-package-lock-json/)。

要是你能跳过所有手动操作，用 GitHub Actions 设置一个自动化工作流，让它帮你处理 npm 包的发布，会怎么样呢？你很幸运，因为有个叫 [Semantic Release](https://semantic-release.gitbook.io/semantic-release/) 的工具，它能和 [GitHub Actions](https://semantic-release.gitbook.io/semantic-release/recipes/ci-configurations/github-actions) 集成。帮助自动化这个过程的关键是，在向项目提交更改时使用所谓的 [常规提交](https://www.conventionalcommits.org/en/v1.0.0/)。这能让自动化相应地更新所有内容，知道怎么为你准备项目的下一个版本发布。

下面的步骤会指导你为现代 npm 包设置这个功能。

1\. 在终端中运行：`npm i -D semantic-release`
2\. 在终端中运行：`npx semantic-release-cli setup`
3\. 按终端提示，提供所需的令牌：

- 你需要一个来自 GitHub 的个人访问令牌。要创建一个令牌，请访问 `https://github.com/settings/tokens/new?scopes=public_repo`
- 创建此令牌时，请使用以下权限范围：
  ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712555/wordpress-sync/blog-create-npm-packages-new-token.png)
- 点击 “生成令牌” 按钮，复制并保存页面上显示的值。
- 你还需要一个来自 npm 的 **自动化类型** 访问令牌，仅用于 CI 环境，这样它就能绕过你账户的双因素身份验证（2FA）。要创建一个这样的令牌，请访问 `https://www.npmjs.com/settings/<your-npm-account>/tokens`。请确保选择 “Automation” 类型，因为这将在 CI/CD 工作流中使用。
  ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712548/wordpress-sync/blog-create-npm-packages-token.png)

```bash
bc@mbp-snyk modern-npm-package % npx semantic-release-cli setup
? What is your npm registry? https://registry.npmjs.org/
? What is vour nom username? clarkio
? What is your pm password? [hidden]
? What is your NPM two-factor authentication code? <2FA code>
Provide a GitHub Personal Access Token (create a token at https://github.com/settings/tokens/new?scopes=repo <token>
? What CI are you using? Github Actions
bc@mbp-snyk modern-npm-package %
```

4\. 在这里把你的 npm 令牌作为仓库密钥添加到 GitHub 仓库中：`https://github.com/<你的名称或组织>/<你的仓库>/settings/secrets/actions/new`。把密钥名称设置为 `NPM_TOKEN`，值为你在前面步骤中获取的令牌。
   ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712560/wordpress-sync/blog-create-npm-packages-secret.png)
5\. 回到你的项目，打开 `package.json` 文件，添加如下所示的 `releases` 键。如果你的仓库主分支仍然名为 `master` 而不是 `main`，就相应地更新上述 `branches` 的值。

```javascript
"release": {
  "branches": ["main"]
}
```

6\. 同时在 `package.json` 文件中添加一个 `publishConfig` 键：

```javascript
"publishConfig": {
  "access": "public"
}
```

7\. 用 `semantic-release` npm 脚本进行试运行，测试所有内容。获取下面的命令，把 `NPM_TOKEN=` 和 `GH_TOKEN=` 的值设置为你各自的令牌值。然后复制完整命令，在终端中运行，看看一切是否正常运行。你会在终端输出中看到这个过程的日志。如果出现任何问题，这里会显示相关信息，还会提供解决问题的详细说明。
8\. 确认试运行成功完成后，你可以为 GitHub 仓库设置一个新的 GitHub Action，让它帮你处理发布过程。前往 GitHub 上的仓库，点击 “Actions”。
9\. 点击 **新建工作流** 选项。
10\. 把工作流重命名为 `release.yml`。
11\. 把下面的 YAML 脚本添加到新的工作流文件中。这个脚本本质上是说，一旦 Snyk 安全检查工作成功完成，就运行发布作业。发布作业会检出代码，设置 Node.js 环境，安装依赖项，然后用你的 GitHub 和 npm 令牌运行语义化发布。

```javascript
name: Release
on:
 workflow_run:
 workflows: ['Snyk Security Check', 'Tests']
 branches: [main]
 types:
 - completed

permissions:
 contents: read

jobs:
 release:
 name: Release
 runs-on: ubuntu-latest
 permissions:
 contents: write # to be able to publish a GitHub release
 issues: write # to be able to comment on released issues
 pull-requests: write # to be able to comment on released pull requests
 id-token: write
 steps:
 - name: Checkout
 uses: actions/checkout@v4
 with:
 fetch-depth: 0
 - name: Setup Node.js
 uses: actions/setup-node@v4
 with:
 node-version: 'lts/*'
 - name: Install dependencies
 run: npm ci
 - name: Verify the integrity of provenance attestations and registry signatures for installed dependencies
 run: npm audit signatures
 - name: Release
 env:
 GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
 NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
 run: npx semantic-release
```

这个脚本本质上表明，一旦 Snyk 安全检查和测试工作成功完成，就运行发布作业。发布作业会检出代码，设置 Node.js 环境，安装依赖项，然后用你的 GitHub 和 npm 令牌运行语义化发布。

- 注意：GitHub Actions 内置了 GITHUB_TOKEN 密钥/环境变量，所以在这个环境中不用提供个人访问令牌。个人访问令牌仅用于对语义化发布配置进行本地测试。

12\. 提交本地更改，把它们推送到你的 GitHub 仓库。

- 你可以在终端中运行命令 `git commit -am '<你的提交消息>'`，然后运行 `git push` 来完成此操作。
- 你也可以通过 VS Code 的[版本控制功能](https://code.visualstudio.com/docs/editor/versioncontrol)来完成。

13\. 设置好所有这些之后，你现在可以用[规范提交](https://www.conventionalcommits.org/en/v1.0.0/)把更改推送到主分支（或者通过合并拉取请求），发布工作流就会运行（当然是在 Snyk 安全检查之后）。你可以在示例[现代 npm 包仓库工作流](https://github.com/snyk-snippets/modern-npm-package/actions/runs/13039904020)中查看这一过程的实例。

## 通过 GitHub 使用 Snyk 进行持续安全监控

虽然在提交代码的过程中直接进行安全检查很有用，但还是有可能错过两次提交之间出现的漏洞。比如，如果你几个月都没往仓库推送任何代码，那在这段时间内发现的任何新漏洞你都不会知道。这时，Snyk 就能发挥更大的作用！当你把 GitHub 仓库连接到 Snyk 时，不管你多久在项目上写一次代码，它都会自动监控新的漏洞并向你发出警报。此外，它还会自动创建拉取请求来帮你解决安全问题。

Snyk 有两款产品对确保 npm 包代码及其依赖项的安全性特别有帮助。[Snyk Code](https://snyk.io/product/snyk-code/) 有助于保障你的包代码安全，[Snyk Open Source](https://snyk.io/product/open-source-security-management/) 则会监控你的开源依赖项是否存在漏洞。

要充分利用你的免费 Snyk 账户，请按照以下步骤操作：

1. [登录你的免费 Snyk 账户](https://app.snyk.io/login)
2. 选择 **添加项目**，然后选择 **GitHub**。
   ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712563/wordpress-sync/blog-create-npm-packages-gh.png)
3. 按名称搜索你的项目仓库，选中其旁边的复选框。
   ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712557/wordpress-sync/blog-create-npm-packages-snyk.png)
4. 确认仓库已成功导入 Snyk。
   ![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712563/wordpress-sync/blog-create-npm-packages-project.png)

## 开始创建现代 npm 包

咱们总结一下你在这篇文章里学到的所有内容。首先，你了解了怎么设置、创建和部署一个简单的 npm 包。这对首次熟悉怎么发布自己的 npm 包很有帮助。不过，要是你想创建一个能用于生产环境的 npm 包，这样做会很麻烦，也不好持续。

为了创建一个能用于生产环境的包，你随后学习了怎么为 CommonJS (CJS) 和 ECMAScript (ESM) 模块格式进行构建，怎么设置并编写单元测试，怎么实施安全检查，以及怎么自动化版本管理和发布。有了这些知识，你现在已经准备好创建更多让社区或公司容易使用的 npm 包了。

试试 Snyk 免费的 [JavaScript 代码检查器](https://snyk.io/code-checker/javascript/)，查找并修复代码中的漏洞。

[英文原文](https://snyk.io/blog/best-practices-create-modern-npm-package/)
