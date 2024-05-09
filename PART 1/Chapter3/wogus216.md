# CHAPTER 3 자바스크립트 뿌리 파헤치기

<br>

- [CHAPTER 3 자바스크립트 뿌리 파헤치기](#chapter-3-자바스크립트-뿌리-파헤치기)
  - [3.1 이터레이션(iteration)](#31-이터레이션iteration)
    - [이터레이터 패턴(iterator pattern)](#이터레이터-패턴iterator-pattern)
    - [3.1.1 이터레이터 소비하기](#311-이터레이터-소비하기)
    - [3.1.2 이터러블(iterable)](#312-이터러블iterable)
  - [3.2 클로저(closure)](#32-클로저closure)
    - [클로저 비동기 코드](#클로저-비동기-코드)
  - [3.3 `this` 키워드](#33-this-키워드)
    - [함수를 호출하는 방식](#함수를-호출하는-방식)
  - [3.4 프로토타입](#34-프로토타입)
    - [3.4.1 객체 연결 장치](#341-객체-연결-장치)
    - [3.4.2 `this` 다시 보기](#342-this-다시-보기)
  - [3.5 ‘왜?’라고 질문하기](#35-왜라고-질문하기)

<br>

## 3.1 이터레이션(iteration)

이터레이터 패턴 수십 년 동안 사용되어 온 디자인 패턴입니다. 이 패턴을 사용하면 데이터를 덩어리 단위로 표준화된 방법을 사용해 각각 처리 할 수 있습니다.

### 이터레이터 패턴(iterator pattern)

이터레이터 패턴에는 처리할 데이터를 참조하는 데이터 구조인 이터레이터가 정의됩니다.
이터레터는 next()라는 메서드를 지원하며, next()를 호출할 때 마다 관계형 데이터 베이스에서 질의 결과에 해당하는 레코드나 줄과 같은 데이터 조각이 차례대로 반환됩니다.

### 3.1.1 이터레이터 소비하기

### 3.1.2 이터러블(iterable)

이터레이터 소비 프로토콜은 순회 가능한 값인 이터러블을 소비하는 기술적인 방법이라고 정의할 수 있습니다.

ES6에서는 문자열,배열,맵,셋 같이 기본이 되는 자료구조나 컬렉션을 이터러블로 정의합니다.

```
// an array is an iterable
var arr = [ 10, 20, 30 ];

for (let val of arr) {
    console.log(`Array value: ${ val }`);
}
// Array value: 10
// Array value: 20
// Array value: 30
```

```
var greeting = "Hello world!";
var chars = [ ...greeting ];

chars;
// [ "H", "e", "l", "l", "o", " ",
//   "w", "o", "r", "l", "d", "!" ]
```

- Map 키-값 형태의 자료구조

```
// given two DOM elements, `btn1` and `btn2`

var buttonNames = new Map();
buttonNames.set(btn1,"Button 1");
buttonNames.set(btn2,"Button 2");

for (let [btn,btnName] of buttonNames) {
    btn.addEventListener("click",function onClick(){
        console.log(`Clicked ${ btnName }`);
    });
}

for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2

var arr = [ 10, 20, 30 ];

for (let [idx,val] of arr.entries()) {
    console.log(`[${ idx }]: ${ val }`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

JS의 내장 이터러블은 키만 대상으로 (keys(),값만 대상으로 values()),키와 값을 대상으로 entries 순회하는 메서드를 제공합니다.
<br>

## 3.2 클로저(closure)

클로저는 반환된 내부함수가 자신이 선언됐을 때의 환경(Lexical environment)인 스코프를 기억하여 자신이 선언됐을 때의 환경(스코프) 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수

클로저는 JS를 지탱하는 근본적인 기술

클로저란 함수가 정의된 스코프가 아닌 다른 스코프에서 함수가 실행되더라도, 스코프 밖에 있는 변수를 기억하고 이 외부 변수에 게속 접근할 수 있는 경우를 의미합니다.

- 클로저의 특징
  - 클로저는 함수의 타고난 특징
  - 클로저를 직접 보고싶다면 함수를 해당 함수가 정의된 스코프가 아닌 다른 스코프에서 실행해야 합니다.

```
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

예시를 실행하면 greetring()이 실행되고 내부 함수인 who()의 인스턴스가 생성됩니다.
내부함수 who()는 greeting()이 있는 외부 스코프에서 넘어온 매개변수 msg를 에어쐅니다.
greeting 호출 시 내부함수 who()가 반환

이때 이 함수에 대한 참조가 외부 스코프에 있는 hello 변수에 할당됩니다.

예시에서 greeting()을 호출한 이후에는 msg가 사라질 거라 예상하지만 변수들은 사라지지 않습니다.
바로 클로저 때문 !!

내부함수 인스턴스들이 각각 hello와 howdy에 각각 할당되어 있음
<br>

### 클로저 비동기 코드

```
function getSomeData(url) {
    ajax(url,function onResponse(resp){
        console.log(
            `Response (from ${ url }): ${ resp }`
        );
    });
}

getSomeData("https://some.url/wherever");
// Response (from https://some.url/wherever): ...
```

## 3.3 `this` 키워드

함수는 정의되는 시점에 클로저를 통해서 함수를 에워싸는 스코프에 부착됩니다.
여기서 스코프는 변수가 어떤 것을 참조하는지를 결정하는 규칙 모음입니다.

함수는 스코프 말고도 자신이 어디까지 접근 가능한지를 결정하는 함수만의 특징을 갖습니다.
이 특정은 실행컨텍스크개념으로 가장 잘 설명되며 함수는 this 키워드를 통해 실행 컨텍스트에 접근합니다.

스코프는 정적이며 함수를 정의하는 순간, 해당 스코프에서 사용할 수 있는 한정된 변수 집함을 포함합니다.
반면 함수의 실행 컨텍스트는 동적입니다. **실행 컨텍스트는 함수를 정의한 위치나 함수를 호출하는 위치와 상관없이 함수의 호출방식에 따라 결정됩니다.**

즉, this는 함수의 정의에 종속되어 결정되는 변치 않는 특성이 아니라 **함수를 호출 할 때마다 결정되는 동적인 특성**

#### 함수를 호출하는 방식

- 함수 호출
- 메소드 호출
- 생성자 함수 호출
- apply/call/bind 호출
  <br>

## 3.4 프로토타입

프로토타입은 객체, 구체적으로 프로퍼티에 접근 할 때 일어나는 동작과 관련된 특징입니다.

프로토타입은 두 객체를 연결하는 연결 장치입니다.
프로토타입을 통해 연결된 일련의 객체는 프로토 타입 체인이라고 부릅니다.
객체 B에서 다른 객체 A를 연결할 수 있게 하는 프로토타입 연결 장치가 존재하는 이유는 B에는 없는 프로퍼티나 메서드에 접근하려 할 때, 객체 A에서 위임을 받아 해당 접근을 처리할 수 있도록 하기 위해서 입니다.

### 3.4.1 객체 연결 장치

### 3.4.2 `this` 다시 보기

<br>

## 3.5 ‘왜?’라고 질문하기
