---
title: Typescirpt教程详解
date: 2019-11-24 13:54:29
tags:
- TS
- 类型声明
- 超集
comments: true
type: "Typescirpt"
categories: 
- Typescript

---
# Typescript

#### 定义：

 [TypeScript](http://www.typescriptlang.org/) 是 JavaScript 的一个超集，主要提供了**类型系统**和**对 ES6 的支持**， 
<!-- more -->


### 基础
# excerpt_link: more

#### 原始数据类型

#### 布尔值

>  布尔值是最基础的数据类型，在 TypeScript 中，使用 `boolean` 定义布尔值类型： 

```javascript
let isDone: boolean = false;

// 编译通过
// 后面约定，未强调编译错误的代码片段，默认为编译通过

```

#### 数值

> 使用 `number` 定义数值类型： 

```javascript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法
let octalLiteral: number = 0o744;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

> 编译结果

```javascript
var decLiteral = 6;
var hexLiteral = 0xf00d;
// ES6 中的二进制表示法
var binaryLiteral = 10;
// ES6 中的八进制表示法
var octalLiteral = 484;
var notANumber = NaN;
var infinityNumber = Infinity;
```

----

#### 字符串

> 使用 `string` 定义字符串类型： 

```javascript
let myName: string = 'Tom';
let myAge: number = 25;

// 模板字符串
let sentence: string = `Hello, my name is ${myName}.
I'll be ${myAge + 1} years old next month.`;
```

----

#### 空值

> JavaScript 没有空值（Void）的概念，在 TypeScript 中，可以用 `void` 表示没有任何返回值的函数： 

```javascript
function alertName(): void {
    alert('My name is Tom');
}
声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null：
let unusable: void = undefined;
```

----

#### null和undefined

> 在 TypeScript 中，可以使用 `null` 和 `undefined` 来定义这两个原始数据类型： 

```javascript
let u: undefined = undefined;
let n: null = null;

与 void 的区别是，undefined 和 null 是所有类型的子类型。也就是说 undefined 类型的变量，可以赋值给 number：

// 这样不会报错
let num: number = undefined;

// 这样也不会报错
let u: undefined;
let num: number = u;

而 void 类型的变量不能赋值给 number 类型的变量

let u: void;
let num: number = u;

// Type 'void' is not assignable to type 'number'.
```

----

#### 任意值

> 任意值（Any）用来表示允许赋值为任意类型。

##### 什么是任意类型的值

- 如果是一个普通类型，在赋值过程中改变类型是不被允许的：

```javascript
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

-  但如果是 `any` 类型，则允许被赋值为任意类型。 

```javascript
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

##### 任意值的属性和方法

> 在任意值上访问任何属性都是允许的： 

```javascript
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
```

> 也允许调用任何方法

```javascript
let anyThing: any = 'Tom';
anyThing.setName('Jerry');
anyThing.setName('Jerry').sayHello();
anyThing.myName.setFirstName('Cat');
```

-  可以认为，**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值**。 

##### 未声明类型的变量

> 变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型 

```javascript
let something;
something = 'seven';
something = 7;

something.setName('Tom');

等价于

let something: any;
something = 'seven';
something = 7;

something.setName('Tom');
```

----

#### 类型推论

> 如果没有明确的指定类型，那么Typescript会依照类型推论 （Type Inference） 的规则推断出一个类型。

##### 什么是类型推论

```javascript
以下代码虽然没有指定类型，但是会在编译的时候报错：
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.

等价于


let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

- TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

######  **如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成** `any` **类型而完全不被类型检查**： 

```javascript
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

----

#### 联合类型

>联合类型（Union Types）表示取值可以为多种类型中的一种。

```javascript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

联合类型使用 | 分隔每个类型。
```

-  这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。 

##### 访问联合类型的属性和方法

> 当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**： 

```javascript
function getLength(something: string | number): number {
    return something.length;
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.

上例中，length 不是 string 和 number 的共有属性，所以会报错。
```

> 访问 `string` 和 `number` 的共有属性是没问题的： 

```javascript
function getString(something: string | number): string {
    return something.toString();
}
```

> 联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型： 

```javascript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错

// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

- 上例中，第二行的 `myFavoriteNumber` 被推断成了 `string`，访问它的 `length` 属性不会报错。

  而第四行的 `myFavoriteNumber` 被推断成了 `number`，访问它的 `length` 属性时就报错了。

#### 对象的类型-接口

----

> 在 TypeScript 中，我们使用接口（Interfaces）来定义对象的类型。 

##### 什么是接口

- 在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

  TypeScript 中的接口是一个非常灵活的概念，除了可用于[对类的一部分行为进行抽象]()以外，也常用于对「对象的形状（Shape）」进行描述。

```javascript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

-  定义的变量比接口少了一些属性是不允许的： 

```typescript
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom'
};

// index.ts(6,5): error TS2322: Type '{ name: string; }' is not assignable to type 'Person'.
//   Property 'age' is missing in type '{ name: string; }'.
```

-  可见，**赋值的时候，变量的形状必须和接口的形状保持一致**。 

##### 可选属性

> 有时我们希望不要完全匹配一个形状，那么可以用可选属性：

```typescript
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom'
};

interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
可选属性的含义是该属性可以不存在。

这时仍然不允许添加未定义的属性：
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// examples/playground/index.ts(9,5): error TS2322: Type '{ name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Object literal may only specify known properties, and 'gender' does not exist in type 'Person'.
```

##### 任意属性

> 有时候我们希望一个接口允许有任意的属性，可以使用如下方式：

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};

使用 [propName: string] 定义了任意属性取 string 类型的值。
```

-  需要注意的是，**一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集** :

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

- 上例中，任意属性的值允许是 `string`，但是可选属性 `age` 的值却是 `number`，`number` 不是 `string` 的子属性，所以报错了。

##### 只读属性

> 有时候我们希望对象中的一些字段只能在创建的时候被赋值，那么可以用 ** `readonly` ** 定义只读属性：

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527;

// index.ts(14,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.
上例中，使用 readonly 定义的属性 id 初始化后，又被赋值了，所以报错了。
```

-  **注意，只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**： 

#### 数组的类型

----

>在 TypeScript 中，数组类型有多种定义方式，比较灵活。

##### 「类型 + 方括号」表示法

```typescript
1/最简单的方法是使用「类型 + 方括号」来表示数组：
let a:number=[1,2,3,4,5]

2/数组的项中不允许出现其他的类型：
let fibonacci: number[] = [1, '1', 2, 3, 5];

// Type 'string' is not assignable to type 'number'.

3/数组的一些方法的参数也会根据数组在定义时约定的类型进行限制：
let fibonacci: number[] = [1, 1, 2, 3, 5];
fibonacci.push('8');

// Argument of type '"8"' is not assignable to parameter of type 'number'.
```

-  上例中，`push` 方法只允许传入 `number` 类型的参数，但是却传了一个 `"8"` 类型的参数，所以报错了。这里 `"8"` 是一个字符串字面量类型，会在后续章节中详细介绍。 

##### 数组的泛型

> 我们也可以使用数组泛型（Array Generic） `Array` 来表示数组：

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

##### 用接口表示数组

```typescript
接口也可以用来描述数组：
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

- `NumberArray` 表示：只要索引的类型是数字时，那么值的类型必须是数字。(虽然接口也可以用来描述数组，但是我们一般不会这么做，因为这种方式比前两种方式复杂多了。)

- 不过有一种情况例外，那就是它常用来表示类数组。

##### 类数组

> 类数组（Array-like Object）不是数组类型，比如 `arguments`：

```typescript
function sum() {
    let args: number[] = arguments;
}

// Type 'IArguments' is missing the following properties from type 'number[]': pop, push, concat, join, and 24 more
```

- 上例中，`arguments` 实际上是一个类数组，不能用普通的数组的方式来描述，而应该用接口：

```typescript
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
```

- 在这个例子中，我们除了约束当索引的类型是数字时，值的类型必须是数字之外，也约束了它还有 `length` 和 `callee` 两个属性。

  事实上常用的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等：

```typescript
function sum() {
    let args: IArguments = arguments;
}
```

- 其中 `IArguments` 是 TypeScript 中定义好了的类型，它实际上就是：

```typescript
interface IArguments {
    [index: number]: any;
    length: number;
    callee: Function;
}
```

#### any在数组中的应用

- 一个比较常见的做法是，用 `any` 表示数组中允许出现任意类型：

```typescript
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];
```

#### 函数的类型

----

>[函数是 JavaScript 中的一等公民](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch2.html)

##### 函数的声明

> 在 JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表达式（Function Expression）：

- 一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到，其中函数声明的类型定义较简单：

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
```

注意，**输入多余的（或者少于要求的）参数，是不被允许的**：

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
sum(1, 2, 3);

// index.ts(4,1): error TS2346: Supplied parameters do not match any signature of call target.

function sum(x: number, y: number): number {
    return x + y;
}
sum(1);

// index.ts(4,1): error TS2346: Supplied parameters do not match any signature of call target
```

##### 函数表达式

```typescript
如果要我们现在写一个对函数表达式（Function Expression）的定义，可能会写成这样：

let mySum = function (x: number, y: number): number {
    return x + y;
};

这是可以通过编译的，不过事实上，上面的代码只对等号右侧的匿名函数进行了类型定义，而等号左边的 mySum，是通过赋值操作进行类型推论而推断出来的。如果需要我们手动给 mySum 添加类型，则应该是这样：

let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
```

-  注意不要混淆了 TypeScript 中的 `=>` 和 ES6 中的 `=>`。 

##### 用接口定义函数的形状

```typescript
我们也可以使用接口的方式来定义一个函数需要符合的形状：

interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

##### 可选参数

>前面提到，输入多余的（或者少于要求的）参数，是不允许的。那么如何定义可选的参数呢？

> 与接口中的可选属性类似，我们用 `?` 表示可选的参数：

```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

- 需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**：

```typescript
function buildName(firstName?: string, lastName: string) {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName(undefined, 'Tom');

// index.ts(1,40): error TS1016: A required parameter cannot follow an optional parameter.
```

##### 参数默认值

> 在 ES6 中，我们允许给函数的参数添加默认值，**TypeScript 会将添加了默认值的参数识别为可选参数**：

```typescript
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

> 此时就不受「可选参数必须接在必需参数后面」的限制了：

```typescript
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

##### 剩余参数

```typescript
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```

-  注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 [ES6 中的 rest 参数](http://es6.ruanyifeng.com/#docs/function#rest参数)。 

##### 重载

- 重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

-  比如，我们需要实现一个函数 `reverse`，输入数字 `123` 的时候，输出反转的数字 `321`，输入字符串 `'hello'` 的时候，输出反转的字符串 `'olleh'`。 

```typescript
利用联合类型，我们可以这么实现：
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。
这时，我们可以使用重载定义多个 reverse 的函数类型：

function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
上例中，我们重复定义了多次函数 reverse，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。

注意，TypeScript 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。
```

#### 类型断言

----

```typescript
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}

```

- 类型断言的用法如上，在需要断言的变量前加上<type> 即可。
- **类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的**：

```typescript
function toBoolean(something: string | number): boolean {
    return <boolean>something;
}

// index.ts(2,10): error TS2352: Type 'string | number' cannot be converted to type 'boolean'.
//   Type 'number' is not comparable to type 'boolean'.
```

#### 内置对象

----

> JavaScript 中有很多[内置对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)，它们可以直接在 TypeScript 中当做定义好了的类型。

>内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

##### ECMAscript的内置对象

- ECMAScript 标准提供的内置对象有：
- `Boolean`、`Error`、`Date`、`RegExp` 等。

```typescript
我们可以在 TypeScript 中将变量定义为这些类型：
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

##### DOM和BOM的内置对象

- DOM 和 BOM 提供的内置对象有：
- `Document`、`HTMLElement`、`Event`、`NodeList` 等。

```typescript
TypeScript 中会经常用到这些类型：
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

#### 声明文件

----

### 进阶

----

#### 类型别名

>类型别名用来给一个类型起个新名字。

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
类型别名常用于联合类型。
```

#### 字符串字面量类型

----

>字符串字面量类型用来约束取值只能是某几个字符串中的一个。

```typescript
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 不能为 'dbclick'

// index.ts(7,47): error TS2345: Argument of type '"dbclick"' is not assignable to parameter of type 'EventNames'
上例中，我们使用 type 定了一个字符串字面量类型 EventNames，它只能取三种字符串中的一种。
```

* 注意，**类型别名与字符串字面量类型都是使用** `type` **进行定义。**

#### 元组

----

> 数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象
>
> 元组起源于函数编程语言，这些语言中会频繁使用元组。

```typescript
简单的例子
定义一对值分别为 string 和 number 的元组：
let tom: [string, number] = ['Tom', 25];
当赋值或访问一个已知索引的元素时，会得到正确的类型：
let tom: [string, number];
tom[0] = 'Tom';
tom[1] = 25;

tom[0].slice(1);
tom[1].toFixed(2);

也可以只赋值其中一项：
let tom: [string, number];
tom[0] = 'Tom';
```

-  但是当直接对元组类型的变量进行初始化或者赋值的时候，需要提供所有元组类型中指定的项。 

```typescript
let tom: [string, number];
tom = ['Tom', 25];

let tom: [string, number];
tom = ['Tom'];

// Property '1' is missing in type '[string]' but required in type '[string, number]'
```

##### 越界的元素

> 当添加越界的元素时，它的类型会被限制为元组中每个类型的联合类型：

```typescript
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true);

// Argument of type 'true' is not assignable to parameter of type 'string | number'
```

### 枚举

----

> 枚举（Enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

### 类

----

- 类(Class)：定义了一件事物的抽象特点，包含它的属性和方法

- 对象（Object）：类的实例，通过 `new` 生成

- 面向对象（OOP）的三大特性：封装、继承、多态

- 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据

- 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性

- 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`

- 存取器（getter & setter）：用以改变属性的读取和赋值行为

- 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法

- 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现

- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

####  ES7 中类的用法 

#####  实例属性 :

> ES6 中实例的属性只能通过构造函数中的 `this.xxx` 来定义，ES7 提案中可以直接在类里面定义： 

```javascript
class Animal {
    name = 'Jack';

    constructor() {
        // ...
    }
}

let a = new Animal();
console.log(a.name); // Jack
```

##### 静态属性

> ES7 提案中，可以使用 `static` 定义一个静态属性： 

```javascript
class Animal {
    static num = 42;

    constructor() {
        // ...
    }
}

console.log(Animal.num); // 42
```

####  TypeScript 中类的用法 

##### public/p'rivate和protected

> TypeScript 可以使用三种访问修饰符（Access Modifiers），分别是 `public`、`private` 和 `protected`。 

- `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的

- `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问

- `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的

```typescript
class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom

上面的例子中，name 被设置为了 public，所以直接访问实例的 name 属性是允许的。
```

```typescript

很多时候，我们希望有的属性是无法直接存取的，这时候就可以用 private 了：
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

> 使用 `private` 修饰的属性或方法，在子类中也是不允许访问的：

```typescript
class Animal {
    private name;
    public constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor(name) {
        super(name);
        console.log(this.name);
    }
}

// index.ts(11,17): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

> 而如果是用 `protected` 修饰，则允许在子类中访问： 

```typescript
class Animal{
    protected name;
    public constructor(name){
    	this.name=name
    }
}
class Cat extends Animal{
	constructor(name){
		super(name)
		console.log(this.name)
	}
}
```

> 当构造函数修饰为 `private` 时，该类不允许被继承或者实例化：

```typescript

class Animal {
    public name;
    private constructor (name) {
        this.name = name;
  }
}
class Cat extends Animal {
    constructor (name) {
        super(name);
    }
}

let a = new Animal('Jack');

// index.ts(7,19): TS2675: Cannot extend a class 'Animal'. Class constructor is marked as private.
// index.ts(13,9): TS2673: Constructor of class 'Animal' is private and only accessible within the class declaration.
```

> 当构造函数修饰为 `protected` 时，该类只允许被继承：

```typescript
class Animal {
    public name;
    protected constructor (name) {
        this.name = name;
  }
}
class Cat extends Animal {
    constructor (name) {
        super(name);
    }
}

let a = new Animal('Jack');

// index.ts(13,9): TS2674: Constructor of class 'Animal' is protected and only accessible within the class declaration.
```

> 修饰符还可以使用在构造函数参数中，等同于类中定义该属性，使代码更简洁。

```typescript
class Animal {
    // public name: string;
    public constructor (public name) {
        this.name = name;
    }
}
```

##### readonly

> 只读属性关键字，只允许出现在属性声明或索引签名中。

```typescript
class Animal {
    readonly name;
    public constructor(name) {
        this.name = name;
    }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(10,3): TS2540: Cannot assign to 'name' because it is a read-only property.
```

- 注意如果 `readonly` 和其他访问修饰符同时存在的话，需要写在其后面。

```typescript
class Animal {
    // public readonly name;
    public constructor(public readonly name) {
        this.name = name;
    }
}
```

##### 抽象类

`abstract` 用于定义抽象类和其中的抽象方法。

- 首先，抽象类是不允许被实例化的：

```typescript
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

let a = new Animal('Jack');

// index.ts(9,11): error TS2511: Cannot create an instance of the abstract class 'Animal'.
```

- 上面的例子中，我们定义了一个抽象类 `Animal`，并且定义了一个抽象方法 `sayHi`。在实例化抽象类的时候报错了。

> 其次，抽象类中的抽象方法必须被子类实现：

```typescript
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public eat() {
        console.log(`${this.name} is eating.`);
    }
}

let cat = new Cat('Tom');

// index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.

```

- 上面的例子中，我们定义了一个类 `Cat` 继承了抽象类 `Animal`，但是没有实现抽象方法 `sayHi`，所以编译报错了。

```typescript
下面是一个正确使用抽象类的例子：
abstract class Animal {
    public name;
    public constructor(name) {
        this.name = name;
    }
    public abstract sayHi();
}

class Cat extends Animal {
    public sayHi() {
        console.log(`Meow, My name is ${this.name}`);
    }
}

let cat = new Cat('Tom');
```

#### 类的类型

> 给类加上 TypeScript 的类型很简单，与接口类似：

```typescript
class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    sayHi(): string {
      return `My name is ${this.name}`;
    }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

### 类与接口

----

#### 类实现接口

> 实现（implements）是面向对象中的一个重要概念。一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用 `implements` 关键字来实现。这个特性大大提高了面向对象的灵活性。

```typescript

interface Alarm {
    alert();
}

class Door {
}

class SecurityDoor extends Door implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert() {
        console.log('Car alert');
    }
}
```

- 一个类可以实现多个接口：

```typescript
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```

上例中，`Car` 实现了 `Alarm` 和 `Light` 接口，既能报警，也能开关车灯。

#### 接口继承接口

- 接口与接口之间可以是继承关系：

```typescript
interface Alarm {
    alert();
}

interface LightableAlarm extends Alarm {
    lightOn();
    lightOff();
}
```

- 上例中，我们使用 `extends` 使 `LightableAlarm` 继承 `Alarm`。

#### 接口继承类

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```

#### 混合类型

> [之前学习过]()，可以使用接口的方式来定义一个函数需要符合的形状：

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

- 有时候，一个函数还可以有自己的属性和方法：

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

### 泛型

----

>泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

```typescript
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

-  上例中，我们在函数名后添加了 ``，其中 `T` 用来指代任意输入的类型，在后面的输入 `value: T` 和输出 `Array` 中即可使用了。 

-  接着在调用的时候，可以指定它具体的类型为 `string`。当然，也可以不手动指定，而让类型推论自动推算出来： 

```typescript
function createAarray<T>(length:number,value:T):Array<T>{
    let result:T[]=[];
    for(let i=0;i<length;i++){
        result[i]=value;
    }
    return result;
}
createArray(3,'x');//['x','x','x']
```

#### 多个类型参数

> 定义泛型的时候，可以一次定义多个类型参数：

```typescript
function fn<T,U>(a:[T,U]):[T,U]{
    return [a[1],a[0]];
}
fn(7,'seven');//['seven',7]
```

- 上例中我们定义了一个fn函数，用来交换输入的元组。

#### 泛型约束

> 在函数内部使用泛型变量的时候，由于事先不知道他是哪种类型，所以不能随意的操作他的属性或方法。

```typescript

function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);
    return arg;
}

// index.ts(2,19): error TS2339: Property 'length' does not exist on type 'T'.
```

- 上例中，泛型 `T` 不一定包含属性 `length`，所以编译的时候报错了。

> 这时，我们可以对泛型进行约束，只允许这个函数传入那些包含 `length` 属性的变量。这就是泛型约束：

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

-  上例中，我们使用了 `extends` 约束了泛型 `T` 必须符合接口 `Lengthwise` 的形状，也就是必须包含 `length` 属性。 

> 此时如果调用 `loggingIdentity` 的时候，传入的 `arg` 不包含 `length`，那么在编译阶段就会报错了：

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}

loggingIdentity(7);

// index.ts(10,17): error TS2345: Argument of type '7' is not assignable to parameter of type 'Lengthwise'.
```

```typescript
多个类型参数之间也可以互相约束：
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });

上例中，我们使用了两个类型参数，其中要求 T 继承 U，这样就保证了 U 上不会出现 T 中不存在的字段。
```

#### 泛型接口

-  当然也可以使用含有泛型的接口来定义函数的形状： 

```typescript
interface CreateArrayFunc {
    <T>(length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```

-  进一步，我们可以把泛型参数提前到接口名上： 

```typescript
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
注意，此时在使用泛型接口的时候，需要定义泛型的类型。
```

#### 泛型类

> 与泛型接口类似，泛型也可以用于类的类型定义中：

```typescript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

#### 泛型参数的默认类型

> 在 TypeScript 2.3 以后，我们可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。 

```typescript
function createArray<T = string>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
```

### 声明合并

----

>如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型：

#### 函数的合并

> [之前学习过]()，我们可以使用重载定义多个函数类型：

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

#### 接口的合并

> 接口中的属性在合并时会简单的合并到一个接口中：

```typescript
interface Alarm {
    price: number;
}
interface Alarm {
    weight: number;
}
相当于
interface Alarm {
    price: number;
    weight: number;
}
```

 注意，**合并的属性的类型必须是唯一的**： 

```typescript
interface Alarm {
    price: number;
}
interface Alarm {
    price: number;  // 虽然重复了，但是类型都是 `number`，所以不会报错
    weight: number;
}


interface Alarm {
    price: number;
}
interface Alarm {
    price: string;  // 类型不一致，会报错
    weight: number;
}

// index.ts(5,3): error TS2403: Subsequent variable declarations must have the same type.  Variable 'price' must be of type 'number', but here has type 'string'.
```

 接口中方法的合并，与函数的合并一样： 

```typescript
interface Alarm {
    price: number;
    alert(s: string): string;
}
interface Alarm {
    weight: number;
    alert(s: string, n: number): string;
}
相当于：
interface Alarm {
    price: number;
    weight: number;
    alert(s: string): string;
    alert(s: string, n: number): string;
}
```

#### 类的合并

类的合并与接口的合并规则一致。


## 参考文献

Typescript中文教程：https://www.tslang.cn/docs/home.html
阮一峰Typescript入门教程： https://ts.xcatliu.com/
