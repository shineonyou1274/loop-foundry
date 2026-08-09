# 산출물 계약

## project.yaml

```yaml
name: "업무 이름"
slug: "workflow-name"
summary: "입력에서 산출물을 만드는 일 한 줄"
trigger: "업무 시작 신호"
frequency: "반복 주기"
status: "draft | testing | accepted"
evidence_level: "interview-only | examples | heldout-verified"
```

## seed.yaml

```yaml
goal: "좋은 산출물의 목표"
constraints:
  - "반드시 지킬 조건"
out_of_scope:
  - "하지 않을 일"
acceptance_criteria:
  - id: "ac_1"
    description: "산출물에서 관찰 가능한 통과 조건"
    judge_type: "verify | code | judge"
    verify_command: "선택 사항"
```

`verify_command`는 실제로 존재하고 실행할 수 있을 때만 넣는다.

## tasks.json

```json
[
  {
    "id": "t1",
    "split": "train",
    "input": "입력 파일 또는 설명",
    "expected_signals": ["판정에 필요한 신호"],
    "ac_ids": ["ac_1"]
  },
  {
    "id": "h1",
    "split": "heldout",
    "input": "처음 보는 시험 입력",
    "expected_signals": ["격리해 보존할 판정 신호"],
    "ac_ids": ["ac_1"]
  }
]
```

## STATUS.md

다음을 짧게 기록한다.

- 마지막 완료 단계
- 다음 단계
- 확보한 train/heldout 수
- gate 결과
- 독립 검증 여부와 제한
- 사용자 승인이 필요한 항목

## automation-recipe.yaml

```yaml
name: "자동화 이름"
enabled: false
trigger: "시작 신호"
schedule_suggestion: "추천 주기 또는 수동"
inputs:
  - "필요한 입력"
workflow:
  - "실행 단계"
guards:
  - "안전 규칙"
approval_required_before:
  - "외부 게시·전송·삭제·결제·원본 변경"
completion:
  - "완료 조건"
failure_policy: "검증 실패 시 변경을 중단하고 근거만 보고한다."
```

항상 `enabled: false`로 생성한다. 예약 또는 실행 활성화는 사용자 승인 뒤에 한다.

## 생성 스킬 호환성

`skill/SKILL.md`는 공통 Agent Skills 형식으로 작성한다.

- frontmatter에는 `name`과 `description`만 둔다.
- 지침과 보조 파일 연결에는 스킬 폴더 기준 상대 경로를 사용한다.
- `$skill-name`, `/skill-name` 같은 특정 호스트의 호출 문법을 핵심 절차에 넣지 않는다.
- `agents/openai.yaml`은 선택적 OpenAI UI 메타데이터로 취급한다. 이 파일이 없어도 핵심 스킬이 작동해야 한다.
