# TS作用

> 1、思维方式的体现

```
面向对象  xx对象有xx方法

函数式		 Dog is Barkable<T>
						Barkable 是类，等价于接口
						T 是指泛型
```

**类型的思维方式在面向对象和函数式中是高度统一的**

1. 什么是什么

   * Integer/string is comparable

   * Array is enumerable

   * React.ComponentType is not React.ComponentClass

     ```ts
     type ComponentType<P = {}> = ComponentClass<P> | FunctionComponent<P>
     ```

2. 类型的转换思维

   ```ts
   componentClass -> Component
   
   Array<ComponentClass> -> Array<Component>
   ```

3. 演化

   ```
   eg.
   React组件由 ComponentClass 转变为 ElementType
   ElementType (ComponentClass & FunctionComponent)
   ```

   **通过类型的演进不断让系统进化** (领域驱动开发)

> 2、错误检查

设计好的类型系统可以做到编译通过即上线

---

# TS 含义

编译器分类：

* 静态编译器
  将代码编译为机器码
* 解释型编译器
  边编译边执行
* 结合动态&静态
  一边解释一遍编译为机器码，下次再执行就执行机器码

TS的编译器属于解释型编译器

TS 含义：

* JS超集
* 同时支持客户端(浏览器)和服务端(Node)
* 多范式
* 同时支持 Duck Typing(长得像)、Gradual Typing(支持部分检查)、Strict Typing

---

# 环境配置

## ts-node

REPL(Read Eval Print Loop) (在命令窗口输入 ts-node 进入环境)

Node环境的 typescript 解释执行器 

```shell
npm i -g ts-node

yarn global add ts-node
```

**使用**

1. 在命令窗口输入 `ts-node` 进入环境
2. 编译文件 `ts-node xxx`

---

## tsc

一个ts的编译器 typescript compiler

```shell
npm i -g tsc

yarn global add tsc
```

**使用**

1. 编译文件 `tsc xxx.ts`，生成 js
   `tsc` 
   需要 tsconfig.json

---

## 配合webpack使用

1. 安装依赖

   ```shell
   yarn add webpack ts-loader typescript webpack-cli
   
   # ts-loader 依赖 typescript
   ```

2. 编写用于测试的ts文件 (src/index.ts)

3. webpack.config.js

   ```js
   const path = require('path')
   module.exports = {
     entry: {
       index: './src/index.ts'
     },
     mode: 'development',
     module: {
       rules: [
         {
           test: /\.ts$/,
           use: 'ts-loader',
           exclude: /node_modules/,
         }
       ]
     },
     resolve: {
       extensions: ['.tsx', '.ts', '.js']
     },
     output: {
       filename: 'bundle.[name].js',
       path: path.resolve(__dirname, 'dist')
     }
   }
   ```

4. 在 package.json 处编写脚本

   ```json
   "start": "webpack"
   ```

5. 执行脚本

   ```shell
   npm start
   ```

6. 用 node 执行编译出来的脚本

---

## React + TS Loader

**step:**

1. 安装依赖

   ```shell
   yarn add react react-dom
   yarn add @types/react @types/react-dom -D
   yarn add awesome-typescript-loader ts-loader -D
   yarn add webpack-dev-server html-webpack -D
   ```

2. 配置 tsconfig.json

   ```json
   {
     "compilerOptions": {
       "esModuleInterop": true,	// 使多套export风格的方式能被识别
       "jsx": "react",
       
     }
   }
   ```

3. 配置 webpack.react.js

   ```js
   const path = require('path')
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   
   module.exports = {
     entry: {
       index: './src/ReactHello.ts'
     },
     mode: 'development',
     module: {
     	rules: [
     		{
     			test: /\.ts$/,
    			 	use: 'ts-loader',
           exclude: /node_modules/
   			}
     	]
   	},
     resolve: {
       extensions: ['.tsx', '.ts', '.js']
     },
     output: {
       filename: 'bundle.[name].js',
       path: path.resolve(__dirname, 'dist')
     },
     devServer: {
     	contentBase: path.resolve(__dirname, 'dist'),
       port: 3000,
     },
     plugins: [
       new HtmlWebpackPlugin({ template: path.resolve(__dirname, 'template.html') })
     ]
   }
   ```

4. 配置模板html template.html

   ```html
   <html>
     <head></head>
     <body>
       <div id="root"></div>
     </body>
   </html>
   ```

5. 配置 package.json

   ```json
   {
     "scripts": {
       "start:react": "webpack serve --config webpack.react.js" // 写上 serve 表示要启动 dev server
     }
   }
   ```

6. 执行命令

   ```shell
   yarn start:react
   ```

---

## React + babel-preset

**babel-preset 优势**

* 缓存 + 优化
* 插件 + 生态

ts-loader 背后是 tsc (ts -> es)

**step**

1. 在上一模块的安装依赖的基础上，再补充babel的依赖

   ```shell
   yarn add babel-loader @babel/core @babel/preset-env @babel/preset-react @babel/preset-typescript
   ```

   preset 是 plugin 的集合

2. 重新配置webpack  webpack.react.withbabel.js

   ```js
   const path = require('path')
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   
   module.exports = {
     entry: {
       index: './src/ReactHello.ts'
     },
     mode: 'development',
     module: {
     	rules: [
     		{
     			test: /\.tsx?$/,
    			 	use: {
             loader: 'babel-loader',
             options: {
               presets: [
                 "@babel/preset-typescript", // typescript 似乎已经有对 react 的支持了？
                 "@babel/preset-react",	// 不要这个配置的话会报错，编译器会不认识jsx语法
                 "@babel/preset-env"
               ]
             }
           },
           exclude: /node_modules/
   			}
     	]
   	},
     resolve: {
       extensions: ['.tsx', '.ts', '.js']
     },
     output: {
       filename: 'bundle.[name].js',
       path: path.resolve(__dirname, 'dist')
     },
     devServer: {
     	contentBase: path.resolve(__dirname, 'dist'),
       port: 3000,
     },
     plugins: [
       new HtmlWebpackPlugin({ template: path.resolve(__dirname, 'template.html') })
     ]
   }
   ```

3. 配置 package.json

   ```json
   {
     "scripts": {
       "satrt:react-babel": "webpack serve --config webpack.react.withbabel.js"
     }
   }
   ```

4. 其他的文件和上一模块一致

---

## Vue + loader

**step**

1. 增加vue需要的配置

   ```shell
   yarn add vue@next	# vue3的配置
   yarn add @vue/compiler-sfc -D # sfc - Single File Component (html style script放在一个文件)
   yarn add vue-loader -D # Vue的loader  这个loader的功能就是编译sfc
   ```

2. 建立 Vue 入口文件 Hello.vue

   ```vue
   <template>
   	<h1>Hello Vue!</h1>
   	<script lant="ts">
     	export default {
         setup() {
           return {}
         }
       }
     </script>
   </template>
   ```

3. main.ts

   ```ts
   import { createApp } from 'vue'
   import Hello from './Hello.vue'
   createApp(Hello).mount('#root')
   ```

4. shims-vue.d.ts
   帮助 IDE 认识 vue文件

   ```ts
   declare module '*.vue' {
     import type { DefineComponent } from 'vue'
     const component: DefineComponent<{}, {}, any>
     export default component
   }
   ```

   shim(垫片)，通常为了处理兼容而命名

5. 编辑webpack配置  webpack.vue.js

   ```js
   const path = require('path')
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   const { VueLoaderPlugin } = require('vue-laoder')
   
   module.exports = {
     entry: {
       index: './src/main.ts'
     },
     mode: 'development',
     module: {
     	rules: [
     		{
     			test: /\.ts$/,
    			 	use: 'ts-loader',
           options: {
             appendTsSuffixTo: [/\.vue$/], // 给vue这种类型的文件追加.ts的后缀
           },
           exclude: /node_modules/
   			},
         {
           test: /\.vue$/,
           loader: 'vue-loader'
         }
     	]
   	},
     resolve: {
       extensions: ['.tsx', '.ts', '.js']
     },
     output: {
       filename: 'bundle.[name].js',
       path: path.resolve(__dirname, 'dist')
     },
     devServer: {
     	contentBase: path.resolve(__dirname, 'dist'),
       port: 3000,
     },
     plugins: [
       new HtmlWebpackPlugin({ template: path.resolve(__dirname, 'template.html') }),
       new VueLoaderPlugin()
     ]
   }
   ```

6. 配置 package.json

   ```json
   {
     "scripts": {
       "start:vue": "webapck serve --config webpack.vue.js"
     }
   }
   ```

---

## Vue + babel-preset

**step**

1. 安装依赖

   ```shell
   yarn add babel-preset-typescript-vue3 -D
   ```

2. webpack.vue.withbabel.js

   ```js
   const path = require('path')
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   const { VueLoaderPlugin } = require('vue-laoder')
   
   module.exports = {
     entry: {
       index: './src/main.ts'
     },
     mode: 'development',
     module: {
     	rules: [
     		{
     			test: /\.tsx?$/,
    			 	use: 'babel-loader',
           options: {
             presets: [
               "@babel/preset-env",
               "babel-preset-typescript-vue3", // 这项去掉依然可以跑
               "@babel/preset-typescript"
             ]
           },
           exclude: /node_modules/
   			},
         {
           test: /\.vue$/,
           loader: 'vue-loader'
         }
     	]
   	},
     resolve: {
       extensions: ['.tsx', '.ts', '.js']
     },
     output: {
       filename: 'bundle.[name].js',
       path: path.resolve(__dirname, 'dist')
     },
     devServer: {
     	contentBase: path.resolve(__dirname, 'dist'),
       port: 3000,
     },
     plugins: [
       new HtmlWebpackPlugin({ template: path.resolve(__dirname, 'template.html') }),
       new VueLoaderPlugin()
     ]
   }
   ```

3. package.json

   ```json
   {
     "scripts": {
       "satrt:vue-babel": "webpack serve --config webpack.vue.withbabel.js"
     }
   }
   ```

**preset-react** 预设了一堆会用到的plugin

**preset-typescript-vue3** 只是增加了 typescript 的转化能力

plugin 的顺序取决于是否一个模块的处理是否依赖其他模块的处理结果

**实战**

建议使用脚手架 `react-create-app` `vue` `vite`

---

# tsconfig.json

```json
{
  "compilerOptions": {
    "outDir": "build"	// 此目录可在 .gitignore 添加
    "module": "common" // umd
  }
}
```

---

# TS 常规类型

> ## 基础类型

* string
* number
* boolean
* null
* undefined

> ## 数组类型

```ts
Array<T>  T代表数组中的元素类型

<> 称作钻石操作符
```



> ## any unknown noImplicitAny

```ts
let obj: any = { x: 0 }  // 后续都不会检查

obj.foo() // 执行报错，但不检查
obj() // 执行报错，但不检查
obj.bar = 100
obj = "hello"
const n: number = obj
```

implicit: 隐式  explicit: 显式

配置项：`noImplicitAny` 为 true 时，当一个变量隐式推断为any时会报错

```ts
function foo(x) { // x隐式推断为any，因此会报错
  
}

function bar(x: any) { // 这样可以
  
}
```

**tsconfig.json**

```json
{
  "compilerOptions": {
    "noImplicitAny": true
  }
}
```

```ts
let value: unknown

value = true	// ok
value = 123		// ok
value = '123' // ok

let b: boolean = value	// not ok 不能把unknown赋值给其他指定类型值

unknown 是更加安全的 any(不检查任何类型)
```

当设定一个值为 unknown 时，其影响范围只有自己(不能赋予给其他人)；当一个值设定为 any 时，其可以赋值给任何变量，影响范围大



> ## 类型标注

`:` 用于类型标注

```ts
let name : string = 'HH'
let name = 'HH'	// 隐式推断 name 为 string 类型
```



> ## 函数

```ts
function greet(name: string): number {
  console.log(name.toUpperCase())
  return 0
}

greet(42)	// false
let x: string = greet('hh') // false
```

**匿名函数**

contexture typing (根据上下文猜测匿名函数参数类型)

```ts
const names = ['a', 'b', 'c']

names.forEach(function(s) {	// 推断s为string
  console.log(s.toCupperCase())
})
```

**可选参数**

一般的js不太关心参数是否可选

```js
function foo(a, b) {
  console.log(a, b)
}

foo()
foo(a)
foo(a, b)

上面三种调用方式均可，这样会让人感觉很混乱
```

```ts
function print(arg1: string, arg2?: string) {
  console.log(arg1, arg2)
}

print()	// false
print('a')	// ok
print('a', 'b')	// ok
```



> ## 对象类型

对象如果描述了类型也需要严格执行

```ts
const pt: {
  x: number;
  y: number;
} = { x: 100, y: 200 }

pt.z = 100	// error
```

```ts
const pt: {
  x: number;
  y: number;
  z?: number;	// 代表可能是 undefined
} = { x: 100, y: 200 }

pt.z = 100	// ok
```

```ts
type Point = {
  x: number;
  y: number;
  z?: number;
}

const pt: Point = {x: 1, y: 2}
```

```ts
const o = {
  a: string;
  b?: {
  	c: string;
	}
} = {a: 'aa'}

console.log(o.b?.c)	// undefined
o.b.c = 'cc'	// error
```

出现 `?` 的变量只能是右值



> ## 联合

```ts
function printId(id: number | string) {
  console.log(`Your ID is: ${id}`)
}

printId(1)		// ok
printId('1')	// ok
printId(true)	// error
```

联合类型只能使用公共方法

```ts
function printId(id: number | string) {
  console.log(id.toUpperCase())	
  // Property 'toUpperCase' does not exist on type 'string | number'.
}
```

ts 会针对联合类型做排除法 (窄化)

```ts
function printId(id: number | string) {
  if (typeof id === 'number') {
    console.log(id)
    return
  }
  console.log(id.toUpperCase())
}
```



> ## 类型别名

```ts
type Point = {
  x: number;
  y: number;
}

function printCoord(pt: Point) {
  console.log(pt.x, pt.y)
}

printCoord({x: 1, y: 2})
```

使用联合的别名

```ts
type ID = number | string

let x: ID = 100	// typeof x === 'number'
x = '100'	// ok
```



> ## 接口

```ts
interface Point {
  x: number;
  y: number;
}

function printCoord(pt: Point) {
  console.log(pt.x, pt.y)
}

printCoord({x: 1, y: 2})
```

**别名和接口的区别**

```ts
interface Animal {
  name: string;
}

interface Bear extends Animal {	// 继承
  honey: boolean;
}

const bear = getBear()
bear.name
bear.honey

//==================================
type Animal = {
  name: string;
}

type Bear = Animal & {	// 组合
  honey: boolean
}

const bear = getBear()
bear.name
bear.honey
```

**接口合并**

可以用于扩展官方提供的接口

```ts
interface Box {
  width: number;
  height: number;
}

interface Box {
  scale: number;
}

let box: Box = { width: 1, height: 2, scale: 3 }
```



> ## 类型断言 (assertrion)

有时候 ts 对类型的理解没有你多，这时就需要类型断言了

```ts
const myCanvase = document.getElementById('main_canvas') as HTMLCanvasElement;

只有 document.getElementById() 时会推断为 HTMLElement

但是 canvas 有其他标签没有的方法，如 getContext('2d')，这时只能用类型断言
```

要注意的是，TS只会接受“说得通”的语言

比如 父类 as 子类  联合 as 单个

像有的“睁眼说瞎话”的断言 ts 会拒绝

```ts
const x = 'hello' as number	// error
```

不过，这时候可以用 `any as T` 来欺骗 ts

```ts
const a = (expr as any) as T;
const a = (expr as unknown) as T;
```



> ## 字面类型

对于常量，在 ts 中实际上是 Literal Type (字面值)

```ts
const str = 'abc' // str 的类型是 'abc'，因为它的值只能是 'abc'
const foo = 1	// foo 的类型是 1(不是整数)

// 但是使用 typeof 操作时仍然会显示其数据类型
typeof str	// string
typeof foo	// number

// 对于let
let bar = 1	// ts 的理解是 number
```

可以用字面类型来约束一些特殊的函数

```ts
function compare(a: string, bL string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1
}
```

```ts
interface Options {
  width: number;
}
function configure(x: Options | 'auto') {
  // ...
}

configure({width: 100})
configure('auto')
configure('automatic')	// Argument of type '"automatic"' is not assignable to parameter of type 'Options | "auto"'.
```

**坑**

```ts
function handleRequest(url: string, method: 'GET' | 'POST') {
  // do...
}

const req = {url: 'https://example.com', method: 'GET'}
handleRequest(req.url, req.method)	// Error
// Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
// 这里报错的原因是虽然req不可修改，但是 req.method = 'PUT' 还是允许的

// s1
const req = {url: 'https://example.com', method: 'GET' as 'GET'}

// s2
handleRequest(req.url, req.method as 'GET')

// s3
const req = {url: 'https://example.com', method: 'GET'} as const
```



> ## null  undefined

null 和 undefined 是 js 的两种基础类型(Primitive type)，它们描述的是不同的行为

* undefined 是一个没有被分配值的变量
* null 是一个被认为分配的空值

```ts
let u = undefined	// ...
```

ts 的一个配置项是 `strictNullChecks`，当打开这个配置时，在使用的值有可能是 null 时需要显式检查

```ts
function doSomething(x: string | null) {
  if (x === null) {
    // do nothing
  } else {
    console.log(x.toUpperCase())
  }
}
```

或者用 `!` 来断言某个值不是空值 (非空断言)

```ts
function doSomething(x: string | null) {
  console.log(x!.toUpperCase())
}
```



> ## 枚举类型

```ts
enum Direction {
  Up = 1,
  Down,		// 2
  Left,		// 3
  Right,	// 4
}
```

枚举类型最后会被翻译成整数

```ts
Direction[Down].toString()	// 2

// 反向映射(reverse mapping)
Direction[Direction.Down]	// Down 
```

可以把枚举类型强制设定为指定值

但是在可以反向映射的情况下，这种做法没有必要

```ts
enum Direction {
  UP = 'UP',
  Down = 'DOWN'
}
```

```ts
enum HH {
  No = 0,
  Yes = 'YES'
}
```

在运行时，Enum 会被解释成对象，Enum 的每项会被解释成常数

```ts
enum E {
  X,
  Y,
  Z
}

function f(obj: {X: number}) {
  return obj.x
}

f(E)	// ok
f({X: 1, Y: 2})	// error
```

---

# 泛型

泛型是提取了一类事物共性特征的一种抽象，如松树、柳树都是树

* 接口 ( Interface )
* 继承 ( Inheritance )
* 泛型 ( Generics )

**继承是一种强表达**

如 松树继承于树，同时也是木材，如果要表达这样的关系，要么让松树多重集成，要么是 松树 <- 树 <- 木材

无论哪种都会加强继承关系的维护成本(耦合)

**接口是一种方面( Aspect ) 描述**

如松树可以生长则其是 Growable；动植物可以进化则其是 Evolvable。

一个类型可以拥有多个方面的特性

**泛型是对共性的提取**

```ts
class BedMaker<T> {
  // ...
  make() {}
}
// Context 在制造床的上下文中传递需要的值
const A = new BedMaker<红木>()
const B = new BedMaker<桃木>()


// 如果这么做
class Wood {
  makeBed() {}	// 这样会很奇怪，虽然木头可以造床，但是没必要写到木头这个类里
}

class Wood implements BedMaker {}	// 这样对类型的描述也很奇怪，有点“以偏概全”
```

在定义类型的时候要注意**关注点(Interest Point)分离**，如果仅仅用接口不用泛型，那么关注点就没有做到完全解耦

**泛型**是一种**抽象共性(本质)**的编程手段，它允许将**类型作为其他类型的参数(表现形式)**，从而**分离不同关注点的实现(作用)**

```ts
Array<T> 	 分离的是数据可以被线性访问、存储的共性
Promise<T> 分离的是数据可以被异步计算的共性
Stream<T>  分离的是数据可以随着时间产生的共性
```

```ts
// 一个 identity函数 是可以自己返回自己的函数
function identity(arg: number): number { // 这样写就限定死了输入值
  return arg
}

function identity(arg: any): any { // 这样写会失去后面的类型检查
  return arg
}

function identity<Type>(arg: Type): Type {
  return arg
}

let output = identity<string>('str')
let output = identity('str')	// 隐式推断

output = 100	// error
```

**泛型类**

```ts
class GenericNumber<NumberType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) { return x + y }

let stringNumeric = new GenericNumber<string>()
stringNumeric.zeroValue = ''
stringNumeric.add = function (x, y) { return x + y }
```

**最佳实践：将声明(Declaration) 和 定义(Definition) 写在一起**

```ts
class GenericNumber<T> {
  zeroValue: T
  constructor(v: T) {
    this.zeroValue = v
  }
  
  add(x: T, y: T) {
    return x + y
  }
}
```

**泛型约束 (Generic Constraints)**

```ts
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length) // Error: Property 'length' does not exist on type 'Type'
  return arg
}
```

考虑为 arg 添加约束

```ts
interface Lengthwise {
  length: number;
}

function loggingIdentity<Type extends Lengthwise>(arg: Type): Type {
  console.log(arg.length)
  return arg
}
```

可以用 **keyof** 作为泛型的约束

```ts
type Point = {x: number, y: number}
type P = keyof Point	// P = "x" | "y"
```

```ts
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key]
}

let x = {a: 1, b: 2}

getProperty(x, 'a')
getProperty(x, 'c')	// Error
// Argument of type '"c"' is not assignable to parameter of type  '"a" | "b"'
```

对于 ts 来说，所有对象的 key 都是静态的

正因为此才可以用 keyof 求所有的 key，如果一个对象的类型是 any，那么 keyof 就没有意义了

**实例化泛型类型** (将类作为参数)

```ts
function create<Type>(c: {new (): Type}): Type {
  return new c()
}

// 传统的js写法
function create(C) {
  return new C()
}
create(Array)	// 得到 []
create('hello') // Error
```

```ts
class BeeKeeper {
  hasMask: boolean = true
}
class ZooKeeper {
  nametag: string = 'HH'
}
class Animal {
  numLegs: number = 4
}

class Bee extends Animal {
  keeper: BeeKeeper = new BeeKeeper()
}
class Lion extends Animal {
  keeper: ZooKeeper = new ZooKeeper()
}

function createInstance<A extends Animal>(c: new () => A): A {
  return new c()
}

createInstance(Bee).keeper.hasMask
createInstance(Lion).keeper.nametag
```

**总结**

* 接口用来约束类型的行为
* 泛型用来提取类型共性

将类型作为参数传递并实例化场景

```ts
React.createClass('xxx')
...
```

---

# 类型窄化

> ## 联合和窄化

```ts
type Padding = number | string
function padLeft(padding: Padding, input: string) {
  return new Array(padding + 1).join(' ') + input // Error
  // Operator '+' cannot be applied to types 'string | number'
}

// 需要增加typeof判断
function padLeft(padding: Padding, input: string) {
  if (typeof padding === 'number') {
    return new Array(padding + 1).join(' ') + input
  }
  return padding + input
}
```

ts 会认为 `typeof padding === number` 这样的表达式是一种类型守卫(type guard)表达式。这是纯粹实现层面的概念，准确来说 `if + type guard` 实现了窄化(Narrowing)

**类型窄化根据类型守卫在子语句块重新定义了更具体的类型**

和 js 不同的是，ts在类型守卫中可以做类型检查

注意：`typeof null === 'object'`

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === 'object') {
    for (const s of strs) {
      console.log(s)	// Error: Object is possibly 'null'
    } else if (typeof strs === 'string') {
      console.log(strs)
    } else {
      // do nothing
    }
  }
}
```



> ## 真值窄化

js 真值表

```js
0
0n (the bigint version of zero)
NaN
""
null
undefined
false
```

```ts
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === 'object') {
    for (const s of strs) {
      console.log(s)
    } else if (typeof strs === 'string') {
      console.log(strs)
    } else {
      // do nothing
    }
  }
}
```

```ts
function multiplyAll(
	values: number[] | undefined,
  factor: number
): number[] | undefined {
	if (!values) {
    return values	// 在这里values肯定是undefined
  }	else {
    return values.map(x => x* factor)
  }
}
```



> ## 相等性窄化

利用 `===`  `!==`  `==`  `!=` 来窄化

```ts
function example (x: string | number, y: string | boolean) {
  if (x === y) {
    // x is string
  } else {
    // x is string | number
    // y is string | boolean
  }
}


interface Container {
  value: number | null | undefined;
}
function multiplyValue(container: Container, factor: number) {
  if (container.value != null) {
    // container.value 一定是 number
    container.value *= factor
  }
}
```



> ## in 窄化

在 js 中，`in`用来判断某个对象是否有某个属性

```ts
type Fish = {swim: () => void}
type Bird = {fly: () => void}

function move(animal: Fish | Bird) {
  if ('swim' in animal) {
    return animal.swim()
  }
  return animal.fly()
}
```



> ## instanceof 窄化

注意下面的 `Date` 不是 别名`type` 而是真实存在的 `Function` 类型

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    // x is Date
  } else {
    // x is string
  }
}
```



> ## 组合类型推导

有时ts能推导出组合类型

```ts
let x = Math.random() < 0.5 ? 10 : 'hello'
```

此时x是 `number | string`



> ## 控制流分析

在语法分析阶段，ts编译器会识别出类型卫兵表达式，包括一些隐式的类型卫兵，比如真值表达式、instanceof等等

在语义分析的时候，ts遇到控制流关键字 `if/while` 等，就会看看这里有没有需要分析的窄化操作，如

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === 'number') {
    return new Array(padding + 1).join(" ") + input
  }
  return padding + input
}
```

1. ts 会看到一个卫兵表达式 `typeof padding === 'number'`
2. 然后ts会对返回值 `return padding + input` 和 `return new` 分别做窄化

**窄化的本质是重新定义类型**

也有其他语句会进行窄化

```ts
function example() {
  let x: string | number | boolean
  
  x = Math.random() < 0.5	// x is boolean
  
  if (Math.random() < 0.5) {
    x = 'hello'	// x is string
  } else {
    x = 100	// x: number
  }
  
  return x	// x is string | number
}
```



> ## 类型断言 (Type Assertions / Predicate)

Assertion 通常用来判断某个表达式的值是不是 true / false，本质上在说某个东西是什么

Predicate 通常是个函数，返回值是 true / false，比如 `list.filter(x => x.score > 500)` 中的 `x => x.score > 500` 这个函数就是一个 `predicate` 函数

* Assertion 的操作符是 `as`
* Predicate 的操作符是 `is`

**as** 操作符提示ts某种类型是什么 (当用户比ts更了解类型的时候使用)

**is** 操作符是用户自定义的类型守卫，用于帮助类型窄化

```ts
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined
}

let pet = { fly: () => {} }

if (isFish(pet)) {	// 如果 isFish 没有做返回值窄化，这里会报错
  pet.swim()
} else {
  pet.fly()
}
```



> ## 判别的联合 (Discriminated unions)

```ts
interface Shape {
  kind: "circle" | "square"
  radius?: number
  sideLength?: number
}

function getArea(shape: Shape) {
  return Math.PI * shape.radius ** 2	// Error
}

function getArea(shape: Shape) {
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius ** 2	// Error: Object is possibly 'undefined'
  }
}

function getArea(shape: Shape) {
  if (shape.kind === 'circle') {
    return Math.PI * shape!.radius ** 2
  }
}
```

**解决办法**

```ts
interface Circle {
  kind: 'circle'
  radius: number
}

interface Square {
  kind: 'square'
  sideLength: number
}

type Shape = Circle | Square

function getArea(shape: Shape) {
  switch(shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2
    case 'square':
      return shape.sideLenth ** 2
	}
}
```



> ## Never 类型

Never 是不应该出现的意思，其代表一个不应该出现的类型，因此对 Never 赋值都会报错

```ts
function getArea(shape: Shape) {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2
    case 'square':
      return shape.sideLength ** 2
    default:
      // 下面这句相当于 throw
      const _exhaustiveCheck: never = shape	// Type xxx is not assignable to type never
      return _exhaustiveCheck
  }
}
```

当在程序中看到 never 类型时就要知道原作者不希望程序走到那

**总结：窄化就是解决联合类型的校验问题**

如果不解决这个问题，在整个计算过程中有很多方法是不能使用的

---

# 类型计算

基本类型的联合是never

```ts
type A = number & string	// A is never
```

对象的联合

```ts
type Point = {x: number, y: number}
type State = {state: string}

type X = Point | State
const a: X = {x: 1, y: 2, state: 'abc'}	// 这里任意排列组合或减少都没问题

type Y = Point & State
const b: Y = {x: 1, y: 2, state: 'abc'} // 这里必须全部类型包括的都写上
```

**infer  推导**

定义一个可以将数组展平的函数 `flattern`

```ts
function flattern(arr: Array<any>): Array<any> {}	// 这样做引入了太多any
```

```ts
type Flattern<T> = T extends Array<infer V> ? V : T	// 如果T是数组就只要里面的东西，否则返回本身
type D = Flattern<Array<number>>	// D 在这里推导为 number
type D = Flattern<Array<Array<number>>	// D 在这里推导为 number[]
```

如果要递归地去拿出数组里面的元素，可以这么写

```ts
type Flattern<T> = T extends Array<infer V> ? Flattern<V> : T
type D = Flattern<Array<Array<number>>	// D 在这里推导为 number
```

更加高级的解决办法

```ts
type Atom = string | boolean | number | bigint
type Nested<T> = (T | (T | T[])[])[]	// 这依然是一个递归定义

function flattern<T extends Atom>(arr: Nested<Atom>): Atom[] {
  return (new Array<Atom>()).concat(
  	...arr.map(x => Array.isArray(x) ? flattern(x) : x)
  )
}
```

更加高级的递归定义

```ts
type Atom = string | boolean | number | bigint
type Nested = Array<Atom | Nested>
const o: Nested = [1,2,3,[[]], 2, [[[[4,5]]]]]
```

举一反三

```ts
type Unwrapped<T> = T extends Promise<infer U> ? U : T
type T0 = Unwrapped<Promise<string>>	// T0在这里推导为string
```

```ts
type Unwrapped<T> = T extends Array<infer U> // 如果拿到的是一个数组类型的数据，走下一行
                                    ? U extends Promise<infer R> // 如果数组的元素是Promise类型，拿其中的value值
                                    	? R[] // 将数组元素(Promise)的value组成数组返回出去
                                      : U // 数组类型不是Promise则直接返回
                                    : T // 一开始拿到的不是数组类型数据则直接返回

type T0 = Unwrapped<Promise<string>[]>	// 要使此处的T0推导为 string[]
```

```ts
type Unwrap<T> = T extends Promise<infer U>
                                   ? Unwrap<U>
                                   : T extends Array<infer V>
                                     ? UnwrapArray<T>
                                   	 : T

type UnwrapArray<T> = T extends Array<infer U>
                                   ? { [P in keyof T]: Unwrap<T[P] }
                                   : T
                                   
type T0 = Unwrapped<Promise<string>[]>	// 要使此处的T0推导为 string[]
type T1 = Unwrapped<Promise<Promise<string>[]>	// string[]
```

习题

```ts
type Flatterned<T> = T extends (infer V)[] ? V : T
type D = Flatterned<Array<number>>	// D ?
type E = Flatterned<Array<Array<number>>> // E ?
```

```ts
type Flatterned<T> = T extends (infer V)[] ? Flatterned<V>[] : T
type K = Flatterned<Array<Array<number>>>	// K ?

// 把下面的any去掉使程序合理 (修改以上的Flatterned)
function flattern<T extends Array<any>> (arr: T) : Array<Flatterned<T>> {
  return (new Array<Flatterned<T>>())
  	.concat(
  		...arr.map(x => Array.isArray(x) ? flattern(x) : x)
  	)
}
```

---

# 函数

> ## 表达构造函数

```ts
type SomeConstructor = {
  new (s: string): String
}

function fn(ctor: SomeConstructor) {
  return new ctor('hello')
}

const str = fn(String)
console.log(str)
```

```ts
type SomeConstructor<T> = {
  new (num: number): T
}

function fn<T>(ctor: SomeConstructor<T>, n: number) {
  return new ctor(n)
}

const arr = fn<Array<string>(Array, 100)	// string[]
```

> ## 泛型函数

```ts
function firstElement<T>(arr: T[]): T {
  return arr[0]
}

// 这里不写any是因为写any的话是没有类型检查的，然后这个函数返回值赋值给其他变量也是没有类型推导的
```

```ts
function map<Input, Output>(
	arr: Input[],
  func: (arg: Input) => Output
): Output[] {
	return arr.map(func)    
}

const parsed = map(['1', '2', '3'], n => parseInt(n))
```

**逻辑关系的一个坑**

```ts
function minimumLength<T extends { length: number }>(
	obj: T.
   minimum: number
): T {
    if (obj.length >= minimum) {
      return obj
    } else {
      return { length: minimum }	// Error
      // Type '{ length: number; }' is not assignable to type 'T'
      // '{ length: number; }' is assignable to the constraint of type 'T', but 'T' could be instantiated with a different subtype of constraint '{ length: number; }'
    }
}

minimumLength<Array<string>>()	// 这样调用的话返回的值就不是 Array<string>
```

**在此处，T 扩展了属性length，但是有 length 的不一定是T**

```ts
function minimumLength<T extends { length: number }>(
	obj: T,
  minimum: number
): T {
    if (obj.length >= minimum) {
      return obj
    } else {
      // 利用 js 的 Function(通过原型链找到的)
      return obj.constructor(minimum)	// 但是这样做的话会让constructor的约束少
    }
}


function minimumLength<T extends { length: number, construcrtor: Function }>(
	obj: T,
  minimum: number
): T {
    if (obj.length >= minimum) {
      return obj
    } else {
      return obj.constructor(minimum)	// 这样做的话会让constructor的约束少
    }
}

minimumLength(new Array<string>(100), 1000)
```

```ts
function minimumLength<T extends { length: number }>(
	obj: T,
  minimum: number,
  typeClass: {
    new (n: number) : T
  }
): T {
    if (obj.length >= minimum) {
      return obj
    } else {
      return new typeClass(minimum)
    }
}

minimumLength(new Array<string>(100), 1000, Array)
```

> ## 手动指定类型

```ts
function combine<Type>(arr1: Type[], arr2: Type[]): Type[] {
  return arr1.concat(arr2)
}

const arr = combind([1, 2, 3], ['hello'])	// Type 'string' is not assignable to type 'number'.
const arr = combine<string | number>([1, 2, 3], ['hello'])

上面这种做法就不能用string和number对比特有的方法
```

> ## 最佳实践

**利用推导(infer)能力，避免使用any**

```ts
function firstElement1<Type>(arr: Type[]) {
  return arr[0]	// 函数调用后拿到的值是确定类型的
}

function firstElement2<Type extends any[]>(arr: Type) {
  return arr[0]	// 函数调用后拿到的值是any
}
```

**尽量减少泛型参数的使用**

```ts
function filter1<Type>(arr: Type[], func: (arg: Type) => boolean): Type[] {	// 只用一个泛型
  return arr.filter(func)
}

function filter2<Type, Func extends (arg: Type) => boolean> ( // 定义了两个泛型
	arr: Type[],
  func: Func
): Type[] {
  return arr.filter(func)
}
```

```ts
// 这个函数根本没必要使用泛型
function greet<Str extends string>(s: Str) {
  console.log('hello ' + s)
}
```

> ## 使用 ? 定义可选参数

```ts
function myForEach(arr: any[], callback: (arg: any, index?: number) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i], i)
  }
}
```

像  `onClick` `onMouseMove` 不一定需要 `e`

> ## 函数重载

```ts
function add<T> (a: T, b: T) {
  return a + b  // Operator '+' cannot be applied to types 'T' and 'T'.
}
```

```ts
function isSet<T>(x: any): x is Set<T> {
  return x instanceof Set
}

//! 注意声明和定义要连着写
function add(a: number, b: number): number
function add(a: string, b: string): string
function add<T>(a: Set<T>, b: Set<T>): Set<T>
function add<T>(a: T, b: T): T {	// 限制a和b传入相同的类型
  if (isSet<T>(a) && isSet<T>(b)) {
    return new Set([...a, ...b]) as any	// 在这里写any也没关系，因为ts只会看上面的声明
  }
  return (a as any) + (b as any)	// 在定义时此时a和b的类型依然是T
}

add(1, '1')	// Error 这种使用方法无法匹配任何一种声明

const a = new Set<string>(['apple', 'redhat'])
const b = new Set<string>(['google', 'ms'])
console.log(add(a, b))
```

操作符重载：参考 babel-plugin-overload-operator

> ## this

```ts
interface DB {
  exec(sql: string) => any
}

function runSql(this: DB, sql: string) {
  this.exec(sql)
}
```

> ## void

```ts
type Fn = () => void

const f1: Fn = () => true	// 没问题
const m = f1()	// m的类型是void，这样就没法用了

function f2(): void {
  return true	// 这样写就会报错了
}
```

> ## unknown

```ts
function safeParse(s: string): unknown {
  return JSON.parse(s)	// 如果本身是string，parse出来还是string
}

const x = safeParse('123')	// x is unknown  这样x不能直接赋值给其他变量
(x as string)...	// 但是可以通过断言来使用
```

```ts
function fail(msg: string): never {
  throw new Error(msg)
}

这么写程序是为了防止对该函数调用的返回值进行任何操作(针对程序编写阶段的检查)
```

> ## rest params

```ts
function multiply(n: number, ...m: number[]) {
  return m.map(x => n * x)
}

const a = multiply(10, 12, 34, 5, 7)
```

**泛型帮助我们让类型检查更加严格、智能**

---

# 描述类型的工具

**灵魂发问：类型可以计算吗？**

> ## keyof

```ts
type Point = { x: number, y: number }
type P = keyof Point	// "x" | "y"

type Arrayish = { [n: number]: unknown }
type A = keyof Arrayish	// "number"

type Mapish = { [k: string]: boolean }
type M = keyof Mapish	// "string" | "number"	之所以会这样是因为对js来说 a[0]和a["0"]是一样的
```



> ## typeof

```ts
console.log(typeof 'xxx')	// string

let s = 'aaa'
let n: typeof s	// n is string
```



> ## Partial

```ts
interface Todo {
  title: string;
  description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldstoUpdate }
}

const todo1 = { title: 'tt', description: 'dd' }
const todo2 = updateTodo(todo1, { description: 'hh' })
```

**Partial 源码**

```ts
type Partial<T> = {
  [P in keyof T]?: T[P];	// T[P] 的含义是定义类型，如 string number
}
```



> ## Required

```ts
interface Props {
  a?: number;
  b?: string;
}

const obj: Props = { a: 5 }
const obj2: Required<Props> = { a: 5 }	// Error
// Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'
```

**Required 源码**

```ts
type Required<T> = {
	[P in keyof T]-?: T[p];
}
```



> ## Readonly

```ts
interface Todo {
  title: string;
}
const todo: Readonly<Todo> = {
  title: 'xxx'
}

todo.title = 'hhh'	// Error	注意这里的报错是编译时的报错
// Cannot assign to 'title' because it is a read-only property.
```

**Readonly 源码**

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
}
```



> ## Record

```ts
interface CatInfo {
  age: number;
  breed: string;
}
type CatName = 'miffy' | 'boris' | 'mordred'

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: 'Persian' },
  boris: { age: 12, breed: 'Maine Coon' },
  mordred: { age: 11, breed: 'British Shorthair' },
}

cats.boris;
```

**Record 源码**

```ts
type Record<K extends keyof any, T> = {	// keyof any 包括 string number symbol
  [P in K]: T;
}
```



> ## Pick

```ts
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, 'title' | 'completed'>

const todo: TodoPreview = {
  title: 'hh',
  completed: false,
}

```

**Pick 源码**

```ts
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];	// 如果 P in T 就是和原来的一模一样了
}
```



> ## Exclude

```ts
type T0 = Exclude<'a' | 'b' | 'c', 'a'>		// 'b' | 'c'
type T1 = Exclude<'a' | 'b' | 'c', 'a' | 'b'>		// 'c'
type T2 = Exclude<string | number | (() => void), Function>	// string | number
```

**Exclude 源码**

```ts
type Exclude<T, U> = T extends U ? never : T;
```



> ## Omit (省略)

和 `Exclude` 的区别是，`Exclude` 针对的是联合类型(参考 Exclude源码)，而 `Omit` 针对的是接口( interface )

```ts
interface todo {
  title: string;
  description: string;
  completed: boolean;
  createdAt: number;
}
type TodoPreview = Omit<Todo, 'description'>

const todo: TodoPreview = {
  title: 'Clean room',
  completed: false,
  createdAt: 1615544252770
}


type TodoInfo = Omit<Todo, 'completed' | 'createdAt'>
const todoInfo: TodoInfo = {
  title: 'Pick up kids',
  description: 'Kindergarten closes at 5pm'
}
```

**Omit 源码**

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>
```



> ## Extract

```ts
type T0 = Extract<'a' | 'b' | 'c', 'a' | 'f'>	// 'a'

type T1 = Extract<string | number | (() => void), Function>	// () => void
```

**Extract 源码**

```ts
type Extract<T, U> = T extends U ? T : nerver
```



> ## NonNullable

```ts
type T0 = NonNullable<string | number | undefined>	// string | number
type T1 = NonNullable<string[] | null | undefined>	// string[]
```

**NonNullable**

```ts
type NonNullable<T> = T extends null | undefined ? never : T
```



> ## Parameters

```ts
declare function f1(arg: {a: number; b: number}): void

type T0 = Parameters<() => string>	// []

type T1 = Parameters<(s: string) => void>	// [s: string]

type T2 = Parameters<<T>(arg: T) => T>	// [arg: unknown]
  
type T3 = Parameters<typeof f1>	// type T3 = [arg: {a: number; b: number}]
type T3 = Parameters<f1>	// 这样写会报错，因为 declare 说明会有实实在在这么一个函数出现，只是不在这里定义
```

**Parameters 源码**

```ts
type Parameters<T extends (...args: any) => any> = t extends (...args: infer P) => any ? P : never
// 正常来说这里的 P 是一个数组
```



> ## ConstructorParameters

```ts
interface ErrorConstructor {
  new (message?: string): Error
  (message?: string): Error
   readonly property: Error
}

interface FunctionConstructor {
  new (...args: string[]): Function
  (...args: string[]): Function
  readonly prototype: Function
}

interface RegExpConstructor {
  new(pattern: RegExp | string): RegExp
  new(pattern: string, flags?: string): RegExp
  (pattern: RegExp | string): RegExp
  (pattern: string, flags?: string): RegExp
  readonly prototype: RegExp
  
  // Non-standard extensions
  $1: string
  $2: string
  $3: string
  $4: string
  $5: string
  $6: string
  $7: string
  $8: string
  $9: string
  lastMatch: string
}

type T0 = ConstructorParameters<ErrorConstructor>	// [message?: string]

type T1 = ConstructorParameters<FunctionConstructor>	// string[]

type T2 = ConstructorParameters<RegExpConstructor>	// [pattern: string | RegExp, flags?: string]

type T3 = ConstructorParameters<any>	// unknown[]

type T4 = ConstructorParameters<Function>	// Error
// Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'
// Type 'Function' provides no match for the signature 'new (...args: any): any'
```

**constructorParameters**

```ts
type ConstructorParameters<T extends abstract new (...args: any) => any> = T extends abstract new (...args: infer P) => any ? P : never
```



> ## ReturnType

```ts
declare function f1(): {a: number, b: string}

type T0 = ReturnType<() => string>	// string

type T1 = ReturnType<(s: string) => void>	// void

type T2 = ReturnType<<T>() => T>	// unknown

type T3 = ReturnType<<T extends U, U extends number[]>() => T>	// number[]
  
type T4 = Returntype<typeof f1>	// {a: number, b: number}
  
type T5 = ReturnType<any>	// any
  
type T6 = ReturnType<never>	// never
  
type T7 = ReturnType<string>	// any
// Type 'string' does not satisfy the constraint '(...args: any) => any'.
  
type T8 = ReturnType<Function>	// any
// Type 'Function' does not satisfy the constraint '(...args: any) => any'
// Type 'Function' provides no match for the signature '(...args: any): any'
```

**ReturnType 源码**

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any
```



> ## InstanceType

```ts
class C {
  x = 0
  y = 0
}

type T0 = InstanceType<typeof C>	// C
// 注意这里不能填 InstanceType<C>
// 在用Promise的时候可以这么用 Promise<C>，原因是这里是要创造实例了
// 但是在声明“别名”的时候只能用其类型

type T1 = InstanceType<any>	// any

type T2 = InstanceType<never>	// never

type T3 = InstanceType<string>	// any
// Type 'string' does not satisfy the constraint 'abstract new (...args: any) => any'

type T4 = InstanceType<Function>	// any
// Type 'Function' does not satisfy the constraint 'abstract new (...args: any) => any'
// Type 'Function' provides no match for the signature 'new (...args: any): any'

```

**InstanceType 源码**

```ts
type InstanceType<T extends abstract new (...args: any) => any> = T extends abstract new (...args: any) => infer R ? R : any
```



> ## ThisParameterType

```ts
function toHex(this: Number) {
  return this.toString(16)
}

function numberToString(n: ThisParameterType<typeof toHex>) {
	return toHex.apply(n)                                             
}
```

**ThisParameterType 源码**

```ts
type ThisParameterType<T> = T extends (this: infer U, ...args: any[]) => any ? U : unknown
```



> ## OmitThisParameter

```ts
function toHex(this: Number) {
  return this.toString(16)
}

const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5)	// () => string
                                   
console.log(fiveToHex())
```

**OmitThisParameter**

```ts
type OmitThisParameter<T> = unknown extends ThisParameterType<T> ? T : T extends (...args: infer A) => infer R ? (...args: A) => R : T
```



> ## ThisType

```ts
type ObjectDescriptor<D, M> = {
  data?: D
  methods?: M & ThisType<D & M>	// Type of 'this' in methods is D & M
}

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {}
  let methods: object = desc.methods || {}
  return { ...data, ...methods } as D & M
}

let obj = makeObject({
  data: {x: 0, y: 0},
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx
      this.y += dy
    }
  }
})
```

**ThisType 源码**

```ts
interface THisType<T> {}
```

更加详细的实现是在更深一层(ts编译器处)

**tsconfig.json 相关配置**

```ts
{
  "compilerOptions": {
    "noImplicitThis": true
  }
}
```



> ## Uppercase / Lowercase

```ts
type Greeting = 'Hello, world'
type ShoutyGreeting = Uppercase<Greeting>	// "HELLO, WORLD"

type ASCIICacheKey<Str extends string> = `ID-${Uppercase<Str>}`
type MainID = ASCIICacheKey<"my_app">	// "ID-MY_APP"
```

```ts
type Greeting = 'Hello, world'
type QuietGreeting = Lowercase<Greeting>	// "hello, world"

type ASCIICacheKey<Str extends string> = `ID-${Uppercase<Str>}`
type MainID = ASCIICacheKey<"my_app">	// "id-my_app"
```

**源码**

下面的类型(方法)都是内部实现的

```ts
type Uppercase<S extends string> = intrinsic

type Lowercase<S extends string> = intrinsic

type Capitalize<S extends string> = intrinsic

type Uncapitalize<S extends string> = intrinsic
```



**总结**

类型是可以计算的

* &
* -
* ?
* infer
* ...

---

# Class

> ## 定义类和成员

```ts
class Point {
  x: number
  y: number
  
  constructor(x: number, y: number) {
    this.x = x
    this.y = y
  }
  
  public add(p: Point): Point {
    return new Point(p.x + this.x, p.y + this.y)
  }
}
```

> ## 成员函数重载

```ts
class Point {
  //...
  
  public add(x: number, y: number): Point;
  public add(p: Point): Point;
  public add(x: number | Point, y?: number) {	// 注意需要做窄化
    if (typeof x === 'number') {
      return new Point(this.x + x, this.y + y!)	// 从声明中可以肯定当存在x的时候y肯定存在
    }
    const p = x
    return new Point(this.x + p.x, this.y + p.y)
  }
}
```

> ## Getter / Setter

```ts
class C {
  _length = 0
  
  get length() {
    return this._length
  }
  
  set length(value) {
    this._length = length
  }
}

// 不建议这么写，原因如下
const c = new C()
c.length	// 在这里隐式调用了函数 因此会造成困惑

// 再比如Vue
const ref = ref(0)
ref.value++	// 在此处，用户可能不知道value被代理了，需要读设计文档才能明白
// 或者说用户不知道隐式调用函数的时候不知道会触发什么行为
// 像这里的 ref.value++ 触发了页面刷新用户完全不知道为什么
```

最好还是老老实实写函数 (最好做到代码即文档)

```ts
class C {
  _length = 0
  getLength() {
    return this._length
  }
  setLength(len) {
    this._length = len
  }
}
```

> ## 索引器

```ts
class Arr<T> {
  [i: number]: T
}

const a = new Arr<number>()
a[10] = 100
console.log(a[10]) // 100
```

> ## 继承

```ts
interface BasicAddress {
  name?: string;
  street: string;
}

interface AddressWithUnit extends BasicAddress {
  unit: string;
}

interface AddressWithUnit {
  citi: string;
}
```

**实战中尽量使用组合**

```ts
interface Movable {
  move() : void;
}

class Dog implements Movable {
  move() {
    // ...
  }
}
```

因为使用继承的话就是强耦合了

解耦合还可以用泛型

```ts
class Movable<T extends Animal> {
  animal: T
  move() {
    console.log(`${animal.getName()} is moving`)
  }
}
```

**继承会加强耦合，组合和泛型不会**

> ## 成员可见域

* public
* protected
* private

`public` 成员允许所有程序访问，不过不标识任何约束，默认就是 public

```ts
class Point {
  x: number
}
```

`private` 成员只允许当前类中访问

```ts
class Point {
  private x: number
  constructor() {
    this.x = x
  }
  
  getX() {
    return this.x
  }
}
```

`protected` 允许在当前类和其继承类中访问成员

```ts
class Animal {
  private _name1: string
  protected _name2: string
}

class Dog extends Animal {
  getName1() {
    return this._name1	// Error
  }
  
  getName2() {
    return this._name2	// pass
  }
}
```

> ## 静态成员

静态成员和类的实例没有关系，静态成员和静态方法都绑定在类型本身上

```ts
class MyClass {
  static x = 0
  static printX() {
		console.log(x)
  }
}

console.log(MyClass.x)
console.log.printX()
```

静态成员也能继承

```ts
class Base {
  static getGreeting() {
    return 'hello'
  }
}

class Derived extends Base {
  myGreeting = Derived.getGreeting
}
```

保留的特殊静态成员名称

```ts
class S {
  static name = 's!'	// Error
  // Static property 'name' conflicts with built-in property 'Function.name' of constructor function 'S'.
}
```

* default
* class

等关键字都不能使用

**js 没有静态类**

虽然 js 也存在类的嵌套，但是遵循闭包的逻辑

```ts
class A {
  x: number
  foo() {
    const y = 0
    class B {
      bar() {
        this.x	// Property 'x' does not exist on type 'B'
        console.log(y)
      }
    }
    return B
  }
}
```

> ## 类型守卫

```ts
class FileSystemObject {
  isFile(): this is FileRep {
    return this instanceof FileRep
  }
  isDirectory(): this is Directory {
    return this instanceof Directory
  }
  isNetworked(): this is Networked & this {
    return this.networked
  }
	constructor(public path: string, private networked: boolean) {}
}

class FileRep extends FileSystemObject {
  constructor(path: string, public content: string) {
    super(path, false)
  }
}

class Directory extends FileSystemObject {
  children: FileSystemObject[]
}

interface Networked {
  host: string
}

const fso: FileSystemObject = new FileRep('foo/bar.txt', 'foo')

if (fso.isFile()) {
  fso.content	// fso is FileRep
} else if (fso.isDirectory()) {
  fso.children	// fso is Directory
} else if (fso.isNetworked()) {
  fso.host	// fso is Networked & FileSystemObject
}
```

> ## 抽象类

不可被实例化的类

```ts
abstract class Base {
  abstract getName(): string
  
  printName() {
    console.log('hello '+ this.getName())
  }
}

// 必须得继承
class Derived extends Base {
  getName() {
    return 'world'
  }
}

const d = new Derived()
d.printName()
```

抽象类的抽象属性和抽象方法必须在继承类中被实现

> ## 类型的关系

```ts
class Point1 {
  x = 0
  y = 0
}

class Point2 {
  x = 0
  y = 0
}

const p: Point1 = new Point2()	// ok
```

```ts
class Person {
  name: string
  age: number
}

class Employee {
  name: string
  age: number
  salary: number
}

const p: Person = new Employee()
```

```ts
class Empty {}

function fn(x: Empty) {
  // can't do anything with 'x'
  // x.addEventListener No
  // (x as any).addEventListener
}

// All ok
fn(window)
fn({})
fn(fn)
```

---

# 模块

模块是一个程序包，保内的成员(函数、变量、类型)仅仅在保内可见，包外想要访问这些成员，除非模块自己主动向包外提供。

例如利用 `import / export ` 语法

```ts
export default function helloWorld() {
  console.log('hello')
}

// other file
import hello from './hello.ts'
hello()
```

**非默认导出**

```ts
// maths.ts
export var pi = 3.14
export let squareTwo = 1.41
export const phi = 1.61

export class RandomNumberGenerator {}

export function absolute(num: number) {
  if (num < 0) return num * -1
  return num
}

// other file
import { pi, phi, absolute } from './maths.js'
// ...
```

**在 package.json 中设置**

```json
{
  "type": "module",	// 在 node 中就可以使用 import / export
}
```

> ## 别名

为了防止模块冲突，可以使用别名

```ts
import { pi as π } from './maths.js'
console.log(π)
```

在浏览器端需要 babel 转译，在 node 端可以直接用

> ## Export 类型

在 ts 中可以导出别名和接口

```ts
// animal.ts
export type Cat = { breed: string; }
export interface Dog {}
```

在接收的文件中可以用 `import ` 或 `import type` 来使用

```ts
import { Cat } from './animal.ts'
import type { Cat } from './animal.ts'
```

`import type` 告诉编译器只要类型信息

```ts
import type { Foo } from './animal'
const foo = new Foo()	// Error
// 'Foo' cannot be used as a value because it was imported using 'import type'
```

ts 对于 class 的理解不仅仅局限于认为它只是一个函数

更准确的是 class 是 类+描述(元数据)

而纯粹的类型数据只是一个 type

> ## TS 的模块解析配置项

TS 的配置项 `module` 可以用来配置 TS 将模块编译成什么

```json
{
  "module": "ES6"
}
```

* ES6
* ES2015
* ES2020
* UMD
* AMD
* Commonjs
* SYSTEM
* ESNext

还可以配置 `moduleResolution` 来决定模块的加载算法

```json
{
  "moduleResolution": "node"
}
```

* node (node_modules) 现在基本都用这种
* classic

**只有一个文件有 export 时，ts 才认为它是一个模块**

> ## Namespace

命名空间现在基本不用了，流行用的是 module

---

# 总结

> ## 类型检查作用

1. 检查错误
2. 减少 debug 的需要
3. 辅助系统设计 (类型的思考方式)

> ## 类型可推导

1. 每个变量 / 值的类型
2. 操作符的类型 ( + (number, number) => number )
3. 表达式类型
4. 函数的类型
5. 类的类型

程序有类型可以获得

* 更好的代码提示
* 更好的书写检查
* 更好的错误提示

**类型可以被计算，正因为此也提供了强大的抽象能力**

```ts
type RouteParams<Route extends string> = 
	Route extends `${string}:${infer Rest}`
		? RouteDic<Rest>
		: never

type RouteDic<Arg extends string> = {
	[p in Arg]: string
}

const path = "/a/:page"
const params: RouteParams<typeof path> = {
	'page': 'abc',	// 如果这里写的page1等对不上的词就会报错
}
```

**帮助用户思考：什么东西是什么**

像 React 的 FC 和 ComponentClass

`type ElementType = FC | ComponentClass`

这样的思考可以帮助我们更好地架构、编码

再比如设计一个类型存储css的长度，判断合不合适

```ts
type CSSSize = {
	value: number;
  unit: string;	// 这里显然不合适 1px 1pt 1% auto
}
```



