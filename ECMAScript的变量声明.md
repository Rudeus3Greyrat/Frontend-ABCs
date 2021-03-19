**「前端ABC」**系列旨在快速讲解前端重要的知识点，并给出最佳实践。

[**「Github传送门」**](https://github.com/Rudeus3Greyrat/Frontend-ABCs)，欢迎star。

## 1 ECMAScript的变量声明

ECMAScript中有三个关键字可以用来声明变量，分别是var, let, const。三者的通用格式如下： 
 [关键字] [标识符] = [值] 
 如 `const message = 'hello'`。 
 其中= [值]是对于var和let是可选的，对于const则是必须的。**「如果不选的话，变量将会被赋予默认值`undefined`。」** 
 接下来让我们逐步搞清三种变量声明方式的区别。

## 2 var

使用var关键字进行变量声明是ECMAScript 2015之前的唯一变量声明方式。根据var声明语句所在作用域的区别，声明的变量具有不同的行为：

### 2.1 在函数作用域中

此时声明的变量**「会成为包含它的函数的局部变量，函数执行完毕后立即销毁」**（在没有闭包的情况下），如下所示：

```
function demo(){
    var message = 'hello'
}

demo()

//ReferenceError: message is not defined
console.log(message)
```

### 2.2 在全局作用域或其他块作用域中

此时声明的变量将变成**「全局变量」**,并成为window对象的属性（浏览器环境）。如下所示：

```
var message = 'hello'

function demo(){
    console.log(message)
}

demo() //hello
if(true){
    var message = 'hello'
}

function demo(){
    console.log(message)
}

demo() //hello

window.alert(window.message) //hello
```

### 2.3 特殊情况：省略var关键字

值得注意的是，使用var声明变量可以省略var关键字，这样声明得到的变量**「将变成全局变量，并成为全局对象的属性」**。如下所示：

```
function demo(){
    message = 'hello'
}

demo()

console.log(message) //hello

console.log(globalThis.message) //hello
```

其中`globalThis`是标准化的全局对象。 通常而言，这会导致意外的全局变量，是不好的实践。 
 
 使用var进行变量声明有以下几个特点：

- **「进行重复声明不会报错」**

```
var message = 'hello'
var message = 'world'
console.log(message) //world
```

- **「存在变量提升」**

```
//可以在变量声明前访问到该变量
console.log(message) //undefined
var message = 'hello'
```

ECMAScript在运行时会把**「var」**变量声明拉到作用域的顶部，因此上面的代码等价于

```
var message //undefined
console.log(message) //undefined
message = 'hello'
```

## 3 let

**「let」**和**「const」**是ECMAScript 2015出现的新的变量命名关键字。它们命名变量的行为和**「var」**差别巨大。

- **「let声明所在的范围会成为块作用域，意味着块之外无法引用该变量」**

```
if(true){
    let message = 'hello'
}

//ReferenceError: message is not defined
console.log(message)
```

如之前所说，在函数作用域中使用**「var」**声明的变量是局部变量，函数作用域外也无法访问。不过在**「if」**, **「while」**等语句块中使用**「var」**声明的变量，语句块之外是可以访问的，因为使用var声明变量不存在块作用域概念。如下所示：

```
function demo(){
    var message = 'hello'
}

//ReferenceError: message is not defined
console.log(message)
if(true){
    var message = 'hello'
}

console.log(message) //hello
```

- **「进行重复声明会报错」**

```
let message = 'hello'

//SyntaxError: Identifier 'message' has already been declared
let message = 'world'
```

- **「全局作用域中let声明的变量不会成为全局对象的属性」**

```
let message = 'hello'
console.log(globalThis.message) //undefined
```

- **「存在暂时性死区」** 
   **「let」**声明的变量不会在作用域中得到提升。因此在**「let」**声明之前对该变量进行引用都会抛出**「ReferenceError」**，我们把**「let」**声明之前的这个阶段称作该变量的“暂时性死区”（temporal dead zone）。如下所示：

```
//ReferenceError: Cannot access 'message' before initialization
console.log(message)
let message = 'hello'
```

事实上，JavaScript引擎已经注意到了之后声明的变量，不过该变量是uninitialized的状态，引擎不允许对该变量的任何引用直到该变量被声明。

## 4 const

**「const」**声明变量的行为与**「let」**基本相同，不过仍有两点需要注意：

- 如前所说，使用**「const」**声明变量时必须**「同时以显式的值对变量进行初始化」**，否则会报错：

```
//SyntaxError: Missing initializer in const declaration
const message
```

- const声明通常表示声明的变量是一个常量，因此**「修改它的值（原始类型的值和引用类型的引用）会报错」**

```
const message = 'hello'
//TypeError: Assignment to constant variable.
message = 'world'
const message = {a:'hello'}
//TypeError: Assignment to constant variable.
message = {b:'hello'}
```

需要注意的是，如果变量指向的是引用类型，该限制**「只适用于变量指向的引用类型的引用」**。修改这个引用类型的内部属性并不会报错。如下所示：

```
const message = {a:'hello'}
message.b = 'world'
//{ a: 'hello', b: 'world' }
console.log(message)
```

## 5 最佳实践

- **「不使用var」** 
   **「var」**声明变量的行为怪异，会造成各种问题，因此使用**「let」**和**「const」**是更好的选择。 
- **「优先使用const」** 
   使用**「const」**可以帮助引擎发现意外赋值导致的非预期行为。因此，只有在我们知道变量的值在未来可能会改变时才使用**「let」**，除此之外使用**「const」**是更好的选择。

## 6 思考

以下代码会输出什么？你知道原因吗？

```
for(var i=0;i<5;i++){
    setTimeout(()=>console.log(i),0)
}
for(let i=0;i<5;i++){
    setTimeout(()=>console.log(i),0)
}
```