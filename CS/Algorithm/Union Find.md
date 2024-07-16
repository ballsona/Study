# Union Find

Union-Find에 대해 알아보자.

## Disjoint Set

- 서로 '중복되지 않는 부분 집합'들로 나눠진 원소들에 대한 자료구조 
- 서로소 집합 = 상호 배타적 집합

## Union-Find 

유니온 파인드는 Disjoint Set을 표현하기 위한 알고리즘이다.

- 어떤 두 요소가 주어졌을 때, 그 요소들이 같은 부분 집합에 속하는 지 판별할 수 있다.
- 이를 표현하기 위해 배열, 연결 리스트 등을 사용할 수 있지만 가장 많이 사용되는 것은 '트리' 구조이다.
- 트리 구조를 사용할 경우, '두 노드의 루트 노드가 같다'면 이들은 같은 집합에 속해 있다고 판단한다.
- 유니온 파인드는 `union`, `find` 연산이 사용된다.

### 연산 

- `find(x)`: x노드의 루트 노드를 찾는다.
- `union(x,y)`: x와 y노드의 루트 노드를 찾고, 만약 루트 노드가 다르다면 y를 x의 자손으로 넣어 두 트리를 합친다.

### 구현

```js
// 각 노드의 루트 노드를 자기 자신으로 초기화
const root = new Array(n).fill(0).map((_, i) => i);

function find(x) {
  if (root[x] == x) return x;
  else return find(root[x]);
}

function union(x, y) {
  const px = find(x);
  const py = find(y);
  root[py] = px;
}
```

## 최적화

최악의 경우를 생각해보자. 

```
1 <- 9
2 <- 3 <- 4 <- 5 <- 6 <- 7 <- 8 
```

- 트리가 위와 같이 형성되었다면, 만약 8의 루트 노드를 찾고자 할 때 `find` 함수를 거의 n번 호출하게 된다.
- 이는 배열로 구현하는 것보다 비효율적이다.

### 경로 압축 

어짜피 같은 집합에 속하는지 판별하기 위해선 각 노드의 부모 노드가 아닌 '루트 노드'가 중요한 거니까, `find` 하면서 만난 모든 값의 부모 노드를 root로  만든다.

```
1 <- 9
2 <- 3. 2 <- 4. 2 <- 5 ...
```

이를 구현하면 다음과 같다.

```js
function find(x) {
  if (root[x] == x) return x;
  else return root[x] = find(root[x]);
}
```
