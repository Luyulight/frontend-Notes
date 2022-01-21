```tsx
//constructor约束
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
//类接口
interface ClockInterface {
    tick();
}

// 类工厂函数的构造器 
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}
//对类的实现
class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}

class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```



## 在泛型里使用类类型

```typescript
//简写
class BeeKeeper {
  hasMask: boolean;
}

class ZooKeeper {
  nametag: string;
}

class Animal1 {
  numLegs: number;
}

class Bee extends Animal1 {
  keeper: BeeKeeper;
}

class Lion extends Animal1 {
  keeper: ZooKeeper;
}

function createInstance<A extends Animal1>(c: new () => A): A {
  return new c();
}

//这样是可以通过类型检查的
createInstance(Lion).keeper.nametag;  // typechecks!
createInstance(Bee).keeper.hasMask;   // typechecks!
```

```
1.import {Context} from "midway"

```

```

```

## TypeScript中的工具类（Utility Types）

https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype

### `Partial<T>`

返回一个新的类型，代表T类型的任意子集

```typescript
interface Todo {
  title: string;
  description: string;
}
 
function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}
 
const todo1 = {
  title: "organize desk",
  description: "clear clutter",
};
 
const todo2 = updateTodo(todo1, {
  description: "throw out trash",
});
```

### `Required<T>`

返回一个新的类型，包含T类型所有属性并且都required

```typescript
interface Props {
  a?: number;
  b?: string;
}
 
const obj: Props = { a: 5 };
 
const obj2: Required<Props> = { a: 5 };
//error! Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```

### `Readonly<T>`

返回一个新的类型，包含T类型的所有属性并且都是readonly

类似于js中的Object.freeze

```typescript
interface Todo {
  title: string;
}
 
const todo: Readonly<Todo> = {
  title: "Delete inactive users",
};
 
todo.title = "Hello";
//error! Cannot assign to 'title' because it is a read-only property.
```

### `Record<Keys, T>`

返回一个新的类型，该类型的属性名为Keys中的每个类型，属性的类型均为T的类型

```typescript
interface CatInfo {
  age: number;
  breed: string;
}
 
type CatName = "miffy" | "boris" | "mordred";
 
const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" },
};
 
cats.boris;
// const cats: Record<CatName, CatInfo>
```

### `Pick<T, Keys>`

返回一个新的类型，类型属性为 keys中的联合属性 存在于T类型中的属性

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}
 
type TodoPreview = Pick<Todo, "title" | "completed">;
 
const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};
 
todo;
//const todo:TodoPreview
```

### `Omit<T, Keys>`

返回一个新的类型，类型属性为T中除去Keys中属性外的其他属性 

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}
 
type TodoPreview = Omit<Todo, "description">;
 
const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
  createdAt: 1615544252770,
};
 
todo;
 
const todo: TodoPreview
 
type TodoInfo = Omit<Todo, "completed" | "createdAt">;
 
const todoInfo: TodoInfo = {
  title: "Pick up kids",
  description: "Kindergarten closes at 5pm",
};
 
todoInfo;
   
// const todoInfo: TodoInfo
```

### `Exclude<T, ExcludeUnion>`

构建一个类型，类型为T中去除所有ExcludeUnion中匹配的类型

```typescript
type T0 = Exclude<"a" | "b" | "c", "a">;    
//type T0 = "b" | "c"

type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
//type T1 = "c"

type T2 = Exclude<string | number | (() => void), Function>;
//type T2 = string | number
```

### `Extract<T, Union>`

类似exclude,取T和Union的交集

```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">;   
//type T0 = "a"

type T1 = Extract<string | number | (() => void), Function>;     
//type T1 = () => void
```

### `NonNullable<T>`

新类型中去除T类型中的null和undefined

```typescript
type T0 = NonNullable<string | number | undefined>;
//type T0 = string | number

type T1 = NonNullable<string[] | null | undefined>;
//type T1 = string[]
```

### `Parameters<T>`

T是一个函数类型的定义，返回一个类型为T的参数类型的集合

如果T不是函数类型则返回never (any类型返回unknown[ ])

```typescript
declare function f1(arg: { a: number; b: string }): void;
 
type T0 = Parameters<() => string>;
//type T0 = []

type T1 = Parameters<(s: string) => void>;
//type T1 = [s: string]

type T2 = Parameters<<T>(arg: T) => T>;
//type T2 = [arg: unknown]

type T3 = Parameters<typeof f1>;
//type T3 = [arg: {
//    a: number;
//    b: string;
//}]
                     
type T4 = Parameters<any>;
//type T4 = unknown[]
                     
type T5 = Parameters<never>; 
//type T5 = never
                     
type T6 = Parameters<string>;
//Type 'string' does not satisfy the constraint '(...args: any) => any'.   
//type T6 = never
                     
type T7 = Parameters<Function>;
//Type 'Function' does not satisfy the constraint '(...args: any) => any'.
//Type 'Function' provides no match for the signature '(...args: any): any'.    
//type T7 = never
```

### `ConstructorParameters<T>`

类似Parameter,返回一些Constructor的参数类型

```typescript
type T0 = ConstructorParameters<ErrorConstructor>;  
//type T0 = [message?: string]

type T1 = ConstructorParameters<FunctionConstructor>;    
//type T1 = string[]

type T2 = ConstructorParameters<RegExpConstructor>;    
//type T2 = [pattern: string | RegExp, flags?: string]

type T3 = ConstructorParameters<any>;
//type T3 = unknown[]
 
type T4 = ConstructorParameters<Function>;
//Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
//Type 'Function' provides no match for the signature 'new (...args: any): any'.   
//type T4 = never
```

### `ReturnType<T>`

类似Parameter,T是一个Function的type定义，返回Function的返回值的类型

不是T函数定义则返回any (never返回never)

```typescript
declare function f1(): { a: number; b: string };
 
type T0 = ReturnType<() => string>;   
//type T0 = string

type T1 = ReturnType<(s: string) => void>;  
//type T1 = void

type T2 = ReturnType<<T>() => T>;
//type T2 = unknown

type T3 = ReturnType<<T extends U, U extends number[]>() => T>;
//type T3 = number[]

type T4 = ReturnType<typeof f1>;
//type T4 = {
//    a: number;
//   b: string;
//}
                     
type T5 = ReturnType<any>; 
//type T5 = any
                     
type T6 = ReturnType<never>;  
//type T6 = never
                     
type T7 = ReturnType<string>;
//Type 'string' does not satisfy the constraint '(...args: any) => any'.    
//type T7 = any
                     
type T8 = ReturnType<Function>;
//Type 'Function' does not satisfy the constraint '(...args: any) => any'.
//Type 'Function' provides no match for the signature '(...args: any): any'. 
//type T8 = any
```

### `InstanceType<T>`

返回一个由 T中构造函数的实例类型组成的类型

```typescript
class C {
  x = 0;
  y = 0;
}
type T0 = InstanceType<typeof C>;  
//type T0 = C
                       
type T1 = InstanceType<any>;
//type T1 = any
                       
type T2 = InstanceType<never>;
//type T2 = never
                       
type T3 = InstanceType<string>;
//Type 'string' does not satisfy the constraint 'abstract new (...args: any) => any'.
//type T3 = any
                       
type T4 = InstanceType<Function>;
//Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'.
//Type 'Function' provides no match for the signature 'new (...args: any): any'.  
//type T4 = any
```

### `ThisParameterType<T>`

T是一个函数类型，返回T的this的类型，如果T没有this则返回unknown

```typescript
function toHex(this: Number) {
  return this.toString(16);
}
 
function numberToString(n: ThisParameterType<typeof toHex>) {
  return toHex.apply(n);
}
```

### `OmitThisParameter<T>`

Removes the [`this`](https://www.typescriptlang.org/docs/handbook/functions.html#this-parameters) parameter from `Type`. If `Type` has no explicitly declared `this` parameter, the result is simply `Type`. Otherwise, a new function type with no `this` parameter is created from `Type`. Generics are erased and only the last overload signature is propagated into the new function type.

```typescript
function toHex(this: Number) {
  return this.toString(16);
}
 
const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);
 
console.log(fiveToHex());
// "5"
```

### `ThisType<T>`

This utility does not return a transformed type. Instead, it serves as a marker for a contextual [`this`](https://www.typescriptlang.org/docs/handbook/functions.html#this) type. Note that the [`noImplicitThis`](https://www.typescriptlang.org/tsconfig#noImplicitThis) flag must be enabled to use this utility.

```typescript
type ObjectDescriptor<D, M> = {
  data?: D;
  methods?: M & ThisType<D & M>; // Type of 'this' in methods is D & M
};
 
function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {};
  let methods: object = desc.methods || {};
  return { ...data, ...methods } as D & M;
}
 
let obj = makeObject({
  data: { x: 0, y: 0 },
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx; // Strongly typed this
      this.y += dy; // Strongly typed this
    },
  },
});
 
obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

In the example above, the `methods` object in the argument to `makeObject` has a contextual type that includes `ThisType<D & M>` and therefore the type of [this](https://www.typescriptlang.org/docs/handbook/functions.html#this) in methods within the `methods` object is `{ x: number, y: number } & { moveBy(dx: number, dy: number): number }`. Notice how the type of the `methods` property simultaneously is an inference target and a source for the `this` type in methods.

The `ThisType<T>` marker interface is simply an empty interface declared in `lib.d.ts`. Beyond being recognized in the contextual type of an object literal, the interface acts like any empty interface.

## Intrinsic String Manipulation Types

### `Uppercase<StringType>`

### `Lowercase<StringType>`

### `Capitalize<StringType>`

### `Uncapitalize<StringType>`



对object的key做处理的泛型怎么写

```typescript
interface A {
    name: string
}

interface B {
    title: string
}

function changeKey<A, B>(
    source: A,
    keyPair: {
        key: keyof A
        newKey: keyof B
    }[]
): B {
    const b = {} as B
    Object.entries(source).forEach(([k, v]) => {
        const kp = keyPair.find(({ key }) => key === k)
        if (kp) {
            Object.assign(b, {
                [kp.newKey]: v,
            })
        } else {
            b[k] = v
        }
    })

    return b
}
```

