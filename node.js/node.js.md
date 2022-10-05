### 논블로킹 I/O
-  동기와 블로킹이 유사. 비동기와 논블로킹이 유사
- 노드 : 싱글 스레드(CPU * 1), 논블로킹 모델 

### Promise
- 프로미스 내부에서 resolve가 호출되면 then이 실행, reject가 호출되면 catch가 실행
```javascript
const condition = true;
const promise = new Promise((resolve,reject))=>{
    if(condition){
        resolve("성공")
    } else{
        reject("실패")
    }
}
promise.then((message)=>{
    console.log(message)
}).catch((error)=>{
    console.log(error)
})
```
### async/await
```javascript
async fnction findAndSaveUser(Users){
    try{
        let user = await Users.findOne({});
        ...
    } catch(error){
        console.error(error);
    }
}
```
- Promise.all = for await of
### AJAX
- 페이지 이동 없이 서버에 요청을 보내고 응답을 받는 기술
- jQuery, axios.. FE 라이브러리
### 노드 기능
> 노드 내장 객체 
- global
- console
- timer
- __filename, __dirname
- module, exports
- process
    - 노드 프로세스에 대한 정보, 운영체제나 실행 환경별로 다른 동작을 하고 싶을 때
    - process.env : 서비스의 중요한 키를 저장하는 공간 (const secretId = process.env.SECRET_ID)
    - process.nextTick(콜백) : 이벤트루프가 nextTick의 콜백을 우선으로 처리(setImmediate, setTimeout보다 더 먼저실행), 마이크로태스크
    - process.exit() : 노드 프로세스 종료         
> 노드 내장 모듈
- os 
    - 운영체제의 정보(약간 process 객체랑 비슷)
- path 
- url 
    - WHATWG 방식과 기존 노드 방식 2가지 
    - WHATWG 방식으로는 처리할 수 없는 url이 존재. (주소가 host 부분 없이 pathname만 오는 경우)(?)
    - WHATWG => searchParams 객체 
    - 기존 노드는 query -> querystring 모듈 사용 
- crypto 
    - 단방향 암호화 : 복호화 하지않음. 해시기법. pbkdf2 알고리즘..
    ```js
    const crypto = require('cyrpto');
    console.log(crypto.createHash('sha512').update('비밀번호').digest('base64'))
    console.log(crypto.createHash('sha512').update('비밀번호').digest('hex'))
    // createHash(알고리즘) md5,sha1,sha256,sha512,,
    // update(반환할 문자열)
    // digest(인코딩) base64,hex,latin1 
    ```
    - 양방향 암호화 
    ```js
    const crypto = require('cyrpto');
    const cipher = crypto.createCipher('aes-256-cbc','열쇠');
    let result = cipher.update('암호화할 문장','utf8','base64');
    result += cipher.final('base64');
    ```
- util
> 파일 시스템 접근
- fs : 동기, 비동기 메서드 차이O
- Buffer546
### 쿠키와 세션
### REST API
- get
- push
- delete
- put
### express
- 