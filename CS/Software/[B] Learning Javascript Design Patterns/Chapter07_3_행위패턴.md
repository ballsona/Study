# Chapter 7. 자바스크립트 디자인 패턴 - 행위 패턴

## 1. 관찰자 패턴

- 한 객체가 변경될 때 다른 객체들에 변경되었음을 알릴 수 있게 해주는 패턴 
- 한 객체(주체)를 관찰하는 여러 객체(관찰자)들이 존재. 변경된 객체는 누가 자신을 구독하는지 알 필요 없이 알림 보낼 수 있음. 
- 클래스를 강하게 결합시키지 않으면서 **관련 객체들 사이의 일관성을 유지**해야 하는 경우

### 구현 예시

```js
```

### 관찰자 패턴 vs 발행/구독 패턴

- 관찰자 패턴은 관찰자 객체가 이벤트를 발생시키는 주체에 알림 대상으로서 '등록'이 되어야 한다.
- 반면 발행/구독 패턴에서는 이벤트 알림을 원하는 구독자와 이벤트를 발생시키는 발생자 사이에 '토픽/이벤트 채널'을 둔다. 이벤트 핸들러를 가지고 있는 구독자라면 누구나 발행자가 전파하는 토픽 알림을 받을 수 있다.
  - 발행자와 구독자를 각각 독립적으로 유지하는 게 핵심. -> 느슨한 결합

### 장단점

- 장점: 주체와 객체 사이에 동적인 관계가 형성

### RxJS

## 2. 중재자 패턴

- 하나의 객체가 이벤트 발생 시 다른 여러 객체들에게 알림을 보낼 수 있는 패턴 

## 3. 커맨드 패턴

- 메서드 호출, 요청 또는 작업을 단일 객체로 캡슐화하여 추후에 실행할 수 있도록 해주는 패턴 

