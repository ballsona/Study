# 변수와 상수

변수와 상수의 차이, 그리고 `let` , `const` ,`var` 키워드에 대해 알아보자.

## 변수(variables)

변수는 데이터를 저장할 때 쓰이는 '이름이 붙은 저장소'이다.

- `let` 키워드를 사용해 변수를 생성하고, 값을 할당한다.
- 값을 재할당하므로써 변수값을 변경할 수 있다.

```js
let message = 'hello';
message = 'world';
```

## 상수(constants)

상수는 값이 변하지 않는 변수를 말한다.

- `const` 키워드를 사용해 상수를 생성하고 값을 할당한다.
- 값을 재할당할 수 없다.

```js
const RED = '#f00';
RED = '#000'; // error!
```

위 `RED` 라는 상수처럼 코드가 실행되기 전에 이미 값이 정해진 상수도 있고, 런타임 과정에서 계산되지만 최초 할당 이후 변하지 않는 상수도 있다.

## 변수 명명 규칙

- 변수명에는 문자와 숫자, `$` 와 `_` 만 들어갈 수 있다.
- 첫 글자는 숫자가 될 수 없다.
- 예약어(let, class, return 등)은 변수명으로 사용할 수 없다.
- 여러 단어를 조합하여 변수명을 만들 땐 camelCase를 되도록 사용하자!
- 변수는 최대한 간결하게, 그러나 서술적이고 명확하게 짓자!

## let vs var

`let` 과 `var` 라는 2가지 키워드를 사용하여 변수를 선언할 수 있다. 이 두 키워드의 차이점을 알아보자.

> 하지만 정말 알아만 보고 사용은 `let` 만 하도록 하자.

### 도입 시점

- `var` 은 초기 버전의 자바스크립트부터 존재했던 변수 선언 키워드로, 최근 코드에서는 거의 사용되지 않는다.
- `let` 은 ES6부터 도입된 키워드이다. 변수를 선언할 때 무조건 `let` 을 쓰도록 하자.

> ES1은 1997년에 발표되었고, ES6은 2015년에 발표되었다. var은 정말 오래된 애다. 참고로 현재는 ES11(ES2020) 까지 업데이트되었다.

### 스코프

모든 변수는 자신을 참조할 수 있는 범위인 [스코프](/Language/Javascript/0.Core%20Javascript/Lexical%20Scope%20and%20Closure.md) 를 갖는다.

- `var` 은 **블록 스코프**가 없다. 즉 코드 블록 내 `var` 로 선언된 변수는 자동으로 전역 스코프 혹은 함수 스코프를 가지게 되어, <u>블록 밖에서도 변수에 접근이 가능</u>하다.

- `let` 은 블록 스코프를 갖는다. 따라서 if문이나 for문 등 내부에서 선언된 변수는 반드시 해당 코드 블록(`{}`) 내부에서만 접근이 가능하다.

### 중복 선언

- `var` 을 이용하면 같은 변수를 여러 번 중복으로 선언해도 에러가 발생하지 않는다. 만약 같은 변수에 중복 선언을 한 경우 첫번째 선언만 유효하고, 나머지는 모두 무시된다.

  ```js
  var message = 'hello';
  var message = 'world'; // 이 구문은 아무것도 하지 않는다.
  ```

- `let` 으로 선언한 변수는 두 번 선언 시 에러가 발생한다. 절대 중복 선언을 할 수 없다.

  ```js
  let message = 'hello';
  let message = 'world'; // SyntaxError
  ```

### 호이스팅 관련

var 와 let 키워드로 선언된 변수들은 모두 코드 실행 전 [호이스팅](#변수-호이스팅) 되지만, 그 과정에서 차이점이 존재한다.

- `var` 으로 선언된 변수는 참조 및 호출이 선언문보다 먼저 나와도 오류 없이 동작한다. `var` 키워드는 호이스팅 과정에서 값이 `undefined` 로 초기화되어 할당된 메모리 공간에 저장되기 때문이다. 즉, <u>메모리 공간이 확보된 직후에 변수 값이 초기화</u>되기 때문에, 코드 실행 이후에는 스코프 내 어디서든 해당 변수에 접근 가능하다.

  ```js
  message = 'hello'; // (1) 값 할당
  console.log(message); // (3) 호출

  var message; // (0) 호이스팅 단계에서 undefined로 초기화
  ```

- 반면 `let` 으로 선언된 변수는 코드 실행 이후 <u>변수 선언문에 도달했을 때 비로소 메모리 공간 확보 및 값 할당을 진행</u>하기 때문에, 변수 선언 이전에 변수에 접근하려고 하면 에러가 발생한다.

  ```js
  console.log(message); // ReferenceError!
  let message = 'message';
  ```

정확히는 `let` 으로 선언된 변수는 스코프의 시작에서 변수의 선언까지 **TDZ**(Temporal Dead Zone)에 빠지기 때문에 선언 전에 변수에 접근하면 에러가 발생한다.

## const

앞서 변수의 스코프나 동작 방식에 대해 살펴보았는데, 그럼 `const` 로 선언된 상수는 어떠할까?

- 도입 시점: `let` 와 함께 ES6에서 도입되었다.
- 스코프: `let` 와 마찬가지로 블록 스코프를 갖는다. 즉 코드 블록 내부에서 상수가 선언되었다면, 해당 블록 내부에서만 접근 가능하다.
- 중복 선언: 당연히 안된다. `const` 선언된 상수는 <u>반드시 선언과 동시에 값을 할당</u>해야 함도 주의하자.
- 호이스팅: `let` 와 동일하다. '상수 선언'이 호이스팅되지만 TDZ에 의하여 선언문보다 호출이 앞서면 에러가 발생한다.

## 변수 호이스팅

호이스팅(hoisting)이란 코드가 실행되기 전, **변수 및 함수 선언이 해당 스코프의 최상단으로 끌어 올려진 것 같은 현상**을 의미한다. 진짜 스코프 최상단으로 끌어올리는 것이 아님을 주의하자.

- 자바스크립트 엔진은 코드를 실행하기에 앞서서 코드가 실행되기 위해 필요한 환경, 즉 실행 컨텍스트를 구축하는 과정을 거친다.
- 이 과정에서 var, let, const, function, class 로 '선언'된 '모든 변수와 함수'를 스코프에 등록한다.
- 더 정확히 말하면 인터프리터가 <u>변수와 함수의 메모리 공간을 미리 할당</u>하는 작업을 진행한다.

이렇게 코드 실행 전에 모든 선언문을 찾아 미리 처리해두는 방식으로 인해, 마치 선언문이 스코프 최상단에 올려진 것 같이 보이는 현상을 '**호이스팅**'이라고 하는 것이다.

- 참고: [[JavaScript] 호이스팅(Hoisting)이란?](https://hanamon.kr/javascript-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85%EC%9D%B4%EB%9E%80-hoisting/)
