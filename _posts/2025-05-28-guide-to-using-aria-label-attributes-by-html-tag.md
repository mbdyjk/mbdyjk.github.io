---
title: "HTML 태그별 ARIA-Label 속성 사용 가이드: 접근성을 위한 실무 팁"
date: 2025-05-28
categories: [Next.js, React, aria-label, HTML]
tags: [Next.js, React, aria-label, HTML]
---

# HTML 태그별 ARIA-Label 속성 사용 가이드

웹 접근성은 모든 사용자가 웹 컨텐츠를 문제없이 이용할 수 있도록 하는 중요한 요소이다. MDN의 HTML 접근성 가이드에 따르면, HTML의 **시맨틱 구조**는 접근성의 기본이며, MDN의 ARIA 문서는 **ARIA(Accessible Rich Internet Applications) 속성**이 동적 컨텐츠와 커스텀 UI의 접근성을 보완한다고 설명한다. 이 포스트에서는 HTML의 시맨틱 요소와 ARIA 속성을 실무에서 어떻게 활용해 접근성을 높일 수 있는지, 실제 예제와 함께 정리한다.

## 1. 웹 접근성과 ARIA의 역할
웹 접근성은 시각, 청각, 운동, 인지 장애를 가진 사용자를 포함한 모든 사용자가 웹을 이용할 수 있도록 만드는 것에 목표가 있다. 이를 위해 HTML은 시맨틱 태그를 제공하며, ARIA는 동적 컨텐츠나 커스텀 컴포넌트에 의미를 추가한다.

### 기본 원칙
- 시맨틱 HTML 우선: 네이티브 HTML 요소는 기본적으로 접근성이 뛰어나므로 우선 사용
- ARIA는 보완적: ARIA는 네이티브 HTML 이외의 상황에서 사용
- 명확성과 간결성: ARIA 속성은 요소의 역할, 상태, 목적을 간결하고 정확히 전달해야 함
- WCAG 준수: WCAG 2.1 가이드라인을 따라 성공 기준을 충족
- 언어 일치: ARIA 속성 텍스트는 페이지 언어와 일치

## 2. HTML 태그와 ARIA 속성 사용 예제
MDN의 권장 사항을 반영하여, 시맨틱 HTML과 ARIA를 조화롭게 활용하는 방법을 기술한다.

### 2.1. button 태그
기본적으로 키보드 접근성이 내장되어 있지만, 아이콘만 있거나 상태가 동적인 경우 ARIA 추가

```html
<!-- 아이콘만 있는 버튼 -->
<button aria-label="검색 실행">
  <svg aria-hidden="true" focusable="false">
    <path d="M10 10l5 5" />
  </svg>
</button>

<!-- 커스텀 토글 버튼 -->
<div role="button" tabindex="0" aria-pressed="false" aria-label="알림 설정 토글" onclick="handleClick()" onkeydown="handleKeydown(event)>
  알림
</div>

<script>
function handleClick() {
  const button = document.querySelector('[role="button"]');
  const isPressed = button.getAttribute('aria-pressed') === 'true';
  button.setAttribute('aria-pressed', !isPressed);
}

function handleKeydown(event) {
  if (event.key === 'Enter' || event.key === ' ') {
    event.preventDefault(); // 스페이스바로 페이지 스크롤 방지
    handleClick();
  }
}
</script>
```

- 시맨틱 HTML: 버튼 태그는 기본적으로 스크린 리더가 버튼으로 인식
- ARIA 사용:
  - **aria-label**: 버튼의 목적 명시
  - **aria-hidden**: 시각적으로만 표시되는 요소를 스크린 리더 읽기에서 제외
  - **role="button", aria-pressed**: 커스텀 요소를 버튼으로 인식시키고 토글 상태 전달
- MDN 팁: 커스텀 버튼에는 **tabIndex**와 **키보드 이벤트**를 추가해야 함을 강조, `tabIndex`는 요소가 키보드 네비게이션(포커스 이동)이 가능하게 하고, `onkeydown`으로 엔터/스페이스 키로 버튼 활성화

### 2.2. a 태그, nav 태그
링크의 목적과 현재 상태를 명확히 하는 방식으로 접근성 준수

```html
<!-- 이미지 링크 -->
<a href="/profile" aria-label="사용자 프로필로 이동">
  <img src="profile-icon.png" alt="" aria-hidden="true">
</a>

<!-- 네비게이션 링크 -->
<nav aria-label="주요 메뉴">
  <ul>
    <li><a href="/home" aria-current="page">홈</a></li>
    <li><a href="/about">소개</a></li>
  </ul>
</nav>
```

- 시맨틱 HTML: `nav` 태그와 `a` 태그는 기본적으로 접근성이 좋음
- ARIA 사용:
  - **aria-label**: 네비게이션의 목적 구체화
  - **aria-current**: 현재 선택된 항목 표시
    - page: 현재 페이지에 해당하는 링크
    - step: 프로세스나 단계에서 현재 단계
    - location: 현재 위치(브레드크럼)
    - date: 캘린더에서 현재 날짜
    - true: 현재 선택된 항목
- MDN 팁: 링크 텍스트가 모호할 경우(예: 여기 클릭, 더보기), `aria-label`로 구체화하고, 여러 네비게이션 요소의 경우에도 레이블을 추가하여 구분할 것을 권장

### 2.3. input 태그

```html
<!-- 레이블이 있는 입력 필드 -->
<label for="search">검색어</label>
<input id="search" type="text" aria-required="true">

<!-- 추가 설명이 있는 입력 필드 -->
<input type="email" aria-describedby="email-help" aria-required="true">
<p id="email-help">유효한 이메일 주소를 입력하세요.</p>
```

- 시맨틱 HTML: <label for="id">는 입력 필드와 레이블을 연결해 접근성을 높임
- ARIA 사용: 
  - **aria-required**: 필수 입력임을 명시
  - **aria-describeby**: 추가 설명(보조 텍스트)을 참조
- MDN 팁: 라벨 태그를 기본적으로 사용하고, 플레이스홀더는 보조적인 역할로만 사용하라고 권장

### 2.4. div 태그
role과 ARIA 속성을 사용해 div에 의미 부여

```html
<!-- 빈 결과 섹션 -->
<div role="region" aria-label="좋아요한 일정 없음">
  <p>좋아요한 일정이 없습니다.</p>
  <a href="/community" aria-label="커뮤니티 페이지로 이동">인기 일정 보러가기</a>
</div>

<!-- 동적 알림 -->
<div aria-live="polite" id="notification">게시물이 업로드되었습니다.</div>
```

- 시맨틱 HTML: div 태그 자체는 의미가 없으므로 `role`로 구조 정의
- ARIA 사용: 
  - **role="region"**, **aria-label**: 섹션 목적 설명
  - **aria-live**: 동적 컨텐츠 업데이트를 스크린 리더에 알림
- MDN 팁: `aria-live="polite`를 일반 알림에, `assertive`를 긴급 알림에 사용하도록 권장

### 2.5. ul 태그/ol 태그
CSS로 구조가 변경된 경우 ARIA 속성으로 보완

```html
<h2 id="likes-header">좋아요한 플랜 목록</h2>
<ul role="list" aria-labelledby="likes-header">
  <li role="listitem">
    <button aria-label="플랜 1 좋아요 토글" aria-pressed="true">
      <span aria-hidden="true" class="icon-heart"></span>
    </button>
    <p>플랜 1</p>
  </li>
</ul>
```

- 시맨틱 HTML: `ul` 태그와 `ol` 태그는 기본적으로 접근성이 좋음
- ARIA 사용: 
  - **role="list"**: CSS(예: display: grid, list-style: none)로 목록 스타일이 제거된 경우 구조 보장
  - **aria-labelledby**: 목록의 제목 참조
- MDN 팁: CSS로 목록 스타일을 변경한 경우 `role="list"`와 `role="listitem"`을 추가해 스크린 리더가 목록 구조를 인식하도록 하라고 권장(구형 스크린리더나 복잡한 스타일링에서 구조 소실 가능성)\

### 2.6. img 태그
alt 속성을 주로 사용, ARIA는 장식용 이미지나 상위 요소에 사용

```html
<!-- 콘텐츠 이미지 -->
<img src="profile.jpg" alt="사용자 프로필 사진">

<!-- 버튼 내 장식용 이미지 -->
<button aria-label="좋아요 토글">
  <img src="heart.png" alt="" aria-hidden="true">
</button>
```

- 시맨틱 HTML: `alt` 속성을 통해 이미지 접근성 제공
- ARIA 사용: 
  - **aria-hidden**: 장식용 이미지를 스크린 리더에서 제외
  - **aria-label**: 상위 버튼의 목적 설명
- MDN 팁: 모든 컨텐츠 이미지에 의미 있는 `alt` 텍스트 제공, 장식용 이미지는 `alt=""`와 `aria-hidden="true"`로 처리할 것을 권장

## 3. 실무 팁
### 3.1. 동적 컨텐츠와 ARIA
- **aria-live**: 동적 업데이트(예: 알림, 에러 메시지)에 사용

    ```html
    <div aria-live="polite">게시물이 업로드되었습니다.</div>
    ```
- ***aria-expanded**: 아코디언, 드롭다운 등 확장/축소 상태를 나타내는데 사용
- ***aria-controls**: 한 요소가 다른 요소의 상태나 컨텐츠를 제어함을 알림

    ```html
    <button aria-expanded="false" aria-controls="panel">메뉴 열기</button>
    <div id="panel" style="display: none;">메뉴 콘텐츠</div>
    ```

### 3.2. 스크린 리더 테스트
- 도구: NVDA(Windows), VoiceOver(macOS/iOS), TalkBack(Android)
- 체크리스트:
  - ARIA 속성이 올바르게 읽히는지
  - 키보드 네비게이션(탭, 엔터, 스페이스 키)이 원활한지
  - 동적 컨텐츠 업데이트가 스크린 리더에 전달되는지

### 3.3. WCAG 준수
- 성공 기준 1.3.1(정보와 관계): 시맨틱 HTML과 ARIA로 컨텐츠 구조 명확히 함
- 성공 기준 4.1.2(이름, 역할, 값): 모든 인터렉티브 요소에 이름과 역할을 제공

## 4. 참고 자료
- [MDN 문서 - HTML 접근성](https://developer.mozilla.org/ko/docs/Learn_web_development/Core/Accessibility/HTML)
- [MDN 문서 - ARIA 속성](https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA)

---