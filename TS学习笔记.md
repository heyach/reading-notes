### TS学习笔记
***

### null和undefined是所有类型的子类型
即任何类型都可以赋值为null和undefined
```js
let str: string = "666"
str = null
str = undefined

let bool: boolean = false
bool = null
bool = undefined
```

### 函数的可选参数一定要放在最后
```js
function fullName(firstname: string, lastname?: string): string {
    return lastname ? firstname + lastname : firstname
}
```

### void表示没有类型，不能与其他基础类型相互赋值，只能赋值为null和undefined
```js
let v: void;
let b: string = a; // error
```

### 通过给never类型赋值来检查类型全面性
```js
type foo = string | number
// 假设f的类型是string或者number，函数要考虑全类型做不同的处理
function fun(f: foo) {
    if(typeof f == "string") {
        //
    } else if(typeof f == "number") {
        //
    } else {
        let check: never = f
        // 如果foo始终是string或者number，这里永远不会执行到
        // 但是如果有一天，foo被改成
        // type foo = string | number | boolean
        // 如果不在fun里加上boolean的处理逻辑，就会报错，因为foo的类型不全面了，防止漏改
    }

}
```

### 类型断言，相当于强制告知ts一个值的类型，因为ts没有运行时的检查能力
```js
let arr: number = [1, 2, 3, 4]
let r: number = arr.find(n => n > 2)
// 实际find函数执行的结果有可能是数字（找到符合条件），也有可能是undefined（没有符合条件的）
// 所以ts会报一个把undefined赋值给number类型的错误
// 所以我们告知ts按照我们的方式做类型检查

// 类型断言语法，更推荐用as，因为尖括号容易与jsx冲突
let r2: number = arr.find(n => n > 2) as number
let r3: number = <number>(arr.find(n => n > 2))
```

### 字面量类型，基础类型的子类型，只有string，number，boolean有
```js
let upperStr: 'upperstr' = "ABC" 
let lowerStr: 'lowerstr' = "abc"
let trueVal: true = true
let one: 1 = 1
// 字面量类型是一种特殊的基础类型，比如大写字符串也是字符串的一种，字面量类型可以复制给基础类型，反之则不行
let str: string = "aBc"
str = upperStr
upperStr = str // error
```
实际上单个的字面量类型可能没啥用，但是可以通过联合类型先定一个集合，比如方向
```js
type direction = 'up' | 'down' | 'left' | 'right'
// 限制参数不仅是字符串，还得是特定的值
function move(d: direction) {
    //
}
move('up')
move('right')

// 更加具体的限制了类型和值的范围
interface config {
    size: 'mini' | 'normal' | 'large',
    enable: true | false,
    margin: 5 | 10 | 15 | 20
}
```

### let和const的缺省推断机制
const声明的变量不可修改，在这个前提下，是不会出现把别的值赋值给const变量的，因此const的推断成字面量类型
```js
const str = 'somestr' // str的类型是somestr而不是string
const num = 123 // num的类型是123而不是number
const bool = true // bool的类型是true而不是boolean
```
而let声明的变量是可以被重新赋值的，但是由于类型的约定，我们认为不同的类型变量不能相互赋值，因此，let的推断成初始值的基础类型
```js
let str = "somestr" // str的类型是string，可以赋值为其他的string
let str2: string = "otherstr"
str = str2
let num = 123 // num的类型是number，可以赋值为其他的number
let bool = true // bool的类型是boolean，可以赋值为其他的boolean
```

### 联合类型
扩大类型的范围，用于不太确定或者本身就要求是多种类型的，最常见的就是js中任意变量都可能是undefined
```js
type name = string | undefined
function fun(name: name) {
    //
}
```
由于联合类型一般是多个类型，因此一般会有一个新的意思，取一个别名来准确的描述意思
```js
type allType = string | number | boolean | object 
```

### 交叉类型
将多个类型合并，但是最好不要用于基础类型，因为不存在一个值既是string又是number，而是用于接口的合并
```js
interface user = {
    id: number,
    name: string
}
interface age = {
    age: number,
    sex: number
}
type person = user & age
// {
//     id: number,
//     name: string,
//     age: number,
//     sex: number
// }
```
同名属性合并，如果类型兼容，会收窄为子类型，如果不同类型（且是基础类型），新的属性是无用类型never，因为一个值不可能同时有两种类型
但是如果不是基础类型，是可以合并的
```js
interface A {
    x: {
        a: true
    }
}
interface B {
    x: {
        b: string
    }
}
type AB = a & B
let v: AB = {
    x: {
        a: true,
        b: "xxx"
    }
}
```

### 接口
用来定义对象的类型，描述对象的结构和对行为进行抽象，即一个对象有哪些属性和方法
不能少属性，也不能多属性，只有可选属性能够动态存在
```js
// 接口首字母一般大写
interface Person {
    name: string,
    age: number,
    money?: number, // 可选属性，可以没有
    readonly id: number, // 只读属性，只能在创建的时候赋值，后面不能修改
    [prop: string]: any, // 有时候无法确定完整的对象，那么可以有任意属性，但是key肯定是string
    [prop: string]: string, // 无法确定完整的对象，但是可以肯定新的属性肯定是string等类型，那么可以收窄类型
}
```

### 黑科技之鸭式辩型法
如果一个动物，像鸭子一样走路，并且像鸭子一样嘎嘎叫，那它就是鸭子
或者说，具有鸭子特征的就认为它是鸭子
有什么用呢，我们假设一个函数用来处理鸭子
```js
// 先定义鸭子的接口
interface Duck {
    name: "duck",
    say: "gagaga",
}
function dealDuck(duck: Duck) {
    //
}
dealDuck({name: "duck", say: "gagaga"}) // 这样是ok的
dealDuck({name: "duck", say: "gagaga", more: "xxx"}) // error， 因为这个参数会直接赋值给duck，而因为不满足接口结构，所以报错

// 绕开类型检查，先声明一个变量
let obj = {name: "duck", say: "gagaga", more: "xxx"} // 这样是ok的
dealDuck(obj) // ok
// 因为obj不会进行额外类型检查，被ts推断为{name: "duck", say: "gagaga", more: "xxx"}
// 然后把obj赋值给duck，根据类型的兼容性，obj既有duck类型的name，又有gagaga类型的say，那obj就是Duck，所以可以通过
```
另外一种方式就是类型断言，直接告诉ts就不会进行额外的检查了
```js
let obj: Duck = {
    name: "duck",
    say: "gagaga",
    more: "xxx"
} as Duck
dealDuck(obj) // ok
```
再一种就是索引签名，给接口里加上任意参数，就没毛病了
```js
interface Duck {
    name: "duck",
    say: "gagaga",
    [key: string]: any
}
```

### 接口interface和类型别名type的区别
ts核心原则就是检查值的结构，而接口的作用就是为这些类型命名，和味代码定义数据模型
type会给一个类型（或者联合类型）起一个别名，如果我们觉得string太长，可以type str = string
**type不会创建新的类型**

都可以描述对象和函数，语法不同
```js
interface add {
    (x: number, y: number): number;
}
type add = (x: number, y: number) => number;

interface user {
    name: string,
    age: number
}

type user = {
    name: string,
    age: number
}
```
type可以用于基础类型，联合类型，元祖等
type定义的类型可以是typeof运算的结果
```js
type dom = typeof document.getElementById("dd")
```

接口可以重复定义，自动合并
```js
interface user {
    name: string
}
interface user {
    age: number
}
// {
//     name: string,
//     age: number
// }
```
接口的扩展通过extends，类型别名的扩展通过交叉类型&
interface扩展interface
```js
interface x {
    x: number
}
interface point extends x {
    y: number
}
// {
//     x: number,
//     y: number
// }
```

type扩展type
```js
type x = {
    x: number
}

type point = x & {
    y: number
}
```

interface扩展type
```js
type x = {
    x: number
}
interface point extends x {
    y: number
}
```

type扩展interface
```js
interface x {
    x: number
}
type point = x & {
    y: number
}
```

### 泛型，是一个抽象类型，只有在调用的时候才能确定值
一个直接返回参数的函数
```js
function identity<T>(arg: T): T {
    return arg
}
identity<number>(12)
identity(12) // T就是number类型
// arg: T很好理解，就是说参数arg是T类型的，实际调用的时候是什么类型
// : T 也好理解，就是说函数返回的类型是T，跟参数是一样的
// <T>，是啥意思，为什么一定要在函数名和参数中间？
// 因为泛型也要先定义让ts知道，所以<T>是一个泛型定义，要在参数用到之前就定义好，而T只是一个代称，可以是别的，K，V，E什么的，但是定义的时候用的什么，后面使用的是也要是什么
// 调用的时候也要带上泛型，但是可以省略由编译器自动推断
```

### 泛型约束
大多数时候，我们并不是直接处理参数，因为参数大多时候都是对象options，我们要处理属性，而由于T可以是任类型，如果我们直接arg.xxx，这个肯定是报错的，因为不是所有的类型都会有xxx这个属性
因此要在我们已知一些情况下对泛型进行约束，比如我们要用到参数的size属性
```js
interface Args {
    size: number
}
function trace<T extends Args>(arg: T): T {
    console.log(arg.size) // 由于泛型扩展了一个具有size属性的接口，所以可以使用
    return arg
}
```