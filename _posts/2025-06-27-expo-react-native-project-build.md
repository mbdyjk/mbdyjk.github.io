---
title: "Expo/React Native 프로젝트 빌드하기 - 처음부터 하나씩"
date: 2025-06-27
categories: [Expo, React Native, Solution]
tags: [Expo, React, Solution]
---

# EAS Build
https://docs.expo.dev/build/setup/ 다음 공식문서를 참조하여 Expo 프로젝트 빌드를 **EAS**를 통해 진행한다.
EAS(Expo Application Services) 빌드는 Expo, React Native 프로젝트를 위한 클라우드 기반 빌드 서비스이며, 앱 바이너리를 쉽게 생성 및 배포할 수 있도록 돕는다.

## 빌드를 위한 준비사항
### 1. React Native 프로젝트 생성
```bash
npx create-expo-app my-app
```

### 2. Expo 회원가입
https://expo.dev/ 링크에서 회원가입을 진행한다.

### 3. 최신 버전의 EAS CLI 설치
```bash
npm install -g eas-cli
```

### 4. Expo 계정 로그인
```bash
eas login
```
가입한 계정의 정보를 CLI 환경에 입력한다.

### 5. 프로젝트 구성
```bash
eas build:configure
```

### 6. 개발용 빌드를 위한 expo-dev-client 라이브러리 설치
```bash
npx expo install expo-dev-client
```
❓왜 설치가 필요할까?
: 앱의 디버깅에 최적화된 빌드, 코드 수정 후 빠르게 테스트하고 반영할 수 있다.

### 7. (선택) 빌드 구성 추가 작업
- 앱이 환경 변수를 필요로 할 때
  : `eas.json` 파일에 환경 변수 추가
  ```json
    {
        "build": {
            "development": {
                "env": {
                    "API_KEY": "your-api-key",
                    "BASE_URL": "https://api.example.com"
                }
            },
            "production": {
                "env": {
                    "API_KEY": "your-api-key",
                    "BASE_URL": "https://api.example.com"
                }
            },
        }
    }
  ```
- 프로젝트가 모노레포(monorepo) 구조로 되어 있을 때
  : `eas.json` 파일에서 빌드가 앱 코드가 있는 폴더에서 일어나도록 지정
  ```json
    {
        "build": {
            "development": {
            "workingdir": "apps/mobile"
            }
        }
    }
  ```
- 비공개 npm 패키지를 사용할 때(생략)
- 앱이 특정 버전의 도구(Node, yarn, npm, Xcode 등)에 의존할 때
  : EAS 빌드가 클라우드 환경에서 실행되므로, `eas.json` 파일에서 서버에 설치된 기본 도구 버전과 프로젝트 요구 사항을 일치시켜야 올바르게 빌드가 이루어진다.
  ```json
    {
        "build": {
            "development": {
                "node": "16.20.0",
                "packageManager": "npm@8.19.2",
                "ios": {
                    "cocoapods": "1.11.3",
                    "xcode": "14.3"
                }
            }
        }
    }
  ```

  ### 8. 안드로이드/iOS 디바이스에서 동작 확인을 위한 빌드 작업 진행
  - 안드로이드
  ```bash
  eas build --platform android // 배포용
  eas build -p android --profile development // 개발용
  ```
  - iOS
  ```bash
  eas build --platform ios // 배포용
  eas build -p ios --profile development // 개발용
  ```


