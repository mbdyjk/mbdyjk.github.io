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
**사용자 경험**을 개선하는데 사용되는 방안들을 하나씩 알아본다.

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

2. **캐싱**: 빌드 시 데이터를 미리 가져오고, **정적 페이지**로 제공

## ⚠️ Error UI, Error Handling
- **Next.js Error 핸들링**
  
    라우트 세그먼트에 맞춘 `error.js`를 생성하여, 오류 발생 부분만 격리하여 나머지 부분이 잘 동작하게 한다.
 
    반드시 **클라이언트 컴포넌트** 형태로 작성
    
    why?) **Error Boundaries**가 클라이언트 측에서만 동작, **동적 상호작용**(`reset`), **상태 관리** 측면에서 필요

    ```tsx
    'use client';
    import { useEffect } from 'react';

    export default function Error({ error, reset }) {
        useEffect(() => {
        console.error(error);
        }, [error]);

        return (
            <div>
                <h2>Something went wrong!</h2>
                <button onClick={() => reset()}>Try again</button>
            </div>
        );
    }
   ```

- 🛡️ **Error Boundary**란?

    **Error Boundary**는 하위 컴포넌트 오류를 잡아 **Fallback UI**를 표시하는 역할 수행

    But 이벤트 핸들러, 비동기 코드, SSR 오류는 처리 불가 

    이 때, 가까운 **상위 에러 바운더리**로 오류가 전달되어 처리

    **루트 레벨 오류**는 `global-error.js`로 처리

- **Error 복구**

    **일시적 오류**를 해결하기 위해 사용자가 다시 시도할 수 있는 버튼을 제공할 수 있다.

    How?) 오류 컴포넌트에서 사용자가 오류를 복구 시도할 수 있도록 제공하는 `reset()` 함수 사용

    ※ **일시적 오류?**: 사용자가 몰렸을 때, timeout이 지나버렸을 때 등

    ```tsx
    'use client';

    import { useRouter } from 'next/navigation';
    import { startTransition } from 'react';

    export default function Error({
        error,
        reset,
    }: {
        error: Error & { digest?: string };
        reset: () => void;
    }) {
        const { refresh } = useRouter();

        return (
            <div>
            <h2>Something went wrong!</h2>
            <h2>{error.message}</h2>
            <button
                onClick={() =>
                startTransition(() => {
                    refresh();
                    reset();
                })
                }
            >
                Try again
            </button>
            </div>
        );
    }
    ```

    ※ **서버 컴포넌트 오류**: Next.js가 오류를 `error.js`로 전달, 프로덕션 환경에서는 민감 정보 제외(message, digest만 포함)

## 🖼️ Next.js Asset 최적화
이미지는 **웹 페이지 무게**와 **LCP**(Largest Contentful Paint)에 큰 영향을 미친다. -> 이미지 최적화 필요

Next.js에서 제공하는 `Image` 컴포넌트를 사용하면, 다음 효과를 얻을 수 있다.

  1. **크기 최적화**: 기기에 맞는 크기의 이미지 자동 제공, WebP/AVIF과 같은 최신 이미지 형식 사용(AVIF의 경우 **IE**에서는 지원하지 않음)
  2. **시각적 안정성**: 레이아웃 이동 방지
  3. **지연 로딩**: 뷰포트 내부에 있는 이미지만 로드

사용 예시

1. **로컬 이미지**

    ```tsx
    import Image from 'next/image';
    import profilePic from './me.png';

    export default function Page() {
        return <Image src={profilePic} alt="Profile" />;
    }
    ```

2. **원격 이미지**

    ```tsx
    import Image from 'next/image';

    export default function Page() {
        return <Image src="https://example.com/img.jpg" width={500} height={500} alt="Remote" />;
    }
    ```
- **Metadata로 SEO 최적화**

1. **정적 메타데이터**

    ```typescript
    export const metadata = { title: 'My Site', description: 'Description' };
    export default function Page() {}
    ```

2. **동적 메타데이터**

    ```typescript
    export async function generateMetadata({ params }) {
        const product = await fetch(`https://.../${params.id}`).then(res => res.json());
        return { title: product.title };
    }
    ```

3. **파일 기반**
   `favicon.ico`, `robots.txt` 등 추가

- **Static Assets**, **Font**, **Script**
1. **Static Assets**: `public` 폴더에서 제공
   
    ```tsx
    import Image from 'next/image';
    export default function Page() {
        return <Image src="/assets/logo.png" height={40} alt="Logo" />;
    }
    ```

2. **Font**: `next/font`로 셀프 호스팅

    ```tsx
    import { Inter } from 'next/font/google';
    const inter = Inter({ subsets: ['latin'], display: 'swap' });

    export default function Layout({ children }) {
        return <html className={inter.className}><body>{children}</body></html>;
    }
    ```
   
3. **Script**: `next/script`로 로딩 전략 설정

    ```tsx
   import Script from 'next/script';
    export default function Page() {
        return <Script src="https://example.com/script.js" strategy="lazyOnload" />;
    }
    ```

## ❓ Route Handler & Server Action
- **Route Handler**

    `route.ts`로 **REST API** 구현

    ex) GET, POST, PATCH, PUT, DELETE

    어떻게? **비동기 함수**로 구현
    동작 확인: **POSTMAN** 사용
    https://www.postman.com/

- **Server Action**

  **서버에서 실행되는 비동기 함수**로, 주로 CRUD 작업 수행
  `use server`로 서버 로직 정의, 클라이언트에서도 호출 가능

  예시

  - 데이터 조회
  
    ```typescript
    "use server";
    export async function getProducts() {
        const res = await fetch('http://localhost:4000/products', { cache: 'no-store' });
        return await res.json();
    }
    ```
  - 데이터 추가
  
    ```typescript
    "use server";
    import { revalidatePath, redirect } from 'next';
    export async function addItemToCart(formData) {
        const { itemId } = Object.fromEntries(formData);
        // Cart API 호출
        revalidatePath('/cart');
        redirect('/cart');
    }
    ```
