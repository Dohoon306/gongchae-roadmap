# 패키지를 도메인별로 먼저 나누고, 그 안에 계층을 둔다

상태: accepted (2026-11-26)

최상위 패키지는 계층(controller, service …)이 아니라 도메인으로 나누고, 각 도메인 안에 `controller`, `dto`, `service`, `domain`, `repository`, `infra` 중 필요한 계층만 만든다. 의존 방향은 `controller → service → domain`이고 `repository`와 `infra`가 `domain`에 의존하며, `domain`은 다른 계층을 모른다. 도메인에 속하지 않는 공통 코드는 `global`(`config`, `exception`, 2차의 `security`)에 둔다.

한 기능을 고칠 때 한 패키지 안에서 끝나게 하려고 이렇게 정했다. 이 프로젝트는 티켓 하나를 여러 에이전트 세션이 병렬로 구현하므로, 도메인 경계가 패키지 경계와 같으면 세션끼리 같은 파일을 건드릴 일이 줄어든다.

## Considered Options

- **계층 우선 패키지**(`controller/`, `service/`, … 아래에 도메인별 클래스): 흔한 방식이지만 기능 하나가 여러 최상위 패키지에 흩어져 병렬 작업 충돌이 늘어난다.
