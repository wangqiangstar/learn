### 一、类型别名 

TypeScript 提供了为类型注解设置别名的便捷语法，你可以使用 `type SomeName = someValidTypeAnnotation` 来创建别名，比如：

```ts
type Pet = 'cat' | 'dog';
let pet: Pet;

pet = 'cat'; // Ok
pet = 'dog'; // Ok
pet = 'zebra'; // Compiler error
```

### 二、基础知识

为了让大家能更好地理解并掌握 TypeScript 内置类型别名，我们先来介绍一下相关的一些基础知识。

#### 2.1 typeof

在 TypeScript 中，`typeof` 操作符可以用来获取一个变量声明或对象的类型。

```ts
interface Person {
  name: string;
  age: number;
}

const sem: Person = { name: 'semlinker', age: 30 };
type Sem= typeof sem; // -> Person

function toArray(x: number): Array<number> {
  return [x];
}

type Func = typeof toArray; // -> (x: number) => number[]
```

#### 

#### [继续阅读：TypeScript typeof 操作符](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484084&idx=1&sn=da6c267d8dd3f6981d1b10cb3cf37254&chksm=ea47a3ecdd302afa38fb56060d2a3e81513b51197cdfaca15b9db1a5dd95cb2a85c06cf8652f&scene=21#wechat_redirect)

#### 2.2 keyof

`keyof` 操作符可以用来一个对象中的所有 key 值：

```ts
interface Person {
    name: string;
    age: number;
}

type K1 = keyof Person; // "name" | "age"
type K2 = keyof Person[]; // "length" | "toString" | "pop" | "push" | "concat" | "join"
type K3 = keyof { [x: string]: Person };  // string | number
```

#### 

#### [继续阅读：TypeScript keyof 操作符](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484077&idx=1&sn=1215e14604232f1da0031dc3ee4f0b82&chksm=ea47a3f5dd302ae3c89633513fb8c0de72458a1915bb2a079e7961a2f4ea198afc4dc8d4c62e&scene=21#wechat_redirect) 

#### 2.3 in

`in` 用来遍历枚举类型：

```ts
type Keys = "a" | "b" | "c"

type Obj =  {
  [p in Keys]: any
} // -> { a: any, b: any, c: any }
```

#### [继续阅读：在 TS 中如何实现类型保护？类型谓词了解一下](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484114&idx=1&sn=af33c36580d21c2ffe4e8204f71c10b8&chksm=ea47a38add302a9c01c9bea63f5974554e2a9ab856f1cb3b66620a02452d12d1967fb676dc9f&scene=21#wechat_redirect)

#### 2.4 infer

在条件类型语句中，可以用 `infer` 声明一个类型变量并且对它进行使用。

```ts
type ReturnType<T> = T extends (
  ...args: any[]
) => infer R ? R : any;
```

以上代码中 `infer R` 就是声明一个变量来承载传入函数签名的返回值类型，简单说就是用它取到函数返回值的类型方便之后使用。

[**继续阅读：用上这几招，轻松实现 TS 类型提取**](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484091&idx=1&sn=ad2cb7721fb6722af8cad48caaf94209&chksm=ea47a3e3dd302af5101c7769db2dfb52d49d7541232b7713e20aa0177b7a55412de059687f4c&scene=21#wechat_redirect)

#### 2.5 extends

有时候我们定义的泛型不想过于灵活或者说想继承某些类等，可以通过 extends 关键字添加泛型约束。

```ts
interface ILengthwise {
  length: number;
}

function loggingIdentity<T extends ILengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

现在这个泛型函数被定义了约束，因此它不再是适用于任意类型：

```ts
loggingIdentity(3);  // Error, number doesn't have a .length property
```

这时我们需要传入符合约束类型的值，必须包含必须的属性：

```ts
loggingIdentity({length: 10, value: 3});
```

### 三、内置类型别名

#### 3.1 Partial

`Partial<T>` 的作用就是将某个类型里的属性全部变为可选项 `?`。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Make all properties in T optional
 */
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

在以上代码中，首先通过 `keyof T` 拿到 `T` 的所有属性名，然后使用 `in` 进行遍历，将值赋给 `P`，最后通过 `T[P]` 取得相应的属性值。中间的 `?`，用于将所有属性变为可选。

**示例：**

```ts
interface Todo {
  title: string;
  description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
  title: "organize desk",
  description: "clear clutter"
};

const todo2 = updateTodo(todo1, {
  description: "throw out trash"
});
```

#### 3.2 Required

`Required<T>` 的作用就是将某个类型里的属性全部变为必选项。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

以上代码中，`-?` 的作用就是移除可选项 `?`。

**示例：**

```ts
interface Props {
  a?: number;
  b?: string;
}

const obj: Props = { a: 5 }; // OK
const obj2: Required<Props> = { a: 5 }; // Error: property 'b' missing
```

#### 3.3 Readonly

`Readonly<T>` 的作用是将某个类型所有属性变为只读属性，也就意味着这些属性不能被重新赋值。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Make all properties in T readonly
 */
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

如果将上面的 `readonly` 改成 `-readonly`， 就是移除子属性的 `readonly` 标识。

**示例：**

```ts
interface Todo {
  title: string;
}

const todo: Readonly<Todo> = {
  title: "Delete inactive users"
};

todo.title = "Hello"; // Error: cannot reassign a readonly property
```

`Readonly<T>` 对于表示在运行时将赋值失败的表达式很有用（比如，当尝试重新赋值冻结对象的属性时）。

```ts
function freeze<T>(obj: T): Readonly<T>;
```

#### 3.4 Record

`Record<K extends keyof any, T>` 的作用是将 `K` 中所有的属性的值转化为 `T` 类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Construct a type with a set of properties K of type T
 */
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

**示例：**

```ts
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const x: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" }
};
```

#### 3.5 Pick

`Pick<T, K extends keyof T>` 的作用是将某个类型中的子属性挑出来，变成包含这个类型部分属性的子类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

**示例：**

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false
};
```

#### 3.6 Exclude

`Exclude<T, U>` 的作用是将某个类型中属于另一个的类型移除掉。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Exclude from T those types that are assignable to U
 */
type Exclude<T, U> = T extends U ? never : T;
```

如果 `T` 能赋值给 `U` 类型的话，那么就会返回 `never` 类型，否则返回 `T` 类型。最终实现的效果就是将 `T` 中某些属于 `U` 的类型移除掉。

**示例：**

```ts
type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // "c"
type T2 = Exclude<string | number | (() => void), Function>; // string | number
```

#### 3.7 Extract

`Extract<T, U>` 的作用是从 `T` 中提取出 `U`。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Extract from T those types that are assignable to U
 */
type Extract<T, U> = T extends U ? T : never;
```

如果 `T` 能赋值给 `U` 类型的话，那么就会返回 `T` 类型，否则返回 `never` 类型。

**示例：**

```ts
type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
type T1 = Extract<string | number | (() => void), Function>; // () =>void
```

#### 3.8 Omit

`Omit<T, K extends keyof any>` 的作用是使用 `T` 类型中除了 `K` 类型的所有属性，来构造一个新的类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Construct a type with the properties of T except for those in type K.
 */
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

**示例：**

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false
};
```

#### 3.9 NonNullable

`NonNullable<T>` 的作用是用来过滤类型中的 `null` 及 `undefined` 类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Exclude null and undefined from T
 */
type NonNullable<T> = T extendsnull | undefined ? never : T;
```

**示例：**

```ts
type T0 = NonNullable<string | number | undefined>; // string | number
type T1 = NonNullable<string[] | null | undefined>; // string[]
```

#### 3.10 ReturnType

`ReturnType<T>` 的作用是用于获取函数 `T` 的返回类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Obtain the return type of a function type
 */
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

**示例：**

```ts
type T0 = ReturnType<() =>string>; // string
type T1 = ReturnType<(s: string) =>void>; // void
type T2 = ReturnType<<T>() => T>; // {}
type T3 = ReturnType<<T extends U, U extendsnumber[]>() => T>; // number[]
type T4 = ReturnType<any>; // any
type T5 = ReturnType<never>; // any
type T6 = ReturnType<string>; // Error
type T7 = ReturnType<Function>; // Error
```

#### [继续阅读：用上这几招，轻松实现 TS 类型提取](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484091&idx=1&sn=ad2cb7721fb6722af8cad48caaf94209&chksm=ea47a3e3dd302af5101c7769db2dfb52d49d7541232b7713e20aa0177b7a55412de059687f4c&scene=21#wechat_redirect)

#### 3.11 InstanceType

`InstanceType` 的作用是获取构造函数类型的实例类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Obtain the return type of a constructor function type
 */
type InstanceType<T extendsnew (...args: any) => any> = T extendsnew (...args: any) => infer R ? R : any;
```

**示例：**

```ts
class C {
  x = 0;
  y = 0;
}

type T0 = InstanceType<typeof C>; // C
type T1 = InstanceType<any>; // any
type T2 = InstanceType<never>; // any
type T3 = InstanceType<string>; // Error
type T4 = InstanceType<Function>; // Error
```

#### 3.12 ThisType

`ThisType<T>` 的作用是用于指定上下文对象的类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Marker for contextual 'this' type
 */
interface ThisType<T> { }
```

> 注意：使用 `ThisType<T>` 时，必须确保 `--noImplicitThis` 标志设置为 true。

**示例：**

```ts
interface Person {
    name: string;
    age: number;
}

const obj: ThisType<Person> = {
  dosth() {
    this.name // string
  }
}
```

#### [继续阅读：TypeScript 中神奇的 this 类型声明](http://mp.weixin.qq.com/s?__biz=MzI2MjcxNTQ0Nw==&mid=2247484120&idx=1&sn=6e8fccc91236c7c23934a6a857f67f2f&chksm=ea47a380dd302a967cfbc7aff9aa156d8b6a1d5b6e1ec87fc1ff35fd00492a9eb49ecf5f7283&scene=21#wechat_redirect)

#### 3.13 Parameters

`Parameters<T>` 的作用是用于获得函数的参数类型组成的元组类型。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Obtain the parameters of a function type in a tuple
 */
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any
  ? P : never;
```

**示例：**

```ts
type A = Parameters<() =>void>; // []
type B = Parameters<typeofArray.isArray>; // [any]
type C = Parameters<typeofparseInt>; // [string, (number | undefined)?]
type D = Parameters<typeofMath.max>; // number[]
```

#### 3.14 ConstructorParameters

`ConstructorParameters<T>` 的作用是提取构造函数类型的所有参数类型。它会生成具有所有参数类型的元组类型（如果 T 不是函数，则返回的是 never 类型）。

**定义：**

```ts
// node_modules/typescript/lib/lib.es5.d.ts

/**
 * Obtain the parameters of a constructor function type in a tuple
 */
type ConstructorParameters<T extendsnew (...args: any) => any> = T extendsnew (...args: infer P) => any ? P : never;
```

**示例：**

```ts
type A = ConstructorParameters<ErrorConstructor>; // [(string | undefined)?]
type B = ConstructorParameters<FunctionConstructor>; // string[]
type C = ConstructorParameters<RegExpConstructor>; // [string, (string | undefined)?]
```