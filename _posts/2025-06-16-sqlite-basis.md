---
title: "SQLite 기초"
date: 2025-06-19
categories: [SQLite, DBMS]
tags: [SQLite, DBMS]
---

## 1. SQLite 특징
- **서버리스** 구조: 별도의 서버 설정이 필요 없이, 애플리케이션에 직접 내장되어 동작
- **단일 파일** 기반: 하나의 파일에 DB 저장

## 2. 데이터 타입
- **NULL**: null 값
- **INTEGER**: 정수
- **REAL**: 실수
- **TEXT**: 문자열
- **BLOB**: 바이너리 데이터(이미지, pdf, 직렬화된 객체)

**DATETIME** 데이터 타입은 존재하지 않아 **TEXT**, **REAL**, **INTEGER** 타입을 활용한다.
1) **TEXT**: ISO-8601 형식으로 저장(YYYY-MM-DD HH:MM:SS.SSS)
   ex) 2025-06-17 12:56:00
   => 사람이 읽기 쉽고, 표준화된 형식
2) **REAL**: Julian Day Number로 저장(기원전 4714년 11월 24일 정오부터의 일수)
   ex) 2460844.038194
   => 날짜 차이 등 계산에 용이, julian('2025-06-17 12:56:00')로 변환하여 저장
3) **INTEGER**: 유닉스 타임스탬프로 저장(1970-01-01 00:00:00 UTC부터 초 단위)
   ex) 1747580160
   => 저장 공간 효율적, 타임스탬프 쉬움, strftime('%s', '2025-06-17 12:56:00')로 변환하여 저장

## 3. CRUD SQL문
- **INSERT** 문
: INSERT INTO 테이블명 (입력할 Column명) VALUES(대응하는 Column 값)
- **SELECT** 문
: SELECT 조회할 열 FROM 테이블명 WHERE 조건문 ORDER BY 정렬할 열 LIMIT 시작행,행개수
- **UPDATE** 문
: UPDATE 테이블명 SET 설정할 열 WHERE 바꿀 열에 대한 조건
- **DELETE** 문
: DELETE FROM 테이블명 WHERE 지울 열에 대한 조건
