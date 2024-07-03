# 변수가 어떻게 작동하는지 이해하기: 호이스팅, 재선언, TDZ

이 챕터에서는 자바스크립트 변수의 중요한 특징인 호이스팅, 재선언, TDZ에 대해 자세히 설명하고 있습니다. 이 챕터에 대한 내용을 정리하며 웹 개발에서 이러한 개념들을 어떻게 활용하고 주의해야 하는지 예제와 함께 작성하였습니다.

## Chapter5. 변수의 비밀 생명주기

### 1. 변수는 언제 사용할 수 있습니까? - 호이스팅 이해하기

자바스크립트 엔진은 코드를 실행하기 전에 변수와 함수 선언을 먼저 처리하는데, 이 과정을 **호이스팅**이라고 합니다. 호이스팅 때문에 마치 변수와 함수 선언이 스코프의 맨 위로 끌어올려진 것처럼 동작합니다.

### 1.1 함수 호이스팅

함수 선언문으로 정의된 함수는 함수 호이스팅 덕분에 함수가 선언되기 전에도 호출될 수 있습니다.

```jsx
greeting(); // "Hello!" 출력

function greeting() {
    console.log("Hello!");
}

```

**설명:** 자바스크립트 엔진이 `greeting()` 함수 호출을 만났을 때, 이미 호이스팅을 통해 해당 함수 선언을 알고 있기 때문에 오류 없이 함수를 실행할 수 있습니다.

### 1.2 변수 호이스팅

`var` 키워드로 선언된 변수는 호이스팅되지만, 실제 값이 할당되기 전에 접근하면 `undefined`를 반환합니다.

```jsx
console.log(greeting); // undefined 출력
var greeting = "Hello!";
console.log(greeting); // "Hello!" 출력

```

**설명:** 첫 번째 `console.log(greeting)`에서는 `greeting` 변수가 선언은 되었지만 아직 값이 할당되기 전이기 때문에 `undefined`가 출력됩니다. 두 번째 `console.log(greeting)`에서는 이미 "Hello!" 값이 할당되었기 때문에 "Hello!"가 출력됩니다.

**주의사항:** `let`과 `const` 키워드로 선언된 변수도 호이스팅되지만, `var`와 다르게 초기화 단계는 거치지 않습니다. 즉, 선언 전에 접근하면 `undefined`가 아닌 `ReferenceError`가 발생합니다. 이는 TDZ(Temporal Dead Zone)와 관련된 내용이며, 이후 섹션에서 자세히 다루겠습니다.

### 2. 호이스팅: 선언 vs. 표현식 - 함수 표현식 주의하기

함수 표현식은 변수에 함수를 할당하는 방식으로 정의됩니다. 함수 표현식은 변수 호이스팅과 동일한 방식으로 동작하기 때문에 함수 표현식이 초기화되기 전에 접근하면 오류가 발생합니다.

### 2.1 함수 표현식

```jsx
console.log(greeting()); // TypeError: greeting is not a function

var greeting = function() {
    console.log("Hello!");
};

```

**설명:** `greeting` 변수는 호이스팅되어 `undefined`로 초기화되지만, 함수 표현식 자체는 아직 할당되지 않았습니다. 따라서 `greeting()`을 호출하면 `greeting`이 함수가 아니라는 오류가 발생합니다.

### 2.2 함수 선언과 함수 표현식 비교

| 구분 | 함수 선언 | 함수 표현식 |
| --- | --- | --- |
| 키워드 | function 키워드를 사용하여 함수 이름과 함께 선언 | 변수에 함수를 할당하는 방식으로 정의 |
| 호이스팅 | 함수 선언과 함수 본문 모두 호이스팅 | 변수만 호이스팅, 함수 할당은 호이스팅 X |
| 호출 가능 시점 | 선언 전에도 호출 가능 | 할당 이후에만 호출 가능 |

**핵심:** 함수 표현식은 변수에 할당되는 값으로 취급되기 때문에, 함수 표현식 자체는 호이스팅되지 않습니다. 따라서 함수 표현식을 사용할 때는 반드시 선언 및 할당 이후에 호출해야 합니다.

### 3. 변수 재선언 - var, let, const 비교

### 3.1 `var`로 재선언

`var` 키워드로 선언된 변수는 같은 스코프 내에서 여러 번 재선언될 수 있습니다. 하지만 이는 코드의 가독성을 떨어뜨리고 예상치 못한 오류를 발생시킬 수 있으므로 권장되지 않습니다.

```jsx
var studentName = "Frank";
console.log(studentName); // "Frank" 출력

var studentName;
console.log(studentName); // "Frank" 출력 - 재선언은 무시됩니다.

```

### 3.2 `let`과 `const`로 재선언

`let`과 `const` 키워드로 선언된 변수는 같은 스코프 내에서 재선언할 수 없습니다. 만약 재선언하려고 하면 `SyntaxError`가 발생합니다.

```jsx
let studentName = "Frank";
console.log(studentName); // "Frank" 출력

let studentName = "Suzy"; // SyntaxError: Identifier 'studentName' has already been declared

```

**핵심:** `var` 키워드는 재선언을 허용하지만, `let`과 `const` 키워드는 재선언을 허용하지 않습니다. `let`과 `const`를 사용하면 의도치 않은 변수 재할당을 방지하여 코드의 안정성을 높일 수 있습니다.

### 4. 루프 내 변수 선언 - 블록 스코프 이해하기

### 4.1 `var`와 루프

`var` 키워드는 함수 스코프를 가지므로 `for` 루프와 같은 블록 내에서 선언하더라도 해당 블록 외부에서도 접근할 수 있습니다.

```jsx
for (var i = 0; i < 3; i++) {
    console.log(i); // 0, 1, 2 출력
}
console.log(i); // 3 출력 - 루프 외부에서도 접근 가능

```

### 4.2 `let`과 `const`와 루프

`let`과 `const` 키워드는 블록 스코프를 가지므로 `for` 루프와 같은 블록 내에서 선언된 변수는 해당 블록 내부에서만 접근할 수 있습니다.

```jsx
for (let i = 0; i < 3; i++) {
    console.log(i); // 0, 1, 2 출력
}
console.log(i); // ReferenceError: i is not defined - 루프 외부에서 접근 불가

```

**설명:** `var`를 사용하면 루프 변수 `i`가 함수 스코프에 속하게 되어 루프 외부에서도 접근 가능합니다. 반면 `let`과 `const`를 사용하면 루프 변수 `i`가 블록 스코프에 속하게 되어 루프 내부에서만 접근 가능합니다.

**핵심:** `for` 루프에서 `let` 또는 `const`를 사용하면 변수의 스코프를 제한하여 루프 외부에서 발생할 수 있는 의도치 않은 변경을 방지할 수 있습니다.

### 5. TDZ (Temporal Dead Zone) - 초기화 전 접근 금지

`let`과 `const` 키워드로 선언된 변수는 선언되기 전에 접근할 수 없으며, 이를 TDZ(Temporal Dead Zone)라고 합니다. TDZ는 변수가 선언된 시점부터 시작하여 초기화되기 전까지의 기간을 의미합니다.

```jsx
console.log(studentName); // ReferenceError: Cannot access 'studentName' before initialization
let studentName = "Suzy";

```

**설명:** `studentName` 변수는 `console.log`에서 사용되기 전에 선언되었지만, 아직 초기화되지 않았기 때문에 TDZ에 위치하게 됩니다. 따라서 `ReferenceError`가 발생합니다.

**TDZ 발생 규칙:**

- `let`과 `const` 키워드로 선언된 변수에만 적용됩니다.
- 변수 선언문이 속한 블록 내에서만 적용됩니다.
- 변수 선언문부터 초기화되기 전까지의 영역을 의미합니다.

**핵심:** TDZ는 `let`과 `const` 키워드로 선언된 변수가 초기화되기 전에 발생하는 오류를 방지하여 코드의 안정성을 높이는 역할을 합니다.

### 6. 웹 개발에서의 실제 사례

### 6.1 DOM 요소 접근

DOM 요소에 접근할 때 변수 호이스팅과 TDZ를 주의해야 합니다. DOM 요소를 참조하는 변수는 해당 요소가 로드된 후에 초기화해야 합니다.

```html
<button id="myButton">Click me</button>
<script>
  let button = document.getElementById('myButton'); // DOM 로드 후 변수 초기화
  button.addEventListener('click', function() {
    alert('Button clicked!');
  });
</script>

```

**설명:** 위 예제에서 `button` 변수는 `document.getElementById('myButton')`를 통해 DOM 요소를 참조합니다. 만약 `button` 변수를 DOM 요소가 로드되기 전에 사용하려고 하면 `null`을 반환하거나 `TypeError`가 발생할 수 있습니다. 따라서 DOMContentLoaded 이벤트 리스너 내부 또는 스크립트 태그를 HTML 문서의 맨 아래에 배치하여 DOM 요소가 로드된 후에 변수를 초기화해야 합니다.

### 6.2 AJAX 요청

AJAX 요청에서 데이터를 처리할 때도 변수 호이스팅과 TDZ를 주의해야 합니다. 비동기적으로 데이터를 가져오는 경우, 데이터가 도착하기 전에 변수에 접근하면 오류가 발생할 수 있습니다.

```jsx
function fetchData() {
  let data; // 변수 선언
  const xhr = new XMLHttpRequest();
  xhr.open('GET', '<https://api.example.com/data>', true);
  xhr.onload = function() {
    if (xhr.status >= 200 && xhr.status < 300) {
      data = JSON.parse(xhr.responseText); // 데이터 수신 후 변수에 할당
      console.log(data); // 데이터 출력
    }
  };
  xhr.send();
}

fetchData();

```

**설명:** 위 예제에서 `data` 변수는 AJAX 요청을 통해 받아온 데이터를 저장하기 위해 선언되었습니다. AJAX 요청은 비동기적으로 처리되기 때문에 `xhr.onload` 이벤트 핸들러 내부에서 `data` 변수에 값이 할당됩니다. 만약 `xhr.onload` 이벤트 핸들러 외부에서 `data` 변수에 접근하면 초기화되지 않은 상태이므로 `undefined`를 반환하거나 `ReferenceError`가 발생할 수 있습니다. 따라서 AJAX 요청을 통해 데이터를 가져온 후에 해당 데이터를 사용하는 로직을 처리해야 합니다.

### 7. 요약

- **호이스팅:** 자바스크립트 엔진이 코드를 실행하기 전에 변수와 함수 선언을 먼저 처리하는 것을 의미합니다.
- **변수 호이스팅:** `var` 키워드로 선언된 변수는 호이스팅되지만 초기화는 이루어지지 않습니다. `let`과 `const`는 호이스팅되지만 TDZ에 의해 초기화 전 접근이 제한됩니다.
- **함수 호이스팅:** 함수 선언문으로 정의된 함수는 호이스팅되어 선언 전에도 호출될 수 있습니다. 함수 표현식은 호이스팅되지 않습니다.
- **TDZ (Temporal Dead Zone):** `let`과 `const` 키워드로 선언된 변수는 선언 후 초기화되기 전까지 접근할 수 없는 기간을 의미합니다.
