# practice

공공기관 취준생용 채용 공고 추천·준비 로드맵 웹사이트.

## 문서

- 무엇을 만드는지, 범위, 기술 선택을 판단할 때: `docs/01-idea.md`
- 공고 데이터(잡알리오 API, 샘플 데이터)를 다룰 때: `docs/research/job-data-sources.md`
- 1차 범위에서 무엇을 어떻게 만들기로 했는지 확인할 때: `docs/02-design-decisions.md`
- 백엔드 코드를 쓰거나 검토할 때(패키지, 계층, 도메인 사이 규칙): `docs/architecture.md`
- 화면을 만들거나 고칠 때(화면 구성, 색과 글꼴, 상태별 모습, 문구): `docs/03-design.md`. 시안 그림은 `docs/design/`

## 작업 흐름

계획은 mattpocock-skills, 실행은 pstack이 맡는다.

1. 설계 질문과 용어 정리: `/mattpocock-skills:grill-with-docs`
2. 명세: `/mattpocock-skills:to-spec`
3. 티켓 나누기: `/mattpocock-skills:to-tickets`. 티켓은 기능 하나를 DB, API, 화면, 테스트까지 관통하는 수직 조각이다.
4. 티켓 구현: `/pstack:poteto-mode`
5. 검토: `/pstack:interrogate`

## 규칙

- 결정과 진행 상황은 대화가 아니라 파일에 남긴다. 이 프로젝트는 컨텍스트 60%에서 자동 compact되어 대화 내용이 요약으로 바뀐다.
- 티켓은 실행해서 확인한 증거(테스트 출력, 화면 동작)가 있을 때 완료다.
- API 키와 비밀번호는 환경 변수로 읽고, 그 값이 든 파일은 `.gitignore`에 둔다. 커밋할 때 `.githooks/pre-commit`이 비밀 값을 검사한다. 막히면 값을 환경 변수로 옮기고 다시 커밋한다.
- 시험용 이력서와 자기소개서는 지어낸 내용만 쓴다. 실제 개인정보는 저장소에 넣지 않는다.
- 저장소를 새로 받으면 `git config core.hooksPath .githooks`를 한 번 실행한다.
- 커밋과 푸시는 사용자가 요청할 때 한다.

## Agent skills

### Issue tracker

명세와 티켓은 GitHub Issues(`Dohoon306/gongchae-roadmap`)에 둔다. `docs/agents/issue-tracker.md` 참고.

### Triage labels

기본 라벨 다섯 개(`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`)를 쓴다. `docs/agents/triage-labels.md` 참고.

### Domain docs

단일 구조: 루트의 `GLOSSARY.md`와 `docs/adr/`. `docs/agents/domain.md` 참고.
