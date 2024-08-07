# Git Hook and Husky

Git 내에서 어떤 이벤트가 발생했을 때 자동으로 특정 스크립트를 실행하도록 하는 기능을 hook 이라고 한다.

이러한 훅을 로컬 훅(클라이언트 훅)과 리모트 훅(서버 훅)으로 나눌 수 있는데, 로컬 훅은 commit 이나 merge 할 때 실행되고, 리모트 훅은 push 할 때 서버에서 실행된다.

<img src="https://user-images.githubusercontent.com/67703882/213880119-c34e9450-f2ce-47bf-a364-034dfc2d15ab.png" alt="image" style="zoom:67%;" />

## Local Hooks (Client-side hooks)

- pre-commit
  - 실행 시점: 커밋 시 가장 먼저 호출되는 훅으로 커밋 메세지 작성하기 전
  - 용도
    - 커밋하는 Snapshot 점검
    - 커밋할 때 꼭 확인해야할 것이 있는 경우
    - `lint` 같은 프로그램으로 코드 스타일 검사하거나, 공백 문서 검사하거나 등
  - `git commit -no-verify` 로 훅 일시적으로 생략 가능
- prepare-commit-msg

  - 실행 시점: 커밋 메세지를 생성하고 편집기를 생성하기 전
  - 용도
    - 커밋 메세지를 수정하기 전에 먼저 프로그램으로 손보고 싶을 때
    - 커밋 메세지를 자동으로 생성하는 커밋인 경우 커밋 메세지에 템플릿 적용 가능하다.
    - Merge, Squash, Amend 커밋

- commit-msg
  - 실행 시점: 최종적으로 커밋이 완료되기 전
  - 용도
    - 프로젝트 상태나 커밋 메세지 검증
- post-commit
  - 실행 시점: 커밋 완료 시
  - 용도
    - 커밋된 것을 누군가 혹은 다른 프로그램에게 알릴 때

### 기타 훅

- pre-base
  - 실행 시점: rebase 하기 전
  - 이 훅이 0이 아닌 값을 반환하면 rebaser가 취소된다.
  - 용도
    - 이미 푸쉬한 커밋을 rebase 하지 못하게 할 수 있다.
- post-rewrite
  - 실행 시점: 커밋을 변경하는 명령을 실행했을 때 실행
    - `git commit --amend` or `git rebase`
  - 용도
    - 디렉토리에서 뭔가 할 일이 있을 때
    - 용량이 크거나 git이 관리하지 않는 파일을 옮기거나, 문서를 자동으로 생성하는 경우에 사용
- post-merge
  - 실행 시점: merge가 끝나고 나서 실행
  - 용도
    - 파일 권한 같이 git이 추적하지 않는 정보를 관리하는 데 사용
    - Merge로 Working Tree가 변경될 때 Git이 관리하지 않는 파일이 원하는 대로 잘 배치됐는지 검사할 때
- pre-push
  - 실행 시점: `git push` 명령을 실행했을 때. 즉 리모트 정보를 업데이트 하고 난 후 리모트로 데이터를 전송하기 전에 동작
  - 용도
    - 푸쉬하기 전에 커밋이 유효한지 확인할 때

## Remote Hooks (Server-side hooks)

서버 훅은 모두 Push 전후에 실행된다. Push 전에 실행되는 훅이 0이 아닌 값을 반환하면 해당 Push는 거절되고 클라이언트는 에러 메세지를 출력한다.

- pre-receive
  - 실행 시점: push 하면 가장 처음 실행되는 훅
  - 용도
    - 브랜치 푸쉬 권한을 제어
    - Fast-forward push 아니면 거절 가능
    - 관리자만 브랜치를 새로 Push 하고 삭제할 수 있고 일반 개발자는 수정사항만 Push 할 수 있게 가능
- update
  - 실행 시점: 각 브랜치마다 한번씩 실행다는 것을 제외하면 `pre-receive` 와 거의 같다.
  - 여러 개의 브랜치를 한번에 푸쉬하면 `pre-receive` 는 한번만 실행되지만, `update`는 브랜치마다 실행된다.
- Post-receive
  - 실행 시점: Push한 이후에 실행
  - 용도
    - 사용자나 서비스에 알림 서비스를 보낼 수 있다.
    - 메일링 리스트에 메일을 보내거나, CI 서버나 ticket-tracking 시스템 정보를 수정할 수 있다.
    - 커밋 메세지 파싱도 가능
  - 해당 스크립트가 완전히 종료될때까지 클라이언트와 연결이 유지되고 푸쉬를 중단하지 않기 때문에, 시간이 오래걸리는 스크립트인 경우 조심해야한다.
