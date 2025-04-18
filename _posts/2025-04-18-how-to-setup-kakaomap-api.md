---
title: "Next.js에서 Kakao Map API 기본 설정하기"
date: 2025-04-18
categories: [Next.js, React, TypeScript, Kakao Map]
tags: [Next.js, React, TypeScript, Kakao Map]
---

## ⚙️ Kakao Map API 셋업
**1.** https://developers.kakao.com/ 링크로 접속해 로그인한 후, 내 애플리케이션에 들어갑니다. Kakao 계정으로 로그인이 됩니다.

![Kakao Map 홈 화면](/assets/img/2025-04-18-150315.png)

**2.** 애플리케이션 추가하기 버튼 클릭 및 나오는 모달에서 정보 입력 후 저장합니다.

![애플리케이션 추가](/assets/img/2025-04-18-153237.png)

![애플리케이션 설정](/assets/img/2025-04-18-153657.png)

**3.** 만들어진 프로젝트를 클릭하시고, 사이드메뉴에서 플랫폼 -> Web 플랫폼 등록 -> 플랫폼 도메인을 입력해줍니다.(일반적인 React, Next,js의 경우 로컬 실행시 localhost 3000번, vite의 경우 localhost 5173번, 혹은 배포 링크 입력)

![웹 플랫폼 선택](/assets/img/2025-04-18-154302.png)

![사이트 도메인 등록](/assets/img/2025-04-18-154432.png)

⚠️ 배포하신 URL이 있으시면 **기본 도메인**을 해당 URL로 지정해주세요!

**4.** 사이드메뉴에서 앱 키 -> JavaScript 키와 REST API 키를 복사하여 프로젝트의 환경 변수 파일에 복사하시면 됩니다!

![웹 플랫폼 선택](/assets/img/2025-04-18-155606.png)

## 🌟 사용해보자!

예시 1) 경로 계산 => Kakao Mobility API를 사용하기 위해 REST API 키 사용
```ts
const headers = {
    Authorization: `KakaoAK ${process.env.NEXT_PUBLIC_KAKAO_REST_KEY}`,
    'Content-Type': 'application/json',
  };

  const queryParams = new URLSearchParams({
    origin: origin,
    destination: destination,
    waypoints: viaPoints,
    priority: 'RECOMMEND',
    car_type: '1',
    alternatives: 'false',
  });

  const requestUrl = `https://apis-navi.kakaomobility.com/v1/directions?${queryParams}`;

  try {
    const response = await fetch(requestUrl, {
      method: 'GET',
      headers,
      cache: 'no-cache',
    });
  }
```

예시 2) Kakao Map 로드를 위해 HTML Script 태그에 추가
```ts
const script = document.createElement('script');
script.src = `https://dapi.kakao.com/v2/maps/sdk.js?appkey=${process.env.NEXT_PUBLIC_KAKAO_MAP_API_KEY}&libraries=clusterer,services&autoload=false`; // services, clusterer 라이브러리 추가
script.async = true;
document.head.appendChild(script);
```