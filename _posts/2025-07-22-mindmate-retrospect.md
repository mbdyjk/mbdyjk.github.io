---
title: "MindMate 프로젝트 회고"
date: 2025-07-22
categories: [React Native, TypeScript, 회고]
tags: [React Native, TypeScript, 회고]
---

프로젝트 링크: https://github.com/MindMate-org/MindMate

MindMate는 React Native 기반의 프로젝트로, 일상 생활에서 까먹기 쉬운 것들을 체계적으로 관리할 수 있게 도와주는 모바일 애플리케이션입니다. 일정 관리, 루틴 관리, 다이어리, 주소록 기능을 통합 제공합니다.

## 📝 **주요 기능**

- ⏰ 루틴 관리
  - 반복 작업 자동화
  - 푸시 알람 기능
  - 서브 태스크 진행률 추적
- 📝 다이어리
  - 개인 기록
  - 다양한 미디어 첨부 지원
  - 검색 기능
- 📞 주소록
  - 그룹화된 연락처 정보 관리
  - 통화, 메시지 바로가기
  - 추가 정보 메모 기능
- 📅 스케줄: 일정 기록
  - 일정 관리
  - 알림 설정 기능

## 🛠 **기술적 특징**

- 크로스 플랫폼: iOS/Android 동시 지원
- 오프라인 사용 가능: SQLite 기반 로컬 데이터 저장
- 타입 안전성: TypeScript로 런타임 오류 방지
- 모던 UI: NativeWind(Tailwind CSS) 기반 반응형 디자인
- 확장 가능: Feature-based Architecture로 유지보수성 극대화

## 📢 **프로젝트 개요**

### 프레임워크: React Native + Expo
- React Native: 크로스 플랫폼 네이티브 앱 개발
- Expo SDK: React Native 개발환경
  - 개발 환경 설정의 복잡성 제거
  - OTA(Over-The-Air) 업데이트 지원

### 언어: TypeScript

### 스타일링: NativeWind (Tailwind CSS)
- 유틸리티 퍼스트 접근법
- 번들 크기 최적화: 사용된 클래스만 번들에 포함

### 데이터베이스: SQLite
- 로컬 데이터 저장: 오프라인 환경에서도 데이터 접근 가능
- 성능: 빠른 읽기/쓰기 속도로 모바일 앱에 최적화

### 상태 관리: Zustand
- 간단한 API: Redux 대비 학습 곡선이 낮음
- 번들 크기: 2KB 미만의 가벼운 라이브러리

### 네비게이션: Expo Router
- 파일 기반 라우팅
- 딥링킹

### 폼 관리: React Hook Form + Zod
- React Hook Form
  - 성능 최적화
    - 최소 리렌더링: uncontrolled 컴포넌트를 사용하여 `useState` 기반의 controlled 폼에 비해 성능 우수
    - ref 기반 상태 관리: `ref`로 입력 요소 상태를 관리하여 메모리 사용량을 줄이고 렌더링 부담 최소화
  - 유효성 검사 최적화
    - 네이티브 HTML 유효성 검사(`required`, `pattern`) 지원
    - 커스텀 유효성 검사 함수로 복잡한 로직 구현 가능
    - 비동기 유효성 검사 지원
- Zod
  - 스키마 기반 유효성 검사(JSON 스키마와 유사)
  - 복잡한 유효성 검사 지원(중첩 객체, 배열, 커스텀 검증 로직)
  - 스키마에서 타입스크립트 타입을 자동 생성하여 타입 안전성 보장

## 🧩 **공통 컴포넌트**

### Button 컴포넌트
파일: `src/components/ui/button.tsx`

설계 특징:
- `TouchableOpacity` 활용: 사용자가 누를 때 불투명도가 줄어드는 터치 가능한 컴포넌트, `activeOpacity`로 터치시 불투명도 조절
- 높이, 모서리 둥근 정도 속성 고정: 56px, 0.5rem(8px)로 설정
- 자식 요소, 클릭 콜백 함수, 커스터마이징 스타일을 props로 전달
 
### Modal 컴포넌트
파일: `src/components/ui/modal.tsx`

설계 특징:
- 반투명 배경: `bg-black/50`으로 모달 외부 어둡게 처리
- 애니메이션: `animationType="fade"`로 모달이 서서히 나타나거나 사라지는 효과 적용
- 접근성: `accessibilityLabel`로 스크린 리더 지원

### Calendar 컴포넌트
파일: `src/components/ui/calendar.tsx`

설계 특징:
- 주 단위 날짜를 표시하는 가로형 캘린더 UI 제공: 상단에 날짜 텍스트와 달력 아이콘, 하단에 요일과 날짜를 표시하는 7개 버튼
- 상태
  - `viewDate`: 현재 표시중인 주의 시작 날짜(`getWeekStart`로 계산)
  - `selectedDate`: 유저가 선택한 날짜로, useEffect로 `viewDate`를 업데이트, 선택된 날짜는 `isSameDay`로 비교하여 `bg-paleYellow`로 강조
- `weekDates`는 useMemo를 사용해 `viewDate` 변경 시 7일치 날짜 배열 계산하여 저장
- `dateText`로 `YYYY년 MM월 DD일` 형식으로 한국식 날짜 표기

### CheckBox 컴포넌트
파일: `src/components/ui/checkbox.tsx`

설계 특징:
- 조건부 스타일: `checked` 상태에 따라 배경색, 테두리 적용
- 체크 아이콘: lucide 아이콘 활용, `size - 4`로 크기를 선택하여 체크박스 내부에 적절히 맞춤
- 접근성: `accessibilityRole`, `accessibilityLabel`로 스크린 리더 지원

## 1️⃣ **상수**
### 색상
파일: `src/constants/colors.ts`

기본 색상, 상태 색상(red, 에러/삭제 등), 브랜드 색상을 상수로 정의

### 메시지
파일: `src/constants/messages.ts`

앱 전체에서 사용되는, 일관된 메시지 표현을 위한 상수

### 날짜
파일: `src/constants/date.ts`

요일에 대한 상수값을 배열로 저장하여 보관

## 🗄️ **데이터베이스 연동**

### 데이터베이스 초기화 시스템
파일: `src/hooks/use-initialize-database.ts`

### 공유 데이터베이스 설정
파일: `src/lib/db/share-db-init.ts`
