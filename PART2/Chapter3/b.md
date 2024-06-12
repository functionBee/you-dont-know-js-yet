## Chapter 3: The Scope Chain 

### 요약

챕터 1과 2에서는 *렉시컬 스코프*(lexical scope)의 정의와 그 구성 요소를 설명하고, 이를 이해하는 데 도움이 되는 비유를 제시했습니다. 이 장으로 넘어가기 전에, 렉시컬 스코프가 무엇인지, 왜 중요한지를 다른 사람에게 설명해 보세요. 이는 학습한 내용을 머릿속에 정리하는 데 큰 도움이 됩니다.

이번 장에서는 스코프 체인의 세부 사항을 다루며, 코드 스니펫을 통해 개념을 강화합니다. 이전 장의 스코프 버블 예제를 다시 떠올려 봅시다. 스코프 체인은 중첩된 스코프 간의 연결을 의미하며, 변수 접근 경로를 결정합니다. 이 체인은 방향성이 있어 조회가 위쪽/바깥쪽으로만 이동합니다.

### "조회"는 (대부분) 개념적이다

스코프 체인은 개념적으로 변수의 위치를 조회하는 방식으로 설명됩니다. 다음 예제를 살펴봅시다:

```js
for (let i = 0; i < students.length; i++) {
    console.log(students[i]);
}
```

위 코드에서 `students` 변수는 RED(1) 스코프에 존재합니다. 이는 스코프 체인을 통해 변수 `students`를 찾는 과정에서 결정됩니다. *엔진*(Engine)은 먼저 현재 스코프에서 변수를 찾고, 상위 스코프 체인으로 이동하여 변수를 찾습니다. 

### 컴파일 시점의 최적화

실제로 실행 시에는 조회가 필요하지 않습니다. 이유는 컴파일 시점에 렉시컬 스코프가 이미 확정되기 때문입니다. 컴파일 시 변수의 스코프가 결정되므로 실행 중에는 이 정보를 이미 알고 있어 조회를 피할 수 있습니다. 이는 렉시컬 스코프의 주요 최적화 이점 중 하나입니다.

### 그림자(Shadowing)

"그림자"란 용어는 다소 신비롭고 약간 의심스러워 보일 수 있지만, 이는 완전히 합법적인 개념입니다. 그림자는 동일한 이름의 변수가 중첩된 스코프에서 선언될 때 발생합니다. 예를 들어:

```js
var studentName = "Suzy";

function printStudent(studentName) {
    studentName = studentName.toUpperCase();
    console.log(studentName);
}

printStudent("Frank"); // FRANK
printStudent(studentName); // SUZY
console.log(studentName); // Suzy
```

위 예제에서 `studentName` 변수는 글로벌 스코프에서 선언된 RED(1) 변수와 함수의 파라미터로 선언된 BLUE(2) 변수가 있습니다. 함수 내부에서는 파라미터 변수 `studentName`이 글로벌 변수 `studentName`을 그림자로 가립니다. 이는 함수 내부에서 글로벌 변수를 참조할 수 없음을 의미합니다.

### 글로벌 언셰도잉(해제) 트릭

스코프 내에서 그림자가 생긴 글로벌 변수를 접근하는 방법은 글로벌 객체의 속성으로 접근하는 것입니다. 예를 들어, 브라우저 환경에서 글로벌 객체는 `window`입니다:

```js
var studentName = "Suzy";

function printStudent(studentName) {
    console.log(studentName);
    console.log(window.studentName);
}

printStudent("Frank"); // "Frank", "Suzy"
```

이 경우 `window.studentName`을 통해 글로벌 변수를 참조할 수 있습니다. 그러나 이 방법은 권장되지 않으며, 이해를 돕기 위해서만 설명하는 것입니다.

### 변수 복사는 접근이 아니다

객체를 사용하는 경우, 변수의 복사를 통해 객체의 값을 참조하는 것은 변수를 직접 접근하는 것과 다릅니다. 예를 들어:

```js
var special = 42;

function lookingFor(special) {
    var another = {
        special: special
    };

    function keepLooking() {
        var special = 3.141592;
        console.log(special); // 3.141592
        console.log(another.special); // 112358132134
        console.log(window.special); // 42
    }

    keepLooking();
}

lookingFor(112358132134);
```

여기서 `another.special`은 `special` 변수를 직접 참조하는 것이 아니라 그 값을 복사한 것입니다. 

### 불법 그림자

모든 그림자가 허용되는 것은 아닙니다. 예를 들어, `let`은 `var`를 그림자로 가릴 수 있지만, 그 반대는 불가능합니다:

```js
function something() {
    var special = "JavaScript";

    {
        let special = 42; // 허용되는 그림자
    }
}

function another() {
    let special = "JavaScript";

    {
        var special = "JavaScript"; // Syntax Error
    }
}
```

위 예제에서 `var` 선언이 `let` 선언을 "넘어서려" 하므로 오류가 발생합니다.

### 함수 이름 스코프

함수 선언은 다음과 같이 생겼습니다:

```js
function askQuestion() {
    // ..
}
```

이와 같은 함수 선언은 외부 스코프에 `askQuestion`이라는 식별자를 생성합니다. 반면, 함수 표현식의 이름 식별자는 함수 내부에서만 사용할 수 있습니다:

```js
var askQuestion = function ofTheTeacher() {
    console.log(ofTheTeacher);
};

askQuestion(); // function ofTheTeacher()...
console.log(ofTheTeacher); // ReferenceError
```

### 화살표 함수

ES6는 "화살표 함수"를 도입했습니다:

```js
var askQuestion = () => {
    // ..
};
```

화살표 함수는 익명으로 선언되며, 기존 함수와 동일한 렉시컬 스코프 규칙을 따릅니다. 

### 요약

함수가 정의될 때 새로운 스코프가 생성됩니다. 중첩된 스코프의 배치는 자연스러운 스코프 계층을 형성하며, 이를 스코프 체인이라 부릅니다. 스코프 체인은 변수 접근을 제어하며, 방향성은 위쪽/바깥쪽으로만 이동합니다. 그림자는 외부 변수를 접근할 수 없게 합니다.

다음 장에서는 모든 JS 프로그램에 포함된 주요 스코프인 글로벌 스코프에 대해 다룰 것입니다.