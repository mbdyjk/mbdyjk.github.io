---
title: "웹개발 수업 복습 - 스파르타 챌린지반 학습자료(2)"
date: 2025-07-15
categories: [React]
tags: [React]
---

이전 포스트(1편): https://mbdyjk.github.io/posts/web-development-sparta-challenge-1/

## 1. 함수형 컴포넌트
#### 클래스형 컴포넌트 특징
- `React.Component`를 상속받음
- `render` 메서드 필수 구현
- `constructor`에서 초기 state 설정, `this.setState`로 비동기적 상태 업데이트
#### 함수형 컴포넌트 특징
- 가독성 좋고 문법이 간결
- `hook` 사용
- 메모리 사용량이 클래스보다 적고 최적화에 용이

## 2. Hook
: 다양한 React 기능을 사용할 수 있게 하는 함수
#### State Hook
: 메모리에 값 저장
- `useState`: state 변수 선언하여 값 직접 업데이트
- `useReducer`: reducer 함수 내부 업데이트 로직을 사용하여 state 변수 선언
#### Effect Hook
: 컴포넌트를 외부 시스템에 연결하고 동기화
  * 외부 시스템?
  : 네트워크, DOM, 애니메이션, 위젯, 외부 변수 등
#### Custom Hook
: 컴포넌트의 로직을 분리하도록 사용자가 생성한 훅, 같은 훅을 공유하더라도 컴포넌트끼리 state 공유되는건 아님

## 3. Reducer
참고문서: https://ko.react.dev/learn/extracting-state-logic-into-a-reducer

문서의 예시처럼

C(Create): setTasks, 스프레드 연산자 활용
U(Update): setTasks, map 메서드로 동일 id인 항목을 새 항목으로 덮어씀
D(Delete): setTasks, filter 메서드로 동일하지 않은 id 항목들만 필터링

하는 이벤트 핸들러에서 상태를 업데이트하는 setTasks 로직이 흩어져 있고, 함수 변경이 필요할 시 관련 핸들러를 찾는 번거로움 존재

`Reducer`를 사용하면 컴포넌트는 액션을 전달하는 역할만 수행하고, 로직을 중앙화하여 단일 함수로 관리에 편리성의 이점을 얻을 수 있음

=> 변경방법

1) 기존 이벤트 핸들러에 state set 함수를 제거하고, `action` 객체를 `dispatch` 함수 안에 넣어 전달

    ```js
    function handleAddTask(text) {
        dispatch({
            type: 'added', // 행위
            id: nextId++,
            text: text,
        });
    }

    function handleChangeTask(task) {
        dispatch({
            type: 'changed',
            task: task
        });
    }

    function handleDeleteTask(taskId) {
        dispatch({
            type: 'deleted',
            id: taskId
        });
    }
    ```

2) reducer 함수 작성
reducer 함수는 state 값과 action 객체를 받아 다음 state 반환, 컴포넌트 외부에서 선언 가능
    * reducer 함수 안에서는 if문 대신 switch 문을 사용하는게 규칙(action의 type에 따라)

3) 컴포넌트에서 useReducer 훅을 사용하기
```js
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

번외) useState와 useReducer 비교
- 코드 크기: useReducer 훅은 reducer 함수와 action 전달 부분 작성이 필요해 조금 더 작성량이 있지만, 이벤트 핸들러에서 중복 로직이 있을 경우 코드 양을 줄이는데 도움
- 가독성: 이벤트 핸들러가 많을 경우 useReducer가 좋고, 간단한 상태 업데이트일 경우 useState가 유리
- 디버깅: useReducer를 사용하면 reducer를 로그에 출력하여 state와 action 분석에 조금은 더 명확한 부분 존재

번외) reducer 잘 작성하는 법
1) Reducer는 순수하게 만들어야 한다!
   
   : state 함수와 마찬가지로 렌더링 중에 실행되므로, 동일 입력/동일 출력이 보장되어야 하고, 함수 실행이 외부 환경에 영향을 미치지 않아야 한다.
   ex) 외부 변수 수정 금지, 네트워크 요청, timeout, dom 조작, 콘솔 출력 등

2) 하나의 사용자 상호작용이 하나의 액션에 대응

    : 의미적 명확성과 디버깅 용이성을 위해

번외) Immer 라이브러리

객체와 배열 변경하는 스타일로 reducer 작성시 Immer 라이브러리가 유용

## 4. Effect가 필요하지 않은 경우
참고문서: https://ko.react.dev/learn/you-might-not-need-an-effect

필요한 경우: 외부 시스템과 연결되어 동작해야 할 때

아닌 경우:
1) 데이터 변환(예: 리스트 필터링)을 위해 useEffect 사용하면 비효율적
   
   why? React의 렌더링 프로세스는
   
   1) 렌더링 단계: 컴포넌트 함수를 호출해 UI 내용/레이아웃 계산, 이전 가상 DOM과 비교해 변경 필요한 부분 계산
   2) 커밋 단계: 계산된 변경 사항을 DOM에 반영
   3) Effect 실행: useEffect가 렌더링 후 실행

    즉, useEffect에서 상태를 업데이트 하면, 다시 렌더링이 일어나 성능 저하 유발, 데이터 변환은 컴포넌트 최상위 레벨에서 수행하는 것이 적합

    만약 필터링이 복잡하여 성능이 걱정된다면, useMemo를 사용해 계산을 메모이제이션 할 수 있다.
    => 계산이 비싼지 확인은 해당 계산 앞뒤로 console.time, console.timeEnd를 추가하여 시간 확인, 예시로 1ms 이상의 상당한 시간 소요가 걸리면 메모이제이션
    => 성능 테스트시 pc 성능을 의도적으로 낮추는 CPU 스로틀링 사용 가능
    참고링크: https://developer.chrome.com/blog/new-in-devtools-61?hl=ko#throttling
    => 개발 중 성능 측정은 strict mode 영향 등 정확하지 않으므로 프로덕션 용으로 빌드하고 확인 필요

2) 사용자 이벤트 처리를 위해 useEffect 사용하면 비직관적

    렌더링이 끝난 후 useEffect가 호출되므로, 다음 렌더링에 바뀐 상태값이 적용되어 보여 불필요한 지연 발생, 처리의 명확성이 부족해진다.

3) prop 변경시 state를 초기화하려고 할 때 useEffect 사용하면 비효율적

    => 컴포넌트를 분할하여 내부 컴포넌트로 key 속성을 prop을 전달해 react가 prop이 바뀌면 서로 다른 컴포넌트로 취급하도록 요청

4) prop 변경시 일부 state 조정할 때
   
   예시로 리스트 항목들이 변경되었을 때 선택된 항목을 초기화해야 할 경우, 이전 리스트 항목들에 관한 상태를 추가
   일반적으로 렌더링 중 계산하거나 상태 추가로 해결

5) 이벤트 핸들러 간 공통 로직 공유할 때
   
   **컴포넌트가 사용자에게 표시되었기 때문에 실행되어야 하는 코드에만 Effect를 사용**하기, 공통 로직을 useEffect에 넣으면, 새로고침시 불필요한 리렌더링 발생 가능

6) 의존성 배열 값에 따른 연쇄 계산을 useEffect 문에 나눠 작성한 경우
   
   매 체인마다 렌더링 발생하는 비효율성 및 코드의 결합성이 높아짐, **렌더링 중에 가능한 계산은 최상위에서 진행**하고, 이벤트 핸들러에서 state를 set하는 방식이 적합

7) 앱 로드시 한번만 실행되어야 하는 코드
   
   개발 모드에서 두 번 실행되는 것에 대한 일관된 처리를 위해 
   - App 컴포넌트 상단에 트리거 변수 추가하여 실행 여부 추적
   - typeof window !== 'undefined' 로 브라우저 실행 환경인지 확인 가능

8) state 변경을 부모 컴포넌트에 알리는 경우
   
   예시로 토글 컴포넌트에서 토글 상태를 내부에서 갖고, 토글 이벤트를 부모에 노출시킨다고 할 때, useEffect의 의존성 배열로 토글 상태와 토글 이벤트 핸들러를 넣게 되면 한번의 렌더링 패스로 처리되지 않는 문제 발생 => 이벤트 핸들러에서 처리 or 두 컴포넌트의 state가 함께 변경되기를 원할 때 **상태 끌어올리기**로 부모 컴포넌트의 상태를 props로 받아 사용

9) 외부 저장소 구독시
    
    ex) 외부 라이브러리나 브라우저 api 사용시 react가 모르는 사이 데이터가 변경될 수 있으므로, 컴포넌트를 수동으로 구독해야 함

    이 떄, `useSyncExternalStore` 훅을 사용하여 외부 스토어를 구독하는 편이 에러를 줄이는데 좋음

번외) useEffect에서 의존성 배열이 비어 있는 경우, 처음 마운트될 떄 한번만 실행, 언마운트될 때 cleanup 함수 실행

## 5. Effect를 사용하는 경우
참고자료: https://ko.react.dev/learn/synchronizing-with-effects

`useEffect`는 렌더링 후 커밋 단계에서 실행되어, 상태나 props 변경에 따라 외부 시스템과 컴포넌트를 일치시키는 역할을 한다.
- 데이터 가져오기
  
  : 서버로부터 데이터를 가져와 상태 업데이트 ex) api 호출
- 이벤트 구독

  : 브라우저 이벤트나 WebSocket 구독 설정
- 외부 라이브러리와 동기화
  
  ex) 설정 객체 변경 시 초기화

#### 주요 원칙
- 렌더링 중(컴포넌트 최상위)에서 사이드 이펙트를 실행하면 안됨(다중 호출 위험 떄문에)
- Cleanup 함수: return () => {...}로 언마운트시 호출시킬 함수 정리
- 의존성 배열: 모든 관련값을 포함하여 불필요한 재실행 방지
- 렌더링과 동기화 분리: 데이터 변환은 렌더링 중 최상위 레벨에서 처리, 외부 시스템과의 동기화는 `useEffect`에서 처리
- 최적화: 복잡한 계싼은 `useMemo`나 `useCallback`으로 최적화