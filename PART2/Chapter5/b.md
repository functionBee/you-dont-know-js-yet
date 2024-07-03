## Chapter 5. 변수의 비밀 생명주기

### 5-1. 변수 사용 가능 시점

자바스크립트 엔진은 코드를 실행하기 전에 변수와 함수 선언을 먼저 처리하는데, 이 과정을 **호이스팅**이라고 합니다. 호이스팅 때문에 마치 변수와 함수 선언이 스코프의 맨 위로 끌어올려진 것처럼 동작합니다.

#### 5.1.1 선언문과 표현식에서의 호이스팅 차이

##### 함수 호이스팅

함수 선언문으로 정의된 함수는 함수 호이스팅 덕분에 함수가 선언되기 전에도 호출될 수 있습니다.

```js
greeting(); // "Hello!" 출력

function greeting() {
    console.log("Hello!");
}
```

**설명:** 자바스크립트 엔진이 `greeting()` 함수 호출을 만났을 때, 이미 호이스팅을 통해 해당 함수 선언을 알고 있기 때문에 오류 없이 함수를 실행할 수 있습니다.

##### 변수 호이스팅

`var` 키워드로 선언된 변수는 호이스팅되지만, 실제 값이 할당되기 전에 접근하면 `undefined`를 반환합니다.

```js
console.log(greeting); // undefined 출력
var greeting = "Hello!";
console.log(greeting); // "Hello!" 출력
```

**설명:** 첫 번째 `console.log(greeting)`에서는 `greeting` 변수가 선언은 되었지만 아직 값이 할당되기 전이기 때문에 `undefined`가 출력됩니다. 두 번째 `console.log(greeting)`에서는 이미 "Hello!" 값이 할당되었기 때문에 "Hello!"가 출력됩니다.

##### 함수 표현식

함수 표현식은 변수에 함수를 할당하는 방식으로 정의됩니다. 함수 표현식은 변수 호이스팅과 동일한 방식으로 동작하기 때문에 함수 표현식이 초기화되기 전에 접근하면 오류가 발생합니다.

```js
console.log(greeting()); // TypeError: greeting is not a function

var greeting = function() {
    console.log("Hello!");
};
```

**설명:** `greeting` 변수는 호이스팅되어 `undefined`로 초기화되지만, 함수 표현식 자체는 아직 할당되지 않았습니다. 따라서 `greeting()`을 호출하면 `greeting`이 함수가 아니라는 오류가 발생합니다.

##### 함수 선언과 함수 표현식 비교

| 구분       | 함수 선언                                    | 함수 표현식                        |
|----------|-------------------------------------------|----------------------------------|
| 키워드      | function 키워드를 사용하여 함수 이름과 함께 선언               | 변수에 함수를 할당하는 방식으로 정의            |
| 호이스팅     | 함수 선언과 함수 본문 모두 호이스팅                      | 변수만 호이스팅, 함수 할당은 호이스팅되지 않음   |
| 호출 가능 시점 | 선언 전에도 호출 가능                               | 할당 이후에만 호출 가능                |

**핵심:** 함수 표현식은 변수에 할당되는 값으로 취급되기 때문에, 함수 표현식 자체는 호이스팅되지 않습니다. 따라서 함수 표현식을 사용할 때는 반드시 선언 및 할당 이후에 호출해야 합니다.

### 5-2. 호이스팅 비유일 뿐입니다

호이스팅 은유는 프로그램의 선언이 실제로 스코프의 최상단으로 이동된다고 설명하지만, 실제로 자바스크립트 엔진은 선언을 코드의 상단으로 이동시키지 않습니다. 대신, 자바스크립트 엔진은 코드를 두 번 처리합니다. 먼저 선언을 등록하고, 그 다음에 코드를 실행합니다. 이는 호이스팅을 실행 시간 동작이 아니라 컴파일 시간 작업으로 보는 것이 더 적절함을 의미합니다.

### 5-3. 중복 선언 처리하기

자바스크립트에서 변수 선언은 중복 선언이 가능하거나 불가능할 수 있으며, 이는 사용된 키워드에 따라 다릅니다.

#### 5.3.1 const 재선언

`const` 키워드는 같은 스코프 내에서 재선언할 수 없으며, 재선언하려고 하면 `SyntaxError`가 발생합니다.

```js
const studentName = "Frank";
console.log(studentName); // "Frank" 출력

const studentName = "Suzy"; // SyntaxError: Identifier 'studentName' has already been declared
```

**핵심:** `const` 키워드를 사용하면 의도치 않은 변수 재할당을 방지하여 코드의 안정성을 높일 수 있습니다.

#### 5.3.2 반복문

`var` 키워드는 함수 스코프를 가지므로 `for` 루프와 같은 블록 내에서 선언하더라도 해당 블록 외부에서도 접근할 수 있습니다.

```js
for (var i = 0; i < 3; i++) {
    console.log(i); // 0, 1, 2 출력
}
console.log(i); // 3 출력 - 루프 외부에서도 접근 가능
```

`let`과 `const` 키워드는 블록 스코프를 가지므로 `for` 루프와 같은 블록 내에서 선언된 변수는 해당 블록 내부에서만 접근할 수 있습니다.

```js
for (let i = 0; i < 3; i++) {
    console.log(i); // 0, 1, 2 출력
}
console.log(i); // ReferenceError: i is not defined - 루프 외부에서 접근 불가
```

**설명:** `var`를 사용하면 루프 변수 `i`가 함수 스코프에 속하게 되어 루프 외부에서도 접근 가능합니다. 반면 `let`과 `const`를 사용하면 루프 변수 `i`가 블록 스코프에 속하게 되어 루프 내부에서만 접근 가능합니다.

**핵심:** `for` 루프에서 `let` 또는 `const`를 사용하면 변수의 스코프를 제한하여 루프 외부에서 발생할 수 있는 의도치 않은 변경을 방지할 수 있습니다.

### 5-4. 초기화되지 않은 변수와 TDZ

`let`과 `const` 키워드로 선언된 변수는 선언되기 전에 접근할 수 없으며, 이를 TDZ(Temporal Dead Zone)라고 합니다. TDZ는 변수가 선언된 시점부터 시작하여 초기화되기 전까지의 기간을 의미합니다.

```js
console.log(studentName); // ReferenceError: Cannot access 'studentName' before initialization
let studentName = "Suzy";
```

**설명:** `studentName` 변수는 `console.log`에서 사용되기 전에 선언되었지만, 아직 초기화되지 않았기 때문에 TDZ에 위치하게 됩니다. 따라서 `ReferenceError`가 발생합니다.

**TDZ 발생 규칙:**

- `let`과 `const` 키워드로 선언된 변수에만 적용됩니다.
- 변수 선언문이 속한 블록 내에서만 적용됩니다.
- 변수 선언문부터 초기화되기 전까지의 영역을 의미합니다.

**핵심:** TDZ는 `let`과 `const` 키워드로 선언된 변수가 초기화되기 전에 발생하는 오류를 방지하여 코드의 안정성을 높이는 역할을 합니다.

### 5-5. 정리

- **호이스팅:** 자바스크립트 엔진이 코드를 실행하기 전에 변수와 함수 선언을 먼저 처리하는 것을 의미합니다.
- **변수 호이스팅:** `var` 키워드로 선언된 변수는 호이스팅되지만 초기화는 이루어지지 않습니다. `let`과 `const`는 호이스팅되지만 TDZ에 의해 초기화 전 접근이 제한됩니다.
- **함수 호이스팅:** 함수 선언문으로 정의된 함수는 호이스팅되어 선언 전에도 호출될 수 있습니다. 함수 표현식은 호이스팅되지 않습니다.
- **TDZ (Temporal Dead Zone):** `let`과 `const` 키워드로 선언된 변수는 선언 후 초기화되기 전까지 접근할 수 없는 기간을 의미합니다.