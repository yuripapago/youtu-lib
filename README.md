# youtu-lib — YouTube Knowledge Vault

YouTube 영상으로 배운 기술 지식을 **다시 꺼내 쓸 수 있는 마크다운 문서**로 축적하는 개인 지식 아카이브입니다.
영상을 한 번 보고 흘려보내지 않고, 나중에 검색·복습·실무 적용이 가능한 형태로 정리해 둡니다.

- **원본을 잃지 않는다** — 모든 문서 상단에 영상 제목·채널·원본 링크·런타임·공개일을 남깁니다.
- **타임스탬프로 되돌아간다** — 편·챕터 단위 타임스탬프를 본문에 붙여, 필요할 때 영상의 해당 지점으로 바로 점프합니다.
- **요약이 아니라 구조** — TL;DR → 타임라인 → 주제별 상세 → 완료 기준/실무 인사이트 순으로 정리해, 읽는 목적에 따라 필요한 깊이만 골라 읽습니다.
- **판단의 근거를 남긴다** — 영상이 제시한 수치·전제·한계를 함께 적어, 나중에 인용할 때 맥락을 잃지 않게 합니다.

---

## 아카이브 인덱스

| 분류 | 제목 | 영상 링크 | 런타임 | 작성일 |
| --- | --- | --- | --- | --- |
| AI 에이전트 | [일반 개발자를 위한 Agentic AI 엔지니어 12단계 (2시간 풀버전)](topics/agentic-ai/2026-11-11-agentic-ai-engineer-12-steps.md) | [KG3JLmcNrUI](https://youtu.be/KG3JLmcNrUI?si=VHtrPezoCBCqMzuD) | 2:09:19 | 2026-11-11 |
| AI 에이전트 | [AI 코딩의 마지막 문제 — 로렌 탄 × 맷 포콕: 미슐랭 주방 비유로 본 검증·스킬·야간 오토파일럿](topics/agentic-ai/2026-11-16-lauren-tan-ai-coding-kitchen.md) | [KC3mH9Zv_mc](https://youtu.be/KC3mH9Zv_mc) | 7:30 | 2026-11-16 |
| AI 에이전트 | [How Cursor Turned AI Agents Into Better Engineers — Maven 워크숍(로렌 탄) 챕터 지도와 주제별 정리](topics/agentic-ai/2026-11-16-lauren-tan-cursor-agents-better-engineers.md) | [Maven](https://maven.com/p/e23d9c/how-cursor-turned-ai-agents-into-better-engineers) | 1:00:00 | 2026-11-16 |
| AI 에이전트 | [Lauren Tan XAi Grokbot — 신뢰 곡선, 검증 스킬, Dune 아키텍처 (커서·그록봇 워크숍)](topics/agentic-ai/2026-11-16-lauren-tan-grokbot-workshop.md) | [Cmoh-yR-usA](https://www.youtube.com/watch?v=Cmoh-yR-usA) | 59:40 | 2026-11-16 |
| LLM·프롬프트 | [요즘 일잘러들은 PPT, HTML+모션그래픽으로 만듭니다 — 마스터 프롬프트와 초안→수정→모션 3단계](topics/llm/2026-11-22-html-slides-master-prompt.md) | [byShnblO4Oc](https://www.youtube.com/watch?v=byShnblO4Oc) | 15:12 | 2026-11-22 |

> 새 문서를 추가하면 이 표에 한 줄을 추가합니다. (분류는 `topics/` 아래 폴더명과 맞춥니다.)

---

## 저장소 구조

```
.
├── README.md                     # 이 문서: 소개 + 전체 인덱스
├── .gitignore
└── topics/
    └── <category>/               # 주제 분류 (예: agentic-ai, backend, llm, infra)
        └── YYYY-MM-DD-<slug>.md  # 영상 1편 = 문서 1개
```

### 분류(category) 예시

| 폴더 | 다루는 것 |
| --- | --- |
| `agentic-ai` | AI 에이전트 설계·평가·운영, 에이전트 프레임워크 |
| `llm` | 모델 활용, 프롬프트·컨텍스트 엔지니어링, RAG |
| `backend` | 언어·프레임워크, 비동기, 데이터, 아키텍처 |
| `infra` | 배포·컨테이너·관측·보안 |

---

## 문서 작성 규칙

각 문서는 아래 형식을 따릅니다.

```markdown
# 영상 제목

| 항목 | 내용 |
| --- | --- |
| 영상 제목 | … |
| 채널 | … (링크) |
| 원본 링크 | … |
| 런타임 | … |
| 공개일 | … |
| 정리일 | … |
| 태그 | `#…` |

## TL;DR — 3줄 요약
## 전체 타임라인 (표: 편 · 시작 · 주제 · 키워드)
## 본문 (챕터별: 질문 → 핵심 개념 → 실습/사례 → 완료 기준 → 한 줄 정리)
## 실무 인사이트 / 한계 / 추천 대상
```

- 파일명: `YYYY-MM-DD-<kebab-case-slug>.md` (정리일 기준)
- 타임스탬프는 영상 표기 그대로(`H:MM:SS`) 사용
- 자동 자막을 근거로 한 경우, 오인식 교정 사실과 **자막 기반임을 문서에 명시**
- 영상이 AI 생성 콘텐츠이거나 수치의 출처가 불분명하면 그 사실을 적는다
