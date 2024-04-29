# CHAPTER 3 자바스크립트 뿌리 파헤치기

<br>

- [CHAPTER 3 자바스크립트 뿌리 파헤치기](#chapter-3-자바스크립트-뿌리-파헤치기)
  - [3.1 이터레이션(iteration)](#31-이터레이션iteration)
    - [이터레이터(iterator)](#이터레이터iterator)
    - [이터레이터 패턴(iterator pattern)](#이터레이터-패턴iterator-pattern)
    - [3.1.1 이터레이터 소비하기](#311-이터레이터-소비하기)
    - [이터레이션 프로토콜(Iteration protocols)](#이터레이션-프로토콜iteration-protocols)
    - [3.1.2 이터러블(iterable)](#312-이터러블iterable)
    - [(추가) `iterator`와 `iterable`의 차이점](#추가-iterator와-iterable의-차이점)
  - [3.2 클로저(closure)](#32-클로저closure)
  - [3.3 `this` 키워드](#33-this-키워드)
  - [3.4 프로토타입](#34-프로토타입)
    - [3.4.1 객체 연결 장치](#341-객체-연결-장치)
    - [3.4.2 `this` 다시 보기](#342-this-다시-보기)
  - [3.5 ‘왜?’라고 질문하기](#35-왜라고-질문하기)
  - [참조](#참조)

<br>

## 3.1 이터레이션(iteration)

### 이터레이터(iterator)

- **참조:**
  - [Iterators and generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_generators)
  - [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator)


### 이터레이터 패턴(iterator pattern)

```js
let colours = ['Red', 'Green', 'Blue', 'Yellow', 'Purple'];

// 이터레이터 객체 생성
let iterator = colours[Symbol.iterator]();

console.log(iterator) // [object Array Iterator]

// next() 메서드를 사용하여 데이터를 순차적으로 처리
let next = iterator.next();

console.log(next) // [object Object]

while(!next.done) {
    alert(next.value);  // 배열의 각 항목을 출력: 'Red', 'Green', 'Blue', 'Yellow', 'Purple'
    next = iterator.next();
}
```

- **참조:** 
  - [MDN: Generator.prototype.next()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
  - [SQL SELECT Statement](https://www.w3schools.com/sql/sql_select.asp)
  - [JavaScript Iterator Design Pattern](https://www.dofactory.com/javascript/design-patterns/iterator)

### 3.1.1 이터레이터 소비하기

### 이터레이션 프로토콜(Iteration protocols)

- **참조:**
  - [Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
  - [Poiemawb, 이터레이션 프로토콜](https://poiemaweb.com/es6-iteration-for-of)

```js
// 어떤 데이터 소스의 반복자가 주어졌습니다:
var it = /* .. */;

// 한 번에 하나씩 결과를 반복합니다.
for (let val of it) {
    console.log(`Iterator value: ${ val }`);
}
// Iterator value: ..
// Iterator value: ..
// ..
```

```js
// 반복자를 배열로 퍼뜨립니다.
// 각 반복된 값이
// 배열 요소 위치를 차지합니다.
var vals = [ ...it ];
```


```js
// 반복자를 함수로 퍼뜨립니다.
// 각 반복된 값이
// 인수 위치를 차지하는 함수를 호출합니다.
doSomethingUseful( ...it );
```

### 3.1.2 이터러블(iterable)

```js
// 배열은 반복 가능합니다.
var arr = [ 10, 20, 30 ];

for (let val of arr) {
    console.log(`Array value: ${ val }`);
}
// Array value: 10
// Array value: 20
// Array value: 30
```

```js
var arrCopy = [ ...arr ];
```

```js
var greeting = "Hello world!";
var chars = [ ...greeting ];

chars;
// [ "H", "e", "l", "l", "o", " ",
//   "w", "o", "r", "l", "d", "!" ]
```

```js
// 두 개의 DOM 요소, `btn1`과 `btn2`가 주어졌습니다.

var buttonNames = new Map();
buttonNames.set(btn1,"Button 1");
buttonNames.set(btn2,"Button 2");

for (let [btn,btnName] of buttonNames) {
    btn.addEventListener("click",function onClick(){
        console.log(`Clicked ${ btnName }`);
    });
}
```

```js
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2
```

```js
var arr = [ 10, 20, 30 ];

for (let [idx,val] of arr.entries()) {
    console.log(`[${ idx }]: ${ val }`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

<br>

### (추가) `iterator`와 `iterable`의 차이점

- **참조**:
  - [Iterators and Iterables in Javascript](https://www.connectwise.com/blog/engineering/iterators-and-iterables-in-javascript#:~:text=Iterators%20provide%20a%20standardized%20way,to%20suit%20their%20specific%20needs.) 

<br>

## 3.2 클로저(closure)

```js
function greeting(msg) {
    return function who(name) {
        console.log(`${ msg }, ${ name }!`);
    };
}

var hello = greeting("Hello");
var howdy = greeting("Howdy");

hello("Kyle");
// Hello, Kyle!

hello("Sarah");
// Hello, Sarah!

howdy("Grant");
// Howdy, Grant!
```

```js
function counter(step = 1) {
    var count = 0;
    return function increaseCount(){
        count = count + step;
        return count;
    };
}

var incBy1 = counter(1);
var incBy3 = counter(3);

incBy1();       // 1
incBy1();       // 2

incBy3();       // 3
incBy3();       // 6
incBy3();       // 9
```


```js
function getSomeData(url) {
    ajax(url,function onResponse(resp){
        console.log(
            `Response (from ${ url }): ${ resp }`
        );
    });
}

getSomeData("<https://some.url/wherever>");
// Response (from <https://some.url/wherever>): ...
```


```js
for (let [idx,btn] of buttons.entries()) {
    btn.addEventListener("click",function onClick(){
       console.log(`Clicked on button (${ idx })!`);
    });
}
```

<br>

## 3.3 `this` 키워드


```js
function classroom(teacher) {
    return function study() {
        console.log(
            `${ teacher } says to study ${ this.topic }`
        );
    };
}
var assignment = classroom("Kyle");
```


```js
assignment();
// Kyle says to study undefined  -- Oops :(
```

```js
var homework = {
    topic: "JS",
    assignment: assignment
};

homework.assignment();
// Kyle says to study JS
```

```js
var otherHomework = {
    topic: "Math"
};

assignment.call(otherHomework);
// Kyle says to study Math
```


<br>

## 3.4 프로토타입

```js
var homework = {
    topic: "JS"
};
```

```js
homework.toString();    // [object Object]
```

### 3.4.1 객체 연결 장치

```js
var homework = {
    topic: "JS"
};

var otherHomework = Object.create(homework);

otherHomework.topic;   // "JS"
```

```js
homework.topic;
// "JS"

otherHomework.topic;
// "JS"

otherHomework.topic = "Math";
otherHomework.topic;
// "Math"

homework.topic;
// "JS" -- not "Math"
```

### 3.4.2 `this` 다시 보기

```js
var homework = {
    study() {
        console.log(`Please study ${ this.topic }`);
    }
};

var jsHomework = Object.create(homework);
jsHomework.topic = "JS";
jsHomework.study();
// Please study JS

var mathHomework = Object.create(homework);
mathHomework.topic = "Math";
mathHomework.study();
// Please study Math
```

<br>

## 3.5 ‘왜?’라고 질문하기


<br>

## 참조

- [Chapter 3: Digging to the Roots of JS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/get-started/ch3.md)