---
title: "Sparta_LolInfo 프로젝트 진행 경과 정리"
date: 2025-03-17
categories: [Next.js, React, TypeScript, 개인프로젝트]
tags: [Next.js, React, TypeScript, 개인프로젝트]
---

링크: https://github.com/mbdyjk/Sparta_LolInfo

**Sparta_LolInfo** 프로젝트를 진행하며 새롭게 알게 된 내용이나, 정리가 필요한 내용을 작성한 문서.

## 🛠️ Next.js, Tailwind CSS  초기 설정

- **Next.js**

    ```bash
    npx create-next-app@14.2.18 <project-name>
    ```

    최신버전인 15버전보다 충분히 안정화되고 검증된 14버전을 사용하였다.

- **Tailwind CSS**

    ```bash
    pnpm install -D tailwindcss@3.3.5 postcss autoprefixer
    npx tailwindcss init -p
    ```

    최신버전인 4버전 대신 3버전을 사용하면 **PostCSS**와 **Autoprefixer** 도구를 별도 설치하고, **CSS**가 아닌 **config** 파일에서 테마를 결정한다.

    - `-D` 옵션으로 **개발 의존성**으로 패키지 설치, 프로덕션 환경에서는 사용되지 않음
    - **postcss**: css 처리 도구
    - **autoprefixer**: css에 자동으로 브라우저별 접두사를 추가해 **브라우저 호환성**을 높일 수 있다.
    - `npx` 명령어를 통해 프로젝트 로컬에 설치된 패키지 버전을 사용하여 실행할 수 있다.
    - `-p` 옵션은 ``--postcss`` 옵션의 축약어로, `postcss.config.js` 파일도 함께 생성한다.

---

## 🆚 상황에 따른 Interface와 Type 사용

- **Interface**
  - `extends`를 사용해 객체 **상속**을 구현한다.
  - **객체**가 **계층 관계**에 있을 때 사용
  - 확장이 있고, **선언 병합**을 활용할 가능성이 있을 경우

- **Type**
  - `&`를 사용해 객체 **결합**을 만들어낸다.
  - **유니온 타입**이나 **복잡한 타입 조합**이 필요한 경우 사용
  - 확장되지 않고, **단일 정의**로 끝날 경우

---