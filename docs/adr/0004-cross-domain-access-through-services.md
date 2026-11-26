# 다른 도메인의 데이터는 그 도메인의 Service로 읽고, Entity끼리 연관 관계를 맺지 않는다

상태: accepted (2026-11-26)

`recommendation`과 `roadmap`은 지원자 프로필과 공고를 읽어야 한다. 다른 도메인의 데이터가 필요하면 그 도메인의 Service를 호출해 읽기 전용 객체로 받는다. Entity에는 상대의 ID 값만 두고, 도메인을 넘는 JPA 연관 관계(`@ManyToOne` 등)는 맺지 않으며, 다른 도메인의 Repository를 직접 쓰지 않는다. 호출 방향은 `recommendation`, `roadmap` → `profile`, `posting` 한쪽이고, `profile`과 `posting`은 서로 모른다.

도메인마다 밖에 제공하는 조회 메서드가 곧 공유 인터페이스가 되어, 티켓을 herdr 창별로 나눠 병렬로 구현할 때 이 메서드만 먼저 합의하면 된다. 다른 도메인이 Entity의 상태를 바꾸거나 테이블 구조에 묶이는 일도 막는다.

## Considered Options

- **다른 도메인의 Repository를 직접 사용**: 코드는 짧지만 상대 Entity를 직접 들게 되어 상태를 바꿀 수 있고, 테이블 구조 변경이 다른 도메인으로 번진다.
- **Entity끼리 JPA 연관 관계**: 조회는 편하지만 도메인 경계가 사라지고 병렬 작업에서 서로의 Entity를 건드리게 된다.

## Consequences

- 여러 도메인의 데이터를 한 화면에 보여 줄 때 조인 대신 Service 호출을 여러 번 한다. 1차의 데이터 양에서는 문제가 되지 않는다.
