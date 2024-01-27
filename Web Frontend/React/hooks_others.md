# 기타 리액트 훅

자주 사용되지는 않지만 리액트에서 제공하는 훅들을 알아보자.

## useImperativeHandle

useImperativeHandle은 부모로부터 넘겨받은 ref, 즉 forwardRef를 원하는대로 수정할 수 있는 훅이다.

### forwardRef

- 만약 자식 컴포넌트에게 ref를 전달하기 위해 `<Child ref={parentRef} />` 같이 일반적인 props 처럼 전달해주면, ref는 key와 같이 props 예약어이기 때문에 경고문이 생기면서 `undefined` 가 반환된다.
- 따라서 `<Child parentRef={parentRef} />` 처럼 다른 이름의 props로 전달하면 정상적으로 ref를 전달할 수 있지만, `forwardRef`는 이러한 네이밍 자유가 주어진 props보다는 <u>확실하게 ref를 전달할 것임을 예측하고 일관성을 제공하기 위해 사용</u>된다.

```jsx
const ParentComponent = () => {
  const inputRef = useRef();
  // ...
  return <ChildComponent ref={inputRef} value={1} />;
};

const ChildComponent = forwardRef((props, ref) => {
  console.log(ref); // {current: HTMLInputElement}
  return <div>{props.value}</div>;
});
```

여기서 `useImperativeHandle`을 사용하면 자식 컴포넌트에서 부모로부터 받은 ref를 수정할 수 있다. 즉, 전달 받은 `{current: Value}` 객체에 추가적인 액션이나 값 등을 담은 프로퍼티를 추가할 수 있는 것이다.

```jsx
const ChildComponent = forwardRef((props, ref) => {
 	useImperativeHandle(ref, () => ({
    alert: () => alert(props.value); // 부모 컴포넌트에서 inputRef.current.alert() 호출 가능
  }))
  return <div>{props.value}</div>;
});
```

## useLayoutEffect

useLayoutEffect는 '모든 DOM의 변경' 이후에 콜백 함수를 '동기적으로' 실행하는 훅이다.

- 즉, `useEffect` 는 브라우저에 변경 사항이 반영된 이후 콜백 함수를 실행했다면, `useLayoutEffect` 는 DOM 업데이트 이후 브라우저에 변경 사항을 반영하기 전에 실행된다.
- 따라서 `useLayoutEffect` 는 항상 `useEffect` 보다 먼저 실행된다.
- `useLayoutEffect`의 함수가 동기적으로 수행된다는 건, 콜백 함수가 실행 종료될 때까지 기다렸다가 브라우저 화면을 그린다는 것을 의미한다. 즉, `useLayoutEffect`의 실행 동안 리액트 컴포넌트가 잠시 일시 중지하므로, 오래 걸리는 작업을 포함해서는 안된다.
- DOM 요소를 기반으로 스크롤 위치를 제어하는 등 작업

## useDebugValue

- 개발하는 과정에서 디버깅하고 싶은 정보가 있는 경우
- 컴포넌트 레벨에서는 실행할 수 없고, 오직 다른 훅 내부에서만 실행 가능하다.

```js
function useDate() {
	const date = new Date();
	useDebugValue(date, (date) => `data: ${date.toISOString()});
	return date;
}
```