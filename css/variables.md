### variables

css 내에서 color 값을 헥스 문자열로 관리하게 되면 코드가 많아졌을 때 관리하기 어려워질 수 있는데, 이런 경우 css 내부에서도 변수처럼 관리할 수 있다. 

만약 전역적으로 사용하고 싶다면,

```css
:root{
  --white:'#fff';
  --black:'#000';
  --primary:'#353535';
}

div{
  color:var(--white)
}
```

변수는 당연히 재사용 가능하다. 



~~IE에서는 지원하지 않아서 사용하는 경우가 적었었는데, 이제 사라져버린 IE 덕에 자유롭게 사용해도 된다고 한다.~~ 

(사실 리액트 쓰는 플젝에서는 문자열 변수로 처리하면 되니 거의 쓸일이 없을 것 같다..)