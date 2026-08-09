# Loop Foundry

반복 업무를 인터뷰해 재사용 가능한 AI 스킬, 판정 기준, train/heldout 사례, 안전한 자동화 레시피로 바꾸는 에이전트 스킬입니다.

하나의 `skills/loop-foundry/SKILL.md`를 Codex, Claude Code, Google Antigravity가 함께 사용합니다. 별도 API 키, Python, 웹서버가 필요하지 않습니다.

## 지원 환경

| 환경 | 설치 방식 | 실행 방법 |
| --- | --- | --- |
| Codex | GitHub 스킬 설치 | `$loop-foundry` |
| Claude Code | GitHub 플러그인 마켓플레이스 | `/loop-foundry:loop-foundry` |
| Google Antigravity | 워크스페이스 또는 전역 플러그인 | 프롬프트에서 `loop-foundry` 언급 |
| 그 밖의 파일 작업형 AI | 저장소 clone 후 `SKILL.md` 직접 읽기 | 자연어 요청 |

## Codex에서 설치

다음 문장을 Codex에 붙여넣습니다.

```text
$skill-installer를 사용해서 다음 스킬을 설치해줘:
https://github.com/shineonyou1274/loop-foundry/tree/main/skills/loop-foundry
```

새 대화에서 실행합니다.

```text
$loop-foundry 내 반복 업무를 인터뷰해서 재사용 가능한 스킬과 자동화 규칙으로 만들어줘.
```

## Claude Code에서 설치

Claude Code 안에서 다음 두 명령을 실행합니다.

```text
/plugin marketplace add shineonyou1274/loop-foundry
/plugin install loop-foundry@loop-foundry
```

설치 요약에서 새로고침을 요구하면 `/reload-plugins`를 실행합니다. 이후 다음처럼 시작합니다.

```text
/loop-foundry:loop-foundry 내 반복 업무를 인터뷰해서 재사용 가능한 스킬과 자동화 규칙으로 만들어줘.
```

설치하지 않고 한 번 시험하려면 저장소를 clone한 뒤 다음처럼 실행할 수도 있습니다.

```text
claude --plugin-dir ./loop-foundry
```

## Google Antigravity에서 설치

Antigravity에 다음 문장을 붙여넣습니다.

```text
다음 저장소를 clone해서 현재 워크스페이스의
.agents/plugins/loop-foundry 경로에 플러그인으로 설치해줘.
필요한 상위 폴더가 없으면 만들고, 기존 파일을 임의로 덮어쓰지 마.
https://github.com/shineonyou1274/loop-foundry
```

모든 워크스페이스에서 쓰려면 설치 경로를 `~/.gemini/config/plugins/loop-foundry`로 바꿉니다. 설치 후 새 대화를 열고 다음처럼 시작합니다.

```text
loop-foundry 스킬을 사용해서 내 반복 업무를 재사용 가능한 스킬과 자동화 규칙으로 만들어줘.
```

## 다른 코딩 에이전트에서 사용

저장소와 파일을 읽을 수 있는 AI에는 다음 문장을 줍니다.

```text
다음 GitHub 저장소를 clone해줘.
https://github.com/shineonyou1274/loop-foundry

skills/loop-foundry/SKILL.md와 그 파일이 직접 연결한 references를 읽고,
지침에 따라 내 반복 업무를 인터뷰해서 재사용 가능한 스킬과 자동화 규칙으로 만들어줘.
별도 API 키, Python, 웹서버는 요구하지 마.
```

AI가 저장소를 clone할 수 없다면 ZIP을 내려받아 대화에 첨부하고 같은 요청을 합니다.

## 만들어지는 결과

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
```

생성되는 `skill/SKILL.md`도 공통 Agent Skills 형식으로 작성하므로 세 환경에서 다시 설치하거나 공유할 수 있습니다. `agents/openai.yaml`은 OpenAI 환경의 선택적 UI 메타데이터이며, Claude Code와 Antigravity는 없어도 핵심 스킬을 실행할 수 있습니다.

## 중요한 원칙

- 스킬 생성 모델에 heldout 정답을 보여주지 않습니다.
- 스킬 자체가 아니라 스킬이 만든 산출물을 평가합니다.
- 외부 게시·전송·삭제·결제·원본 변경은 사용자 승인 전 실행하지 않습니다.
- 자동화 레시피는 항상 비활성 상태로 생성합니다.
