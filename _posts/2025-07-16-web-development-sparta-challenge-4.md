---
title: "웹개발 수업 복습 - 스파르타 챌린지반 학습자료(4)"
date: 2025-07-16
categories: [React]
tags: [React]
---

## 1. 클로저
개념: 함수와 그 함수가 선언된 렉시컬 환경의 조합, 내부 함수가 외부 함수의 변수와 상태를 기억하고, 외부 함수의 실행이 끝난 후에도 변수가 메모리에 유지되어 그 변수에 접근할 수 있게 해주는 메커니즘을 가짐

- 렉시컬 스코프: 함수가 **선언된 위치**에 따라 접근할 수 있는 변수가 결정

```js
function outer() {
  let count = 0; // 원래라면 접근 못했을법한 곳
  return function inner() {
    count++;
    console.log(`Count: ${count}`);
  }
}

const counter = outer();
count++;

counter(); // Count: 1
counter(); // Count: 2
counter(); // Count: 3
```

1) 외부 함수 `outer` 호출
   outer가 종료되어도 inner가 참조하는 count 변수는 메모리에 유지(클로저)
2) 내부 함수 `inner`를 counter에 저장
   counter는 inner 함수이고, inner는 count 변수를 기억
3) 내부 함수 호출
   내부 함수가 외부 함수의 변수를 참조하면 메모리에 유지

효과
- 데이터 은닉: 외부에서 변수에 직접 접근으로부터 보호
- 상태 유지: 함수 호출 간 상태 유지
- 모듈화

하지만 불필요한 클로저가 많으면 메모리 누수 가능하고, 성능에 영향

예시
- `useState` 훅: set 함수는 변수를 기억하는 클로저 함수
- 이벤트 핸들러

## 2. useState, useEffect 훅

**React는 Hooks를 배열로 관리하고 있다**

- useState
  - **상태 초기화**: hooks 배열의 현재 훅의 인덱스의 값이 undefined면 초기값으로, 있다면 기존값으로 설정
  - **상태 유지**: 상태가 컴포넌트 호출 간 유지되며, 클로저를 통해 hooks 배열이 렌더링 간 사라지지 않음, 즉 setState가 hooks 배열과 hookIndex를 기억하게 함
  - **업데이트**: 함수형 업데이트 지원(이전 상태 기반으로 새로운 상태 계산)

- useEffect
  - **effect 실행**: 의존성 변경 시 callback 실행
  - **클린업**: effect가 재시행 되기 전, 이전 effect의 클린업 함수 실행(예: 타이머 제거), 콜백 실행 후 반환된 클린업 함수를 hooks[currentHook].cleanup에 저장
  - **의존성 관리**: hooks 배열에 의존성 배열과 클린업 함수를 저장해 호출 간 유지

## 3. Flux와 Redux
모던 웹에서 다양한 요구사항들을 적용하면서 웹사이트의 규모가 커지고 관리해야 할 상태가 많아져, 상태 업데이트가 언제 어디서 어떻게 되는지 파악하기가 힘들어짐
=> 상태 관리만을 위한 기술인 Redux 등장

Redux 이전에 등장하고 기반이 된 Flux는 단방향 데이터 흐름 기반 아키텍처이다.

![Flux 아키텍처](/assets/img/2025-07-17-183200.png)
- Action: 애플리케이션에서 발생한 이벤트 객체
- Dispatcher: 발생한 Action을 받아서 다른 요소들에게 전달
- Store: Action의 결과로 변경된 상태 저장
- View: 사용자에게 데이터를 표시하고, 다른 Action을 트리거할 수 있는 UI

이 일련의 과정이 **단방향**으로 이루어진다.
Redux는 Flux 아키텍처를 실제로 구현한 구현체(Reducer + Flux)

![Redux 아키텍처](/assets/img/2025-07-17-185300.png)
- Store의 역할 차이: Flux에서는 Store가 상태와 상태 갱신 로직을 모두 담고 있지만, Redux에서는 Store가 단순히 상태를 보관하며, 갱신 로직은 리듀서에서 처리
- Dispatcher 삭제: 리듀서 함수가 상태 관리하기 때문에 단일 Store 사용
- 불변성 준수

#### Reducer
: 상태 업데이트 로직을 관리하는 함수, 현재 상태와 액션 객체를 입력 받아 새로운 상태 반환

- 3가지 원칙
  - Single Source of Truth(SSOT): 모든 상태는 하나의 Store에 저장
  - State is Read-Only: 상태는 직접 변경할 수 없고, Action을 통해서만 변경 가능
  - Changes are Made with Pure Functions: 상태 변화는 순수 함수인 Reducer를 통해 이루어짐
- Redux의 단점: 사용하기 위해 작성해야 하는 코드가 많다
  - RootReducer 정의: `combineReducers`로 하위 reducer 결합
  - 세부 Reducer 정의
  - store 생성: `createStore`
  - App을 Provider로 감싸 store 추가
  - 컴포넌트에서 Redux 사용: 핸들러 함수에서 `dispatch` 함수로 export한 액션 생성자를 인자로 넣어 호출

## 4. Redux Toolkit(RTK)
Redux의 복잡성을 줄이고 개발 효율성을 높이기 위해 만들어진 패키지, `spread` 문법을 사용하지 않아도 불변성 자동 관리

=> 불필요한 보일러플레이트 코드를 줄이고, 상태 관리 로직을 간결하게 작성할 수 있다.

- RTK 작성방법
  - redux slice 생성: `createSlice`를 사용해 reducer와 action을 한 번에 정의, Immer 라이브러리를 내부적으로 사용
  - store 설정: `configureStore`로 여러 리듀서를 결합하고 여러 미들웨어와 devtools 결합 가능
  - app에 store 적용
  - 컴포넌트에서 redux 적용

## 5. Context vs Redux
Context는 업데이트할 때 Child에 있는 모든 컴포넌트를 리렌더링하고, Redux는 업데이트된 부분만 리렌더링

why?) Context는 Context.Provider는 값을 제공하고 값이 변경되면 이를 구독하는 모든 컴포넌트가 리렌더링, Redux는 전역 스토어로 상태 관리하고, `useSelector`로 특정 상태만 구독하여 변경된 상태에 연관된 컴포넌트만 리렌더링