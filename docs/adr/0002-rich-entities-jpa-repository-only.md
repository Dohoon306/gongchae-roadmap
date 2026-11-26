# 규칙은 Entity 메서드에 두고, 데이터 접근은 Spring Data JPA Repository만 쓴다

상태: accepted (2026-11-26)

자기 필드만으로 판단하거나 상태를 바꾸는 규칙은 Entity의 메서드로 쓰고 setter는 열지 않는다. Service는 조회, 도메인 객체 호출, 저장, 트랜잭션 같은 흐름만 맡는다. Controller는 DTO만 주고받고 Entity를 밖으로 내보내지 않는다. 데이터 접근은 Spring Data JPA Repository로만 하고 DAO 계층은 두지 않는다.

규칙이 Service에 흩어지면 같은 판단이 여러 곳에 복사되고, setter가 열려 있으면 어디서든 Entity를 규칙에 어긋난 상태로 만들 수 있다. Repository가 이미 데이터 접근 추상화이므로 DAO를 하나 더 두면 같은 역할의 계층이 둘이 된다.

## Considered Options

- **Service에 규칙을 두고 Entity는 getter/setter만**(빈약한 도메인 모델): 처음엔 빠르지만 규칙이 늘수록 Service가 비대해진다.
- **Repository 위에 DAO 계층 추가**: 쿼리를 감추는 이점보다 계층 중복 비용이 크다.
