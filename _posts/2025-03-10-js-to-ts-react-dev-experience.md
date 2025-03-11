---
title: "JS에서 TS로! React 개발 경험 변화기"
date: 2025-03-10
categories: [React, TypeScript, 학습]
tags: [React, TypeScript, 학습]
---

JavaScript로 React를 개발하다가 TypeScript로 전환하면서 학습한 내용을 정리한다.

## 🧐 왜 TypeScript로 전환했을까?
타입스크립트는 자바스크립트의 상위 집합 언어로, 자바스크립트에 **정적 타입 시스템**을 추가한 언어

- **자바스크립트의 한계**:  
  자바스크립트는 **동적 타입 언어**로, 변수나 함수의 타입이 런타임(코드가 실행되는 시점)에 결정된다.

  예를 들어, 아래와 같은 코드를 생각해보자.

  ```javascript
  function add(a, b) {
    return a + b;
  }
  console.log(add(5, "10")); // "510"
  ```
  타입 변환 규칙에 따른 암시적 형변환이 일어나 자바스크립트로 작성된 위 코드의 경우 의도하지 않은 결과("510")을 리턴한다.

  이런 타입 오류는 디버깅을 어렵게 하고, 코드의 안정성을 해치게 된다.

- **타입스크립트의 강점**:  
  타입스크립트는 변수와 함수의 타입을 코드 작성 단계에서 명시하여 컴파일시 이를 고정한다.

  위 add 함수를 타입스크립트로 바꾸면,
  ```typescript
  function add(a: number, b: number): number {
    return a + b;
  }
  console.log(add(5, "10")); // 컴파일 오류: Argument of type 'string' is not assignable to parameter of type 'number'
  ```
  a와 b를 `number` 타입으로 정의했기 때문에, 문자열 "10"을 전달할 때 컴파일러가 즉시 오류를 잡아준다.

  즉, 코드를 실행하기 전 문제를 확인할 수 있어, **안정성**을 향상시킨다.

  또한, 타입 정의는 코드의 의도를 명확히 드러내, 다른 개발자가 코드를 읽을 때 변수나 함수의 사용 목적을 쉽게 파악할 수 있게 하여 **유지보수성**을 높일 수 있다.

## 🔍 타입스크립트 동작 원리
타입스크립트는 컴파일 과정에서 문법, 타입 체크 등을 수행해준다.

- **컴파일 과정**:
1. `tsc` 명령어로 컴파일러를 실행해 컴파일을 시작, `tsconfig.json`에서 설정을 참고한다.
2. 파일 로드 → 코드 분석(AST 생성) → 심볼 테이블 생성 → 자바스크립트로 변환 → 타입 검사 순으로 진행
3. 오류가 없으면 자바스크립트 파일이 생성되며, 런타임에는 100% **자바스크립트로 동작**

## 🛠️ TypeScript로 React 프로젝트 만들기
- **설치**:  
1. Vite로 프로젝트를 생성
   
    ```bash
    npm create vite@latest my-react-ts-app -- --template react-ts
    ```

2. 종속성 설치

    ```bash
    cd my-react-ts-app
    npm install
    ```

3. Vite 개발 서버 실행

    ```bash
    npm run dev
    ```

## ✨ 주요 타입 예시
- `boolean`: `let isDone: boolean = false;`

- `number`: `let integer: number = 6;`

- `string`: `let red: string = "Red"; let myColor: string = "My color is ${red}";`  

- `Array`: `let fruits: string[] = ["Apple", "Banana"];` 또는 `string[]` 대신 `Array<string>`  

- `interface`:  
  ```tsx
  interface IUser { name: string; age: number; isValid: boolean; }
  let user: IUser = { name: "Neo", age: 10, isValid: true };

- `type`:  
  ```tsx
  type User = { name: string; age: number; isValid: boolean; }
  let user: User = { name: "Neo", age: 10, isValid: true };

- `any`(모든 타입 허용): `let any: any = 123; any = "str";`

- `void`: `function log(msg: string): void { console.log(msg); }`

- `union`(OR 연산자): `let union: string | number = "Hello"; union = 777;`

- `intersection`(AND 연산자): 
  ```tsx
  interface User { name: string; age: number; }
  interface Validation { isValid: boolean; }
  const test: User & Validation = { name: "jisu", age: 30, isValid: true };
  ```

- `readonly`(수정 불가): `let arr: readonly number[] = [1, 2, 3];`
  
  타입 앞에 or 프로퍼티 이름 앞에 명시

- `tuple`(고정된 길이와 타입): `let tuple: [string, number] = ["a", 1];`

- `enum`:
  ```tsx
  enum Week { Sun, Mon, Tue }
  let day: Week = Week.Mon;
  ```

- `unknown`(타입 확인 전 사용 불가): `let u: unknown = 123;`

- `never`(절대 반환 안 함): `function error(msg: string): never { throw new Error(msg); }`

- **Type vs Interface**:
  - `interface`는 **확장 가능**(한번 정의된 이후에도 추가 정의 가능)
  - `type`은 복잡한 타입 표현에 유리: `type Union = string | number;`

  ※ **구조적 타입**: 타입스크립트는 구조가 같으면 같은 타입으로 간주!!

## ✨ 타입 어노테이션과 추론
- **타입 어노테이션**: 개발자가 직접 타입을 명시 => 코드 의도 명확히 전달

- **타입 추론**: 타입스크립트가 문맥으로 타입을 자동으로 판단

- **언제 어노테이션이 필요할까?**
1. `any` 타입을 리턴할 경우, 이 값을 명확히 해야 할 때
2. 선언되어진 후 초기화 과정에서
3. 추론 불가한 경우: `let result = false; result = 10;` -> `boolean | number 명시`

## ✨ 제네릭
**제네릭**은 타입을 **파라미터처럼 사용**해 코드의 유연성과 재사용성을 높이는 방법이다.

```typescript
function printAnything<T>(arr: T[]): void {
    for (let i = 0; i < arr.length; i++)
    console.log(arr[i]);
}
printAnything(["a", "b"]); // T → string
printAnything([1, 2]); // T → number
```

※ `useState`에서도 제네릭으로 타입 지정 가능

```tsx
const [counter, setCounter] = useState<number>(1); // 어노테이션
const [counter, setCounter] = useState(1); // 추론
```

## ⚙️ 유틸리티 타입
기존 타입을 기반으로 계산을 수행해주는 타입

- `Pick<T, K>`: 특정 프로퍼티 선택

```typescript
interface Todo { title: string; completed: boolean; description: string; }
type TodoPreview = Pick<Todo, "title" | "completed">; // { title: string; completed: boolean }
```

- `Omit<T, K>`: 특정 프로퍼티 제외

```typescript
type TodoNoDesc = Omit<Todo, "description">; // { title: string; completed: boolean }
```

- `Exclude<T, U>`: `T`에서 `U`에 할당할 수 있는 모든 속성을 제외한 타입 구성

```typescript
type T0 = Exclude<string | number | (() => void), Function>;  // string | number
```

- `Partial<T>`: 모든 프로퍼티를 선택적으로 만드는 타입 구성

```typescript
interface Todo {
    title: string;
    description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
    return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
    title: 'organize desk',
    description: 'clear clutter',
};

const todo2 = updateTodo(todo1, {
    description: 'throw out trash',
});

console.log(todo1) //{ title: 'organize desk', description: 'clear clutter' }
console.log(todo2) //{ title: 'organize desk', description: 'throw out trash
```

- `Readonly<T>`: 읽기 전용으로 타입 구성

```typescript
type ReadonlyTodo = Readonly<Todo>; // { readonly title: string; ... }
```

- `Record<K, T>`: 키와 값 매핑

```typescript
type Page = "home" | "about";
type Pages = Record<Page, { title: string }>;
```

- `Extract<T, U>`: `T`에서 `U`에 할당할 수 있는 모든 속성을 추출하여 타입 구성(Exclude의 역)

```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">;  // "a"
type T1 = Extract<string | number | (() => void), Function>;  // () => void
```

- `ReturnType<T>`: 함수 반환 타입 추출

```typescript
function getUser() { return { name: "Alice" }; }
type User = ReturnType<typeof getUser>; // { name: string }
```

- `Parameters<T>`: 함수 매개변수를 `튜플` 형태로 반환

```typescript
function log(msg: string, id: number) {}
type Params = Parameters<typeof log>; // [string, number]
```

- `Awaited<T>`: `Promise` 결과 타입 추출

```typescript
async function fetchData() { return "data"; }
type Data = Awaited<ReturnType<typeof fetchData>>; // string
```
  
- **응용**

```typescript
type Todo = { id: number; title: string; completed: boolean };
type TodoId = Pick<Todo, "id">; // { id: number }
type CreateTodo = Omit<Todo, "id">; // { title: string; completed: boolean }
type EditTodo = Partial<Todo> & Pick<Todo, "id">; // { id: number; title?: string; completed?: boolean }
```

## 🚀 강력한 타입스크립트 기법

- **조건부 타입**: `extends` 키워드를 사용하여 조건에 따라 타입 결정

```typescript
function process<T>(value: T): T extends string ? string[] : T[] {
  return (typeof value === 'string' ? value.split('') : [value]) as any;
}

const result1 = process("hello");  // ["h", "e", "l", "l", "o"]
const result2 = process(123);      // [123]
```

- **제네릭 인터페이스**:

```typescript
interface GenericIdentityFn<T> {
  (arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

- **제네릭 클래스**:

```typescript
class Stack<T> { 
    private items: T[] = [];
    push(item: T) { this.items.push(item); }
}
```

- **모듈과 네임스페이스**:

    **모듈**은 코드의 조직화를 도와 **재사용성**을 높이고,
**네임스페이스**는 코드의 논리적 그룹화를 통해 **이름 충돌을 방지**한다.

1. **모듈**

    `export`, `import` 키워드를 사용하여 정의, 코드를 **파일 단위로 분리**하고 관리

2. **네임스페이스**
   
    `namespace` 키워드를 사용하여 정의, 코드의 **논리적 그룹화**를 통해 관련된 코드를 하나의 단위로 묶는다.

3. **모듈 보강**
   
    `declare module` 키워드를 사용해 **기존 모듈을 확장하여 새로운 기능 추가**

- **타입 심화**

1. **글로벌 타입**
   
   `declare` 키워드 사용 -> `window`, `global` 등의 전역 객체를 통해 접근

2. **타입 좁히기**: 변수의 타입을 보다 구체적인 타입으로 좁히는 과정
   
   `Type Guards`를 통해 구현

   1)`typeof` 방식

   2)`instanceof` 방식

   3)`in` 방식

## 🌟 React 18 버전 사용해보자!
크게 3가지로 나눠서 정리하였다.

- **새롭게 추가된 훅**

1. **useId**: **고유 ID** 생성 -> 서버/클라이언트 일관성 보장

2. **useTransition**: UI 차단 없이 상태를 업데이트(백그라운드에서 **급하지 않은 작업으로 처리**하게 하여 기존 UI 작업이 끊기지 않게 한다.)

3. **useDeferredValue**: 렌더링 지연 수행, 디바운스와 차이는 **고정된 지연 시간 없이 첫번째 렌더링이 완료된 후** 수행된다는 것

- **자동배치**: **여러 상태 업데이트를 묶어** 렌더링 최적화
    
    ※ `flushSync`: 배치 해제

- **Suspense**: 비동기 로딩 처리 강화

    ex) 비동기 컴포넌트가 로드될 때까지 대체 UI 제공

```tsx
import React, { Suspense } from 'react';

const LazyComponent = React.lazy(() => import('./LazyComponent'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}

export default App;

```

  - Next.js에서도 사용 가능해짐
  - 컴포넌트가 화면에 실제로 노출될 때 effect 실행
  - 컴포넌트 자체에서 Suspense에 의해 보여지고 있는지 알 수 있게 됨
  - 쓰로틀링 추가