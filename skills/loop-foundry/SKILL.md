---
name: loop-foundry
description: "Turn a person's repeated work into a reusable AI skill, acceptance criteria, test cases, and a safe automation recipe through a guided Korean conversation. Use when the user asks to extract their workflow, make a personal skill, automate repeated work, apply loop engineering, create evaluation rules, or package a workflow for reuse or sharing. Do not use merely to perform a one-off task."
---

# Loop Foundry

사용자의 반복 업무를 인터뷰하고 실제 사례를 분석해 재사용 가능한 스킬과 자동화 규칙 한 벌로 만든다. API 키나 별도 앱을 요구하지 말고 현재 AI의 파일·추론·도구 능력을 사용한다. Codex, Claude Code, Antigravity에서 공통으로 실행할 수 있는 Agent Skills 형식을 유지한다.

## 시작

새 작업이면 `references/interview-guide.md`를 읽는다. 이어 사용자에게 한 번에 한 질문씩 묻고 답을 현재 대화와 작업 폴더에 누적한다.

기존 Loop Foundry 폴더가 있으면 `project.yaml`과 `STATUS.md`를 먼저 읽고 중단 지점부터 이어간다. 완료된 단계를 반복하지 않는다.

작업 위치가 정해지지 않았다면 현재 작업 폴더 아래 `loop-projects/<workflow-name>/`을 사용한다고 알리고 진행한다. 다른 위치를 원하는 경우에만 바꾼다.

## 산출물

다음 구조를 만든다.

```text
loop-projects/<workflow-name>/
├── project.yaml
├── seed.yaml
├── checklist.md
├── tasks.json
├── automation-recipe.yaml
├── STATUS.md
├── skill/
│   ├── SKILL.md
│   └── agents/openai.yaml
└── runs/
    ├── train/
    └── heldout/
```

파일 형식은 `references/output-contract.md`를 따른다. 템플릿이 필요하면 `assets/`의 파일을 복사해 채운다.

## 1. 업무를 수확한다

다음을 대화로 확인한다.

1. 반복해서 수행하는 일 한 줄
2. 시작 신호와 반복 주기
3. 실제 입력과 최종 산출물
4. 잘된 결과와 실패한 결과의 차이
5. 외부 전송·수정·게시처럼 승인이 필요한 행동

가능하면 실제 `입력 → 산출물` 쌍을 받는다. 최소 train 1개와 heldout 1개를 확보한다. 사례가 부족해도 중단하지 말고 현재 증거 수준을 `STATUS.md`에 표시한다.

heldout 산출물은 사용자가 제공하더라도 별도 위치에 격리한다. 스킬을 작성하거나 수정할 때 heldout 산출물 내용을 다시 읽지 않는다.

## 2. 성공 기준을 확정한다

`seed.yaml`을 판정 기준의 유일한 원천으로 만든다. 기준마다 `ac_id`를 부여하고 다음 중 하나로 분류한다.

- `verify`: 이미 존재하는 테스트 명령이나 공식 검증 절차
- `code`: 개수, 필수 항목, 길이, 값처럼 결정적으로 확인 가능한 것
- `judge`: 의미, 정합성, 품질처럼 독립 판단이 필요한 것

기준을 산출물에서 관찰 가능한 문장으로 쓴다. 같은 산출물을 두 번 판단했을 때 결론이 달라질 기준은 게이트에서 제외하고 `(육안)`으로 표시한다.

기준 초안을 사용자에게 보여주고 승인받은 뒤 다음 단계로 간다.

## 3. 사례를 분리한다

`tasks.json`에 `id`, `split`, `input`, `expected_signals`, `ac_ids`를 기록한다.

- `input`에는 업무 입력만 넣는다.
- `expected_signals`에는 정답 전문이 아니라 판정에 필요한 신호만 넣는다.
- train 사례는 절차를 고치는 데 사용한다.
- heldout 사례는 마지막 비교에만 사용한다.
- 근거가 없는 채점 항목은 새로 만들지 않는다. 필요한 경우 seed 기준으로 되돌려 사용자 승인을 받는다.

## 4. 스킬 초안을 만든다

`skill/SKILL.md`에는 일을 수행하는 방법만 적는다. 체크리스트 문장을 복사하거나 특정 사례의 정답·고유명사·수치를 넣지 않는다.

각 지침을 명령형 절차로 쓴다.

- 나쁜 예: `목표와 평가가 일치해야 한다.`
- 좋은 예: `최종 산출물을 먼저 정의하고 각 구성요소를 평가기준에 연결한 뒤 활동을 역산한다.`

스킬 이름은 영문 kebab-case, 64자 이하로 정한다. description에는 수행 업무와 발동 상황을 함께 적는다.

생성되는 `SKILL.md`의 핵심 절차에는 특정 호스트의 호출 문법, 전용 환경 변수, 절대 경로를 요구하지 않는다. 공통 frontmatter는 `name`과 `description`만 사용하고, 호스트별 UI 메타데이터나 설치 파일은 핵심 스킬 밖에 둔다. 보조 파일은 스킬 폴더 기준 상대 경로로 연결한다.

## 5. 루프를 실행한다

다섯 단계를 순서대로 수행한다.

1. `RUN`: 스킬과 train 입력만 사용해 산출물을 만든다.
2. `JUDGE`: seed 기준으로 산출물만 판정한다.
3. `TRACE`: 실패한 기준의 기대·실제·근거를 기록한다.
4. `EDIT`: 실패 근거를 일반 작업 절차로 바꿔 스킬을 수정한다.
5. `GATE`: 기존 스킬과 후보 스킬을 heldout 입력에 각각 실행해 비교한다.

작업자에게 seed, checklist, expected, heldout 산출물을 보여주지 않는다. 검증자에게 스킬 문서를 보여주지 않는다. 같은 AI가 두 역할을 맡아야 한다면 각 역할의 입력을 별도 파일과 새 컨텍스트로 격리하고, 완전한 독립 검증이 아니라는 제한을 `STATUS.md`에 남긴다.

후보가 heldout에서 실제로 좋아졌을 때만 accept한다. 같거나 나빠지면 reject한다. heldout 정보가 스킬에 유출되면 점수와 관계없이 reject한다.

## 6. 자동화 규칙을 만든다

`automation-recipe.yaml`에 다음을 포함한다.

- 시작 신호 또는 추천 주기
- 필요한 입력과 접근 범위
- 실행 단계와 완료 조건
- 원본 보존·개인정보·사실 확인 규칙
- 사람 승인이 필요한 행동
- 실패 시 중단·보고 정책

예약 작업을 실제로 만들거나 외부 시스템을 변경하지 않는다. 먼저 레시피를 보여주고 사용자의 명시적 승인을 받는다. 사용 환경에 예약 기능이 있으면 승인 뒤 그 환경의 공식 도구로 생성한다.

## 7. 인수 검사와 전달

완료 전에 다음을 확인한다.

- 스킬이 heldout 정답이나 특정 사례를 암기하지 않았는가
- 판정 경로가 스킬 문서 자체를 점수화하지 않는가
- gate가 heldout만 비교하는가
- 승인 없는 외부 전송·게시·삭제·결제가 막혀 있는가
- 스킬 단독이 아니라 seed, tasks, checklist, automation recipe가 함께 보존되는가

마지막에는 다음 세 항목을 사용자에게 제공한다.

1. 생성된 스킬을 Codex, Claude Code, Antigravity에서 부르는 방법
2. 다음 실제 업무에서 추가할 `입력 → 산출물` 쌍
3. heldout을 교체하거나 늘릴 권장 시점
