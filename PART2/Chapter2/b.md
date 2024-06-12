# 제2장: 렉시컬 스코프 설명하기

제2장은 렉시컬 스코프를 다양한 비유로 설명하면서 JavaScript 코드로 이를 구체화합니다.

## 렉시컬 스코프란?

렉시컬 스코프는 코드가 작성된 위치에 따라 스코프가 결정되는 모델입니다. 이는 컴파일 시에 변수가 어디에서 선언되고 어디에서 참조되는지를 결정합니다.

## 구슬과 버킷 비유

변수를 구슬에 비유하고, 스코프를 버킷에 비유하여 이해할 수 있습니다. 변수는 특정 스코프에서 선언되며, 이 스코프는 변수를 담고 있는 버킷으로 생각할 수 있습니다.

### 코드 예제

```javascript
// 외부/글로벌 스코프: 빨강

var students = [
    { id: 14, name: "Kyle" },
    { id: 73, name: "Suzy" },
    { id: 112, name: "Frank" },
    { id: 6, name: "Sarah" }
];

function getStudentName(studentID) {
    // 함수 스코프: 파랑

    for (let student of students) {
        // 반복문 스코프: 초록

        if (student.id == studentID) {
            return student.name;
        }
    }
}

var nextStudent = getStudentName(73);
console.log(nextStudent);   // Suzy
```

이 코드에서는 세 가지 스코프가 있습니다:
- 글로벌 스코프 (빨강)
- 함수 스코프 (파랑)
- 반복문 스코프 (초록)

### 스코프의 시각화

각 스코프의 경계를 시각화하기 위해 색깔이 있는 거품을 사용합니다.

1. **빨강 스코프**: 글로벌 스코프로서 `students`, `getStudentName`, `nextStudent` 변수를 포함합니다.
2. **파랑 스코프**: `getStudentName` 함수의 스코프로서 `studentID` 변수를 포함합니다.
3. **초록 스코프**: `for` 루프의 스코프로서 `student` 변수를 포함합니다.

## 변수 선언과 참조

변수는 선언된 스코프에서 구슬 색깔을 가지며, 이는 해당 스코프의 버킷에 속합니다. 변수가 참조될 때, 현재 스코프 또는 상위 스코프에서 해당 변수를 찾습니다.

### 선언 예제

```javascript
var students = [ /* ... */ ];  // 빨강 구슬
function getStudentName(studentID) {  // 파랑 구슬
    for (let student of students) {  // 초록 구슬
        if (student.id == studentID) {
            return student.name;
        }
    }
}
```

여기서, `students`는 글로벌 스코프에서 선언되어 빨강 구슬을 가지며, `studentID`는 함수 스코프에서 선언되어 파랑 구슬을 가집니다. `student`는 반복문 스코프에서 선언되어 초록 구슬을 가집니다.

### 참조 예제

```javascript
var nextStudent = getStudentName(73);  // 빨강 스코프에서 함수 호출
console.log(nextStudent);  // 빨강 스코프에서 변수 참조
```

`getStudentName(73)` 호출은 파랑 스코프를 생성하며, 이 함수 내에서 `students` 변수는 상위 스코프인 빨강 스코프에서 참조됩니다.

## 스코프 체인과 변수 조회

변수가 참조될 때, 현재 스코프에서 변수를 찾지 못하면 상위 스코프로 이동하여 변수를 찾습니다. 이를 스코프 체인이라고 합니다.

### 스코프 체인 예제

```javascript
function getStudentName(studentID) {
    for (let student of students) {
        if (student.id == studentID) {
            return student.name;
        }
    }
}
```

`students` 변수는 `getStudentName` 함수 내에서 참조되지만, 이 변수는 함수 스코프에 없기 때문에 상위 스코프인 글로벌 스코프에서 찾습니다.

## 스코프 매니저와 엔진의 대화

자바스크립트 엔진은 변수를 조회하고 실행하기 위해 스코프 매니저와 대화를 나눕니다.

### 대화 예제

**컴파일러와 스코프 매니저의 대화:**

1. `var students` 선언 시:
    - 컴파일러: "글로벌 스코프 매니저, `students` 변수 있어?"
    - 글로벌 스코프 매니저: "없어, 지금 만들게."

2. `function getStudentName(studentID)` 선언 시:
    - 컴파일러: "글로벌 스코프 매니저, `getStudentName` 함수 있어?"
    - 글로벌 스코프 매니저: "없어, 지금 만들게."

**엔진과 스코프 매니저의 대화:**

1. `students` 변수 참조 시:
    - 엔진: "글로벌 스코프 매니저, `students` 변수 있어?"
    - 글로벌 스코프 매니저: "있어, 여기 있어."

2. `student` 변수 참조 시:
    - 엔진: "반복문 스코프 매니저, `student` 변수 있어?"
    - 반복문 스코프 매니저: "있어, 여기 있어."

## 엄격 모드와 선언되지 않은 변수

엄격 모드에서는 선언되지 않은 변수에 접근하려고 할 때 `ReferenceError`가 발생합니다.

### 엄격 모드 예제

```javascript
"use strict";

function getStudentName() {
    nextStudent = "Suzy";  // ReferenceError 발생
}

getStudentName();
```

엄격 모드에서는 선언되지 않은 변수에 값을 할당하려고 할 때 오류가 발생합니다.
