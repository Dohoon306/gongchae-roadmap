# practice

공공기관 취준생용 채용 공고 추천·준비 로드맵 웹사이트. herdr로 여러 에이전트 세션을 병렬로 돌리는 연습을 겸한다.

## 문서

- 무엇을 만드는지, 범위, 기술 선택을 판단할 때: `docs/01-idea.md`
- 공고 데이터(잡알리오 API, 샘플 데이터)를 다룰 때: `docs/research/job-data-sources.md`

## 작업 흐름

계획은 mattpocock-skills, 실행은 pstack이 맡는다.

1. 설계 질문과 용어 정리: `/mattpocock-skills:grill-with-docs`
2. 명세: `/mattpocock-skills:to-spec`
3. 티켓 나누기: `/mattpocock-skills:to-tickets`. 티켓은 기능 하나를 DB, API, 화면, 테스트까지 관통하는 수직 조각이다.
4. 티켓 구현: herdr 창 하나에 티켓 하나, 그 안에서 `/pstack:poteto-mode`
5. 검토: `/pstack:interrogate`

병렬 구현은 herdr 창으로 나눈다. `implement-spec`과 `swarm`은 창이 보이지 않아 이 프로젝트에서는 쓰지 않는다.

## 세션 역할

- **오케스트레이션 세션**: 계획, 티켓 배정, 서브 세션 결과 확인, 병합을 맡는다. 서브 세션의 보고는 파일과 `git`으로 직접 확인한 뒤 받아들인다.
- **서브 세션**: 배정받은 티켓 하나만 구현한다. 선행 티켓이 모두 끝난 티켓만 시작한다.

## 규칙

- 결정과 진행 상황은 대화가 아니라 파일에 남긴다. 이 프로젝트는 컨텍스트 60%에서 자동 compact되어 대화 내용이 요약으로 바뀐다.
- 티켓은 실행해서 확인한 증거(테스트 출력, 화면 동작)가 있을 때 완료다.
- 다른 티켓과 함께 쓰는 인터페이스를 바꿔야 하면 구현을 멈추고 오케스트레이션 세션에 알린다.
- API 키와 비밀번호는 환경 변수로 읽고, 그 값이 든 파일은 `.gitignore`에 둔다. 커밋할 때 `.githooks/pre-commit`이 비밀 값을 검사한다. 막히면 값을 환경 변수로 옮기고 다시 커밋한다.
- 저장소를 새로 받으면 `git config core.hooksPath .githooks`를 한 번 실행한다.
- 커밋과 푸시는 사용자가 요청할 때 한다.
