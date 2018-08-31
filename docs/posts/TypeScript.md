# 简介
## TypeScript

TypeScript 是 JavaScript 的一个超集，主要提供了类型系统和对 ES6 的支持

优缺点：[https://ts.xcatliu.com/introduction/what-is-typescript.html]

# 基础
## 基本类型

boolean, Boolean, undefined, null, void

## 任意类型: any

允许被赋值为任意类型，可以访问任意属性（和调用）

变量如果在声明的时候未指定类型，就会被识别为任意类型（那不是和类型推论冲突了吗?）

## 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型，接下来就有可能因为类型的原因而报错

如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查

## 联合类型

表示取值可以为多种类型中的一种，使用 '|' 分隔

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法（如果变量已经被赋值了的话就可以推断出一个类型）

## 接口

用于对类的一部分行为进行抽象，以及对对象的形状进行描述，新定义的变量必须在属性上按照规则保持一致

```ts 
interface Person {
    name: string;
    age?: number; // 可选属性
    [propName: string]: any; // 任意属性
    readonly id: number; // 只读属性
}
```

一旦定义了任意属性，那么确定属性和可选属性都必须是它的子属性

只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候

## 数组的类型

- 「类型 + 方括号」：(number | string)[]
- 数组泛型：Array<number>
- 接口：`interface NumberArray { [index: number]: number; }`

类数组需要使用自己对应的接口（IArguments, NodeList, HTMLCollection）




