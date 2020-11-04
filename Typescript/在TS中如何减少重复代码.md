### 在TS中如何减少重复代码

减少重复的最简单方法是命名类型，而不是通过以下这种方式来定义一个 distance 函数：

```ts
function distance(a: {x: number, y: number}, b: {x: number, y: number}) { 
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}

```

在上述的 distance 方法中，我们重复使用 `{x: number, y: number}` 来定义参数 a 和参数 b 的类型，要解决这个问题很简单，我们可以定义一个 `Point2D` 接口：

```ts
interface Point2D { 
  x: number;
  y: number;
}

function distance(a: Point2D, b: Point2D) { /* ... */ }

```

如果多个函数共享相同的类型签名，比如：

```ts
function get(url: string, opts: Options): Promise<Response> { /* ... */ } 
function post(url: string, opts: Options): Promise<Response> { /* ... */ }
	
```

对于上面的 get 和 post 方法，为了避免重复的代码，我们可以提取统一的类型签名：

```ts
type HTTPFunction = (url: string, opts: Options) => Promise<Response>; 

const get: HTTPFunction = (url, opts) => { /* ... */ };
const post: HTTPFunction = (url, opts) => { /* ... */ };
```

在定义接口的时候也要小心，避免出现以下类似的重复代码。比如：

```ts
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate {
  firstName: string;
  lastName: string;
  birth: Date;
}

```

很明显，相对于 `Person` 接口来说，`PersonWithBirthDate` 接口只是多了一个 `birth` 属性，其他的属性跟 `Person` 接口是一样的。那么如何避免出现例子中的重复代码呢？要解决这个问题，可以利用 extends 关键字：

```ts
interface Person { 
  firstName: string; 
  lastName: string;
}

interface PersonWithBirthDate extends Person { 
  birth: Date;
}

```

当然除了使用 extends 关键字之外，也可以使用交叉运算符（&）：

```ts
type PersonWithBirthDate = Person & { birth: Date };

```

下面我们来继续看另一个例子，假设你已经定义 State（代表整个应用程序的状态）和 TopNavState（只代表部分应用程序的状态）两个接口：

```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}

interface TopNavState {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
}

```

上述的 TopNavState 接口相比 State 接口只是缺少了 `pageContents` 属性，但我们却重复声明其他三个相同的属性。为了减少重复代码，我们可以这样做：

```ts
type TopNavState = {
  userId: State['userId']; 
  pageTitle: State['pageTitle']; 
  recentFiles: State['recentFiles'];
};

```

在上面代码中，我们通过成员访问的语法来提取对象中属性的类型，从而避免重复定义接口中相关属性的类型。但这并没有解决本质的问题，我们还有很大的优化空间。针对这个问题，我们可以利用映射类型来进一步做优化：

```ts
type TopNavState = {
  [k in 'userId' | 'pageTitle' | 'recentFiles']: State[k]
};

```

鼠标悬停在 TopNavState 显示它的声明，实际上，这个定义与前一个定义完全相同。



![top-nav-state-definition](https://user-gold-cdn.xitu.io/2020/5/27/172537da07ab1120?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



通过映射类型优化后的代码，相比 TopNavState 接口最初的代码简洁了许多。那还有没有优化空间呢？其实是有的，我们可以利用 TypeScript 团队为我们开发者提供的工具类型，这里我们可以使用 `Pick`：

```ts
type TopNavState = Pick<
  State, 'userId' | 'pageTitle' | 'recentFiles'
>;

```

其实除了 `Pick` 之外，在实际开发过程我们还可以利用其他内置的工具类型来减少重复代码。这里我们再来介绍另一个比较常用的工具类型，即 `Partial`。以下是未使用 `Partial` 的例子：

```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}

interface OptionsUpdate {
  width?: number;
  height?: number;
  color?: string;
  label?: string;
}

class UIWidget {
  constructor(init: Options) {
    /* ... */
  }
  update(options: OptionsUpdate) {
    /* ... */
  }
}

```

在以上示例中，我们定义了 Options 和 OptionsUpdate 两个接口，它们分别用于描述 UIWidget 的初始化配置项和更新配置项。相比初始化配置项，更新配置项的所有属性都是可选的。

现在我们来开始优化上述的代码，我们先来看一下不使用 `Partial` 的情形：

```ts
type OptionsUpdate = {[k in keyof Options]?: Options[k]};

```

keyof 操作符接受一个类型，并返回一个由 key 组成的联合类型：

```ts
type OptionsKeys = keyof Options;
// Type is "width" | "height" | "color" | "label"

```

而 `in` 操作符是用来遍历枚举类型或联合类型。接着，我们来看一下使用 `Partial` 的情形：

```ts
class UIWidget {
  constructor(init: Options) { /* ... */ } 
  update(options: Partial<Options>) { /* ... */ }
}

```

其实 `Partial` 并没有什么神奇的地方，我们来看一下它的定义：

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};

```

在以上代码中，首先通过 `keyof T` 拿到 `T` 的所有属性名，然后使用 `in` 进行遍历，将值赋给 `P`，最后通过 `T[P]` 取得相应的属性类型。中间的 `?` 号，用于将所有属性变为可选。

有时候，你可能还会发现自己想要定义一个类型来匹配一个初始配置项的形状，比如：

```ts
const INIT_OPTIONS = {
  width: 640,
  height: 480,
  color: "#00FF00",
  label: "VGA",
};

interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}

```

对于 Options 接口来说，我们还可以使用 typeof 操作符来快速定义该接口类型：

```ts
type Options = typeof INIT_OPTIONS;

```

此外，在使用可辨识联合（代数数据类型或标签联合类型）的过程中，也可能出现重复代码。比如：

```ts
interface SaveAction { 
  type: 'save';
  // ...
}

interface LoadAction {
  type: 'load';
  // ...
}

type Action = SaveAction | LoadAction;
type ActionType = 'save' | 'load'; // Repeated types!

```

为了避免重复定义 `'save'` 和 `'load'`，我们可以使用前面提到的成员访问语法，来提取对象中属性的类型：

```ts
type ActionType = Action['type']; // 类型是 "save" | "load"

```

这里需要注意的是，`Action['type']` 返回的是联合类型，而如果我们使用前面介绍的 `Pick` 工具类型，它会返回一个含有 type 属性的接口：

```ts
type ActionRec = Pick<Action, 'type'>; // {type: "save" | "load"}

```

本文通过一些简单的示例，介绍了在 TypeScript 开发过程中如何减少重复代码，其实除了文中介绍了 `Pick` 和 `Partial` 之外，TypeScript 团队还为我们开发者提供了很多工具类型，可用于减少重复代码和提高开发效率


