# TS

## 类型检测



### any

* 任何值都可以赋值给 any 类型
* any 类型也可以赋值给任何类型
* any 类型有任意属性和方法

**注意：**

* 标注为 any类型，也意味着放弃对该值的类型检测，同时放弃IDE的智能提示
* 在 tsconfig.json 中指定 `noImplicityAny` 为`true`时，当函数参数出现隐含的 `any`类型时报错



### unknown

在 TS3.0 中新增的属性，属于安全版的 any，但是与 any 不同的是：

* unknown 仅能赋值给 unknown、any
* unknown 没有任何属性和方法

unknown 只用于本体一次性使用

```ts
let a: unknown
a.indexOf() // 报错
```

---

## 接口

```ts
interface Point {
  x: number;
  y: number;
  color?: string; // 可选
  readonly size: number;	// 只读属性
  [prop: string]: number; // 字符串类型的任意属性
  [prop: number]: number;	// 数字索引的任意属性
}
```

索引签名必须为 string 或 number，二者可同时出现

当同时存在数字类型索引和字符串类型索引时，数字类型的值类型必须是字符串类型的值类型的子集

**因为**，数字索引最后都会转成字符串类型，所以数字类型索引的值也必须满足字符串类型索引值的类型

---

## 类型系统

### 联合类型

标注语法

```
变量: 类型一 | 类型二
```

```ts
function css (ele: Element, attr: string, value: string | number) {
  // ...
}

const box = document.querySelector('.box')	// box: Element | null
if (box) {
  css(box, 'width', '100px')
  css(box, 'opacity', 1)
}
```



### 交叉类型

交叉类型又称为合并类型

标注语法

```
变量: 类型一 & 类型二
```

```ts
interface o1 {
  x: number;
  y: number;
}

interface o2 {
  z: number;
}

const obj1: o1 & o2  = {
  x: 1,
	y: 2,
  z: 3
}
```



### 字面量类型

**type** 主要用于联合类型的提取

```ts
type direc = 'left' | 'right' | 'top' | 'bottom' | 'center'
function setPosition (ele: Element, direction: direc) {
  // ...
}

const box = document.querySelector('.box')
if (box) {
  setPosition(box, 'left')
}
```



### 类型推导

 TS 编译器会根据当前上下文自动推导出对应的类型标注，这个过程发生在

* 初始化变量
* 设置函数默认参数值
* 返回函数值

```ts
let x = 1 // 自动推导x为number
x = 'a' // 报错
```

```ts
// 这里默认推导x为number类型
function fn (x = 1) {
  // ...
}
```

```ts
function fn(x: number, y: number) {
  return x + y // 这里默认推导返回值为number
}
```



### 类型断言

有的时候我们可能要标注一个更加精确的类型(缩小类型标注范围)，比如：

```ts
const img = document.querySelector('#img')
```

我们可以看到 img 的类型为 Element，而 Element 类型其实只是元素类型的通用类型，如果我们去访问 `src` 这个属性是有问题的，我们需要把它的类型标注得更加精确： HTMLImageElement

```ts
const img = <HTMLImageElement>document.querySelector('#img')

// 或者
const img = document.querySelector('#img') as HTMLImageElement
```

**注意：**断言只是一种预判，并不会对数据本身产生实际的作用 (如类型转换)



### 类型操作符

**typeof**

`typeof` 操作的是**值**

```ts
// 一般使用
const colors = {
  color1: 'red',
  color2: 'blue'
}

typeof colors // object
```

```ts
const colors = {
  color1: 'red',
  color2: 'blue'
}

type color = typeof colors
/*
	color = {
		color1: string;
		color2: string;
	}
*/

// 错误示范
interface someInterface {sth:string;}
type wrong = typeof someInterface	// 报错
```



**keyof**

获取类型的所对应的类型的`key`的集合，返回值是`key`的联合类型

**注意：** keyof 操作的是类型

```ts
interface Person {
  name: string;
  age: number;
}

type a = keyof Person  // type a = "name" | "age"

// 错误示范
const colors = {color1: 'red'}
type b = keyof colors	// 报错，keyof不可以操作值
```



**使用场景**

```ts
const p1 = {
  name: 'aa',
  age: 18
}

function getPersonVal (k: keyof typeof p1) {
  return p1[k]
}

getPseronVal('name')
```

```ts
function css (ele: Element, attr: keyof CSSStyleDeclaration) {
  return getComputedStyle(ele)[attr]
}

const box = document.querySelector('.box')
if (box) {
  css(box, 'width')
  css(box, 'height')
}
```



**in**

* 针对值进行操作

  ```ts
  console.log('name' in {name: 'abc', age: 18})
  ```

* 针对类型进行操作

  ```ts
  interface Person {
    name: string;
    age: number;
  }
  
  type personKeys = keyof Person
  
  type newPerson = {
  	[k in personKeys]: string;
    // 等同于
    // [k in 'name' | 'age']: string;
    // [k in keyof Person]: string;
  }
  ```

  **注意：** in 后面的值必须是 string、number 或 symbol



**extends**

```ts
interface type1 {
  x: number;
  y: number;
}

interface type2 extends type1 {
  z: number;
}

const a: type2 = {
  x: 1,
  y: 2,
  z: 3
}
```

```ts
type type1 = {
  x: number;
  y: number;
}
function fn<T extends type1> (args: T) { 
	// ...
}
fn({x: 1, y: 2, str: 'string'})
```



### 类型保护

有时候值的类型并不唯一，比如一个联合类型的参数，这个时候在该参数使用过程中只能调用联合类型**共有**的属性和方法

**解决办法1: 断言**

```ts
function toUpperCase (arg: string | string[]) {
  // arg.length  可以使用，因为string 和 string[] 都有
  
  // 断言只对本行有效
  if ((<string>arg).toUpperCase) {
    return (<string>arg).toUpperCase()
  } else {
    return (<string[]>arg).map(str => str.toUpperCase())
  }
}
```

**解决办法2: typeof**

TS 能够把 typeof 识别为类型保护作为类型检查的依据，不仅仅在 if 中有效，还在 else 中有效

```ts
function toUpperCase (arg: string | string[]) {
  if (typeof arg === 'string') {
    return arg.toUpperCase()
  } else {
    return arg.map(str => str.toUpperCase())
  }
}
```

**解决办法3：instanceof**

原理类似 typeof 

```ts
function toUpperCase (arg: string | string[]) {
  if (arg instanceof Array) {
    return arg.map(str => str.toUpperCase())
  } else {
    return arg.toUpperCase()
  }
}
```

**解决办法4：自定义类型保护**

```ts
const eles = document.querySelectorAll('.box')

fn(eles)

function fn (elements: Element[] | NodeList | Element) {
  if (canTraverse(elements)) {	// 检测传入参数是否有 forEach 方法
    elements.forEach(() => {})
  } else {
    elements.classList.add('box')
  }
}

// 如果返回值为true，data is Element[]|NodeList 即为真
function canTraverse (data: Element[] | NodeList | Element): data is Element[] | NodeList {
  return (<NodeList>data).forEach !== undefined
}
```





