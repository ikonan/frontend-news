# React 的 TypeScript 知识

**编者注：** ==TypeScript 是 React 开发者的必备技能==，它能帮你 1) 在代码运行前捕获错误，2) 获得更好的编辑器智能提示，3) 使代码更易于维护。本文介绍了 TypeScript 的核心概念和 React 中的最佳实践，助你快速上手。

我经常看到这样的帖子——说实话，这让我很心痛 🥲

**如果你还在用 JavaScript 开发 React 应用，那你是在给自己增加难度。**

学习 TypeScript 所花的时间，100% 会从你节省的调试时间中赚回来。

所以这里有一份指南帮你完成转换。

只讲精华。不废话 😉。

## TypeScript 工作原理

TypeScript 在 JavaScript 基础上添加了额外语法，帮助你在应用崩溃前捕获错误。

最终它会被编译成 JavaScript。

最棒的是？==你可以逐步迁移到 TypeScript==。

更多细节请参考这个[指南](https://github.com/Microsoft/TypeScript-React-Conversion-Guide?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react#typescript-react-conversion-guide)。

## 为什么你应该尽快切换到 TypeScript

### 优势 #1: 在生产环境前捕获错误

这段 JavaScript 代码看起来没问题...直到它崩溃：

```ts
function printFirstChar(users, userIndex) {
  const name = users[userIndex];
  console.log(name[0]);
}

const users = ["Fatou", "Bob"];

printFirstChar(users, 5); // ❌ 运行时错误: 无法读取 undefined 的属性
```

TypeScript 版本：

```ts
function printFirstChar(users: string[], userIndex: number) {
  const name = users[userIndex];

  if (name != null) {
    console.log(name[0]);
  }
}
```

TypeScript 不会修复你的逻辑，但会给你==早期警告==比如"嘿，这里可能是 undefined 👀"

### 优势 #2: 获得库的自动补全

特别有用当你探索新库时。

如果那个库有 TypeScript 类型定义，你就能在大多数编辑器中获得自动补全。

### 优势 #3: 让你的代码更能适应变化

当你正确添加类型后，TypeScript 就像安全网。

例如：

##### 没有 TypeScript，这段代码可能悄无声息地出错

```ts
function getLabel(status) {
  switch (status) {
    case "idle":
      return "Idle";
    case "loading":
      return "Loading";
    case "success":
      return "Success";
  }
}

getLabel("loading"); // ✅ 正常工作

// 几个月后...

getLabel("error"); // ❌ 返回 undefined，但没有 JS 错误
```

##### 使用 TypeScript，你需要处理变化

```ts
type Status = "idle" | "loading" | "success";

function getLabel(status: Status): string {
  switch (status) {
    case "idle":
      return "Idle";
    case "loading":
      return "Loading";
    case "success":
      return "Success";
    case "error":
      return "Error";
  }
}

// 几个月后...
type Status = "idle" | "loading" | "success" | "error"; // 添加了 'error'

// ✅ TypeScript 会显示错误: "并非所有代码路径都返回值"
```

如果你忘记处理某个情况，TypeScript 会提醒你。这正是你想要的 😄。

![](https://media.beehiiv.com/cdn-cgi/image/fit=scale-down,format=auto,onerror=redirect,quality=80/uploads/asset/file/a0dce78c-3200-4d1c-b113-1ed4c392dde7/Screenshot_2025-04-13_at_13.03.17.png?t=1744545617)

<small>当我们忘记处理某个情况时的 TypeScript 错误</small>

## React 开发需要掌握的 TypeScript 概念

让我们覆盖==最基础==的入门知识。

### ✅ 类型

**类型让你能描述数据的形状并强制执行。**

TypeScript 中有原生类型和用户定义类型。

#### 原生类型

这些类型是默认提供的。

```ts
const name = "Ada"; // string
const age: number = 31; // number
const isActive = true; // boolean
const scores: number[] = [90, 85, 100]; // number[]
```

💡 在上面的一些例子中，我显式地标注了类型（如 `const age: number = 31`）。

在实际代码中你不需要这样做——TypeScript 可以通过值推断出类型。

#### 自定义类型

你可以用原生类型创建新类型。

```ts
type User = {
  id: number;
  name: string;
};

const user: User = {
  id: 1,
  name: "Fatou",
};
```

### ❌ 避免使用 `any`

除非你正在迁移过程中，否则不要使用 `any`。

它只是伪装成 JavaScript。

**改用** `unknown` **——它会强制你在使用前检查类型。**

```ts
// ❌ 不好: 没有警告，运行时崩溃
function logLength(value: any) {
  console.log(value.length);
}

logLength(123);

// ✅ 更安全
function logLength(value: unknown) {
  if (typeof value === 'string' || Array.isArray(value)) {
    console.log(value.length);
  }
}

// ✅✅ 最好: 使用显式类型
function logLength(value: string | ArrayLike<unknown>){
  console.log(value.length);
}
```

### 🧩 联合与交叉类型

**联合类型让一个值可以是多种类型之一。**

```ts
type Status = 'idle' | 'loading' | 'error';

let currentStatus: Status = 'idle';
```

**交叉类型将多个类型合并为一个。**

```ts
type Name = { name: string };
type Age = { age: number };

type Person = Name & Age;

const person: Person = { name: 'Ada', age: 30 };
```

### 🧱 接口

**接口定义对象的结构。**

```ts
interface User {
  name: string;
  age: number;
}
```

你可以扩展接口：

```ts
interface Admin extends User {
  role: string;
}
```

什么时候用 `type` 还是 `interface`？区别不大。

### 🔁 泛型

**泛型让你能编写可复用的灵活类型。**

假设你要为 API 响应添加类型。

**没有泛型：**

```ts
type UserResponse = {
  status: number;
  data: User;
  error?: string;
};

type ProductResponse = {
  status: number;
  data: Product;
  error?: string;
};
```

太多重复代码了。

**使用泛型：**

```ts
type ApiResponse<T> = {
  status: number;
  data: T;
  error?: string;
};

type UserResponse = ApiResponse<User>;
type ProductResponse = ApiResponse<Product>;
```

你也可以在函数中使用泛型：

```ts
function wrap<T>(value: T): T[] {
  return [value];
}

wrap(42); // 类型: number[]
wrap('hi'); // 类型: string[]
```

我喜欢把泛型想象成==函数的参数==——不过是针对类型的 😉。

### 🔧 函数类型

**你可以为函数参数和返回值添加类型。**

你总是需要为参数添加类型，但不必总是为返回值添加类型。

```ts
// 这样可行
function add(a: number, b: number): number {
  return a + b;
}

// 这样也可行: 你不需要为返回值添加类型
function add(a: number, b: number) {
  return a + b; // 推断为 number
}
```

你也可以为函数变量添加类型：

```ts
const add: (a: number, b: number) => number = (a, b) => a + b;
```

根据上下文，你可能想为返回值添加类型。==不确定时，就加上返回类型==。

## 如何在 React 中使用 TypeScript

### Props

**Props 就是对象。像为其他对象一样添加类型。**

使用 `type`:

```tsx
type GreetingProps = {
  name: string;
};

function Greeting({ name }: GreetingProps) {
  return <p>你好 {name}</p>;
}
```

使用 `interface`:

```tsx
interface GreetingProps {
  name: string;
}

function Greeting({ name }: GreetingProps) {
  return <p>你好 {name}</p>;
}
```

### State

`useState` **接受泛型类型，但 TypeScript 也能推断它。**

```ts
const [count, setCount] = useState(0); // 推断为 number
```

但如果不能推断，就显式声明：

```tsx
const [count, setCount] = useState<number>(0);
```

### 组件变量和函数

就像普通 TypeScript 一样：

```ts
const users: User[] = [{name: "Fatou", age: 31}, {name: "Bob", age: 25} ];
const age = 30; // 推断为 number
```

当你有一些像事件处理函数时，可以悬停在 DOM 元素上看要使用的类型。

```tsx
const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
  console.log('点击了!', event);
};

// 或者:
const handleClick: React.MouseEventHandler<HTMLButtonElement> = (event) => {
  console.log('点击了!', event);
};
```

### 跨组件复用类型

**你可以扩展或组合类型/接口来避免重复。**

使用接口：

```tsx
interface ButtonProps {
  label: string;
}

interface IconButtonProps extends ButtonProps {
  icon: React.ReactNode;
}
```

### Hooks

Hooks 就是函数，所以你可以使用适用于函数的相同 TypeScript 工具。

#### **原生 hooks**

几乎所有原生 hooks 都有泛型参数。

```tsx
const [count, setCount] = useState<number>(0);
// 或者让 TS 推断:
const [count, setCount] = useState(0);

const inputRef = useRef<HTMLInputElement>(null);

const [state, dispatch] = useReducer(
  (state: number, action: 'inc' | 'dec') => {
    switch (action) {
      case 'inc':
        return state + 1;
      case 'dec':
        return state - 1;
    }
  },
  0
);
```

#### **自定义 hooks**

```tsx
function useToggle(initial: boolean): [boolean, () => void] {
  const [state, setState] = useState(initial);
  const toggle = () => setState((prev) => !prev);
  return [state, toggle];
}

const [isOpen, toggleOpen] = useToggle(false);
```

## 延伸学习资源

如果你想深入学习 TypeScript，这里有一些优质资源：

- 🧠 [TypeScript 官方文档](https://www.typescriptlang.org/docs/handbook/intro.html?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): 最佳起点。清晰、结构良好且包含大量示例。还包括一个[演练场](https://www.typescriptlang.org/play?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react)供你测试。
- 🎯 [Total TypeScript](https://www.totaltypescript.com/?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): Matt Pocock 创建的优秀网站——特别适合 React 开发者。有免费初学者课程、高级模式和许多真实案例。
- ✨ [TypeScript 风格指南](https://mkosir.github.io/typescript-style-guide/?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): 关于如何编写干净一致的 TypeScript 代码的简短实用建议。当你开始构建真实应用时强烈推荐。
- 🧩 [Type Challenges](https://github.com/type-challenges/type-challenges?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): 从入门到疯狂的 TypeScript 谜题集合。如果你喜欢通过解决问题来学习并想锻炼类型系统技能，这很棒。
- 📘 [Effective TypeScript](https://effectivetypescript.com/?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): Dan Vanderkam 的书+博客。更高级，但教你如何编写更好的 TypeScript。掌握基础后值得一看。
- ⚛️ [React TypeScript 备忘单](https://react-typescript-cheatsheet.netlify.app/?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react): 专为 React 开发者定制。涵盖从组件类型到上下文和 hooks 的所有内容。非常新手友好，作为快速参考超级有用。

💡 确保也查看我的[101 React 技巧与窍门](https://dev.to/_ndeyefatoudiop/101-react-tips-tricks-for-beginners-to-experts-4m11?utm_source=www.frontendjoy.com&utm_medium=referral&utm_campaign=typescript-to-know-for-react#category-12-react-amp-typescript)中的 TypeScript 部分 😉。

## 总结

在 React 中使用 TypeScript 是你能做的最佳升级之一。

它帮你==提前捕获错误==，提供==更好的工具支持==，让你的代码==更易于维护==。

最棒的是？==你不需要一开始就学习所有内容==。从基础开始——类型、接口、泛型——然后逐步深入。

网上有很多例子能帮你解决问题，AI 工具也能快速帮你突破障碍。

如有任何问题欢迎交流 🙂。

![](https://media.beehiiv.com/cdn-cgi/image/fit=scale-down,format=auto,onerror=redirect,quality=80/uploads/asset/file/392eb46b-3917-461a-80de-4b66d82ad61a/newsletter_divider__2_.png?t=1736759018)