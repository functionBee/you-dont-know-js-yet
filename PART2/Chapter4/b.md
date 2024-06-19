# 4장: 전역 스코프에 대하여

## 전역 스코프의 중요성

현대 JS에서는 대부분의 작업이 함수와 모듈 내부에서 이루어집니다. 하지만 전역 스코프는 여전히 중요한 주제입니다. 이 장에서는 전역 스코프가 어떻게 유용하고 관련이 있는지, 다양한 JS 환경에서 전역 스코프에 접근하는 방법을 살펴봅니다.

## 전역 스코프의 역할

### 모듈 로딩
1. **ES 모듈 사용**: 각 파일은 개별적으로 로드되고 `import`를 통해 다른 모듈과 협력합니다.
2. **번들러 사용**: 파일들이 하나의 큰 파일로 결합되고 로컬 변수로 상호 접근을 허용합니다.
    ```js
    (function wrappingOuterScope(){
        var moduleOne = (function one(){ /* .. */ })();
        var moduleTwo = (function two(){
            function callModuleOne() {
                moduleOne.someMethod();
            }
        })();
    })();
    ```
3. **개별 파일 로딩**: 전역 스코프에서 변수 접근이 가능합니다.
    ```js
    var moduleOne = (function one(){ /* .. */ })();
    var moduleTwo = (function two(){
        function callModuleOne() {
            moduleOne.someMethod();
        }
    })();
    ```

### 전역 객체
전역 스코프는 기본 타입과 네이티브 객체, 전역 함수 등을 노출합니다.

### 전역 변수
전역 스코프는 변수 충돌을 피하기 위해 신중하게 관리해야 합니다.
```js
window.something = 42;
let something = "Kyle";
console.log(something); // "Kyle"
console.log(window.something); // 42
```

## 다양한 환경의 전역 스코프

### 브라우저
전역 스코프는 `window` 객체와 연결됩니다. DOM 요소 ID는 자동으로 전역 변수가 됩니다.
```js
var studentName = "Kyle";
function hello() {
    console.log(`Hello, ${ studentName }!`);
}
hello(); // Hello, Kyle!
```

### 웹 워커
`self` 객체를 사용하여 전역 스코프에 접근합니다.
```js
var studentName = "Kyle";
function hello() {
    console.log(`Hello, ${ self.studentName }!`);
}
self.hello(); // Hello, Kyle!
```

### 개발자 도구 콘솔/REPL
개발자 도구는 전역 스코프를 에뮬레이트합니다.

### ES 모듈 (ESM)
모듈의 최상위 스코프는 전역 변수를 생성하지 않으며 모듈 전체에 걸쳐 유효합니다.
```js
var studentName = "Kyle";
export function hello() {
    console.log(`Hello, ${ studentName }!`);
}
hello(); // Hello, Kyle!
```

### Node.js
모든 파일은 모듈로 처리됩니다.
```js
global.studentName = "Kyle";
function hello() {
    console.log(`Hello, ${ studentName }!`);
}
hello(); // Hello, Kyle!
```

## 전역 객체 참조

### 다양한 접근 방법
- 브라우저: `window`
- 웹 워커: `self`
- Node.js: `global`

### 통합 접근 방법
ES2020부터는 `globalThis`를 사용합니다.
```js
const theGlobalScopeObject =
    (typeof globalThis != "undefined") ? globalThis :
    (typeof global != "undefined") ? global :
    (typeof window != "undefined") ? window :
    (typeof self != "undefined") ? self :
    (new Function("return this"))();
```

## 결론

전역 스코프는 모든 JS 프로그램에서 중요합니다. 모듈화 패턴이 이를 줄였지만, 전역 스코프의 차이점을 이해하는 것은 중요합니다. 다음 장에서는 렉시컬 스코프와 변수 사용에 대해 더 깊이 다룹니다.
