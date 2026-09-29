# R4 산출물

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
└── run-state.json
```

## 단계별 필수 파일

| 단계 | 필수 파일 | 완료 판정 |
|---|---|---|
| 1. 입력 정리 | `content-plan.md`, `references.md` | 파일 2개가 모두 존재 |
| 2. 콘셉트 기획 | `design-concept.md` | 파일 1개가 존재 |
| 3. 디자인 제작 | `main-page-design.html` | 파일 1개가 존재 |
| 4. 문서화·검토 | `screen-spec.pptx`, `developer-handoff.pptx`, `requirements-checklist.json` | 파일 3개가 모두 존재 |

## 규칙 SSOT

- 디자인 규칙의 유일한 SSOT는 루트의 `design.md`다.
- 다른 파일에는 디자인 규칙을 복제하지 않고 `design.md`의 경로만 참조한다.

## 실행 상태

- `.harness/run-state.json`에는 `current_stage`, 단계별 `status`, `inputs`, `outputs`, `updated_at`을 기록한다.
- 단계 상태는 `pending`, `in_progress`, `passed`, `failed`만 허용한다.

## 재개 규칙

- 재실행 시 `passed` 단계의 파일은 유지한다.
- `failed` 단계와 그 이후 단계만 다시 생성한다.
- 중단된 실행은 마지막 `passed` 단계 다음부터 재개한다.
