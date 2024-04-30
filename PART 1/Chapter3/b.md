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

클로저는 함수와 그 함수가 선언될 때의 렉시컬 환경(Lexical Environment)과의 조합입니다. 이 렉시컬 환경은 해당 함수가 선언된 위치의 변수 스코프를 포함합니다. 함수가 클로저를 형성하는 중요한 특징은, 외부 함수가 반환된 후에도 외부 함수 스코프에 있던 변수에 접근할 수 있다는 점입니다.

클로저는 실행 컨텍스트의 외부 환경 참조(Outer Environment Reference)를 사용하여 만들어집니다. 외부 함수의 실행 컨텍스트가 스택에서 제거되더라도, 외부 환경에 있는 변수는 여전히 접근 가능하며, 내부 함수에서 사용될 때까지 메모리에 남아 있습니다. 이것은 내부 함수가 해당 변수의 참조를 유지하기 때문입니다

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

**실행 컨텍스트 생성과 클로저 형성:**
- `greeting` 함수 호출: `greeting` 함수가 "Hello"와 "Howdy"라는 인자와 함께 각각 호출됩니다. 각 호출 시, `greeting` 함수의 실행 컨텍스트가 생성되고, 이 컨텍스트는 자체 변수 환경을 가집니다. 이 환경에는 인자 msg가 포함되며, 각각 "Hello"와 "Howdy"로 설정됩니다.
- 클로저 반환: `greeting` 함수는 `who`라는 내부 함수를 반환합니다. `who` 함수는 클로저입니다. 왜냐하면 `who`는 자신이 생성될 때의 렉시컬 환경을 참조하기 때문입니다. 이 렉시컬 환경은 greeting 함수의 변수 환경을 포함하며, 이 환경에는 `msg` 변수가 존재합니다.
- `who` 함수 실행:
  - `hello`와 `howdy` 변수는 각각 `greeting` 함수로부터 반환된 `who` 함수의 인스턴스입니다. 이 인스턴스들은 각각 자신들의 msg 값("Hello" 또는 "Howdy")에 대한 참조를 유지합니다.
  - `hello("Kyle")` 또는 `howdy("Grant")`와 같이 호출될 때, `who` 함수의 실행 컨텍스트가 생성됩니다. 이 컨텍스트 내에서는 name 변수에 전달된 인자가 설정됩니다.
  - 함수 내부에서 `${msg}, ${name}!`를 출력하기 위해 msg와 name에 접근합니다. 여기서 name은 who의 지역 변수이며, msg는 클로저를 통해 접근되는 greeting의 변수입니다.

> 이 예제에서 중요한 점은, `greeting` 함수의 실행 컨텍스트가 종료된 후에도 `msg` 변수가 사라지지 않는다는 것입니다. `who` 함수는 `greeting` 함수의 변수 환경에 대한 참조를 유지하고 있으므로, `msg`는 메모리에 계속 남아 있게 됩니다. 이로 인해 `hello` 또는 `howdy`를 호출할 때마다 해당 메시지에 접근할 수 있습니다.

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
> 두 번째 예제에서 counter 함수는 count 변수를 초기화하고 단계 증가를 정의합니다. 이후 클로저 increaseCount를 반환합니다:

- 외부 함수 스코프: count와 step은 외부 함수 counter의 변수입니다.
- 클로저: increaseCount 함수는 count와 step을 포착하는 클로저로, 호출될 때마다 count 변수에 접근하고 수정합니다. 클로저 덕분에 함수 호출 사이에 count의 상태가 유지되어, 클로저가 생성될 때 정의된 step에 따라 count를 지속적으로 업데이트할 수 있습니다.

<br>

**클로저와 비동기 코드**

클로저는 콜백과 같은 비동기 코드에서 특히 흔히 볼 수 있습니다. 이러한 클로저를 통해 비동기 작업의 컨텍스트를 유지하며, 필요한 데이터를 안전하게 보관하고 접근할 수 있습니다.

```js
// 비동기 API 호출과 클로저: 
// 클로저는 API 호출이 시작된 시점의 환경을 "캡처"하여, 요청이 완료되었을 때 그 환경을 그대로 사용할 수 있게 해 줍니다.
function fetchData(userId) {
    const url = `https://api.example.com/users/${userId}`;

    // 비동기 요청을 시작하기 전에 클로저를 통해 userId를 캡처
    fetch(url).then(response => response.json()).then(data => {
        // 여기서 클로저를 통해 userId에 계속 접근할 수 있음
        console.log(`사용자 ${userId}의 데이터:`, data);
    }).catch(error => {
        console.error(`사용자 ${userId}의 데이터를 불러오는 데 실패했습니다:`, error);
    });
}

fetchData(1);
```

```js
function getSomeData(url) {
    // 클로저를 통해 url을 캡처
    ajax(url,function onResponse(resp){
        console.log(
            `Response (from ${ url }): ${ resp }`
        );
    });
}

getSomeData("<https://some.url/wherever>");
// Response (from <https://some.url/wherever>): ...
```

<br>

**비동기 루프 처리**

루프 내에서 비동기 작업을 수행할 때 각 반복마다 클로저를 사용하여 이터레이션의 상태를 안전하게 유지할 수 있습니다.

```js
const files = ['file1.txt', 'file2.txt', 'file3.txt'];

files.forEach((file, index) => {
    // 비동기 함수 내부에서 클로저를 사용하여 각 파일 이름과 인덱스를 캡처
    readFile(file, (err, content) => {
        if (err) {
            console.error(`${file} 읽기 실패:`, err);
            return;
        }
        console.log(`${file} (${index + 1}번째 파일)의 내용:`, content);
    });
});
```

```js
// 클로저를 사용하여 이터레이션 상태를 유지
for (let [idx,btn] of buttons.entries()) {
    btn.addEventListener("click",function onClick(){
       console.log(`${ idx }번째 버튼 클릭!`);
    });
}
```

> **저자曰:**<br>
> JavaScript에서 클로저는 거의 필수적이며, 클로저를 사용하지 않은 복잡한 프로그래밍을 상상하기 어렵습니다. 클로저에 대한 이해가 부족하다면, 추가적인 자료를 참고하는 것이 좋습니다.

<br>

## 3.3 `this` 키워드

-  `this` 키워드는 JavaScript에서 매우 강력하지만 종종 잘못 이해되는 개념입니다. 많은 개발자들이 `this`가 항상 함수 자체나, 메서드가 속한 객체 인스턴스를 참조한다고 생각하지만, 실제로는 그렇지 않습니다.

-  함수는 자신이 정의된 범위(scope)에 *부착*됩니다. 범위는 변수가 어디서 접근 가능한지를 결정하는 정적인 환경입니다.

- **실행 컨텍스트와 `this`**: 함수의 실행 컨텍스트는 해당 함수가 실행될 때 생성되며, 함수의 동작 방식에 영향을 미칩니다. `this` 키워드는 실행 컨텍스트를 통해 노출되며, 함수가 호출될 때마다 `this`의 값이 결정됩니다. 이는 `this`가 가리키는 값이 동적으로 변할 수 있음을 의미합니다.

- **실행 컨텍스트의 동적 성질**: 실행 컨텍스트는 함수 실행 중에 사용할 수 있는 속성들을 포함한 구체적인 객체로 간주할 수 있습니다. 이는 함수가 실행되는 상황에 따라 다르게 동작할 수 있게 해줍니다.


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


> 예를 들어, `classroom(..)` 함수는 `this`를 참조하지 않지만, 그 내부에 정의된 `study()` 함수는 `this`를 참조할 수 있습니다.

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

> `assignment`라는 함수가 `homework` 객체의 속성으로 설정되고 `homework.assignment()`로 호출될 때, `this`는 `homework` 객체를 참조합니다.

```js
var otherHomework = {
    topic: "Math"
};

assignment.call(otherHomework);
// Kyle says to study Math
```

> `call(..)` 메서드를 사용하면 함수 호출 시 `this` 참조를 명시적으로 설정할 수 있습니다. 이를 통해 함수를 다양한 컨텍스트에서 재사용할 수 있습니다.


`this`를 인식하는 함수는 다양한 객체의 데이터를 사용하여 같은 함수를 더 유연하게 재사용할 수 있습니다.

```js
function classroom(teacher) {
    return {
        study() {
            console.log(
                `${ teacher } says to study ${ this.topic }`
            );
        }
    };
}

var homework = classroom("Kyle");
homework.topic = "JS";
homework.study();
// Kyle says to study JS

var otherHomework = {
    topic: "Math",
    assignment: homework.study
};

otherHomework.assignment();
// Kyle says to study Math
```


<br>

## 3.4 프로토타입

프로토타입은 객체의 특성으로, 특히 속성 접근 해결에서 특징적입니다. 이것은 객체 생성시 두 객체 사이의 숨겨진 연결입니다.

이 "프로토타입 체인"은 속성/메소드 접근이 객체 B에 있지 않아도 다른 객체 A로 위임될 수 있게 해줍니다. 이를 통해 객체들이 작업을 수행하는데 협력할 수 있습니다.

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