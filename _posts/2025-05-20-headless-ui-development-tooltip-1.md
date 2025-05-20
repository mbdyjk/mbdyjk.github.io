---
title: "[Headless UI 개발기] - 개념과 필요성 탐구"
date: 2025-05-20
categories: [React, TypeScript, Storybook, Headless UI]
tags: [React, TypeScript, Storybook, Headless UI]
thumbnail: /assets/img/headless-ui-thumbnail.png
---

## 🎨 Headless UI란?

Headless UI는 스타일링이 적용되지 않은, 순수한 기능적 UI 컴포넌트 라이브러리다. 즉, 컴포넌트의 동작과 접근성을 제공하지만, CSS나 스타일은 개발자가 자유롭게 적용할 수 있도록 설계되어 있다.

**주요 특징**
- **스타일링 자유도**: 시각적 스타일은 직접 커스터마이징
- **접근성(A11y)**: WCAG 표준을 준수한 접근성 보장
- **상태 관리**: 복잡한 상태 로직을 내부적으로 처리
- **다양한 컴포넌트 제공**: 다양한 UI 패턴을 제공

### 💡 언제 Headless UI를 사용해야 할까?

프로젝트의 **디자인 시스템이 있는 경우** 편하게 적용할 수 있다.
기존 디자인 시스템의 스타일을 그대로 활용하면서 기능적인 부분만 재사용하고 싶을 때 적합하다.

ex) 회사 내부 디자인 시스템과의 통합이 필요한 경우

## 📖 Storybook이란?
Storybook은 UI 컴포넌트를 독립적으로 개발, 테스트, 문서화하기 위한 오픈소스 도구이다.

**주요 특징**
- **컴포넌트 격리**:
  - 실제 애플리케이션의 상태나 데이터 흐름에 영향을 받지 않고 컴포넌트 개발 가능
  - 각 컴포넌트의 동작을 독립적으로 테스트하고 디버깅할 수 있어 개발 효율성 향상
- **문서화**: 컴포넌트의 Props, 사용 예시, 상태 변형을 자동으로 문서화(MDX, Docs Addon 지원)
- **상호작용 테스트**: 컴포넌트의 이벤트, 상태 변화 등을 시뮬레이션하여 동작 검증 가능
- **반응형 디자인 테스트**: 다양한 뷰포트 환경(모바일, 태블릿, 데스크톱)에서 컴포넌트 렌더링 확인
- **플러그인 생태계**: 접근성(a11y), 디자인 토큰, Figma 연동, 테스트 도구 등 다양한 Addon으로 기능 확장
- **팀 협업**: 디자이너, 개발자, QA 팀이 컴포넌트의 시각적 결과물을 공유하고 피드백을 주고받을 수 있는 환경 제공

## 📚 Storybook 상세 가이드

### 🚀 초기 설정
```bash
# 프로젝트 생성
npx degit chromaui/intro-storybook-react-template taskbox

# 의존성 설치
cd taskbox
yarn

# Storybook 실행
yarn storybook

# frontend 앱 실행
yarn dev
```

### 📁 기본 디렉토리 구조
```
.storybook/
├── main.ts        # Storybook 설정 파일
├── preview.ts     # 전역 설정 및 데코레이터
└── theme.ts       # 커스텀 테마 설정

src/
└── components/    # 컴포넌트 소스 코드
```

### 🧩 핵심 구성 요소
- **Stories**: 컴포넌트의 다양한 상태와 변형을 정의
- **Docs**: 컴포넌트 문서화 (MDX 형식)
- **Controls**: Props를 실시간으로 조작하는 인터페이스
- **Actions**: 이벤트 핸들러 테스트
- **Viewport**: 반응형 디자인 테스트
- **A11y**: 접근성 검증

### 📁 설정 파일 구조
```typescript
// .storybook/main.ts
// Storybook의 핵심 설정 파일입니다.
// 컴포넌트 스토리 위치, 애드온, 프레임워크 등을 정의합니다.

const config: StorybookConfig = {
  // 스토리 파일의 위치 정의
  // MDX 파일과 스토리 파일(.stories.tsx 등)을 모두 포함
  "stories": [
    "../src/**/*.mdx",
    "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"
  ],
  // Storybook의 기능을 확장하는 애드온들을 설정
  // - essentials: 기본적인 기능 제공
  // - onboarding: 초기 설정 가이드
  // - chromatic: 시각적 회귀 테스트
  // - experimental-addon-test: 실험적 테스트 기능
  "addons": [
    "@storybook/addon-essentials",
    "@storybook/addon-onboarding",
    "@chromatic-com/storybook",
    "@storybook/experimental-addon-test"
  ],
  // 사용할 프레임워크를 지정
  // React + Vite 조합을 사용하며, 추가 옵션을 설정 가능
  "framework": {
    "name": "@storybook/react-vite",
    "options": {}
  }
};

export default config;
```

컴포넌트의 기능적 측면과 스타일링을 분리하여 개발하고 테스트할 수 있다는 점에서 Storybook을 활용한 Headless UI 개발 방식을 채택하였다.




