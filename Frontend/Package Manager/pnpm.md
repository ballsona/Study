# Pnpm

**pnpm(performant npm)**은 Zoltan Kochan이 2017년에 내놓은 패키지 매니저이다.

기존 패키지 매니저에 비해 종속성 관리, 디스크 효율성, 속도 등의 관점에서 휼륭한 성능을 보이고 있는 [pnpm][https://pnpm.io/] 에 대해 알아보자.

## 1. 소개 및 특징

- 기존의 npm 와 yarn classic은 호이스팅을 통해 모듈들의 종속 관계들을 플랫하게 만들어 패키지들을 관리하나, 이런 방식은 유령 의존성이나 플랫 알고리즘의 복잡성 등 여러 어려움이 존재했다.

- 이에 대안책으로 pnpm 은 **중첩된 `node_modules` 구조**를 채택했고, 의존성을 더욱 효과적으로 저장하기 위해 '내용 주소화 저장소(Content-addressable storage)' 방식을 사용한다.

  ![image](https://user-images.githubusercontent.com/67703882/218006980-0d3f16a2-f6c7-4680-a140-7668e7890f15.png)

  - 홈 폴더 (`~/.pnpm-store/`) 의 전역 저장소에 패키지를 저장한다. 이때 의존성 패키지들의 모든 버전은 해당 폴더에 딱 한번만 복사본으로 설치가 된다.

  - `node_modules` 레이아웃은 심볼릭 링크를 사용하여 의존성의 중첩 구조를 생성한다. 여기서 폴더 내의 모든 패키지들의 파일들은 저장소에 대한 하드 링크가 된다.

    [symlink된 node_modules 구조][https://pnpm.io/ko/symlinked-node-modules-structure]

> **심볼릭 링크(Symbolic Link)** : 원본 파일의 이름을 가리키는 링크로, 원본 파일이 사라지게 되면 링크 파일은 역할을 수행할 수 없다. (윈도우의 '바로가기'와 비슷한 개념)
>
> **하드링크(Hard Link)** : 심볼릭 링크와 달리, 하드링크는 원본 파일과 동일한 inode를 가지게 되므로, 원본 파일이 삭제되어도 링크된 파일은 여전히 사용 가능하다.

- pnpm 이용하여 'gdsc-pack' 라는 패키지를 설치했다고 가정했을 때 폴더 구조를 살펴보자.

  ```
   .
   ├── node_modules/
   │   └── .pnpm/
   │   └── .modules.yaml/
   │   └── gdsc-lib
   ├── .npmrc
   ├── package.json
   └── pnpm-lock.yml
  ```

  npm이나 yarn을 사용하여 설치하게 되면 gdsc-pack 패키지와 이 패키지와 의존 관계에 있는 패키지들이 모두 `node_modules` 에 설치되어 flat하게 관리되지만, pnpm을 이용해 설치하면 `node_modules` 에는 gdsc-lib 패키지만 설치되는 것을 볼 수 있다.

​ 이와 의존 관계에 있는 패키지들은 `node_modules/.pnpm` 에 flat한 구조로 존재하게 된다.

- 자체 버전의 lock 파일인 `pnpm-lock.yml` 을 생성한다. 선택적으로 `.npmrc` 파일을 사용하여 추가 설정도 할 수 있다.
- npm에 대한 드롭 인 대체(Drop-in replacement) 이므로 npm 프로젝트에서는 바로 pnpm을 사용할 수 있다.

> **드롭 인 대체(Drop-in replacement)** : 어떤 부품이나 프로그램을 대체했을 때 별다른 설정 등을 수정하지 않아도 되고 속도, 안정성, 용량 등의 성능이 올라가는 대체를 의미한다.

## 2. 성능

- 글로벌 저장소에 패키지를 물리적으로 한번만 저장하므로 디스크 공간을 절약할 수 있다.
- `node_modules` 에 유령 종속성이 없도록 깔끔하게 유지한다.

- 모듈 설치 및 실행 속도가 매우 빠르다. 다른 패키지 매니저들과 비교했을 때에도 설치 속도가 빠른 편임을 확인할 수 있다.

  <img src="https://user-images.githubusercontent.com/67703882/218030605-52bbb5bc-84f9-448f-b921-b7128feec12e.png" alt="image" style="zoom:67%;" />

## 3. 보안

- 코드가 실행되기 전에 체크섬을 이용하여 설치된 모든 패키지의 무결성을 확인한다.

- npm 와 yarn classic 에서 사용하는 패키지 호이스팅을 하지 않기 때문에 이와 관련한 유령 의존성 등 보안 이슈를 피할 수 있다.

- 대신 불법 의존성 접근의 위험을 제거하는 중첩된 `node_modules` 폴더를 생성한다.

  즉, 의존성이 `package.json`에 명시적으로 선언된 경우에만 다른 의존성에 액세스할 수 있도록 한다.

## 3. 사용

- pnpm 설치

  ```
  npm i -g pnpm
  ```

- `pnpm-lock.yaml` 생성

  ```
  pnpm import
  ```

​ 이후 `package-lock.json` 이나 `yarn.lock` 파일 삭제

- 의존성 설치

  ```
  pnpm i
  ```

## 4. 문제점

pnpm이 하드 링크를 사용하므로서 생기는 호환성 문제들이 발생할 수 있다.

- 운영체제와 파일 시스템에 따라 방식이 다르고, 하드 링크나 심볼릭 링크를 지원하지 않는 파일 시스템도 존재한다.
- 파일 감시툴인 watchman 에서 동작하지 않는다.

<br />

## 참고 자료

https://dev-boku.tistory.com/entry/%EB%B2%88%EC%97%AD-JavaScript-%ED%8C%A8%ED%82%A4%EC%A7%80-%EB%A7%A4%EB%8B%88%EC%A0%80-%EB%B9%84%EA%B5%90-npm-Yarn-%EB%98%90%EB%8A%94-pnpm

https://medium.com/zigbang/%ED%8C%A8%ED%82%A4%EC%A7%80-%EB%A7%A4%EB%8B%88%EC%A0%80-%EA%B7%B8%EA%B2%83%EC%9D%B4-%EA%B6%81%EA%B8%88%ED%95%98%EB%8B%A4-5bacc65fb05d
