# 백엔드 구조

상태: 확정 (2026-11-26). 근거는 ADR [0001](adr/0001-domain-first-packages.md), [0002](adr/0002-rich-entities-jpa-repository-only.md), [0003](adr/0003-external-integrations-behind-domain-interfaces.md), [0004](adr/0004-cross-domain-access-through-services.md).

Spring Boot 백엔드의 패키지 구조와 계층 규칙이다. 설계 결정의 경위는 `docs/02-design-decisions.md`에 있다. 용어는 루트의 `GLOSSARY.md`를 따른다.

## 패키지

저장소는 `backend/`(Spring Boot, Gradle, Java 17)와 `frontend/`(React, Vite, TypeScript)로 나뉜다. 아래는 `backend/src/main/java/` 아래의 구조다.

```
com/gongchae/roadmap/
├── {도메인}/
│   ├── controller/   HTTP 요청을 받아 DTO로 주고받는다
│   ├── dto/          요청·응답 객체, 다른 도메인에 주는 읽기 전용 객체
│   ├── service/      흐름: 조회 → 도메인 객체 호출 → 저장, 트랜잭션
│   ├── domain/       Entity, 값 객체, 외부 연동 인터페이스
│   ├── repository/   Spring Data JPA Repository
│   └── infra/        외부 연동 구현체 (샘플 데이터, 실제 API)
└── global/
    ├── config/       AI 클라이언트 설정 포함
    ├── exception/
    └── security/     2차 (member 도메인과 함께)
```

- 도메인별로 먼저 나누고, 그 안에 계층을 둔다.
- 필요한 계층만 만든다. 외부 연동이 없는 도메인에는 `infra`가 없다.
- 도메인에 속하지 않는 공통 코드만 `global`에 둔다.

## 의존 방향

```
controller → service → domain
                 ↘
         repository → domain
              infra → domain
```

- `controller`는 `service`를 호출한다.
- `service`는 `domain`의 객체와 인터페이스, 그리고 `repository`를 쓴다. 외부 연동은 `domain`의 인터페이스로만 부르고 `infra` 구현체를 직접 참조하지 않는다.
- `repository`와 `infra`는 `domain`에 의존한다.
- `service`와 `controller`는 `dto`를 쓴다.
- `domain`은 `controller`, `dto`, `service`, `repository`, `infra`를 모른다. Entity에 붙는 JPA 매핑 어노테이션은 예외로 허용한다.

## 계층별 규칙

### domain

- 자기 필드만으로 판단하거나 상태를 바꾸는 규칙은 Entity의 메서드로 쓴다.
- setter를 열지 않는다. 상태는 의미 있는 이름의 메서드로만 바꾼다.
- 외부 연동(잡알리오 공고 조회, AI 호출)의 인터페이스를 여기에 둔다. 인터페이스의 인자와 반환값은 도메인 객체다.

### service

- 조회, 도메인 객체 호출, 저장, 트랜잭션 같은 흐름만 맡는다.
- 규칙을 직접 판단하지 않고 도메인 객체에 묻는다.
- 공개 메서드는 Entity를 돌려주지 않는다. `dto` 패키지의 객체로 바꿔 돌려주며, 변환은 트랜잭션 안에서 끝낸다.

### controller, dto

- Controller는 DTO만 받고 DTO만 돌려준다. Entity를 밖으로 내보내지 않는다.
- Entity를 DTO로 바꾸는 코드는 DTO 쪽에 둔다. DTO는 Entity를 알고, Entity는 DTO를 모른다.
- 화면에 보내는 응답 객체와 다른 도메인에 주는 읽기 전용 객체는 이름으로 구분하고 함께 쓰지 않는다.

### repository

- 데이터 접근은 Spring Data JPA Repository만 쓴다. DAO는 두지 않는다.

### infra

- 외부 연동 인터페이스의 구현체를 둔다. 한 인터페이스에 샘플 데이터 구현체와 실제 API 구현체를 함께 두고 설정으로 교체한다.
- AI 호출 인터페이스(`profile`, `recommendation`, `roadmap`에 하나씩)도 같다. OpenAI 구현체와 가짜 구현체를 함께 두며, 기본은 가짜 구현체이고 OpenAI 키가 환경 변수에 있을 때만 실제 구현체가 켜진다.
- OpenAI와 통신하는 공통 코드는 `global`에 한 번만 둔다(Spring AI). 각 도메인의 OpenAI 구현체는 질문 만들기와 답을 도메인 객체로 바꾸기만 맡는다.
- 외부 응답 형식은 구현체 안에서 도메인 객체로 바꾼다. 외부 형식의 클래스는 `infra` 밖으로 나가지 않는다.
- API 키는 환경 변수로 읽는다.

## 도메인 사이

근거: ADR [0004](adr/0004-cross-domain-access-through-services.md).

```
recommendation ─┬─→ profile
roadmap ────────┴─→ posting
```

- 다른 도메인의 데이터가 필요하면 그 도메인의 Service를 호출한다. 다른 도메인의 Repository를 직접 쓰지 않는다.
- Service는 다른 도메인에 Entity가 아니라 읽기 전용 객체를 돌려준다.
- Entity에는 다른 도메인의 ID 값만 둔다. 도메인을 넘는 JPA 연관 관계는 맺지 않는다.
- 호출 방향은 위 그림의 한쪽뿐이다. `profile`과 `posting`은 서로 모르고, `recommendation`과 `roadmap`도 서로 모른다.

## 도메인 목록

| 도메인 | 맡는 것 | 단계 |
|---|---|---|
| profile | 입력(직무 분류, 이력서·자기소개서)으로 지원자 프로필을 만들어 저장한다. 이력서 분석용 AI 호출 인터페이스와 구현체를 가진다 | 1차 |
| posting | 외부 출처에서 공고를 수집해 저장하고, 공고를 조회한다. 외부 출처 인터페이스와 구현체(샘플 데이터, 잡알리오)를 가진다 | 1차 |
| recommendation | 지원자 프로필에 맞는 공고를 골라 순위와 이유를 붙여 저장한다. 프로필과 공고는 ID로만 참조한다 | 1차 |
| roadmap | 지원자 프로필이 고른 공고들로 통합 로드맵을 만들어 저장한다. 프로필과 공고는 ID로만 참조하고 `recommendation`은 참조하지 않는다 | 1차 |
| member | 회원 가입, 로그인 | 2차 |
