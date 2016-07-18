# ES6 для людей

<br>

### Содержание

* [let, const и область видимости](#1-let-const-и-область-видимости)
* [Стрелочные функции](#2-Стрелочные-функции)
* [Параметры функции по умолчанию](#3-Параметры-функции-по-умолчанию)
* [Оператор расширения / Оставшиеся параметры](#4-Оператор-расширения--Оставшиеся-параметры)
* [Расширение литералаов объекта](#5-Расширение-литералаов-объекта)
* [Восьмеричные и Бинарные литералы](#6-Восьмеричные-и-Бинарные-литералы)
* [Деструктуризация массивов и объектов](#7-Деструктуризация-массивов-и-объектов)
* [super в объектах](#8-super-в-объектах)
* [Шаблонные строки](#9-Шаблонные-строки)
* [Отличия for...of и for...in](#10-Отличия-forof-и-forin)
* [Map и WeakMap](#11-map-и-weakmap)
* [Set и WeakSet](#12-set-и-weakset)
* [Классы в ES6](#13-Классы-в-es6)
* [Символ](#14-Символ)
* [Итераторы](#15-Итераторы)
* [Генераторы](#16-Генераторы)
* [Promise (Обещание)](#17-promise)

<br>

### Переводы

* [English version (metagrover)](https://github.com/metagrover/ES6-for-humans)
* [Chinese Version (barretlee)](http://www.barretlee.com/blog/2016/07/09/a-kickstarter-guide-to-writing-es6/)
* [Portuguese Version (alexmoreno)](https://github.com/alexmoreno/ES6-para-humanos)

<br>

### 1. let, const и область видимости

*MDN:
[let](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/let),
[const](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/const)*
<br>
*javascript.ru:
[let](https://learn.javascript.ru/let-const#let),
[const](https://learn.javascript.ru/let-const#const)*

Оператор `let` позволяет объявить локальную переменную с ограниченной текущим блоком кода областью видимости. В отличие от ключевого слова `var`, которое объявляет переменную глобально или локально во всей функции независимо от области блока. В ES6 рекомендуется использовать `let`.

```javascript
var a = 2
{
  let a = 3
  console.log(a) // 3
}
console.log(a) // 2
```
Оператор `const` создаёт новую константу. Имена констант подчиняются тем же правилам что и обычные переменные. Значение константы нельзя менять/перезаписывать. Также её нельзя объявить заново.

```javascript
{
  const ARR = [5,6]
  ARR.push(7)
  console.log(ARR) // [5,6,7]
  ARR = 10 // TypeError
  ARR[0] = 3 // значение может изменяться
  console.log(ARR) // [3,6,7]
}
```

Важно помнить:

* `let` и `const` видны только после объявления и только в текущем блоке.
* `let` и `const` нельзя переобъявлять (в том же блоке).
* Константы, которые жёстко заданы всегда, во время всей программы, обычно пишутся в верхнем регистре.
* Константа (`const`) должна быть определена при объявлении.

<br>

### 2. Стрелочные функции

*javascript.ru:
[Стрелочные функции](https://learn.javascript.ru/es-function#функции-через)*

Выражения стрелочных функций имеют более короткий синтаксис по сравнению с функциональными выражениями и лексически привязаны к значению `this` (но не привязаны к собственному `this`, `arguments`, `super`, `new.target`). Стрелочные функции всегда анонимные.

```javascript
let addition = function(a, b) {
    return a + b
}

// Реализация со стрелочной функцией
let addition = (a, b) => a + b // Краткая форма.

let additions = (a, b) => { // Блочная форма.
  return a + b
}
```

**Обратите внимание**, что в приведенном выше примере стрелочная функция с краткой формой возвращает полученное значение по умолчанию, а блочная форма требует явного возврата значения через `return`.

**Не имеют своего this**

Поведение стрелочной функции с ключевым словом `this` отличается от обычной функции. У каждой обычной функции есть свой `this` контекст, но стрелочная функция захватывает контекст `this` из внешнего контекста.

```javascript
function Person() {
  // В конструктор Person() `this` указывает на себя.
  this.age = 0

  setInterval(function growUp() {
    // В нестрогом режиме, в функции growUp() `this` указывает
    // на глобальный объект, который отличается от `this`,
    // определяемом в конструкторе Person().
    this.age++
  }, 1000)
}

var p = new Person()
```

В ECMAScript 3/5, данная проблема решалась присваиванием значения `this` близко расположенной переменной:

```javascript
function Person() {
  var self = this
  self.age = 0

  setInterval(function growUp() {
    // В функции используется переменная `self`, которая
    // имеет значение требуемого объекта.
    self.age++
  }, 1000)
}
```

Как писалось выше, стрелочные функции захватывают значение `this` окружающего контекста, поэтому нижеприведенный код работает как предполагалось:

```javascript
function Person(){
  this.age = 0

  setInterval(() => {
    this.age++ // `this` указывает на объект Person
  }, 1000)
}

var p = new Person()
```
[Узнать больше о лексике this в стрелочных функциях (MDN)](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Лексика_this)

<br>

### 3. Параметры функции по умолчанию

*javascript.ru:
[Параметры по умолчанию](https://learn.javascript.ru/es-function#параметры-по-умолчанию)*

ES6 позволет задавать формальным параметрам функции значения по умолчанию, если для них не указано значение или передан `undefined`.

```javascript
let getFinalPrice = (price, tax=6) => (price + price) * tax
console.log(getFinalPrice(50))            // 600
console.log(getFinalPrice(50, 2))         // 200
console.log(getFinalPrice(50, null))      // 0
console.log(getFinalPrice(50, 'string'))  // null
console.log(getFinalPrice(50, undefined)) // 600
```

<br>

### 4. Оператор расширения / Оставшиеся параметры

*MDN:
[Spread operator](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Spread_operator),
[Rest parameters](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Functions/Rest_parameters)*
<br>
*javascript.ru:
[Spread и Rest](https://learn.javascript.ru/es-function#оператор-spread-вместо-arguments)*

**Оператор расширения** позволяет расширять выражения в тех местах, где предусмотрено использование нескольких аргументов (при вызовах функции) или ожидается несколько элементов (для массивов).

```javascript
function foo(x, y, z) {
  console.log(x, y, z)
}

let arr = [1, 2, 3]
foo(...arr) // 1 2 3
```

Синтаксис **оставшихся параметров** функции позволяет представлять неограниченное множество аргументов в виде массива.

```javascript
const foo = (...args) => {
  console.log(args)
}
foo(1, 2, 3, 4, 5) // [1, 2, 3, 4, 5]
```

<br>

### 5. Расширение литералаов объекта

*javascript.ru:
[Короткое свойство](https://learn.javascript.ru/es-object#короткое-свойство),
[Вычисляемые свойства](https://learn.javascript.ru/es-object#вычисляемые-свойства)*
ES6 позволяет объявлять литералы объекта, использую сокращенный синтаксис для инициализации свойств переменных и свойств-функциий. Так же появилась возможность использовать вычисляемые названия свойств объекта.


```javascript
const getCar = (make, model, value) => {
  return {
    // значение свойства можно опустить,
    // если оно соответствует названию переменной
    make,  // эквивалентно: make: make
    model, // эквивалентно: model: model
    value, // эквивалентно: value: value

    // вычисляемые значения теперь работают
    // с литералами объекта
    ['make' + make]: true,

    // при определении метода сокращенным синтаксисом,
    // опускается ключевое слово `function` и двоеточие
    depreciate() {
        this.value -= 2500
    }
  }
}

let car = getCar('Kia', 'Sorento', 40000)
console.log(car)
// {
//     make: 'Kia',
//     model:'Sorento',
//     value: 40000,
//     makeKia: true,
//     depreciate: function()
// }
```

<br>

### 6. Восьмеричные и Бинарные литералы

*MDN:
[Numeric literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Numeric_literals)*

В ES6 добавили поддержку восьмеричных и двоичных(бинарных) литералов.
Добавьте перед числом `0o` или `0O` для получения его восьмеричного значения, либо `0b` или `0B` для бинарного.

```javascript
let octalValue = 0o10 // `0o` или `0O` - восьмеричный
console.log(octalValue) // 8

let binaryValue = 0b10 // `0b` или `0B` - бинарный
console.log(binaryValue) // 2
```

<br>

### 7. Деструктуризация массивов и объектов

*MDN:
[Destructuring assignment](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)*
<br>
*javascript.ru:
[Деструктуризация](https://learn.javascript.ru/destructuring)*

Деструктуризация помогает избежать необъодимости создавать временные переменные при работе с массивами и объектами.

```javascript
const arr = [1, 2, 3]
let [a, b, c] = arr
console.log(a, b, c) // 1 2 3

const obj = {
  x: 4,
  y: 5,
  z: 6
}
let {x: aX, y: bY, z: cZ} = obj
console.log(aX, bY, cZ) // 4 5 6
```

<br>

### 8. super в объектах

*MDN: [Super](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/super)*

ES6 позволяет использовать метод `super` в (бесклассовых) объектах с прототипами.

```javascript
const parent = {
  foo() {
    console.log('Hello from the Parent')
  }
}

const child = {
  foo() {
    super.foo()
    console.log('Hello from the Child')
  }
}

Object.setPrototypeOf(child, parent)
child.foo() // Hello from the Parent
            // Hello from the Child
```

<br>

### 9. Шаблонные строки

*MDN: [Template strings](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/template_strings)*

Шаблонные строки (шаблоны) является строковыми литералами, допускающими использование выражений.
Вы можете использовать многострочные литералы и возможности интерполяции.


```javascript
let user = 'Kevin'
console.log(`Hi ${user}!`) // Hi Kevin!

console.log(`5 + 5 = ${5 + 5}!`) // 5 + 5 = 10!

console.log(`string text line 1
string text line 2`)
// string text line 1
// string text line 2
```

<br>

### 10. Отличия for...of и for...in

*MDN:
[for...of](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/for...of),
[for...in](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Statements/for...in)*

`for...of` обходит значения свойств.

```javascript
let nicknames = ['di', 'boo', 'punkeye']
nicknames.size = 3
for (let nickname of nicknames) {
  console.log(nickname)
}
// di
// boo
// punkeye
```

`for...in` обходит имена свойств.

```javascript
let nicknames = ['di', 'boo', 'punkeye']
nicknames.size = 3
for (let nickname in nicknames) {
  console.log(nickname)
}
// 0
// 1
// 2
// size
```

<br>

### 11. Map и WeakMap

ES6 introduces new set of data structures called `Map` and `WeakMap`. Now, we actually use maps in JavaScript all the time. Infact every object can be considered as a `Map`.

An object is made of keys (always strings) and values, whereas in `Map`, any value (both objects and primitive values) may be used as either a key or a value. Have a look at this piece of code:

```javascript
var myMap = new Map()

var keyString = 'a string',
    keyObj = {},
    keyFunc = function () {}

// setting the values
myMap.set(keyString, 'value associated with "a string"')
myMap.set(keyObj, 'value associated with keyObj')
myMap.set(keyFunc, 'value associated with keyFunc')

myMap.size; // 3

// getting the values
myMap.get(keyString)    // "value associated with 'a string'"
myMap.get(keyObj)       // "value associated with keyObj"
myMap.get(keyFunc)      // "value associated with keyFunc"
```

**WeakMap**

A `WeakMap` is a Map in which the keys are weakly referenced, that doesn’t prevent its keys from being garbage-collected. That means you don't have to worry about memory leaks.

Another thing to note here- in `WeakMap` as opposed to `Map` *every key must be an object*.

A `WeakMap` only has four methods `delete(key)`, `has(key)`, `get(key)` and `set(key, value)`.

```javascript
let w = new WeakMap()
w.set('a', 'b')
// Uncaught TypeError: Invalid value used as weak map key

let o1 = {}
let o2 = function(){}
let o3 = window

w.set(o1, 37)
w.set(o2, 'azerty')
w.set(o3, undefined)

w.get(o3) // undefined, потому что мы установили это значение

w.has(o1) // true
w.delete(o1)
w.has(o1) // false
```

<br>

### 12. Set и WeakSet

*MDN:
[Set](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Set)*
<br>
*javascript.ru:
[Set](https://learn.javascript.ru/set-map#set)*

Объекты `Set` представляют коллекции значений, по который вы можете выполнить обход в порядке вставки элементов. Значение элемента (любого типа, как примитивы, так и другие типы объектов) в `Set` может присутствовать только в одном экземпляре, что обеспечивает его уникальность в рамках коллекции `Set`.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3])
mySet.size // 3
mySet.has(1) // true
mySet.add('strings')
mySet.add({ a: 1, b:2 })
```

`Set` обладает и другими [методами](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Set#Методы).

`forEach` и `for...of` позволяют пройти значения `Set` в порядке их добавления в набор.

```javascript
mySet.forEach((item) => {
  console.log(item)
  // 1
  // 2
  // 3
  // 'strings'
  // Object { a: 1, b: 2 }
})

for (let value of mySet) {
  console.log(value)
  // 1
  // 2
  // 3
  // 'strings'
  // Object { a: 1, b: 2 }
}
```

**WeakSet**

*MDN:
[WeakSet](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)*
<br>
*javascript.ru:
[Weakmap и Weakset](https://learn.javascript.ru/set-map#weakmap-и-weakset)*

Объект `WeakSet` - коллекция, элементами которой могут быть только объекты. Ссылки на эти объекты в WeakSet являются слабыми. Каждый объект может быть добавлен в WeakSet только один раз.

```javascript
var ws = new WeakSet()
var obj = {}
var foo = {}

ws.add(window)
ws.add(obj)

console.log(ws.has(window)) // true
console.log(ws.has(foo)) // false, т.к. мы не добавили `foo` в набор

ws.delete(window) // удаляет `window` из набора
console.log(ws.has(window))    // false, т.к. мы удалили `window` из наборы
```

<br>

### 13. Классы в ES6

*MDN:
[Classes](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Classes)*

Классы представляют собой синтаксический сахар существующих в языке прототипных наследований. Синтаксис класса не вводит новую объектно-ориентированную модель наследования, он просто продоставляет гораздо более простой и понятный способ создания объектов.

Ключевое слово `static` определяет для класса статический метод. Статические методы вызываются без инстанцирования класса и не могут быть вызваны у экземпляров класса. Статические методы часто используются для создания служебных функций для приложения.

```javascript
class Task {
  constructor() {
    console.log('task instantiated!')
  }

  showId() {
    console.log(23)
  }

  static loadAll() {
    console.log('Loading all tasks..')
  }
}

console.log(typeof Task) // function
let task = new Task() // "task instantiated!"
task.showId() // 23
Task.loadAll() // "Loading all tasks.."
task.loadAll() // error: task.loadAll is not a function
```

**extends and super в классах**

Ключевое слово `extends` используется в объявлениях классов и выражениях классов для создания класса дочернего относительно другого класса.

Ключевое слово `super` используется для вызова функций на родителе объекта.

```javascript
class Car {
  constructor() {
    console.log('Creating a new car')
  }
}

class Porsche extends Car {
  constructor() {
    super()
    console.log('Creating Porsche')
  }
}

let c = new Porsche()
// Creating a new car
// Creating Porsche
```

`extends` позволяет дочернему классу наследовать родительский класс, при этом конструктор дочернего класса должен содержать вызов `super()`.

Использовать метод родительского класса в методах дочернего класса можно с помощью `super.parentMethodName()`.

**Важно:**

* Объявлением класса не совершает подъём (*hoisted*). Поэтому вначале необходимо объявить ваш класс и только затем работать с ним, иначе получите ошибку **ReferenceError**.
* При определении функции внутри определения класса не нужно использовать ключевое слово `function`.

<br>

### 14. Символ

*MDN:
[Symbol](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Symbol)*

Символ — это уникальный и неизменяемый тип данных, который может быть использован как идентификатор для свойств объектов. Задача символа в генерации уникального идентификатора значение которого получить нельзя.

Создадим символ:

```javascript
const sym = Symbol('some optional description');
console.log(typeof sym) // symbol
```

**Обратите внимание**, что оператор `new` нельзя использовать c `Symbol(…)`.

```javascript
const sym = new Symbol() // TypeError
```

Символы невидимы при итерации `for...in`. В дополнение к этому, `Object.getOwnPropertyNames()` не вернет символьные свойства объекта.

```javascript
const obj = {
  val: 10,
  [ Symbol('random') ]: 'I\'m a symbol'
}

console.log(Object.getOwnPropertyNames(obj)) // ["val"]
for (let i in obj) {
  console.log(i) // val
}
```

Символьное свойство объекта можно получить с помощью `Object.getOwnPropertySymbols(obj)`.

<br>

### 15. Итераторы

*MDN:
[Iterators](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Iterators_and_Generators#Итераторы)*

An iterator accesses the items from a collection one at a time, while keeping track of its current position within that sequence.
Это обеспечивает метод `next()`, который возвращает следующий элемент в последовательности. Этот метод возвращает объект с 2 свойствами: **done** и **value**.

ES6 has `Symbol.iterator` which specifies the default iterator for an object. Whenever an object needs to be iterated (such as at the beginning of a for..of loop), its @@iterator method is called with no arguments, and the returned iterator is used to obtain the values to be iterated.

Let’s look at an array, which is an iterable, and the iterator it can produce to consume its values:

```javascript
let arr = [11, 12, 13]
let itr = arr[Symbol.iterator]()

console.log(itr.next()) // { value: 11, done: false }
console.log(itr.next()) // { value: 12, done: false }
console.log(itr.next()) // { value: 13, done: false }

console.log(itr.next()) // { value: undefined, done: true }
```

Note that you can write custom iterators by defining `obj[Symbol.iterator]()` with the object definition.

<br>

### 16. Генераторы

*MDN:
[Generators](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Iterators_and_Generators#Генераторы)*

Генераторы — это специальный тип функции, который работает как фабрика итераторов. Функция становится генератором, если содержит один или более `yield` операторов и использует `function*` синтаксис.

Генераторы позволяют определить алгоритм перебора, написав единственную функцию, которая умеет поддерживать собственное состояние.

```javascript
function* infiniteNumbers() {
  let n = 1
  while (true) {
    yield n++
  }
}

let numbers = infiniteNumbers() // возвращает итерируемый объект

console.log(numbers.next()) // { value: 1, done: false }
console.log(numbers.next()) // { value: 2, done: false }
console.log(numbers.next()) // { value: 3, done: false }
```

После каждого вызова генератор получает следующее в последовательности значение.

**Обратите внимание**, что генераторы вычисляют значение по требованию, это позволяет им эффективно вычислять даже бесконечные последовательности.

<br>

### 17. Promise

*MDN:
[Promise](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Global_Objects/Promise)*

Интерфейс **Promise** (обещание) представляет собой обертку для значения, неизвестного на момент создания обещания. Он позволяет обрабатывать результаты асинхронных операций так, как если бы они были синхронными: вместо конечного результата асинхронного метода возвращается обещание получить результат в некоторый момент в будущем.

При создании Обещание находится в ожидании (**pending**), а затем может стать выполнено (**fulfilled**), вернув полученный результат (значение), или отклонено (**rejected**), вернув причину отказа. В любом из этих случаев вызывается обработчик, прикрепленный к обещанию методом `then`. Если в момент прикрепления обработчика обещание уже сдержано или нарушено, он все равно будет выполнен, т.е. между выполнением обещания и прикреплением обработчика нет «состояния гонки», как, например, в случае с событиями в DOM.

Для создания обещания используется конструктор `new Promise()`, который принимает обработчик. Этот обработчик получает две функции в качестве аргументов. Первый аргумент (обычно называют **resolve**) вызывает успешное выполнение обещания. Второй (обычно называют **reject**) отклоняет это обещание.

```javascript
var p = new Promise((resolve, reject) => {
  if (/* условие */) {
    resolve(/* значение */)  // операция завершена успешно
  } else {
    reject(/* причина */)  // операция завершена с ошибкой.
  }
})
```

У обещания есть метод `then`, который принимает такие же аргументы, как и само обещание.

```javascript
p.then(
  val => console.log('Promise Resolved', val),
  err => console.log('Promise Rejected', err)
)
```

Возвращаемое значение из `then` передается в следующий `then` через аргумент.

```javascript
var hello = new Promise((resolve, reject) => {
  resolve('Hello')
})

hello.then(str => `${str} World`)
  .then(str => `${str}!`)
  .then(str => console.log(str)) // Hello World!
```

**Очередь асинхронных событий**

Для последовательного выполнения асинхронных действий можно связять вызовы `then`.

Когда ты возвращаешь что-то из колбэка `then`, происходит немного магии. Если ты возвращаешь любое значение, это значение передастся функции обратного вызова следующего `then`. А если ты вернёшь что-то похожее на обещание, следующий `then` подождёт его и вызовет колбэк только когда оно выполнится.
Это простой способ избежать "*ад колбеков* (callback hell)".

```javascript
const p = new Promise((resolve, reject) => {
  resolve(1)
})

const increment = val => {
  return new Promise((resolve, reject) => {
    resolve(val + 1)
  })
}

p.then(increment)
  .then(val => {
    console.log(val) // 2
    return val
  })
  .then(val => val + 1)
  .then(val => {
    console.log(val) // 3
    return val
  })
  .then(increment)
  .then(val => console.log(val)) // 4
```
