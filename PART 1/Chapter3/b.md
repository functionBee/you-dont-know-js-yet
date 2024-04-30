# CHAPTER 3 자바스크립트 뿌리 파헤치기

<br>

- [CHAPTER 3 자바스크립트 뿌리 파헤치기](#chapter-3-자바스크립트-뿌리-파헤치기)
  - [3.1 이터레이션(iteration)](#31-이터레이션iteration)
    - [3.1.1 이터레이터 소비하기](#311-이터레이터-소비하기)
    - [3.1.2 이터러블(iterable)](#312-이터러블iterable)
    - [(추가) `iterator`와 `iterable`, `iteration`의 차이점](#추가-iterator와-iterable-iteration의-차이점)
  - [3.2 클로저(closure)](#32-클로저closure)
  - [3.3 `this` 키워드](#33-this-키워드)
  - [3.4 프로토타입](#34-프로토타입)
    - [3.4.1 객체 연결 장치](#341-객체-연결-장치)
    - [3.4.2 `this` 다시 보기](#342-this-다시-보기)
  - [3.5 ‘왜?’라고 질문하기](#35-왜라고-질문하기)
  - [참조](#참조)

<br>

## 3.1 이터레이션(iteration)

**이터레이터(iterator)**

자바스크립트에서 이터레이터(iterator)는 시퀀스를 정의하고 종료 시 반환 값이 있는 객체입니다.

```js
let iterator = sequence[Symbol.iterator]();
let next = iterator.next();
```

- 이터레이터(iterator)는 `next()` 메서드를 가지고 있어 Iterator 프로토콜을 구현할 수 있는 모든 객체입니다.

```js
const numbers = [1, 2, 3, 4, 5];

console.log(Symbol.iterator in numbers) // true

// for...of 루프 사용
for (const number of iterator) {
  console.log(number);
}
```

- **참조:**
  - [Iterators and generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_generators)
  - [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator)
  - [MDN: Generator.prototype.next()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)

<br>

**이터레이터 패턴(iterator pattern)**

자바스크립트에서 Iterator 패턴은 컬렉션의 요소들을 순차적으로 접근할 수 있도록 하는 디자인 패턴입니다. 이 패턴은 컬렉션 자체의 구조를 노출하지 않고 요소들을 하나씩 꺼내는 인터페이스를 제공합니다.

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

- 수십 년 동안 존재해 왔으며, 데이터를 조각조각 소비하는 표준적인 접근법을 제공하여 가독성과 효율성을 향상시킵니다.
- 큰 관계형 데이터베이스 'SELECT' 쿼리와 같이 반복적인 처리가 필요한 큰 데이터 세트에 유용합니다.
- 데이터 소스에 연결된 "이터레이터" 데이터 구조를 사용하며, `next()`와 같은 메서드를 갖고 있습니다.
- **참조:** 
  - [SQL SELECT Statement](https://www.w3schools.com/sql/sql_select.asp)
  - [JavaScript Iterator Design Pattern](https://www.dofactory.com/javascript/design-patterns/iterator)

<br>

**`next()` 메서드**

`next()`는 JavaScript에서 이터레이터 객체와 함께 사용되는 내장 메서드입니다.

```js
let iterator = sequence[Symbol.iterator]();
let next = iterator.next();
while(!next.done) {
    console.log(next.value);
    next = iterator.next();
}
```

- `next()` 메서드는 두 가지 속성을 가진 객체를 반환합니다: `value`와 `done`.
    - `value`는 시퀀스에서의 다음 값을 나타냅니다.
    - `done`은 시퀀스의 마지막 값이 이미 소비되었는지 여부를 나타내는 불리언 값입니다. 마지막 값이 이미 소비되었을 경우 `done`은 true입니다.
    - `done`이 거짓(false)인 경우, next()를 다시 호출하여 다음 값을 얻을 수 있습니다.
- 이 메서드는 이터레이터가 시퀀스의 끝에 도달할 때까지 반복적으로 호출될 수 있습니다. 이를 통해 이터레이터는 시퀀스의 모든 요소를 차례대로 방문할 수 있습니다.
- 이는 데이터의 순차적인 처리를 가능하게 하며, 이터레이터 패턴의 핵심적인 부분입니다.

<br>

### 3.1.1 이터레이터 소비하기

`for..of` 루프와 스프레드 연산자 모두 이터레이터(iterator) 소비 프로토콜(consumption protocol)을 따라 이터레이터(iterator)에서 가능한 모든 값을 검색합니다.

- **`for..of` 루프 사용**: 반복자를 소비하는 표준화된 방법. 각 결과를 하나씩 반복합니다.
    
    ```js
    // 어떤 데이터 소스의 이터레이터(iterator)가 주어졌습니다:
    let it = /* .. */;

    // 한 번에 하나씩 결과를 반복합니다.
    for(let value of iterator) {
        console.log(`Iterator value: ${ value }`);
    }

    // Iterator value: ..
    // Iterator value: ..
    // ..
    ```

- **스프레드 연산자 `...` 사용**: 반복자를 배열이나 함수 호출로 펼칩니다. 모든 값을 한 번에 처리하는 데 유용합니다.
    
    ```js
    // 이터레이터(iterator)를 배열로 퍼뜨립니다.
    // 각 반복된 값이
    // 배열 요소 위치를 차지합니다.
    let array = [...iterator];

    // 이터레이터(iterator)를 함수로 퍼뜨립니다.
    // 각 반복된 값이
    // 인수 위치를 차지하는 함수를 호출합니다.
    let functionResult = someFunction(...iterator);
    ```

<br>

**이터레이션(iteration)**

- **참조**
  - [JS 📚 이터러블 & 이터레이터 - 💯완벽 이해](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-%EC%9D%B4%ED%84%B0%EB%9F%AC%EB%B8%94-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0-%F0%9F%92%AF%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4)

<br>

**이터레이션 프로토콜(Iteration protocols)**

이터레이션 프로토콜은 JavaScript에서 반복 가능한 객체를 정의하는 표준 방법입니다. 이 프로토콜은 `Symbol.iterator` 메서드를 사용하여 iteration을 반환하는 객체를 정의합니다.

- **참조:**
  - [Iteration protocols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
  - [Poiemawb, 이터레이션 프로토콜](https://poiemaweb.com/es6-iteration-for-of)


<br>

### 3.1.2 이터러블(iterable)

- 이터러블은 순회할 수 있는 값을 뜻합니다.
- 자바스크립트(ES6)에서는 문자열, 배열, 맵, 세트와 같은 기본 데이터 구조가 반복 가능합니다.
- 배열과 문자열은 반복될 수 있으며, 스프레드 연산자(`...`)를 통해 배열을 얕은 복사를 할 수 있습니다.

```js
// 배열
var arr = [ 10, 20, 30 ];
for (let val of arr) {
    console.log(`배열 값: ${ val }`);
}

// 배열 복사
var arrCopy = [...arr];

// 문자열
var greeting = "Hello world!";

// 스프레드 연산자를 사용하여 한 번에 한 문자씩 문자열의 문자를 반복
var chars = [ ...greeting ];

console.log(chars); 
// [ "H", "e", "l", "l", "o", " ",
//   "w", "o", "r", "l", "d", "!" ]
```

- `Map` 데이터 구조는 객체 키와 값을 연결하고 기본적으로 항목(키-값 튜플)을 반복합니다

```js
// 두 개의 DOM 요소, `btn1`과 `btn2`가 주어졌습니다.

var buttonNames = new Map();
buttonNames.set(btn1,"Button 1");
buttonNames.set(btn2,"Button 2");
```

- `for..of` 루프에서 "배열 디스트럭처링" ([btn,btnName])을 사용하여 튜플을 키/값 쌍으로 소비할 수 있습니다.

```js
for (let [btn,btnName] of buttonNames) {
    btn.addEventListener("click",function onClick(){
        console.log(`Clicked ${ btnName }`);
    });
}
```

```js
// buttonNames 맵의 값만 소비하려면 values()를 호출하여 값만 있는 이터레이터(iterator)를 얻을 수 있습니다.
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2
```

- **참조**:
  - [Object.values()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/values)
  - [Array.prototype.values()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/values)
  - [Map.prototype.values()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map/values)
  - [FormData.values()](https://developer.mozilla.org/ko/docs/Web/API/FormData/values)
  - [NodeList.values()](https://developer.mozilla.org/ko/docs/Web/API/NodeList/values)

```js
// 또는 배열 반복에서 인덱스 및 값을 원한다면 entries() 메서드로 이터레이터(iterator)를 만들 수 있습니다.
var arr = [ 10, 20, 30 ];

for (let [idx,val] of arr.entries()) {
    console.log(`[${ idx }]: ${ val }`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

- **참조**:
  - [Object.entries()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)
  - [Array.prototype.entries()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/entries)
  - [Map.prototype.entries()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map/entries)
  - [NodeList.entries()](https://developer.mozilla.org/ko/docs/Web/API/NodeList/entries)
  - [FormData.entries()](https://developer.mozilla.org/ko/docs/Web/API/FormData/entries)

<br>

### (추가) `iterator`와 `iterable`, `iteration`의 차이점

- **참조**:
  - [Iterators and Iterables in Javascript](https://www.connectwise.com/blog/engineering/iterators-and-iterables-in-javascript#:~:text=Iterators%20provide%20a%20standardized%20way,to%20suit%20their%20specific%20needs.) 

<br>

## 3.2 클로저(closure)

자바스크립트에서 클로저는 자신의 스코프, 외부 함수의 스코프, 그리고 전역 스코프에 접근할 수 있는 함수입니다.

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

클로저는 콜백과 같은 비동기 코드에서 특히 흔히 볼 수 있습니다.

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

> **저자曰:**<br>
> JavaScript에서 클로저는 거의 필수적이며, 클로저를 사용하지 않은 복잡한 프로그래밍을 상상하기 어렵습니다. 클로저에 대한 이해가 부족하다면, 추가적인 자료를 참고하는 것이 좋습니다.

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