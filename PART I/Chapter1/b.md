# 1. 자바스크립트

<br>

- [1. 자바스크립트](#1-자바스크립트)
  - [1.1 책에 대하여](#11-책에-대하여)
  - [1.2 자바스크립트 이름의 유래](#12-자바스크립트-이름의-유래)
  - [1.3 명세서](#13-명세서)
    - [1.3.1 JS를 지배하는 웹](#131-js를-지배하는-웹)
    - [1.3.2 JS지만 JS가 아닌 웹 전용 문법](#132-js지만-js가-아닌-웹-전용-문법)
    - [1.3.3 모든 코드가 JS인 것은 아닙니다.](#133-모든-코드가-js인-것은-아닙니다)
  - [1.4 JS의 다양한 얼굴](#14-js의-다양한-얼굴)
  - [1.5 `하위 호환성`과 `상위 호환성`](#15-하위-호환성과-상위-호환성)
    - [1.5.1 간극을 줄이기 위한 노력](#151-간극을-줄이기-위한-노력)
    - [1.5.2 간극을 메우기 위한 방법 찾기](#152-간극을-메우기-위한-방법-찾기)
  - [1.6 인터프리터 이해하기](#16-인터프리터-이해하기)
    - [1.6.1 웹 어셈블리](#161-웹-어셈블리)
  - [1.7 엄격 모드](#17-엄격-모드)
  - [키워드](#키워드)
  - [참고](#참고)

<br>


## 1.1 책에 대하여

이 책의 내용을 완전히 이해하기 위해 시간을 투자하십시오. 잘 시작하는 것이 중요합니다.


<br>


## 1.2 자바스크립트 이름의 유래

자바스크립트의 이름은 처음에 마케팅 전략의 일환으로 자바의 인기에 편승하려고 지어졌습니다.

하지만 실제로 자바스크립트는 자바와 구문과 사용법이 매우 다른 별개의 언어입니다. 특히 자바는 컴파일 언어인데 비해 자바스크립트는 인터프리터 언어입니다.

자바스크립트가 처음 선보일 때 자바 개발자들을 겨냥한 마케팅 전략이었다는 것은 사실이지만, 지금은 완전히 독립된 언어로 자리잡았다고 볼 수 있습니다.

자바스크립트는 동적 타입, 프로토타입 기반의 객체지향 언어로서 독특한 특성을 가진 언어입니다.

<br>

## 1.3 명세서

TC39(Ecma International Technical Committee 39)는 JS를 관리하는 기술 운영 위원회로, 자바스크립트의 공식 사양을 관리합니다. JS에는 버전이 없습니다. TC39와 ECMA에 의해 유지되는 공식적인 표준 JS는 단 하나뿐입니다.


### 1.3.1 JS를 지배하는 웹

JS는 브라우저, 서버(Node.js), 심지어 로봇이나 전구에서도 실행되고 있으며 그 영역을 확장하고 있습니다. 그러나 가장 큰 비중을 차지하는 분야는 웹입니다. 그래서 JS 구현체를 만들 때는 웹 브라우저 환경을 가장 중요하게 고려해야 합니다.

### 1.3.2 JS지만 JS가 아닌 웹 전용 문법


JS는 JavaScript의 줄임말로, 웹 전용 문법을 갖춘 프로그래밍 언어입니다. JavaScript는 `Node.js`, 브라우저 엔진 등의 실행 환경에서 특정 API를 전역 스코프에 추가하여 자체적인 기능을 제공합니다. 예를 들어, `alert()`, `fetch()`, `getCurrentLocation()`, `getUserMedia()` 등의 함수는 웹에서만 지원되는 API입니다.

`Node.js` 환경에서는 개발자가 API를 편리하게 사용할 수 있도록 `fs.write()`와 같은 빌트인 모듈을 제공합니다. 이러한 메서드들은 JavaScript의 명세서에 포함되어 있지 않지만 JavaScript처럼 동작합니다. `console.*` 메서드도 이에 해당합니다.

JavaScript 코드는 실행 환경에 따라 동작하는 방식이 달라질 수 있지만, JavaScript 생태계에 속하려면 JavaScript 문법 규칙을 준수해야 합니다. 이는 JavaScript의 유연성을 확보하고 다양한 환경에서의 실행을 가능하게 합니다.

```jsx
// 웹 전용 API 예시
if (typeof window !== 'undefined') { // 웹 브라우저 환경에서만 실행되도록
    alert("이 코드는 웹 브라우저에서 실행되었습니다."); // alert()은 웹에서만 사용 가능한 API입니다.
}

// Node.js 환경에서의 기능
if (typeof process !== 'undefined') { // Node.js 환경에서만 실행되도록
    const fs = require('fs');
    fs.writeFileSync('test.txt', '이 코드는 Node.js 환경에서 실행되었습니다.'); // fs는 Node.js에서 제공하는 빌트인 모듈입니다.
}

// console.* 메서드는 웹과 Node.js 모두에서 사용 가능합니다.
console.log("이 코드는 JS 실행 환경에서 돌아갑니다.");
```

위의 코드는 `alert()`, `fs.write()` 같은 특정 환경에서만 동작하는 API와 `console.log()` 같이 JS 실행 환경 어디에서든 동작하는 API를 보여줍니다. 이렇게 JS는 환경에 따라 동작하는 방식이 달라질 수 있지만, JS 문법 규칙을 준수하며 다양한 환경에서 실행될 수 있습니다.


### 1.3.3 모든 코드가 JS인 것은 아닙니다.

개발자 도구의 콘솔은 JavaScript 엔진이 `.js` 파일을 실행하는 것과 정확히 일치하는 방식으로 코드를 실행하지 않습니다.

콘솔의 주된 목적은 개발자가 짧은 코드 조각을 빠르게 테스트해보고 결과를 확인하는 것입니다.

따라서 콘솔에서 보이는 동작이 `.js` 파일이 실행될 때의 실제 동작과 다를 수 있습니다. 특히 변수 선언 방식, 호이스팅, `strict mode` 적용 방식, `this` 바인딩 등에서 차이가 있을 수 있습니다.

콘솔은 개발 편의성을 위한 도구이기 때문에 JavaScript 엔진의 정확한 동작을 보장하지 않습니다.

`.js` 파일 실행과 콘솔 사용 사례가 완전히 다르다는 점을 인지하고, 콘솔에서의 동작을 `.js` 파일 실행 시 기대해서는 안됩니다.

```js
// 콘솔
var test = 'hello'
console.log(window.test) // "hello"

// .js 파일
var test = 'hello'
console.log(window.test) // undefined
```

콘솔에서는 최상위 레벨에 선언한 변수가 전역 객체의 프로퍼티가 되지만, `.js` 파일에서 실행하면 전역 객체에 추가되지 않습니다.

```js
// 콘솔
function test() {
  console.log('hello')  
}

test() // "hello"

// .js 파일
function test() {
  console.log('hello')
}

test() // test is not defined
```

콘솔에서는 호이스팅된 함수를 바로 호출할 수 있지만, `.js` 파일에서는 함수 선언 전에 호출하면 에러가 발생합니다.

```js 
// 콘솔의 strict mode 적용 방식

'use strict'

x = 1 // 에러 없음

// .js 파일의 strict mode

'use strict'

x = 1 // ReferenceError
```
콘솔의 `strict mode`는 해당 세션만 적용되지만, `.js` 파일에서는 전역에 적용됩니다.

이처럼 콘솔과 `.js` 파일의 실행 방식에 차이가 있음을 인지하고 접근해야 합니다.


<br>

## 1.4 JS의 다양한 얼굴

프로그래밍 분야에서 '패러다임'이라는 용어는 코드를 어떻게 구조화할 것인지에 대한 접근 방식과 사고 방식을 의미합니다. 자바스크립트는 여러 패러다임을 사용하여 코드를 구조화할 수 있으며, 이를 다중 패러다임 언어라고 하며, 궁극적인 유연성을 제공합니다.

```jsx
// 절차적 프로그래밍
function addNumbers(a, b) {
    return a + b;
}
console.log(addNumbers(3, 4));  // 출력: 7

// 객체 지향 프로그래밍
class Rectangle {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
    area() {
        return this.height * this.width;
    }
}
const myRectangle = new Rectangle(3, 4);
console.log(myRectangle.area());  // 출력: 12

// 함수형 프로그래밍
const multiply = (a, b) => a * b;
console.log(multiply(3, 4));  // 출력: 12

```

- **절차적 프로그래밍**: 코드가 상단에서 하단으로 선형적으로 구조화되며, 프로시저라는 코드 단위에 미리 정해진 일련의 연산이 작성되는 방식입니다.
- **객체 지향 프로그래밍**: 코드가 클래스 기준으로 구조화되며, 클래스에서 로직과 데이터가 함께 정의되는 방식입니다.
- **함수형 프로그래밍**: 코드가 함수 단위로 구조화되며, 부수 효과가 없는 순수 함수를 사용하고, 함수 자체가 값을 가지는 것이 특징인 방식입니다.

<br>

## 1.5 `하위 호환성`과 `상위 호환성`


하위 호환성이란 한 번이라도 유효한 JS 문법으로 인정되면 사양이 변경되더라도 그 유효성이 절대로 사라지지 않는다는 것을 의미합니다.

예를 들어, ES5에서는 아래와 같이 `var`를 사용하여 변수를 선언하고, 함수 범위(function scope)를 가지는 것이 일반적이었습니다.

```js
function exampleFunction() {
    var x = 10;
    if (true) {
        var x = 20;  // 이 'x'는 바깥에 있는 'x'와 같은 변수입니다.
        console.log(x);  // 출력: 20
    }
    console.log(x);  // 출력: 20
}
exampleFunction();
```
그러나 ES6에서는 let과 const를 도입하여 블록 범위(block scope)를 가지는 변수를 선언할 수 있게 되었습니다. 그러나 여전히 var를 사용하여 함수 범위의 변수를 선언할 수 있습니다. 이는 하위 호환성 때문입니다.

```js
function exampleFunction() {
    let y = 10;
    if (true) {
        let y = 20;  // 이 'y'는 바깥에 있는 'y'와 다른 변수입니다.
        console.log(y);  // 출력: 20
    }
    console.log(y);  // 출력: 10

    var a = 40;
    if (true) {
        var a = 50;  // 이 'a'는 바깥에 있는 'a'와 같은 변수입니다.
        console.log(a);  // 출력: 50
    }
    console.log(a);  // 출력: 50
}
exampleFunction();
```

이처럼 새로운 JavaScript 버전에서도 이전 버전의 문법이 계속 유효하게 유지되는 것을 하위 호환성이라고 합니다. 이를 통해 기존 코드가 여전히 작동하며, 개발자들이 새로운 문법을 점차적으로 도입할 수 있도록 합니다.

### 1.5.1 간극을 줄이기 위한 노력

> 명세서에 새롭게 추가되었지만 구 엔진과 호환되지 않는 문법은 트랜스파일(transpile)을 사용해 문제를 해결할 수 있습니다.<br> 트랜스 파일은 새로운 문법을 구 이전 버전의 문법으로 변환하는 것을 의미합니다.<br>트랜스 파일러는 Babel이 대표적인데, 이는 ES6 이상의 문법을 ES5로 변환해주는 역할을 합니다.<br>
> **p. 41**

```js
// 트랜스 파일러를 사용해 ES6 문법을 ES5 문법으로 변환하는 예시
// ES6 문법
const arrowFunction = () => {
  console.log('Hello, Babel!');
};
```

```js
// ES5 문법
var arrowFunction = function arrowFunction() {
  console.log('Hello, Babel!');
};
```

> 최신 JS를 쓰는 것을 권유하는 이유는 코드를 더 간격하게 작성할 수 있고, 의도를 더 명확하게 표현할 수 있기 때문입니다.<br>
> **p. 42**


### 1.5.2 간극을 메우기 위한 방법 찾기

> 폴리필(polyfill)은 새로운 기능을 구현해주는 코드를 의미합니다. 폴리필은 새로운 기능을 지원하지 않는 구 버전 브라우저에서도 새로운 기능을 사용할 수 있게 해줍니다.<br>

```js
// es2019의 프로미스 프로타입에 추가된 finally 메서드를 폴리필로 구현한 코드
if (!Promise.prototype.finally) {
  Promise.prototype.finally = function (callback) {
    return this.then(
      value => Promise.resolve(callback()).then(() => value),
      reason => Promise.resolve(callback()).then(() => { throw reason; })
    );
  };
}
```

> 코드를 작성할 때는 항상 구현 의도를 효과적으로 전달해야하므로 그에 걸맞은 적절한 문법을 사용해야 합니다.
> **p. 44**


> 필자는 JS기반으로 프로젝트를 만들 때 트랜스파일이나 폴리필 사용이 표준으로 잡아야 한다고 생각합니다.
> **p. 44**

<br>

## 1.6 인터프리터 이해하기

JS는 컴파일 언어입니다. JS 코드는 브라우저나 Node.js와 같은 JS 엔진이 이해할 수 있는 바이트 코드로 변환됩니다. JS 코드를 실행하는 과정은 소스 코드를 읽고, 파싱하고, 바이트 코드로 변환하고, 실행하는 과정을 거칩니다. 이 과정을 통해 JS는 인터프리터 언어라고 불립니다.

```jsx
console.log("Hello, World!");
```

이 코드를 실행하면 다음과 같은 과정이 진행됩니다:

1. JavaScript 엔진이 소스 코드를 읽습니다.
2. 코드를 파싱하여 이해합니다. 이 경우, `console.log` 함수가 `"Hello, World!"` 인자와 함께 호출되는 것을 인식합니다.
3. 파싱된 코드는 바이트 코드로 변환됩니다. 바이트 코드는 기계가 이해하고 실행하기 쉬운 추상 머신 코드입니다.
4. JavaScript 엔진이 바이트 코드를 실행하여 "Hello, World!"를 콘솔에 출력합니다.

런타임에 고급 코드 (예: JavaScript)를 기계가 실행할 수 있는 형태 (바이트 코드)로 변환하는 이 과정이 JavaScript를 인터프리터 언어로 만듭니다.

### 1.6.1 웹 어셈블리

> 웹 어셈블리(WebAssembly)는 어셈블리 언어와 유사하며 JS엔진에서 일어나는 과정을 생략하고 바이트 코드를 직접 실행하는 기술입니다. 웹 어셈블리는 JS의 성능을 향상시키기 위해 만들어졌으며, JS와 함께 사용할 수 있습니다.<br>

<br>

## 1.7 엄격 모드
ES5부터 추가된 엄격 모드는 JS의 문법을 좀 더 엄격하게 적용하여 오류를 발생시키는 모드입니다. 엄격 모드는 전역 스코프나 함수 스코프에서 코드를 실행할 때 적용할 수 있습니다.

```jsx
// 엄격 모드 없음
function withoutStrict() {
    x = 3.14;  // 이것은 오류를 발생시키지 않습니다.
               // JavaScript는 전역 변수 x 라고 가정합니다.
    console.log(x);
}
withoutStrict();

// 엄격 모드 적용
function withStrict() {
    'use strict';
    y = 3.14;  // 이것은 오류를 발생시키는데, 그 이유는 y가 선언되지 않았기 때문입니다.
    console.log(y);
}
withStrict();

```

첫 번째 함수 `withoutStrict()`에서는, 우리는 변수 `x`를 선언하지 않고 값을 할당할 수 있습니다. 그러나 엄격 모드를 사용하는 두 번째 함수 `withStrict()`에서는 동일한 작업을 변수 `y`에 대해 수행하면 `y`가 선언되지 않았기 때문에 오류가 발생합니다.

<br>

## 키워드

<br>

## 참고

- [TC39](https://tc39.es/)
- [TC39 Process](https://tc39.es/process-document/)
- [ECMAScript proposals](https://github.com/tc39/proposals)
- TC39 Smooshgate 케이스: [ECMA TC39: “SmooshGate” was officially resolved by renaming flatten to flat](https://developer.chrome.com/blog/smooshgate)
- [B, Additional ECMAScript Features for Web Browsers](https://tc39.es/ecma262/multipage/additional-ecmascript-features-for-web-browsers.html)
