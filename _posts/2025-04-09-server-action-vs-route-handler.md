---
layout: post
title: "Next.js Server Actions vs Route Handlers 어떤 상황에서 사용해야 할까?"
date: 2025-04-09
categories: [Next.js, React, TypeScript, 웹 기술]
tags: [Next.js, Server Actions, Route Handlers, TypeScript]
author: 고용준
---

# Next.js Server Actions vs Route Handlers: 어떤 상황에서 사용해야 할까?

서버 측 로직을 처리하는 두 가지 주요 방법인 Server Actions와 Route Handlers를 어떤 상황에서 어떤 걸 채택해야 하는지 헷깔리시나요? 이 글에서는 두 가지 접근법을 사용 사례와 함께 비교해보겠습니다.

## 1. 기본 개념 이해하기

### Server Actions란?

Server Actions는 Next.js 13.4부터 도입된 기능으로, 클라이언트 컴포넌트에서 직접 서버 함수를 호출할 수 있게 해줍니다. 'use server' 지시문을 사용하여 서버에서 실행될 함수를 정의할 수 있습니다.

**특징**
- 별도의 api 엔드포인트 설정 없이 사용하는 컴포넌트에서 직접 정의
- form 태그의 action 속성을 사용 기존에는 url만 설정할 수 있었는데 함수도 지정 가능해짐(함수 참조값을 서버로 보내 실행)
- 서버에서 실행되므로 민감한 정보가 브라우저를 통해 유출될 걱정을 하지 않아도 됨

```typescript
// app/actions.ts
'use server'

export async function createPost(data: FormData) {
  const title = data.get('title')
  const content = data.get('content')
  
  // 데이터베이스에 게시물 저장
  await db.posts.create({
    data: {
      title,
      content,
      createdAt: new Date()
    }
  })
}
```

### Route Handlers란?

Route Handlers는 Next.js의 API 라우트를 구현하는 전통적인 방법입니다. HTTP 메서드(GET, POST, PUT, DELETE 등)에 따라 엔드포인트를 정의할 수 있습니다.

**특징**
- app/api 디렉토리에 별도 파일로 정의하여 처리
- 클라이언트에서 fetch/axios와 같은 도구로 접근하여 http 요청
- 개발자가 직접 보안을 위한 인증 로직 등 구현 필요

```typescript
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  const data = await request.json()
  
  // 데이터베이스에 게시물 저장
  const post = await db.posts.create({
    data: {
      title: data.title,
      content: data.content,
      createdAt: new Date()
    }
  })
  
  return NextResponse.json(post)
}
```

## 2. 주요 사용 사례와 장단점

### Server Actions의 주요 사용 사례

1. **폼 제출 처리**
   - Progressive Enhancement 지원(https://developer.mozilla.org/en-US/docs/Glossary/Progressive_Enhancement)
   - 서버 액션이 초기 HTML에 포함되어 있으므로 JavaScript가 비활성화/로딩 완료되지 않은 환경에서도 작동

2. **간단한 서버 작업**
   - 데이터베이스 업데이트
   - 데이터 생성/삭제

```typescript
// Server Action 예시: 게시물 생성
'use server'

export async function createPost(formData: FormData) {
  const title = formData.get('title')
  const content = formData.get('content')
  
  try {
    await db.posts.create({
      data: { title, content }
    })
    revalidatePath('/posts')
    return { success: true }
  } catch (error) {
    return { success: false, error: '게시물 생성 실패' }
  }
}
```

### Route Handlers의 주요 사용 사례

1. **RESTful API 구현**
   - 외부 서비스/타사 솔루션과의 통신에 용이
   - 설계 명세가 명확
   - 응답값 커스터마이징 가능

2. **복잡한 API 엔드포인트**
   - 미들웨어 통합
   - 인증/인가 처리

```typescript
// Route Handler 예시: 파일 다운로드
export async function GET(request: Request) {
  const file = await getFileFromStorage()
  
  return new NextResponse(file, {
    headers: {
      'Content-Type': 'application/pdf',
      'Content-Disposition': 'attachment; filename="document.pdf"'
    }
  })
}
```

## 3. 결론 및 요약

| 특성             | Server Actions       | Route Handlers            |
| ---------------- | -------------------- | ------------------------- |
| 사용 목적        | 폼 제출, 데이터 변형 | RESTful API, 외부 통합    |
| 설정 복잡도      | 낮음                 | 중간                      |
| 보안             | 자동 CSRF 보호       | 미들웨어 기반             |
| 성능             | 최적화된 통신        | 세밀한 제어               |
| 개발 경험        | 간단한 설정          | 유연한 구성               |
| 적합한 사용 사례 | 폼 처리, 데이터 변형 | API 엔드포인트, 외부 통합 |

## 4. 참고 자료

- [Next.js 공식 문서 - Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions)
- [Next.js 공식 문서 - Route Handlers](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)

---
