---
title: "[Headless UI 개발기] - Tooltip 구현 계획"
date: 2025-05-21
categories: [React, TypeScript, Storybook, Headless UI]
tags: [React, TypeScript, Storybook, Headless UI, Tooltip]
thumbnail: /assets/img/tooltip-thumbnail.png
---

# Tooltip 컴포넌트 개발 계획서

## 📌 개요

Tooltip은 사용자 인터페이스에서 특정 요소에 대한 추가 정보를 제공하는 UI 컴포넌트로, 사용자가 마우스를 올리거나 포커스했을 때 나타나는 간결한 정보 창이다. 본 문서에서는 Headless UI 설계 철학을 기반으로 접근성이 뛰어나고, 재사용 가능하며, 커스터마이징이 용이한 Tooltip 컴포넌트의 구현 계획을 다룬다.

이번 문서에서는 다음 내용을 포함한다.
- Tooltip 컴포넌트의 특징과 핵심 기능 정의
- Headless UI 기반 Tooltip 설계 원칙 정리
- 구현 계획
- 테스트 및 문서화 전략

### 💡 Tooltip의 주요 특징
- **비침투성**: 기본 UI를 방해하지 않고 추가 정보 제공
- **일시성**: 사용자의 상호작용에 따라 나타났다 사라짐
- **위치 자동 조정**: 화면 경계에 따른 위치 자동 조정
- **접근성**: 키보드 사용자를 위한 포커스 지원

### 📋 Tooltip 컴포넌트에서 고려해야 할 기능

#### 1. 핵심 기능
- 마우스 호버/포커스 시 툴팁 표시
- 툴팁 위치 자동 조정 (상, 하, 좌, 우)
- 툴팁 표시/숨김 애니메이션
- 키보드 접근성 지원

#### 2. 추가 기능
- 커스텀 트리거 요소 지원
- 지연 시간 설정 (showDelay, hideDelay)
- 툴팁 내용 HTML 지원
- 화면 경계 감지 및 위치 자동 조정
- 다크 모드 지원

#### 3. 접근성 고려사항
- ARIA 속성 지원 (aria-describedby)
- 키보드 네비게이션
- 스크린 리더 지원
- 포커스 관리

### 🛠️ 구현 계획

#### 1. 기본 컴포넌트 구조 설계
```typescript
type TooltipProps = {
  children: React.ReactNode;
  content: React.ReactNode;
  position?: 'top' | 'bottom' | 'left' | 'right';
  showDelay?: number;
  hideDelay?: number;
  isDisabled?: boolean;
};
```

#### 2. 핵심 기능 구현
- **위치 계산 로직**
  - Popper.js 활용한 위치 계산
  - 화면 경계 감지 및 자동 조정

- **상태 관리**
  - 호버/포커스 상태 관리
  - 애니메이션 상태 관리
  - 지연 시간 처리

- **접근성 구현**
  - ARIA 속성 설정
  - 키보드 이벤트 처리
  - 포커스 트랩 구현

#### 3. Storybook 통합
- 다양한 상태와 변형을 보여주는 스토리 작성
- Controls를 통한 props 조작 인터페이스
- 접근성 테스트 케이스 작성

#### 4. 테스트 계획
- 단위 테스트
  - 위치 계산 로직
  - 상태 변경 로직
  - 이벤트 핸들러

- 통합 테스트
  - 실제 사용 시나리오
  - 접근성 테스트
  - 반응형 동작 테스트

### 📚 문서화 계획

#### 1. 사용 가이드
- 기본 사용법
- Props 설명
- 접근성 가이드
- 커스터마이징 가이드

#### 2. 예제 코드
- 기본 사용 예제
- 위치 조정 예제
- 커스텀 스타일링 예제
- 애니메이션 예제

### 🔍 기술 스택

- React + TypeScript
- Storybook
- Playwright (E2E 테스트)
- Popper.js(위치 계산)
- Framer Motion(애니메이션)