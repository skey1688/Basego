># 数据类型检测之instanceof()、typeof()、constructor()和Object.prototype.toString.call()
- [constructor](#constructor)
- [instanceof](#instanceof)
- [typeof](#typeof)
- [Object.prototype.toString.call()](#objectprototypetostringcall)
# constructor
constructor是构造函数的prototype（原型）里指向构造函数本身的属性，是随着prototype而产生的，用来解决prototype（原型）指向构造函数的问题。
如果prototype被赋值改变，则对应的constructor属性也会消失。
所以用constructor来判断是否是构造函数的实例不是非常严谨。

例子：
```js
function Person(){

}
//第一种情况
let person1 = new Person()
console.log(Person.prototype.constructor === Person);//true
console.log(person1.constructor === Person);//true

//第二种情况
Person.prototype = {
    name:'Mark',
    age:21
}
let person2 = new Person()
console.log(Person.prototype.constructor === Person);//false
console.log(person2.constructor === Person);//false

//第三种情况
Person.prototype = {
    constructor:Person,
    name:'Mark',
    age:21
}
let person3 = new Person()
console.log(Person.prototype.constructor === Person);//true
console.log(person3.constructor === Person);//true
console.log(Object.keys(Person.prototype));//['constructor', 'name', 'age']
//第四种情况
Object.defineProperty(Person.prototype,'constructor',{
    value:Person,
    enumerable:false
})
console.log(Object.keys(Person.prototype));//['name', 'age']

```
# instanceof
instanceof是一个运算符，只是用来操作对象（null除外）类型数据，通俗讲是判断左侧对象是否是右侧构造函数的实例。
具体讲就是判断的左侧对象的原型链__proto__是否在右侧构造函数的prototype上。
解决的也是实例与构造函数的关系判断（同constructor）,相比constructor更加严谨。

例子：
```js
function Person(){

}
// 常规属性
let person1 = new Person()
console.log(person1 instanceof Person);//true
console.log(person1 instanceof Object);//true
// 证明是其不存在constructor的缺陷,
Person.prototype = {
    name:'MArk',
    age:21
}
let person2 = new Person()
console.log(person2 instanceof Person);//true
console.log(person2 instanceof Object);//true

// 因为instanceof只是沿着原型链__proto__查找，所以只受其__proto__变化而影响。
person2.__proto__ = {}
console.log(person2 instanceof Person);//false
console.log(person2 instanceof Object);//true

// 当左侧对象的原型链上只有null时，instanceof会失真
Object.create(null) instanceof Object//false
/*注意，Object.create(null)方法创建的对象的__proto__是null,是绝对的空对象，没有原型，也没有任何继承*/

// 当右侧是null时，会报错，因为null没有__proto__以及constructor
console.log(person1 instanceOf n);//报错
console.log(person2 instanceOf n);//报错

// 只适用与对象，不适用原始类型值 以及 undefined 和 null
console.log('abc' instanceof Object);//false
console.log(12 instanceof Object);//false
console.log(true instanceof Object);//false
console.log(undefined instanceof Object);//false
console.log(null instanceof Object);//fale 
```
# typeof
typeof一般用于检测基本数据类型（string、number,boolean、undefined、symbol、null除外）和function
>1. 基本类型检测没有问题，包括string、number,boolean、undefined、function和symbol
```js
typeof('abc')//string
typeof(123)//number
typeof(true)//boolean
typeof(undefined)//undefined
typeof(function(){})//function
typeof(Symbol())//symbol
```
>2. 特殊值null检测结果是object,不适用null
```js
typeof(null)//Object
```
>3. 使用构造函数创建的基本数据类型也不适用
```js
typeof(new Number(12))//object
```
# Object.prototype.toString.call()
最优的能够准确判断数据类型的方法，如果需要全类型判断即可采用该方法。
```js
Object.prototype.toString.call('asd')
'[object String]'
Object.prototype.toString.call(123)
'[object Number]'
Object.prototype.toString.call(true)
'[object Boolean]'
Object.prototype.toString.call(null)
'[object Null]'
Object.prototype.toString.call(undefined)
'[object Undefined]'
Object.prototype.toString.call({})
'[object Object]'
Object.prototype.toString.call(function(){})
'[object Function]'
Object.prototype.toString.call([])
'[object Array]'
Object.prototype.toString.call(Symbol())
'[object Symbol]'
Object.prototype.toString.call(123n)
'[object BigInt]'
```
># 小结
- typeof只适用于null除外的基本数据类型的判断，即String、Number、Boolean、Undefined、Symbol、Bigint留种类型。
- constructor只适用于对象数据类型Object的判断,但是一旦prototype被修改，则会失效。
- instanceof只适用于对象数据类型Object的判断，但是其__proto__若被修改，则也会失效。
- Object.prototype.toString.call()适用于全部数据类型的判断。