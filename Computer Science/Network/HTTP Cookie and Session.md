# HTTP Cookie & Session

## Cookie와 Session 을 사용하는 이유

쿠키와 세션을 왜 사용하는지 알기 위해서는 HTTP 프로토콜의 특징을 알아야한다. HTTP 프로토콜은 connectionless 하고 stateless 한 특징을 가진다. 

- connectionless : 클라이언트가 요청을 보내고 서버가 응답을 주면 HTTP 연결을 끊어버리는 것
- stateless : 연결이 끊기는 순간 서버와 클라이언트의 통신은 끝나며 상태 정보를 유지하지 않는 것 

이러한 특징으로 인해 서버 리소스 낭비가 줄어들어 더 많은 요청을 처리할 수 있는 성능을 확보한다는 장점도 있지만, 클라이언트 측면에서 상태 유지를 위해 통신할때마다 특정 절차를 거쳐야한다는 단점이 존재한다. 이를 보완하기 위한 방법인 쿠키와 세션을 알아보자. 

## Cookie 

<img src="https://user-images.githubusercontent.com/67703882/201508437-7de7fd8e-e08c-41d6-bbb0-b1db5b513cdd.png" alt="image" style="zoom:67%;" />

브라우저에서 어떤 사이트를 방문할 경우 그 사이트가 사용하고 있는 서버를 통해 클라이언트 로컬에 설치(저장)되는 작은 데이터 파일을 말한다. 

쿠키는 **key-value** 로 이루어진 데이터이다. 쿠키가 생성되고 송수신되는 과정을 살펴보자. 

1. 클라이언트가 서버에 HTTP 요청을 보낸다.

   ```http
   GET /index.html HTTP/1.1
   Host: www.test.com
   ```

2. `www.test.com` 의 서버는  `Set-Cookie` HTTP Response Header 에 브라우저가 수신할 쿠키 정보를 담은 후 응답한다. 

   ```http
   HTTP/1.1 200 OK
   Content-Type: text/html
   Set-Cookie : name=sona
   Set-Cookie : age=24
   ```

3. 브라우저는 서버로부터 쿠키를 응답 받은 브라우저는 해당 쿠키를 하드디스크에 저장해놓는다. 

4. 이후 `www.test.com` 에 요청을 할때마다 서버로부터 받았던 쿠키를 `Cookie` 헤더에 담아서 그대로 돌려 보낸다. 

   ```http
   GET /index.html HTTP/1.1
   Host: www.test.com
   Cookie: name=sona; age=24
   ```

   이때 브라우저는 `www.test.com` 에 머무는 한 /index.html 를 요청하든 /user.html 를 요청하든 매번 같은 쿠키를 서버에 돌려 보낸다. 

5. 서버는 브라우저로부터 받은 쿠키를 읽어 여러 용도로 데이터를 활용한다. 

#### 유효기간 

서버가 `Set-Cookie` 헤더를 통해 브라우저로 쿠키를 보내는 것은 일회성 작업이지만, 브라우저가 `Cookie` 헤더를 통해 서버로 쿠키를 돌려 보내는 것은 일정 시간동안 반복해서 수행되는 작업이다. 

이때 브라우저가 쿠키를 얼마나 오래동안 돌려 보내야하는지, 즉 **쿠키의 유효기간**을 서버가 맨 처음에 쿠키를 생성하여 보낼때 설정할 수 있다.

- 세션 쿠키(session cookie) : 유효기간을 지정하지 않은 쿠키. 브라우저 닫으면 쿠키 삭제. 
- 영속 쿠키(persistent cookie) : 특정 만료시점이나 유효 기간을 지정한 쿠키. 브라우저 닫아도 유효기간이 남아있다면 쿠키가 삭제되지 않는다. 

```
Set-Cookie: <key>=<value>; Expires=<종료시점>
Set-Cookie: <key>=<value>; Max-Age=<유효기간> // 초단위
```

Expires 속성에는 쿠키 유효기간 종료 시점을 지정해주고, Max-Age 속성에는 유효 기간을 지정해준다. 만약 두 속성 모두 지정되어 있다면 Max-Age 속성값이 우선이 된다. 

#### 적용 범위

`Set-Cookie` 의 Domain 속성을 통해 어떤 url 을 방문할때 해당 쿠키를 보내야할지 말지를 더 세밀하게 제어할 수 있다.

```
Set-Cookie: <key>=<value>; Domain=<도메인>
```

Domain 속성값으로 `test.com` 을 지정하면 브라우저는 `a.test.com` 으로부터 받은 쿠키를 `b.test.com` 으로도 보내게 된다. 즉 `test.com` 의 서브 도메인들이 쿠키를 공유하는 효과를 발생. 

```
Set-Cookie: <key>=<value>; Path=<경로>
```

Path 속성을 명시하면 해당 도메인의 특정 경로로만 쿠키의 범위를 축소시킬 수 있다. 예를 들어 Path 속성값으로 `/users` 를 설정하면 브라우저는  `/users` 를 포함한 하위 경로로 요청할 때만 쿠키를 돌려 보낸다. 

#### 쿠키의 활용

쿠키는 클라이언트의 하드 디스크에 저장되기 때문에 보안성이 낮다. 따라서 개인 정보 등 보안에 민감한 데이터들은 사용되지 않고, 주로 아이디(비밀번호 X) 자동 저장 기능, 스크롤링 위치 저장, 오늘 팝업 보지 않기 등에 사용이 된다. 



## Session

<img src="https://user-images.githubusercontent.com/67703882/201508448-22e8d059-69b0-4fb3-94f8-3a4853c650ac.png" alt="image" style="zoom:67%;" />

쿠키가 클라이언트의 상태 정보 유지 기술이었다면, 서버의 상태정보 유지 기술으로는 세션이 있다.

세션은 클라이언트 별로 각각의 상태 정보를 서버에 저장하는 기술이다. 데이터 파일을 브라우저에서 관리하는 쿠키와 달리 세션은 서버 측에서 이를 관리한다. 이러한 세션 기술을 통해 사용자의 로그인 상태를 유지하거나 쇼핑몰의 장바구니 기능 등을 제공할 수 있다. 

세션의 동작 과정을 살펴보자. 

1. 클라이언트는 서버에 접속 요청을 한다. 
2. 서버는 클라이언트 별로 고유한 Session ID(SID) 를 생성한 후, 응답 헤더에 `Set-Cookie: session_id=<SID>` 를 추가하여 응답한다.
3. 클라이언트는 세션 쿠키를 저정했다가, 동일한 서버에 요청할때 이 쿠키에 담긴 Session ID 를 서버에 전달한다. 
4. 서버는 전달 받은 Session ID로 세션 저장소에 있는 클라이언트 정보를 가져온다. 
5. 클라이언트 정보를 가지고 서버 요청을 처리하여 클라이언트에 응답한다. 

#### 세션과 쿠키의 차이

- 세션은 결국 쿠키를 기반으로 동작한다. 

- 세션도 유효기간을 설정할 수 있지만, 기본적으로 브라우저가 종료되면 유효기간과 상관 없이 세션이 삭제된다. 

- 세션은 쿠키와 다르게 서버에도 데이터를 저장하기 때문에 보안 면에서 쿠키보다 안전하지만, 사용자가 많아질수록 서버 메모리를 많이 차지하게 된다. 

- 쿠키는 쿠키 자체에 데이터가 있기 때문에 서버 요청시 헤더를 바로 참조하면 되므로 속도 면에서 빠르지만, 세션은 전달 받은 Session ID 를 통해 서버에서 다시 데이터를 참조해야 하므로 비교적 속도가 느리다. 

  

## 참고 자료

https://interconnection.tistory.com/74

https://www.daleseo.com/http-cookies/

https://thecodinglog.github.io/web/2020/08/11/what-is-session.html