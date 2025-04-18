---
title: "Next.js의 다양한 렌더링 방식"
date: 2025-04-18
categories: [Next.js, React, TypeScript, 기술 면접]
tags: [Next.js, React, TypeScript, 기술 면접]
---

## 📖 React와 비교하여 알아보는 Next.js의 장점

1번. **서버 컴포넌트**
- **정의**: **서버**에서 실행되고 렌더링되는 컴포넌트
- **특징**: `app` 디렉토리에서 기본값으로 적용, 서버(`node.js`) 환경에서 동작
- **장점**:
  - 성능 최적화 측면
   1. **번들 사이즈 감소**: 클라이언트로는 최종 결과물인 HTML만 전송되므로, 사용자가 웹사이트에 접속할 때 외부 라이브러리나 의존성 등의 추가 JavaScript 코드를 다운로드할 필요가 없어짐
   => 빠른 페이지 로드를 바탕으로 더 나은 사용자 경험을 제공, 모바일 기준 데이터 사용량도 줄일 수 있음
   
   2. **초기 로딩 성능**
      - 로딩 과정 비교
        - React: 브라우저가 빈 HTML을 받음 -> 번들 다운로드 -> JavaScript 실행 -> API 호출/데이터 fetching -> 화면 렌더링
        - Next.js: 서버에서 완성된 HTML 생성 -> 브라우저에서 전달받음 -> 화면에 컨텐츠 표시 -> 필요한 JavaScript는 백그라운드에서 로드
  
        => **FCP**(First Contentful Paint, 사용자가 처음으로 의미있는 컨텐츠를 보는 시점)이 개선, **TTI**(Time To Interactive, 사용자가 특정 기능과 상호작용할 수 있는 시점) 단축 by 우선순위 기반 점진적 hydration

    3. **데이터 페칭 최적화**
     - 워터폴 현상(데이터를 순차적으로 가져올 수 밖에 없는 상황) 방지하여 여러 번의 API 호출을 단일 호출로 통합
       - 예시 코드
            ```ts
            // 2가지 예시는 각 작업이 이전 작업에 의존적인 경우
            // 1. 인증 후 사용자 정보 조회
            /* React */
            async function loadUserData() {
                // 1단계: 로그인
                const auth = await login();
                // 2단계: 인증 토큰으로 사용자 정보 조회
                const user = await fetchUserProfile(auth.token);
                // 3단계: 사용자 ID로 주문 내역 조회
                const orders = await fetchUserOrders(user.id);
            }

            /* Next.js */
            async function getUserData() {
            // DB나 ORM을 통해 한 번의 쿼리로 필요한 모든 데이터 조회
                const userData = await prisma.user.findUnique({
                    where: { id: userId },
                    include: {
                    profile: true,
                    orders: true,
                    }
                });

                return userData;
            }

            // 2. 계층적 데이터 구조
            /* React */
            async function loadComments() {
                // 1단계: 게시글 조회
                const post = await fetchPost(postId);
                
                // 2단계: 댓글 조회
                const comments = await fetchComments(post.id);
                
                // 3단계: 각 댓글의 작성자 정보 조회
                const commentAuthors = await Promise.all(
                    comments.map(comment => fetchUser(comment.userId))
                );
            }

            /* Next.js */
            // 서버 사이드에서 한 번에 처리
            async function getPostWithComments(postId: string) {
                // JOIN을 사용하여 한 번의 쿼리로 모든 데이터 조회
                const postData = await prisma.post.findUnique({
                    where: { id: postId },
                    include: {
                    comments: {
                        include: {
                        author: true, // 댓글 작성자 정보도 함께 조회
                        }
                    }
                    }
                });

                return postData;
            }
            ```

       - 클라이언트에서 API 서버를 거쳐 DB에 접근하지 않고, 바로 DB에 접근할 수 있음 => 네트워크 홉 감소
  
    4. **이미지 최적화**
       -  자동 Webp 변환: 이미지 용량 줄임
       -  반응형 이미지 구현 가능
            ```ts
            <Image
                src="/banner.jpg"
                fill
                sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
                style={{ objectFit: 'cover' }}
            />
            ```
        -  지연 로딩(loading = "lazy", blurDataURL)
   

   - 개발 효율성 측면
    1. **백엔드와 통합 용이**: 바로 DB에 접근할 수 있어 API 서버 호출 코드 작성이 필요없어짐

   - SEO 최적화 측면
    1. **검색 엔진 크롤링에 유리**: 완성된 HTML을 서버로부터 제공받으므로
    2. 메타 태그 동적 생성 가능

   - 보안 측면
    1. 환경 변수나 API 키를 클라이언트에 노출하지 않고 사용 가능
    2. 민감한 로직을 서버에 두고 실행하도록 함

2번. **다양한 렌더링 방식 지원**

1. **CSR**: 브라우저에서 자바스크립트를 실행하여 HTML 생성(렌더링)
2. **SSR**: 서버에서 HTML 생성(브라우저 요청에 따라)
3. **ISR**: 정적 HTML 생성 후 revalidate 값에 따라 주기적 업데이트
4. **SSG**: 빌드 시점에 정적 HTML 생성

## 🆚 정적 렌더링과 동적 렌더링의 기준
1. **데이터가 빌드 시점에 결정되는지**
2. **모든 사용자에게 동일한 컨텐츠를 제공하는지**
3. **데이터 캐싱을 하는지**

   Next.js에서 설정 방법
   - 정적 렌더링
    ```ts
    export const dynamic = 'force-static';
    export const revalidate = 3600; // 1시간
    ```
   - 동적 렌더링
    ```ts
    export const dynamic = 'force-dynamic';
    export const revalidate = 0;
    ```
