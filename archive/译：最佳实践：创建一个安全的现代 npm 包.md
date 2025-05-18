# 最佳实践：创建一个安全的现代 npm 包

技术不断发展，你的流程和实践也需要与时俱进。虽然 npm 到 2025 年已有 15 年历史，但 ==你创建 npm 包的方式应该更加现代化==。如果你觉得自己的方法可能有些过时，请继续阅读。

在本教程中，我们将逐步介绍如何使用 2025 年的现代最佳实践来创建 npm 包。首先你将学习如何创建 npm 包，熟悉构建和发布包到 npm 注册表的流程。接着你将了解如何通过设置测试框架、持续集成和部署流水线、安全检查以及自动化的语义版本管理来打造 ==更健壮且可用于生产环境的 npm 包==。学完本教程后，你将能够自信地创建现代化且可持续维护的 npm 包。让我们开始吧！

## 先决条件

1. 熟悉 Node.js、JavaScript/TypeScript、GitHub 和 GitHub Actions
2. 具备协助创建 npm 包的开发工具

## 简单示例 npm 包

首先通过一个简单示例来熟悉创建和发布 npm 包的流程。如果你已经熟悉这些内容，可以直接跳转到[生产就绪的 npm 包](https://snyk.io/blog/best-practices-create-modern-npm-package/#npm-package)部分，该部分涵盖更高级的主题。

### 设置你的项目

你需要一个 GitHub 项目来开始，所以请按照以下步骤来初始化项目。如果你已经有一个可用的项目，可以跳过下一节，但 ==请务必仔细检查本节中的第 5 步== 关于你的包名称。

- 创建一个 GitHub 仓库：[https://github.com/new](https://github.com/new)  
- 将仓库克隆到本地。  
    **示例：** `git clone` [https://github.com/snyk-snippets/simple-npm-package.git](https://github.com/snyk-snippets/simple-npm-package.git)
- 打开终端并切换到克隆项目的文件夹。  
    **示例：** `cd simple-npm-package`
- 运行 `npm init -y` 来创建 `package.json` 文件。**注意：** 如果你克隆了示例仓库，则不需要执行此步骤。
- 使用作用域名称更新 `package.json` 中的 `name` 属性。  
    **示例：** `@clarkio/simple-npm-package`。请确保使用你的用户名或组织名称而不是 `@clarkio`。
- 为包编写代码（或直接使用 index.js 中的 hello world 示例）。

项目创建完成后，你可以继续创建 npm 账户。

### 设置 npm 账户

为了让其他人能够使用你的 npm 包，你需要一个 npm 账户。以下步骤将指导你创建自己的账户（如果你还没有），启用双重身份验证（2FA）以提高账户安全性，并将账户连接到本地机器。

1\. 在 [https://www.npmjs.com/signup](https://www.npmjs.com/signup) 注册 npm 账户。

2\. 为了更好的安全性，在 npm 账户上启用 2FA：[https://docs.npmjs.com/configuring-two-factor-authentication](https://docs.npmjs.com/configuring-two-factor-authentication)。

3\. 在终端使用 `npm login` 命令登录你的 npm 账户，并按照屏幕上的指示操作。例如：

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

当你拥有 npm 项目并注册了 npm 账户后，就可以将包发布到 [官方 npm 公共注册表](https://npmjs.org/)。以下是发布前检查与正式发布的完整流程：

1\. 在终端运行 `npx pack --dry-run` 预览将要发布的包内容：

```bash
> npx pack --dry-run 
npm notice Tarball Contents 
npm notice 1.1kB LICENSE 
npm notice 1.9kB README.md 
npm notice 108B index.js 
npm notice 700B package.json 
npm notice Tarball Details
```

这可确保你没有遗漏任何包正常运行所需的源代码文件。另外，确保不会意外地向公众泄露敏感信息（如包含数据库凭证或 API 密钥的本地配置文件）也是一个好习惯。

2\. 在终端中运行 `npm publish --dry-run` 命令，以查看实际执行该命令时会发生什么。

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
npm notice unpacked size: 3.1 kB 15npm notice shasum:40ede3ed630fa8857c0c9b8d4c81664374aa811c 
npm notice integrity:sha512-QZCyWZTspkcUXL... ]L60ZKBOOBRLTg== 
npm notice total files:4 
npm notice 
+ @clarkio/simple-npm-package@0.0.1
```

3\. 在终端中运行 `npm publish --access=public` 以将包实际发布到 npm。**注意**：作用域包（如 @clarkio/modern-npm-package）默认是私有的，因此需要 `--access=public` 选项。如果包没有作用域，并且 `package.json` 中的 `private` 字段未设置为 `true`，则该包也将是公开的。

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

你已经完成了！你已经构建并部署了自己的 npm 包。接下来，你将学习如何创建一个更健壮的、可用于生产环境且能被更广泛使用的包。

## 生产就绪的 npm 包

虽然之前的示例包有可能在生产环境中使用，但随着时间的推移，它需要手动维护。使用工具和自动化，结合适当的测试和安全检查，将有助于减少保持包平稳运行所需的总体工作量。让我们深入了解这涉及哪些方面。

以下部分将涵盖：

1. 设置你的 `modern-npm-package` 项目

2. 为 ECMAScript (ESM) 模块格式进行构建

3. 设置并编写单元测试

4. 实施安全检查

5. 自动化版本管理和发布

如果你在阅读本文时没有自己的项目可以使用，可以参考以下示例项目：[https://github.com/snyk-snippets/modern-npm-package](https://github.com/snyk-snippets/modern-npm-package)。

### 为 ECMAScript 模块格式进行构建

从 Node.js 12 版本及更高版本开始，ECMAScript 模块格式已得到原生支持，最新的长期支持版本是 22.x。此外，JavaScript 运行时领域也有了更多竞争对手，如 Bun.js 和 Deno，这些都使得现在专注于使用 ESM 格式变得更加容易。我们将使用 TypeScript 来让你的 npm 包支持 ESM 格式。

- 首先，创建一个名为 `tsconfig.json` 的 TypeScript 配置文件。这是用于在使用 ESM 构建你的包时可使用的编译设置。你可以根据项目需求随意调整此文件，特别是如果你不使用提供的示例项目结构，你需要调整 `files` 属性以与你的项目结构保持一致。

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

- `lib` 属性向 TypeScript 指明在为项目编写代码时应引用哪些类型以提供帮助。
- `target` 属性向 TypeScript 指明应将项目代码编译成哪个 JavaScript 版本。
- `module` 属性向 TypeScript 指明在编译项目代码时应使用哪种 JavaScript 模块格式。
- `moduleResolution` 属性帮助 TypeScript 确定如何引用 “import” 语句。
- `outDir` 和 `declarationDir` 属性向 TypeScript 指明应将编译代码的结果以及定义其中使用的类型的文件放置在何处。

2\. 更新 `package.json` 文件，添加一个 `files` 字段，该字段指向 `lib` 文件夹，该文件夹包含 TypeScript 为你构建包的结果。

3\. 更新 `package.json` 文件中的 `main` 和 `types` 字段，使其指向构建/编译后的包的输出。这是作为默认的备用选项。

```javascript
"types": "./lib/index.d.ts", 
"main": "./lib/index.js",
```

4\. 在 `package.json` 文件中添加一个 `files` 字段，以指明在 npm 打包你的代码进行发布时应包含哪些文件。

```javascript
"files": [ 
  "lib/**/*" 
],
```

5\. 通过 `package.json` 中的 `scripts` 字段创建命令，以使用 `tsc` 编译包。这将生成 `lib` 文件夹的源文件。

```javascript
"clean": "del-cli ./lib", 
"build": "npm run clean && tsc -p ./tsconfig.json", 
"prepack": "npm run build",
```

- `clean` 脚本用于删除之前构建生成的输出文件，以便从头开始构建。

- `build` 脚本会清理之前的输出文件，并使用 TypeScript 编译器将包构建到输出目录。

- `prepack` 脚本由 npm 在打包 npm 包以准备发布到注册表之前使用。

6\. 现在你需要安装所有这些功能正常工作所需的开发依赖项。运行 `npm install -D typescript del-cli`。

7\. 现在你可以在终端中运行 `npm run build`，让 TypeScript 构建你的项目，为使用和发布做准备。

这就是使用 TypeScript 构建支持 CommonJS 和 ECMAScript 模块格式的 npm 包所需的所有设置。接下来，你将学习如何针对 npm 包代码设置并运行测试，以确保它能产生你期望的结果。

### 设置并添加测试

为了对代码的行为和结果有信心，你需要实施一个测试流程。测试会促使你在初次编写代码时，以不同于常规顺利路径的方式来思考代码的功能。例如，你可以思考如何破坏一个函数，使其抛出错误或产生非预期的结果。这样做会让你的应用程序更具弹性和可持续性，同时确保在添加更多功能时不会出现问题。

如果你想深入了解测试并学习其最佳实践，请务必阅读 Yoni Goldberg 的 [JavaScript 最佳实践仓库](https://github.com/goldbergyoni/javascript-testing-best-practices)。

### 单元测试

确保你的包按照你期望的方式运行，需要针对你的代码编写测试。你需要一些工具来帮助设置项目以运行单元测试并显示结果。这些工具现在作为 Node.js 的内置模块提供（[自 20.x、18.x 版本以及 16.17.x 版本在实验标志后可用](https://nodejs.org/docs/latest-v22.x/api/test.html#test-runner)）。请按照以下步骤为你的 npm 包设置并运行测试：

1\. 在终端中使用以下命令安装开发依赖项：`npm i -D @types/node`

2\. 在项目的根目录下创建一个 `tests` 文件夹。

3\. 在 `tests` 文件夹中创建一个 `index.test.ts` 文件。

4\. 在 `index.test.ts` 文件中编写单元测试，以测试 `index.ts` 中的代码。

**注意**：你可以参考示例 npm 包仓库作为示例：[https://github.com/snyk-snippets/modern-npm-package](https://github.com/snyk-snippets/modern-npm-package)。

5\. 在 `package.json` 文件的 `scripts` 部分添加一个 `test` 属性，并将其值设置为 `node --experimental-strip-types --test`

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

6\. 在项目的根目录下的终端中运行 `npm test` 以执行测试并查看结果：

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

既然你已经有了用于验证代码行为的测试，就可以在流水线中使用它们了。这将有助于确保推送到仓库中的任何更改都不会破坏代码的行为。请按照以下步骤在项目流水线中创建一个测试工作流。

1. 为你的仓库创建一个新的 GitHub Action：`https://github.com/<你的账户或组织>/<你的仓库名称>/actions/new`

2. 将工作流重命名为 `tests.yml`

3. 在你的工作流文件中插入以下 Snyk GitHub Action 脚本：

```yml
name: Tests 
 
on: 
 push: 
 branches: [main] 
 pull_request: 
 branches: [main] 
 
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

此 YAML 脚本会检出你最新的代码，安装其依赖项，并运行 `npm test` 命令来执行测试。它会针对 `node-version` 字段中列出的每个 Node.js 版本执行这些操作，这样你就可以确保代码在每个运行时中都能按预期工作。

你现在已经完成了对 npm 包代码进行测试运行和评估的项目设置。不过，你可能会想：“我如何在另一个项目中 _使用_ 我的 npm 包进行测试呢？” 接下来让我们看看如何实现这一点。

### 包测试

通过单元测试对 npm 包的代码有信心是一回事，但确保整个 npm 包的使用体验又是另一回事。这涉及到将你的 npm 包作为依赖项引入到另一个项目中，并查看其在该项目中的使用是否如你预期的那样顺利。以下是五种可以进行测试的方法：

1. 通过 `npm pack` 输出进行安装
2. 通过相对路径进行安装
3. 通过 `npm link` 进行安装
4. 通过注册表（例如 [npmjs.com](https://npmjs.com/) 上的 npm 公共注册表）进行安装
5. 使用 [Verdaccio](https://verdaccio.org/)（一个开源的 npm 私有注册表项目）在持续集成（CI）中运行端到端的包发布和安装步骤

#### npm pack

这种方法将利用 `npm pack` 命令将你的 npm 包打包并压缩成一个单独的文件（`<包名>.tgz`）。然后你可以进入想要使用该包的项目，并通过这个文件来安装它。具体步骤如下：

1. 在你的 npm 包目录下，在终端中运行 `npm pack` 命令。注意它生成的 `.tgz` 文件及其位置。

2. 切换到你想要使用 npm 包的项目目录。**示例：** `cd /path/to/project`

3. 在 **客户端** 项目目录中，运行 `npm install /path/to/package.tgz`，但要将 `/path/to/package.tgz` 替换为步骤 1 中 `.tgz` 文件的正确路径。

4. 然后你就可以在这个 **客户端** 项目中开始使用该包进行测试了。

这将为你提供使用 npm 包最接近生产环境的体验。

#### npm link

这种方法将利用 `npm link` 命令，在尝试将你的包安装到 **客户端** 项目中时，指向你的包目录。具体步骤如下：

1. 在你的 npm 包目录下的终端中运行 `npm link`。

2. 切换到你想要使用该 npm 包的项目目录。**示例：** `cd /path/to/project`

3. 在 **客户端** 项目目录下运行 `npm link <你的包名>`。

这将在你的代码引用该包时，将 **客户端** 项目指向 npm 包目录。这种方法不会给你提供完全类似于生产环境的包使用体验，但可以确保包的功能按预期工作。

#### 相对路径

这种方法利用了你现有的 `npm install` 命令使用知识。它类似于 `npm link`，但无需学习像 `link` 这样的新命令。

1. 在你的 **客户端** 项目目录下的终端中运行 `npm install /path/to/your/package`。

与 `npm link` 方法类似，这能让你在客户端项目中快速测试包的功能，但无法提供完全类似生产环境的体验。这是因为它指向的是完整的包源代码目录，而 **不是** 你在 npm 注册表中能找到的打包版本。

#### npm 注册表

这种方法利用了 npm 包的公共（或你自己的）注册表。它涉及发布你的包，并像安装其他任何 npm 包一样进行安装。

1. 使用本文前面概述的步骤，通过 `npm publish` 命令发布你的 npm 包。

2. 切换到你想要使用该 npm 包的项目目录。**示例：** `cd /path/to/project`

3. 在 **客户端** 项目目录中运行 `npm install <name-of-your-package>`。
Shout out to Mirco Kraenz ([@MKraenz](https://twitter.com/MKraenz)) who created a [Twitter thread](https://twitter.com/MKraenz/status/1559188177696436226?s=20&t=LJg1FbyDLTmiOGAUQh7LmQ) summarizing our learnings of this during a livestream!

此时，你已经构建了支持现代模块格式的包，并通过单元测试和打包测试确保它能按预期运行。接下来，你需要确保 npm 包中不存在安全问题，并防止引入新的安全问题。

## 实施安全检查

就像你不希望自己的项目存在安全漏洞一样，你也不希望将漏洞引入到其他人的项目中。构建一个预计会在许多其他项目中使用的 npm 包，意味着你需要承担更大的责任来确保一切都是安全的。你需要设置安全检查，以帮助监控漏洞、发出警报并提供缓解漏洞的协助。像 [Snyk](https://snyk.io/product/) 这样的工具就可以简化实现这些需求所需的工作。

对于这个示例 npm 包，你使用 GitHub 作为源代码管理工具，因此你可以利用其 GitHub Actions 功能将 Snyk 集成到你的工作流程中。Snyk 有一个 [GitHub Actions](https://github.com/snyk/actions) 参考项目，它可以帮助你快速启动这项工作，并为你可能在项目中使用的其他编程语言和工具提供示例。

1. Snyk 是免费的，因此请注册并获取你的 [Snyk API 令牌](https://app.snyk.io/account)。

2. 在 GitHub 上将你的 Snyk API 令牌添加为仓库密钥：`https://github.com/<你的账户或组织>/<你的仓库名称>/settings/secrets/actions/new`

3. 为你的仓库创建一个新的 GitHub Action：`https://github.com/<你的账户或组织>/<你的仓库名称>/actions/new`

4. 将工作流重命名为 `snyk.yml`。

5. 在你的工作流文件中插入以下 Snyk Action 脚本：

```yml
name: Snyk Security Check 
on: [push,pull_request] 
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

设置完成后，**每当有人向你的仓库推送代码或打开拉取请求时，都会运行安全检查**，以确保不会向包中引入任何漏洞。如果发现问题，操作将失败，并向你发出找到的安全问题的详细信息警报。接下来，你将自动化 npm 包的版本管理和发布过程。

你是否想在更改推送到仓库之前就了解安全问题？[为你选择的开发工具安装 Snyk 插件](https://snyk.io/platform/ide-plugins/)。你更喜欢使用命令行工具吗？[也将 Snyk 命令行工具添加到你的工具链中](https://snyk.io/platform/snyk-cli/)。这些工具将帮助你在开发过程中检测安全问题，并在项目工作流程的早期向你发出警报。

关于当前设置需要注意的一点是，它仅利用了 [Snyk 开源](https://snyk.io/product/open-source-security-management/)（SCA）产品，而没有使用 [Snyk 代码](https://snyk.io/product/snyk-code/)（SAST）。Snyk 代码是我们的代码安全产品，你需要先通过 Snyk 账户免费启用它，然后将其添加到此处的工作流脚本中，以充分利用它。要了解更多关于在管道中使用 Snyk 代码的信息，请阅读这篇关于 [使用 GitHub Actions 构建安全管道](https://snyk.io/blog/building-a-secure-pipeline-with-github-actions/) 的文章（该文章使用 Java 和 Maven，但可以替换为 Node.js 和 npm）。

## 自动化版本管理和发布

每当你将更改合并到主分支时，你可能不想每次都手动更新 npm 包的版本并发布它。相反，你会希望将这个过程自动化。如果你还记得本文前面简单 npm 包的示例，你使用以下命令来发布 npm 包：

你还希望遵循语义化版本控制的行业标准，这样你的包的使用者就能理解你发布到注册表的不同版本变更会带来什么影响。

### 什么是语义化版本控制？

语义化版本控制规定版本号由三个占位符组成。第一个是主版本号，第二个是次版本号，最后一个是补丁版本号。要了解更多关于语义化版本控制、版本管理和锁文件的信息，请阅读[什么是 Package Lock JSON 以及锁文件如何与 Yarn 和 NPM 包一起工作](https://snyk.io/blog/what-is-package-lock-json/)。

如果你可以跳过所有手动操作，而是使用 GitHub Actions 设置一个自动化工作流来为你处理 npm 包的发布，会怎么样呢？你很幸运，因为有一个名为 [Semantic Release](https://semantic-release.gitbook.io/semantic-release/) 的工具，它可以与 [GitHub Actions](https://semantic-release.gitbook.io/semantic-release/recipes/ci-configurations/github-actions) 集成。帮助自动化这个过程的关键是，在向项目提交更改时使用所谓的 [常规提交](https://www.conventionalcommits.org/en/v1.0.0/)。这使得自动化可以相应地更新所有内容，并知道如何为你准备项目的下一个版本发布。

以下步骤将指导你为现代 npm 包设置这个功能。

1. 在终端中运行：`npm i -D semantic-release`

2. 在终端中运行：`npx semantic-release-cli setup`

3. 按照终端提示并提供所需的令牌：

- 你需要一个来自 GitHub 的个人访问令牌。要创建一个令牌，请访问 `https://github.com/settings/tokens/new?scopes=public_repo`

- 创建此令牌时，请使用以下权限范围：

![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712555/wordpress-sync/blog-create-npm-packages-new-token.png)

- 点击 “生成令牌” 按钮，并复制并保存页面上显示的值。

- 你还需要一个来自 npm 的 **自动化类型** 访问令牌，仅用于 CI 环境，以便它能够绕过你账户的双因素身份验证（2FA）。要创建一个这样的令牌，请访问 `https://www.npmjs.com/settings/<your-npm-account>/tokens`。请确保选择 “Automation” 类型，因为这将在 CI/CD 工作流中使用。

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

4\. 在此处将你的 npm 令牌作为仓库密钥添加到 GitHub 仓库中：`https://github.com/<你的名称或组织>/<你的仓库>/settings/secrets/actions/new`。将密钥名称设置为 `NPM_TOKEN`，值为你在前面步骤中获取的令牌。

![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712560/wordpress-sync/blog-create-npm-packages-secret.png)

5\. 回到你的项目，打开 `package.json` 文件，并添加如下所示的 `releases` 键。如果你的仓库主分支仍然名为 `master` 而不是 `main`，则相应地更新上述 `branches` 的值。

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

7\. 使用 `semantic-release` npm 脚本进行试运行来测试所有内容。获取以下命令，并将 `NPM_TOKEN=` 和 `GH_TOKEN=` 的值设置为你各自的令牌值。然后复制完整命令并在终端中运行，查看一切是否正常运行。你将在终端输出中看到该过程的日志。如果出现任何问题，这里会显示相关信息，并提供解决问题的详细说明。

8\. 确认试运行成功完成后，你可以为 GitHub 仓库设置一个新的 GitHub Action 来为你处理发布过程。前往 GitHub 上的仓库，点击 “Actions”。

9\. 点击 **新建工作流** 选项。

10\. 将工作流重命名为 `release.yml`。

11\. 将以下 YAML 脚本添加到新的工作流文件中。该脚本本质上是说，一旦 Snyk 安全检查工作成功完成，就运行发布作业。发布作业会检出代码，设置 Node.js 环境，安装依赖项，然后使用你的 GitHub 和 npm 令牌运行语义化发布。

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

这个脚本本质上表明，一旦 Snyk 安全检查和测试工作成功完成，就运行发布作业。发布作业会检出代码，设置 Node.js 环境，安装依赖项，然后使用你的 GitHub 和 npm 令牌运行语义化发布。

- 注意：GitHub Actions 内置了 GITHUB_TOKEN 密钥/环境变量，因此在这个环境中无需提供个人访问令牌。个人访问令牌仅用于对语义化发布配置进行本地测试。

12\. 提交本地更改并将其推送到你的 GitHub 仓库

- 你可以在终端中运行命令 `git commit -am '<你的提交消息>'`，然后运行 `git push` 来完成此操作。

- 你也可以通过 VS Code 的[版本控制功能](https://code.visualstudio.com/docs/editor/versioncontrol)来完成。

13\. 设置好所有这些之后，你现在可以使用[规范提交](https://www.conventionalcommits.org/en/v1.0.0/)将更改推送到主分支（或者通过合并拉取请求），并且发布工作流将运行（当然是在 Snyk 安全检查之后）。你可以在示例[现代 npm 包仓库工作流](https://github.com/snyk-snippets/modern-npm-package/actions/runs/13039904020)中查看这一过程的实例。

## 通过 GitHub 使用 Snyk 进行持续安全监控

虽然在提交代码的过程中直接进行安全检查很有好处，但仍有可能错过两次提交之间出现的漏洞。例如，如果你几个月都没有向仓库推送任何代码，那么在这段时间内发现的任何新漏洞你都不会知晓。这时，Snyk 就能发挥更大的作用！当你将 GitHub 仓库连接到 Snyk 时，无论你多久在项目上编写一次代码，它都会自动监控新的漏洞并向你发出警报。此外，它还会自动创建拉取请求来帮你解决安全问题。

Snyk 有两款产品对确保 npm 包代码及其依赖项的安全性特别有帮助。[Snyk Code](https://snyk.io/product/snyk-code/) 有助于保障你的包代码安全，[Snyk Open Source](https://snyk.io/product/open-source-security-management/) 则会监控你的开源依赖项是否存在漏洞。

要充分利用你的免费 Snyk 账户，请按照以下步骤操作：

1\. [登录你的免费 Snyk 账户](https://app.snyk.io/login)

2\. 选择 **添加项目**，然后选择 **GitHub**。

![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712563/wordpress-sync/blog-create-npm-packages-gh.png)

3\. 按名称搜索你的项目仓库，并选中其旁边的复选框。

![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712557/wordpress-sync/blog-create-npm-packages-snyk.png)

4\. 确认仓库已成功导入 Snyk。

![ ](https://res.cloudinary.com/snyk/image/upload/f_auto,w_2560,q_auto/v1663712563/wordpress-sync/blog-create-npm-packages-project.png)

## 开始创建现代 npm 包

让我们总结一下你在本文中学到的所有内容。首先，你了解了如何设置、创建和部署一个简单的 npm 包。这对于首次熟悉如何发布自己的 npm 包非常有帮助。然而，如果你想创建一个可用于生产环境的 npm 包，以这种方式操作会非常繁琐且难以持续。

为了创建一个可用于生产环境的包，你随后学习了如何为 CommonJS (CJS) 和 ECMAScript (ESM) 模块格式进行构建，如何设置并编写单元测试，如何实施安全检查以及如何自动化版本管理和发布。有了这些知识，你现在已经准备好创建更多易于社区或公司使用的 npm 包了。

试试 Snyk 免费的 [JavaScript 代码检查器](https://snyk.io/code-checker/javascript/)，以查找并修复代码中的漏洞。

[原文](https://snyk.io/blog/best-practices-create-modern-npm-package/)
