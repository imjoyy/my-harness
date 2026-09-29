# 허들링 Codex 하네스

## 목적

허들링의 디자이너가 매번 달라지는 프로젝트 요구사항·페이지 콘텐츠·참고 레퍼런스를 입력하면, 필수 산출물 3개를 만들고 요구사항 미반영 항목 0개를 확인하여 작업을 완료한다.

최종 산출물은 다음 3개다.

1. 메인 페이지 디자인 시안
2. 화면 구성안 PPT
3. 개발자 소통용 PPT

## 기준 문서

작업 전에 다음 파일을 읽는다.

- `story-service.md`: 서비스 목적과 R0-A 판정 기준
- `story-work.md`: 사람이 수행했던 실제 작업 흐름
- `r2-purpose.md`: 하네스 목적과 완료 기준
- `r3-pipeline.md`: 단계별 입출력과 복귀 지점
- `r4-artifacts.md`: 산출물 경로와 재개 규칙
- `r5-gates.md`: 기계 게이트와 사람 승인
- `r6-roles.md`: 역할과 편집 권한
- `r7-orchestrator.md`: 실행 순서와 정지 조건
- `r8-validation.md`: 판정 스크립트와 리뷰 규칙
- `design.md`: 디자인 규칙의 유일한 SSOT
- `prd.md`: 제품 요구사항 원문

`story-service.md`의 R0-A 판정 기준을 작업 재료와 섞지 않는다. R0-A는 결과물의 통과·실패를 판정할 때만 사용한다.

## 산출물 구조

```text
work/
├── 01-input/
│   ├── content-plan.md
│   └── references.md
├── 02-concept/
│   └── design-concept.md
├── 03-design/
│   └── main-page-design.html
└── 04-handoff/
    ├── screen-spec.pptx
    ├── developer-handoff.pptx
    └── requirements-checklist.json

.harness/
├── run-state.json
├── human-approval.json
└── reports/
```

## 역할과 편집 권한

| 역할 | 담당 | 유일한 편집 폴더 |
|---|---|---|
| 입력 기획 에이전트 | 콘텐츠 기획·레퍼런스 정리 | `work/01-input/` |
| 콘셉트 에이전트 | 디자인 콘셉트 선정 | `work/02-concept/` |
| 디자인 에이전트 | 메인 페이지 디자인 제작 | `work/03-design/` |
| 인계 문서 에이전트 | PPT·체크리스트 작성 | `work/04-handoff/` |
| 읽기 전용 판정자 | 게이트 판정 | `.harness/reports/` |

각 작업 에이전트는 자신의 편집 폴더 한 곳만 수정한다. 이전 단계 결과와 규칙 파일은 읽기만 한다.

판정자는 `work/` 아래 파일을 수정하지 않는다. 판정 결과만 `.harness/reports/`에 기록한다.

자기 폴더 밖의 오류를 발견하면 직접 수정하지 않고 오류 파일, 실패 원인, 복귀 단계를 판정 결과에 기록한다.

## 실행 순서

```text
입력 기획 에이전트 → G1
  → 콘셉트 에이전트 → G2
  → 디자인 에이전트 → G3
  → 사람 승인에서 정지
  → 승인 후 인계 문서 에이전트 → G4
  → 완료
```

G1과 G2를 통과하면 다음 단계로 자동 진행한다.

G3를 통과하면 반드시 정지하고 디자이너의 승인을 기다린다. `status=approved`일 때만 4단계를 시작한다. `status=rejected`이면 3단계로 복귀한다.

## 게이트

### G1 입력

다음 조건을 모두 만족해야 한다.

- `work/01-input/content-plan.md`가 존재한다.
- `work/01-input/references.md`가 존재한다.
- 참고 레퍼런스 수가 3개 이상이다.

실패하면 1단계로 복귀한다.

### G2 콘셉트

다음 조건을 모두 만족해야 한다.

- `work/02-concept/design-concept.md`가 존재한다.
- 선택된 콘셉트 수가 정확히 1개다.

실패하면 2단계로 복귀한다.

### G3 디자인

다음 조건을 모두 만족해야 한다.

- `work/03-design/main-page-design.html`이 존재한다.
- HTML 파싱 오류 수가 0개다.
- 필수 섹션 누락 수가 0개다.
- 요구사항 미반영 수가 0개다.
- R0-A 위반 수가 0개다.
- R8 디자인 검증 실패 수가 0개다.

통과 후 사람 승인 지점에서 정지한다. 실패 또는 승인 거절 시 3단계로 복귀한다.

### G4 인계

다음 조건을 모두 만족해야 한다.

- `work/04-handoff/screen-spec.pptx`가 존재한다.
- `work/04-handoff/developer-handoff.pptx`가 존재한다.
- `work/04-handoff/requirements-checklist.json`이 존재한다.
- 미반영 요구사항 수가 0개다.
- R0-A 위반 수가 0개다.

실패하면 4단계로 복귀한다.

## R0-A 필수 판정

다음 조건 중 하나라도 실패하면 즉시 실행을 중단한다.

- 멤버 자산의 `visibility` 누락 수는 0이어야 한다.
- `visibility` 기본값은 `private`여야 한다.
- `visibility=member` 또는 `sale`이면 `owner_consent=true`여야 한다.
- 개인정보 탐지 수는 0이어야 한다.
- 고객정보 탐지 수는 0이어야 한다.
- 회사기밀 탐지 수는 0이어야 한다.
- 권리 미확인 타인 저작물 수는 0이어야 한다.

R0-A 위반은 자동 재시도하지 않는다. 위반 항목과 발생 파일을 사용자에게 보고한다.

## 판정 명령

```text
scripts/validate_harness.py --gate G1|G2|G3|G4
```

- 통과 종료 코드: `0`
- 실패 종료 코드: `1`
- 결과 파일: `.harness/reports/<gate>.json`
- 필수 필드: `gate`, `status`, `checks`, `failure_count`, `return_stage`
- `status` 허용값: `passed`, `failed`
- `failure_count=0`일 때만 통과한다.

## 디자인 검증

다음 조건을 모두 만족해야 한다.

- `390×844`에서 렌더링에 성공한다.
- `360px` 너비에서 렌더링에 성공한다.
- 가로 스크롤 발생 수는 0이다.
- 44px 미만 터치 대상 수는 0이다.
- 360px에서 2열 이상으로 남은 그리드 수는 0이다.
- 드롭 섀도 사용 수는 0이다.
- CTA의 액센트 블루 사용 수는 0이다.
- 페이지당 액센트 블루 요소 수는 0~2다.
- 허용되지 않은 UI 액센트 색상 수는 0이다.
- 제목의 Pretendard 700 미적용 수는 0이다.
- 자간이 0이 아닌 텍스트 스타일 수는 0이다.
- 사각형 버튼 수는 0이다.
- 휴먼 인물 컬러 사진 수는 0이다.

태블릿과 데스크톱은 MVP 검증 범위에서 제외한다.

## 사람 승인

사람 승인 지점은 G3 통과 후 한 곳만 둔다.

판정자는 다음 자료 3개를 준비한다.

1. G3 판정 보고서
2. `390×844` 화면 캡처
3. `360px` 화면 캡처

디자이너가 세 자료를 함께 검토해 승인 또는 거절한다.

승인 상태는 `.harness/human-approval.json`에 기록하며 `pending`, `approved`, `rejected`만 허용한다.

## 실패와 재시도

- 게이트 실패 시 해당 단계의 담당 에이전트로 복귀한다.
- 게이트별 자동 재시도는 최대 2회다.
- 같은 게이트가 3번째 실패하면 실행을 중단하고 사용자에게 보고한다.
- R0-A 위반은 재시도 횟수와 관계없이 즉시 중단한다.
- `passed` 단계의 산출물은 다시 만들지 않는다.
- `failed` 단계와 이후 단계만 다시 실행한다.

## 실행 상태

`.harness/run-state.json`에는 다음 항목을 기록한다.

- `current_stage`
- 단계별 `status`
- `inputs`
- `outputs`
- `updated_at`
- 게이트별 재시도 횟수

단계 상태는 `pending`, `in_progress`, `passed`, `failed`만 허용한다.

오케스트레이터는 `.harness/run-state.json`과 `.harness/human-approval.json`만 직접 수정할 수 있다.

## 자연어 트리거

| 요청 | 동작 |
|---|---|
| “전체 작업을 시작해줘” | 1단계부터 실행 |
| “이어서 진행해줘” | 마지막 `passed` 단계 다음부터 재개 |
| “현재 상태를 보여줘” | 현재 실행 상태 보고 |
| “입력을 정리해줘” | 입력 기획 에이전트 실행 |
| “콘셉트를 만들어줘” | 콘셉트 에이전트 실행 |
| “메인 페이지를 디자인해줘” | 디자인 에이전트 실행 |
| “인계 문서를 만들어줘” | 인계 문서 에이전트 실행 |
| “검사해줘” 또는 “판정해줘” | 읽기 전용 판정자 실행 |
| “시안을 승인해” | 승인 상태를 `approved`로 기록하고 4단계 실행 |
| “시안을 거절해” | 승인 상태를 `rejected`로 기록하고 3단계 복귀 |
