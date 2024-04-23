# 실행 컨텍스트(Execution Context)

이 글에서는 실행 컨텍스트의 개념과 이를 구성하는 요소들에 대해 살펴보겠습니다.

<br>


- [실행 컨텍스트(Execution Context)](#실행-컨텍스트execution-context)
  - [1. 들어가기에 앞서](#1-들어가기에-앞서)
    - [1-1. 실행 컨텍스트의 정의](#1-1-실행-컨텍스트의-정의)
    - [1-2. 실행 컨텍스트 스택(콜 스택)](#1-2-실행-컨텍스트-스택콜-스택)
      - [2-2. 외부 환경 참조(Outer Environment Reference)](#2-2-외부-환경-참조outer-environment-reference)
      - [2-3. `this` 바인딩](#2-3-this-바인딩)
    - [3. 실행 컨텍스트의 유형](#3-실행-컨텍스트의-유형)
      - [3-1. 글로벌 실행 컨텍스트](#3-1-글로벌-실행-컨텍스트)
      - [3-2. 함수 실행 컨텍스트](#3-2-함수-실행-컨텍스트)
      - [3-3. `eval` 실행 컨텍스트](#3-3-eval-실행-컨텍스트)
    - [4. 실행 컨텍스트의 생명 주기](#4-실행-컨텍스트의-생명-주기)
      - [4-1. 생성 단계](#4-1-생성-단계)
      - [4-2. 실행 단계](#4-2-실행-단계)
    - [실행 단계 예제](#실행-단계-예제)
  - [6. 클로저(Closure)와 실행 컨텍스트](#6-클로저closure와-실행-컨텍스트)
    - [6-1. 클로저의 정의 및 작동 원리](#6-1-클로저의-정의-및-작동-원리)
    - [6-2. 클로저와 스코프 체인](#6-2-클로저와-스코프-체인)
  - [7. 실행 컨텍스트와 비동기 처리](#7-실행-컨텍스트와-비동기-처리)
    - [7-1. 이벤트 루프(Event Loop)](#7-1-이벤트-루프event-loop)
    - [7-2. `콜백 함수(Callback Functions)`](#7-2-콜백-함수callback-functions)
    - [7-3. `프로미스(Promises)`와 `async/await`](#7-3-프로미스promises와-asyncawait)
  - [8. 실제 사례 분석](#8-실제-사례-분석)
    - [8-1. 코드 예시를 통한 실행 컨텍스트의 이해](#8-1-코드-예시를-통한-실행-컨텍스트의-이해)
    - [8-2. 문제 상황 및 해결 방안](#8-2-문제-상황-및-해결-방안)
  - [9. Keywords](#9-keywords)
  - [10. References](#10-references)

<br>

## 1. 들어가기에 앞서

### 1-1. 실행 컨텍스트의 정의

실행 컨텍스트(Execution Context)는 코드가 실행되기 위해 필요한 정보를 모아놓은 객체로 생각하면 됩니다.

```js
var num = 2; // 전역 변수

function multiplyByThree() {
    var result; // 지역 변수
    result = num * 3;
    console.log(result);
}

multiplyByThree(); // 함수 호출
```

코드가 실행되면 아래와 같은 순서로 실행 컨텍스트가 생성 및 관리됩니다.

1. **전역 실행 컨텍스트 생성**
    - 전역 변수 `num`과 함수 `multiplyByThree`가 메모리에 할당됩니다.
    - 코드 실행이 시작되면, `num`은 `2`로 할당됩니다.
2. **`multiplyByThree` 함수 호출**
    - 새로운 함수 실행 컨텍스트가 생성됩니다.
    - 지역 변수 `result`가 생성되고, `undefined`로 초기화됩니다.
    - 이후 `result`에 `num * 3`이라는 연산 결과가 할당됩니다.
    - 함수 내부에서 `console.log(result);`를 실행하여 결과를 출력합니다.

코드에서 `multiplyByThree` 함수 호출 시 새로운 실행 컨텍스트가 생성되며, 함수 실행이 완료되면 해당 컨텍스트가 제거되고 제어가 전역 컨텍스트로 돌아갑니다.

> `var` 키워드를 사용하면, 컴파일 시에 변수가 스코프의 상단으로 이동하고 `undefined` 값으로 초기화됩니다. 이 예제에서는 사용되지 않았지만, `let`과 `const` 키워드도 변수를 선언하면 블록의 상단으로 호이스팅되지만 초기화되지 않습니다. 선언 전에 접근하려고 하면 `ReferenceError`가 발생합니다. 이는 블록 시작부터 선언이 처리될 때까지 "임시 사각 지대(TDZ)"에 있기 때문입니다.

```plaintext
# "임시 사각 지대(Temporal Dead Zone, TDZ)"는 let과 const로 선언된 변수가 자신이 선언되기 전에 접근될 수 없는 상태를 말합니다. 
```

```js
let num = 2; // 전역 변수

function multiplyByThree() {
    let result; // 지역 변수
    result = num * 3;
    console.log(result);
}

multiplyByThree(); // 함수 호출
```

<br>

### 1-2. 실행 컨텍스트 스택(콜 스택)

```plaintext
# "호출 스택(Call Stack)"은 실행 중인 함수의 실행 컨텍스트를 순서대로 저장하는 자료 구조입니다. JavaScript 엔진은 이 스택을 사용하여 함수가 어떻게 그리고 언제 실행되어야 할지를 관리합니다.
```

1. **스택(Stack)에 의한 실행 컨텍스트 관리**
   함수가 호출되면 해당 함수의 실행 컨텍스트가 생성되고 호출 스택의 최상위에 푸시(push)됩니다. 함수 실행이 완료되면 해당 실행 컨텍스트는 스택에서 팝(pop)되어 제거됩니다. 이 과정은 동기적(Synchronous)으로 수행되며, 호출 스택의 크기는 실행할 수 있는 컨텍스트 수를 제한합니다.

   ```js
   function first() {
        second();
        console.log('첫 번째 함수');
    }

    function second() {
        third();
        console.log('두 번째 함수');
    }

    function third() {
        console.log('세 번째 함수');
    }

    first();  // 첫 번째 함수 호출
    ```

    위 코드에서 `first` 함수가 호출되면 `second` 함수가 실행되고, `third` 함수가 실행됩니다. 이후 `console.log`가 차례대로 실행되며, 실행 컨텍스트는 호출 스택에서 제거됩니다. 

    ```plaintext
    # 실행 순서
    1. first() 호출
    2. second() 호출
    3. third() 호출
    4. third() 실행 완료
    5. second() 실행 완료
    6. first() 실행 완료
    ```
   
2. **스택 프레임(Stack Frame)**
   - 스택 프레임은 호출 스택에 쌓이는 각각의 함수 호출에 대한 정보를 포함하는 단위입니다.
   - 하나의 스택 프레임은 특정 함수 호출의 실행 컨텍스트를 나타내며, 다음과 같은 정보를 포함할 수 있습니다: 함수의 인자, 지역 변수, 반환 주소 등.
   
<br>

### 2. 실행 컨텍스트의 레코드(Record of Execution Context)

실행 컨텍스트는 자바스크립트 코드를 실행할 때 중요한 정보를 저장하고 관리하는 구조로, 각 실행 컨텍스트에는 세 가지 주요 구성 요소가 있습니다. 이 구성 요소들은 실행 컨텍스트의 레코드로, 실행 컨텍스트가 코드를 어떻게 처리하고 관리하는지를 정의합니다.

#### 2-1. 환경 레코드(Environment Record)

환경 레코드는 실행 컨텍스트와 관련된 식별자(변수, 함수, 매개변수 등)의 모든 지역적 선언을 저장하는 구조입니다. 이 레코드는 실행 컨텍스트가 생성될 때 해당 컨텍스트 내의 모든 로컬 정보를 포함하며, 두 가지 주요 유형이 있습니다.

- **선언적 환경 레코드(Declarative Environment Record):** 함수 선언, 변수 선언, 클래스 선언 등과 같은 선언적 식별자를 포함합니다.
- **객체 환경 레코드(Object Environment Record):** 주로 글로벌 환경에서 사용되며, 전역 객체와 연관된 식별자를 포함합니다.

```javascript
function exampleFunction() {
    var localVariable = 'A local value';
    function localFunction() {
        return localVariable;
    }
}
```

위 예에서 `localVariable`과 `localFunction`은 `exampleFunction`의 실행 컨텍스트의 환경 레코드에 저장됩니다.

#### 2-2. 외부 환경 참조(Outer Environment Reference)

외부 환경 참조는 현재 컨텍스트 외부의 환경, 즉 부모 실행 컨텍스트의 환경 레코드를 참조합니다. 이 참조는 스코프 체인을 형성하는 데 사용되며, 함수가 외부 변수에 접근할 때 필요한 메커니즘을 제공합니다.

```javascript
var globalVar = 'global';

function outerFunction() {
    var outerVar = 'outer';

    function innerFunction() {
        console.log(outerVar); // 'outer'
        console.log(globalVar); // 'global'
    }

    innerFunction();
}

outerFunction();
```

`innerFunction`은 자신의 외부 환경 참조를 통해 `outerFunction`의 환경과 전역 환경에 접근할 수 있습니다.

#### 2-3. `this` 바인딩

`this` 바인딩은 실행 컨텍스트가 생성될 때 결정되는 `this`의 값입니다. `this`의 값은 함수 호출 방식에 따라 다르게 결정되며, 전역 컨텍스트, 함수 실행 컨텍스트, `eval` 컨텍스트에서 각각 다른 방식으로 설정됩니다.

- **전역 컨텍스트:** `this`는 전역 객체(브라우저에서는 `window`, Node.js에서는 `global`)를 가리킵니다.
- **함수 실행 컨텍스트:** 기본적으로 `this`는 전역 객체를 가리키지만, 메서드 호출이나 생성자 함수 등 특정 조건에서는 다른 객체를 참조할 수 있습니다.

```javascript
function showThis() {
    console.log(this);
}

showThis(); // 전역 객체 (브라우저에서는 window)

var obj = {
    method: function() {
        console.log(this);
    }
};

obj.method(); // obj
```

이렇게 실행 컨텍스트의 각 레코드는 자바스크립트 엔진이 코드를 실행할 때 환경을 구성하고 관리하는 데 핵심적인 역할을 수행합니다.

<br>

### 3. 실행 컨텍스트의 유형

자바스크립트에서 실행 컨텍스트는 코드의 실행 환경을 나타내며, 그 유형은 크게 세 가지로 구분됩니다. 이러한 분류는 코드가 실행되는 방식과 범위를 이해하는 데 중요하며, 각각의 실행 컨텍스트는 자바스크립트 엔진에 의해 다르게 처리됩니다.

#### 3-1. 글로벌 실행 컨텍스트

글로벌 실행 컨텍스트는 자바스크립트 코드가 실행되기 시작할 때 가장 먼저 생성되는 실행 컨텍스트입니다. 이 컨텍스트는 자바스크립트 애플리케이션 전체에서 단 하나만 존재하며, 모든 전역 변수와 함수는 이 컨텍스트에 포함됩니다. 글로벌 실행 컨텍스트는 전역 스코프 역할을 하며, 모든 스크립트와 함수에서 접근할 수 있는 베이스 레벨 환경을 제공합니다.

```javascript
var globalVar = 'This is a global variable';

function checkGlobal() {
    return globalVar;  // 전역 변수에 접근
}

console.log(checkGlobal());  // "This is a global variable" 출력
```

위 코드에서 `globalVar`는 글로벌 실행 컨텍스트에 포함되며, 어떤 함수에서도 이 변수에 접근할 수 있습니다.

#### 3-2. 함수 실행 컨텍스트

함수 실행 컨텍스트는 함수가 호출될 때마다 생성됩니다. 각 함수 호출에 대해 새로운 함수 실행 컨텍스트가 생성되어 호출 스택에 푸시됩니다. 이 컨텍스트는 함수 내부의 로컬 변수, 인수, 그리고 함수의 동작과 관련된 기타 정보들을 포함합니다.

```javascript
function greet(name) {
    var greeting = 'Hello ' + name;
    return greeting;
}

console.log(greet('Alice'));  // "Hello Alice" 출력
```

함수 `greet`가 호출될 때, `name`과 `greeting` 변수는 함수 실행 컨텍스트 내에 생성됩니다. 이 컨텍스트는 함수 호출이 완료되면 스택에서 제거됩니다.

#### 3-3. `eval` 실행 컨텍스트

`eval` 실행 컨텍스트는 `eval` 함수를 사용하여 실행되는 코드 블록에 대해 생성됩니다. `eval` 함수는 문자열을 코드로 평가하며, 이 코드 블록은 자체적인 실행 컨텍스트를 가집니다. `eval` 컨텍스트는 보안상의 이유로 사용을 지양하는 것이 좋으며, 현대의 자바스크립트 개발에서는 거의 사용되지 않습니다.

```javascript
var scope = 'global scope';
function evaluate() {
    var scope = 'local scope';
    eval('console.log(scope);');  // "local scope" 출력
}

evaluate();
```

`eval` 내에서 실행된 코드는 `evaluate` 함수의 실행 컨텍스트 내에서 평가되며, 따라서 해당 함수의 로컬 변수에 접근할 수 있습니다.

<br>

### 4. 실행 컨텍스트의 생명 주기

실행 컨텍스트의 생명 주기는 크게 두 단계로 나뉘며, 각 단계는 자바스크립트 엔진이 코드를 처리하는 방식을 규정합니다. 이 단계들을 통해 자바스크립트가 어떻게 변수와 함수를 관리하고, 실행 순서를 결정하는지 이해할 수 있습니다.

#### 4-1. 생성 단계

실행 컨텍스트가 생성되는 초기 단계에서는 다음과 같은 활동들이 일어납니다.

- **호이스팅(Hoisting):** 자바스크립트 엔진은 함수 내에서 선언된 변수(var), 함수 선언을 스코프의 최상단으로 끌어올립니다. 이는 선언된 위치에 관계없이 코드 내 어느 곳에서든 변수와 함수를 사용할 수 있게 해줍니다. 단, `let`과 `const` 선언된 변수는 호이스팅되지만, 초기화는 실제 코드에 도달했을 때 수행되어 접근 시 오류를 발생시킬 수 있습니다.

- **스코프 체인의 형성:** 실행 컨텍스트는 해당 컨텍스트의 변수뿐만 아니라 외부 환경의 변수에도 접근할 수 있도록 스코프 체인을 형성합니다. 이 체인을 통해 함수는 자신의 로컬 스코프, 외부 함수의 스코프, 그리고 전역 스코프에 선언된 변수와 함수에 접근할 수 있습니다.

- **this 값의 결정:** 실행 컨텍스트가 생성될 때 `this` 키워드의 값이 결정됩니다. `this`의 값은 함수 호출 방식(일반 함수 호출, 메소드 호출, 생성자 함수 등)에 따라 달라지며, 이는 함수가 어떻게 호출되었는지에 따라 결정됩니다.

#### 4-2. 실행 단계

생성 단계 다음에는 실행 단계가 이어집니다.

- **변수 할당:** 변수에 값이 할당됩니다. 호이스팅으로 인해 선언된 변수는 이미 스코프 최상단에 있으므로, 실제 실행 단계에서는 값을 할당받습니다. 이 때, 초기화되지 않은 변수는 `undefined` 값을 가지게 됩니다.

- **함수 실행:** 함수 호출이 있을 경우, 해당 함수의 실행 컨텍스트가 생성되어 호출 스택에 푸시되고, 함수 코드가 실행됩니다. 이 과정에서 함수 내부의 로컬 변수도 호이스팅되고, 새로운 스코프 체인이 형성되어 외부 스코프와의 연결이 이루어집니다.

### 실행 단계 예제

아래의 예제 코드는 실행 컨텍스트의 생명 주기를 보여줍니다:

```javascript
function printName(firstName) {
    function constructFullName() {
        return firstName + " " + lastName;
    }

    var lastName = "Doe";
    console.log(constructFullName());
}

printName("John");
```

이 코드에서 `printName` 함수가 호출되면 다음과 같은 순서로 처리됩니다:

1. **생성 단계:** `lastName` 변수와 `constructFullName` 함수가 호이스팅됩니다. `this` 값이 결정되고, 스코프 체인이 형성됩니다.
2. **실행 단계:** `lastName`에 "Doe"가 할당되고,

 `constructFullName` 함수가 호출되어 "John Doe"를 출력합니다.


<br>

## 6. 클로저(Closure)와 실행 컨텍스트

### 6-1. 클로저의 정의 및 작동 원리

### 6-2. 클로저와 스코프 체인


<br>

## 7. 실행 컨텍스트와 비동기 처리


### 7-1. 이벤트 루프(Event Loop)

### 7-2. `콜백 함수(Callback Functions)`

### 7-3. `프로미스(Promises)`와 `async/await`


<br>

## 8. 실제 사례 분석

### 8-1. 코드 예시를 통한 실행 컨텍스트의 이해

### 8-2. 문제 상황 및 해결 방안

<br>

## 9. Keywords

<br>

## 10. References

