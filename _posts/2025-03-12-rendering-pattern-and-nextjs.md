---
title: "Next.js 기능을 렌더링 패턴과 함께 알아보자"
date: 2025-03-12
categories: [Next.js, React, TypeScript, 학습]
tags: [Next.js, React, TypeScript, 학습]
---

## 📖 React 서버 컴포넌트와 클라이언트 컴포넌트
Next.js 13버전에서 제시하는 **렌더링 패턴**과 밀접한 연관이 있어 이를 먼저 알아본다.

1. **서버 컴포넌트**
- **정의**: **서버**에서 실행되고 렌더링되는 컴포넌트
- **특징**: `app` 디렉토리에서 기본값으로 적용, 서버(node.js) 환경에서 동작
    
    ex) `console.log`가 터미널에서 출력, `os.hostname`을 통해 현재 컴퓨터의 이름 반환, `alert()`가 동작하지 않고 에러 발생

2. **클라이언트 컴포넌트**
- **정의**: **브라우저**에서 실행되고 렌더링되는 컴포넌트
- **특징**: Next.js에서 react 훅, 이벤트 등 클라이언트에서만 동작하는 기능을 사용하려면 `"use client"` 선언 필요
    
    ex) `console.log`가 브라우저에서 출력, `os.hostname`을 통해 현재 컴퓨터의 이름 반환, `alert()`가 동작하지 않고 에러 발생

=> User와 상호작용이 있는 경우 **클라이언트 컴포넌트**를, 그 외의 경우는 **서버 컴포넌트**를 쓰는 것이 권장된다.

이 때, 기본적으로 **서버 컴포넌트**로 동작하는 Next.js 특성상, 필요한 곳에만 **"use client"**를 이용해 클라이언트 컴포넌트로 만들어야 한다. **컴포넌트 분리**가 중요

## 🎨 렌더링 기법: MPA와 SSR
1. **MPA(Multi-Page Application)**: 전통적인 **서버사이드 렌더링** 방식
- **특징**: 서버에서 페이지별로 개별 **HTML 파일** 제공 ex) `/about` -> `about.html`
- **장점**: 단순한 구조
- **단점**: 페이지 이동, 렌더링 시 깜빡임 현상, 내부 컨텐츠 양에 따라 편차가 심함

2. **SPA(Single-Page Application)**
- **특징**: 브라우저에서 **JavaScript로 동적 렌더링**
- **장점**: 새로고침 없이 웹서비스 이용
- **단점**: 최초 서버로부터 빈 `div`를 불러오기 때문에 JavaScript의 평가 및 실행까지 하얀 화면이 유저에게 노출, `Code Splitting`을 통한 **지연 로딩**으로 번들된 코드를 여러 코드로 나눠 **필요한 코드만 불러오는 방식**을 사용할 수 있지만 완벽한 해결이 되지는 않음

## 🎨 4가지 주요 렌더링 기법
1. **CSR(Client-Side Rendering)**
- **특징**: 순수 React 방식, 브라우저에서 모든 **렌더링** 처리
- **장점**: 최초 로드 이후에는 **상호작용**이 빠르고, 서버 부하가 적음
- **단점**: 초기 로딩이 느릴 수 있음, JavaScript 로드, 실행될 때까지(**Hydration**이 될 때까지) 페이지가 비어 있어 **SEO**에 불리
- **예시**:
  
    ```tsx
    import ReactDOM from "react-dom";
    function App() {
        return <h1>CSR 예제</h1>;
    }
    ReactDOM.render(<App />, document.getElementById("root"));
    ```

2. **SSG(Static Site Generation)**
- **특징**: **빌드** 시 **정적 HTML** 생성, 클라이언트가 홈페이지 요청을 하면, 서버에서는 이미 만들어져있는 사이트 바로 제공
- **장점**: 빠른 **TTV**, **SEO**에 유리, CDN 캐싱 가능
- **단점**: **정적인 데이터**에만 사용할 수 있고, 상호작용이 느림, 서버 부하가 큼
- **예시**:
  
    ```tsx
    export async function getStaticProps() {
    const data = await fetch("https://api.example.com").then(res => res.json());
        return { props: { data } };
    }
    export default function Home({ data }) {
        return <div>{data}</div>;
    }
    ```
   
3. **ISR(Incremental Static Regeneration)**
- **특징**: **SSG** + **주기적 재생성**
- **장점**: 정적인 페이지를 먼저 제공하므로 사용자 경험이 좋고, 최신 상태 업데이트 가능
- **단점**: **실시간 데이터** 반영 불가
- **예시**:
  
    ```tsx
    export async function getStaticProps() {
    const data = await fetch("https://api.example.com").then(res => res.json());
        return { props: { data }, revalidate: 60 };
    }
    ```
   
4. **SSR(Server-Side Rendering)**
- **특징**: 클라이언트 요청 시 서버에서 HTML 생성
- **장점**: 빠른 **TTV**(사용자와 상호작용하는 부분을 제외한 껍데기를 브라우저에 제공 - `pre-rendering`), **SEO** 좋음, **실시간 데이터** 표시
- **단점**: 사이트 컨텐츠가 변경되면 **전체 사이트를 빌드**하게 되어 **서버 부하**가 큼, 요청 때마다 페이지를 만들어야 함
- **예시**:
  
    ```tsx
    export async function getServerSideProps() {
    const data = await fetch("https://api.example.com").then(res => res.json());
        return { props: { data } };
    }
    ```

    SSR에서는 사용자와 상호작용하는 데에 필요한 모든 파일을 다운로드 받는 **hydration** 과정이 있어야 동적인 부분들이 동작하므로, 이 **hydration** 시간을 줄이는 것이 관건

## ❓ Suspense, Loading UI, Streaming SSR
사용자 경험을 개선하는데 사용되는 방안들을 하나씩 알아본다.

- **데이터 로딩 느릴 때 대처법**
1. **비동기 데이터 로딩**: 비동기 로딩을 통해 다른 요소들을 먼저 렌더링되게 한다.
   => **Loading UI**를 사용해 비동기 컴포넌트 로드되는 동안 상태 표시, **Suspense**를 사용해 **로딩 상태 처리 매커니즘** 구현

   ```tsx
    // Loading.tsx
    export default function Loading() {
        return <div>Loading...</div>;
    }

    // Home.tsx
    import { Suspense } from "react";
    import NewProductList from "@/components/NewProductList";
    import Loading from "./loading";

    export default async function Home() {
        return (
            <div>
                <h1>Shop</h1>
                <Suspense fallback={<Loading />}>
                    <NewProductList />
                </Suspense>
            </div>
        );
    }
   ```

   => **Streaming**으로 서버에서 준비된 컨텐츠를 부분으로 **나눠서** 클라이언트에 보내, 페이지 일부를 더 빨리 볼 수 있게 한다.

   ```tsx
    export default async function Home() {
        return (
            <div>
            <h1>Sparta Shop</h1>
            <Suspense fallback={<Loading />}>
                <NewProductList />
            </Suspense>
            <Suspense fallback={<Loading />}>
                <ProductList />
            </Suspense>
            </div>
        );
    }
   ```

2. **캐싱**: 빌드 시 데이터를 미리 가져오고, 정적 페이지로 제공


