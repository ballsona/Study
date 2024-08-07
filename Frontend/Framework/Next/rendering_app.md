# Rendering in App Router

1. '응답'은 무조건 서버 -> 클라이언트로 단방향으로 흐른다. 만약 클라이언트에서 서버에 접근하고 싶다면, 요청을 다시 보내야한다.
2. 서버 컴포넌트를 렌더링하는 것이 '서버 렌더링' 이고, '클라이언트 컴포넌트'를 렌더링하는 것이 '클라이언트 렌더링' 임.

## Server Rendering

서버 렌더링은 서버에서 컴포넌트를 렌더링하고 필요한 경우에 이를 캐싱하는 것.

### 어떻게 서버 컴포넌트가 렌더링되나?

1. 서버에서 RSC Payload 랑 클라이언트 컴포넌트 자바스크립트 구조 이용해서 서버에서 HTML 파일로 렌더링한다.
2. 클라이언트에서는 서버에서 받은 HTML 파일을 즉시 보여준다.
3. 이후 RSC Payload 사용해서 클라이언트 컴포넌트 트리와 서버 컴포넌트 트리를 일치시키고, DOM 업데이트한다.
4. 자바스크립트 코드가 클라이언트 컴포넌트에 hydrate 되고, 애플리케이션이 인터렉티브해진다.

> RSC는 서버 컴포넌트 렌더링 결과, 클라이언트 컴포넌트가 렌더링되어야할 위치와 해당 컴포넌트에 대한 자바스크립트 코드 참조, 서버 컴포넌트에서 클라이언트 컴포넌트로 전달할 props 등에 대한 정보 담고있는 파일임.

### 서버 렌더링을 하는 방법은?

- static rendering, dynamic rendering, streaming 3가지 방식
- 개발자는. 렌더링을 static 하게 할지, dynamic 하게 할지 고르는게 아니라. 특정 데이터를 캐싱하고 업데이트하는 시점만 결정하면. 혹은 ui를 stream 할지 결정하면. 렌더링 방식은 next가 알아서 결정해주는 거임.

### Static Rendering

- default
- 페이지는 빌드 타임 (과 데이터 업데이트 후 백그라운드) 에서 렌더링된다.
- 렌더링 결과는 CDN에 캐싱. 유저와 서버 요청이 캐싱 데이터를 공유
- 페이지 내 데이터가 특정 유저에게 국한되어있지 않고 빌드 시점에 데이터를 모두 알 수 있는 경우 사용 -> 공식문서 등

### Dynamic Rendering

- 매 요청마다 렌더링된다.
- 특정 유저에게 국한되어 있는 페이지거나 요청 시점에 데이터를 알 수 있는 경우 사용 -> 쿠키나 url search params
- 데이터 캐싱 안되어 있으면 무조건 다이나믹 렌더링. 그러나 캐싱된 데이터를 가지고 다이나믹 렌더링 가능
- dynamic function 을 발견하면. 모든 라우트?를 다이나믹 렌더링. 이 함수들은 요청 시점에만 알 수 있는 정보이기 때문임.
  - cookies() and headers()
  - searchParams()

### Streaming

- 서버에서 컴포넌트가 렌더링되는대로 바로바로 클라이언트에 보내는 방식
- `loading.js` 랑 `React Suspense` 사용하면 라우트 조각 스트리밍 가능

## Client Rendering

클라이언트 컴포넌트는 요청 시점에 컴포넌트 렌더링하는 것.

### 어떻게 클라이언트 컴포넌트가 렌더링되나?

요청 종류에 따라 렌더링 방식이 다름.

1. 페이지 전체가 로드 되는 경우

   - 첫 요청이면. 서버와 클라이언트 컴포넌트 모두 서버에서 렌더링되어 HTML 파일이 생성된다.
   - 즉, 유저가 처음으로 애플리케이션에 방문하는거면. 페이지 내 모든 컨텐츠를 보게 되는거지.
   - 이후 RSC Payload 사용해서 클라이언트 컴포넌트 hydrate 해. 하이드레이트는 이벤트 리스너를 돔에 붙이는 과정 의미함

2. subsequent 네비게이션되는 경우
   - 클라이언트 컴포넌트는 완전히 클라이언트에서만 렌더링된다. ??

### 클라이언트 렌더링을 하는 방법은?

- `use client` 를 상단에 넣어
- 클라이언트 컴포넌트와 자식 컴포넌트, 그리고 해당 컴포넌트가 import하는 모듈까지 모두 클라이언트 바운더리로 설정되어 -> 클라이언트 번들로 들어가

## Server vs Client Rendering

- 서버 렌더링 이점
  - 서버에서 데이터를 요청하니까. 요청 속도 빠르고 보안도 좋아.
  - 캐싱
  - 번들 사이즈?
  - FCP
  - SEO
  - Streaming?
- 클라이언트 이점
  - 인터렉티브
  - 브라우저 API

## Server + Client component

https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns
