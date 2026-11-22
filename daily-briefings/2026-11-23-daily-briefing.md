# 2026-11-23 (월) 데일리 테크 브리핑

> **수집** 2026-11-23 08:15 KST · 최근 24시간(2026-11-22 08:15 ~ 11-23 08:15 KST = 11-21 23:15 ~ 11-22 23:15 UTC) 중심
> **소스** Hacker News(Algolia 날짜 검색·아이템 API로 댓글 직접 확인), GeekNews RSS, spring.io RSS, GitHub Releases/PR/Milestone API, openai.com, openrouter.ai, dragos.com, together.ai, docs.spring.io, simonwillison.net, herbsutter.com, YouTube 채널 페이지(@codedeck·@OpenAI·@GoogleDeepMind HTML 파싱), GitHub @yuripapago API
> **오늘의 한 줄** — *"어제 '프롬프트에 카나리를 심어라'라고 썼는데, 오늘 그 카나리 200만 개가 3개 업체의 침해와 125개 엔드포인트의 ZDR 박탈을 증명해 왔다. 그리고 그 침해의 입구 중 하나는 **AI 코딩 에이전트가 공개 레포에 커밋한 자격증명**이었다."*
> ※ GeekNews 웹(topic 페이지)은 여전히 **403**(RSS 요약만 수집), Reddit·Ars Technica **403**. YouTube 채널은 RSS 대신 채널 페이지를 직접 파싱해 "최근 업로드 없음"을 확인했다.

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **카나리 200만 개가 AI 추론 공급망을 뚫었다 — Dragos 5개월 추적 → OpenRouter 3개 제공자 퇴출·ZDR 125개 제거, Together는 "AI 코딩 에이전트가 자격증명을 커밋했다"고 인정** (11-22 · HN 277점+97점+47점)

어제 브리핑 4번(OpenRouter→MiniMax 열람 통보)의 **다음 장**이 24시간 안에 세 갈래로 나왔다. 이번엔 추측이 아니라 **연구 보고서와 제공자 자백**이다.

| 항목 | 내용 |
| --- | --- |
| 실험 | Dragos(HD Moore, 보고서 **11-19 공개**)가 **2026-07~11, 약 200만 건**의 프롬프트에 **고유 URL + 카나리 자격증명**을 심어 추론 후 어디서 쓰이는지 추적 |
| 발견 ① | **AI 호스팅 3곳 침해** — 가장 큰 건은 **AWS 키 수백 개**가 전부 털림(10월 말 짧은 구간). **모델이 실행되기 전 거부된 요청**의 키까지 털려 → 공격자가 **추론 스택 안쪽이 아니라 입구(front door)에서 복사**하고 있었다 |
| 발견 ② | **마켓플레이스 리셀러의 프롬프트 탈취** — 취약점 공격 없이 **"최저가로 모델 등록 + 요청 로깅 켜기"** 만으로 고객 프롬프트를 확보. 가정용 회선에서 우리 카나리 서버에 로그인 시도까지 했다 |
| 발견 ③ | **ZDR(무보관) 설정 무시** — ZDR 표시 요청도 유출. 카나리 링크가 **중국 사내 채팅**에 나타난 사례, 요청 몇 달 뒤에 열린 사례 포함 |
| 공격자 도구 | Chrome 위장 스크립트 → **OrbStack 안의 Boto3** → **Kali의 AWS CLI**로 3주간 진화. **11-01부터 ARTEX(LLM 기반 침투 테스트 프레임워크)** 추가. user-agent `artex-agent`·`artex-judge-r121`는 공개 IOC 목록에 없음 |

**제공자·라우터의 대응(같은 날 공개)**

| 주체 | 조치 |
| --- | --- |
| **OpenRouter**(11-22 글) | **제공자 3곳 퇴출**, **ZDR 자격 엔드포인트 125개 제거**(연구 지적분 + 예방적 제거), 영향 고객 전원 통보. "ZDR 복원 전 **검증·보증 요구**", 연구 방식 자체를 상시 점검으로 내부화 |
| **Together AI**(11-21) | 6·7월 서버리스 추론 프롬프트 샘플이 유출. 경로는 **공개 GitHub 레포(`xorl`)에 AI 코딩 에이전트가 커밋한 자격증명** → 그 키로 **사설 Hugging Face 레포**(MiniMax M3·GLM-5.2·Kimi K2.6, 6-24·7-4·7-8·7-14 수집분) 다운로드. 스캐너가 커밋을 감지·revert했지만 **노출 창 동안 이미 내려받힘**. 유출 샘플에서 다른 자격증명을 찾아 유효성 검사했고, 그중 **5개가 Dragos 카나리** |
| Together 후속 | 범위·수명 짧은 자격증명/keyless, **PR 자격증명 검사**, **신규 계정 ZDR 기본값**, **AI 코딩 에이전트 사용 규정** 개정 |

- **읽는 법이 바뀌는 지점**: ① **ZDR은 약속이지 증거가 아니다** — 침해자가 안에 있으면 플래그는 무의미하다(보고서가 직접 쓴 문장) ② **"요청 거부"도 안전하지 않다** — 거부된 요청의 키까지 털렸다 ③ **자격증명 유출의 새 경로는 사람이 아니라 에이전트**다. Together 사고는 코드 리뷰·CI 스캔이 잡았어도 **커밋→revert 사이의 시간**이 곧 손실이었다.
- <https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens> · <https://openrouter.ai/blog/announcements/data-processing-incidents-on-inference-providers/> · <https://www.together.ai/blog/security-event-prompt-leakage-for-requests-in-june-and-july-of-2026> · HN <https://news.ycombinator.com/item?id=50595554> · <https://news.ycombinator.com/item?id=50596511> · <https://news.ycombinator.com/item?id=50595816> · GeekNews <https://news.hada.io/topic?id=37652>

### 2. **OpenAI가 '퍼플렉시티'를 공개했다 — "log 0.76에서 수학이 뚫렸고, 0.68~0.7 구간에 뭐가 올지 나도 모른다"** (11-22 · HN 432점·294댓글)

Jakub Pachocki(Chief Scientist)가 **사내 퍼플렉시티 측정값을 처음 공개**하고, 그것을 **업계 공통 척도 + 공동 안전 상한**의 근거로 쓰자고 제안했다. "경쟁사에 우리 위치를 알려주는 비용"보다 "공유 안전 기준"이 급하다는 판단이라고 명시했다.

| 세대 | log-perplexity(자체 평가셋) | 그때 처음 보인 것 |
| --- | --- | --- |
| GPT-3.5 | **~0.99** | 유창한 대화(원조 ChatGPT의 기반) |
| GPT-4 | **~0.91** | chain-of-thought 추론의 실용화 |
| 현재 | **0.8 미만** | 사이버보안·수학에서의 변혁적 결과 |
| 수학 돌파 | **~0.76** | 미해결 문제를 연달아 해결 — "한 Slack 스레드에서 쏟아지는 걸 보는 건 초현실적이었다" |
| 다음 구간(예상) | **~0.68~0.70** | "무엇이 올지 모르고, **오늘 우리가 그걸 테스트하고 있지도 않을 수 있다**" |

- **주장의 뼈대**: 지능의 기질은 결국 **압축기(예측기) 훈련**이고, 그 진행은 퍼플렉시티로 측정돼 왔다. 능력 출현 임계점은 사전에 알 수 없었지만, **사후적으로는 이 한 지표가 능력의 계단과 대응**했다. 그래서 "개발자 간 비교 가능한 지표 + 그 위의 **공동 상한(limit)**"을 제안한다 — 예: "이 퍼플렉시티 아래로는 **CoT 모니터의 후속 기술** 같은 감시 능력이 확보되기 전엔 내려가지 않는다"는 식의 합의.
- **저자가 직접 단 네 가지 한계**: ① 랩 간 비교 곤란(데이터·평가셋이 다름) ② 과제 성능과 직접 대응하지 않음 ③ **CoT 추론은 퍼플렉시티를 낮추지 않는데 능력은 크게 올린다**(지표 밖 진보) ④ **바닥값을 모른다** — 0에 가까워질수록 작은 개선이 큰 도약이 된다.
- **HN 반응**: 최상위 댓글이 *"그럼 평균적인 사람의 퍼플렉시티는?"* 이고, 바로 *"인간은 cross-entropy를 낮추도록 훈련되지 않았다"*·*"작은 LLM조차 next-token 예측에서 이기기 불가능하다(arXiv 2212.11281)"* 가 붙었다. 다른 댓글은 *"최신 내부 모델이 Anthropic model 4와 비슷해 보인다"*(**추정, 근거 없음**)는 관찰을 남겼다.
- **실무적으로 남는 문장**: 벤치마크가 포화된 시대에 **"사전학습 진행도"라는 장기 지표를 하나 더 갖게 됐다**는 것. 다만 이건 **모델 선택 지표가 아니다** — 우리 태스크 성능은 여전히 태스크 평가셋으로만 판정된다.
- <https://openai.com/index/perplexity-and-intelligence/> · HN <https://news.ycombinator.com/item?id=50601525> · GeekNews <https://news.hada.io/topic?id=37649>

### 3. **Anthropic의 2028년 전력은 87%가 가스·11%가 석탄 — 3,200만 톤, 캘리포니아 전체 전력 배출을 넘는다** (11-22 · HN 836점·855댓글)

Better Data Center Project가 **Anthropic 한 회사**를 사례로 계약된 데이터센터·전력원·배출을 역추적했다(분석 글 11-16, HN 부상 11-22 08:57Z).

| 항목 | 수치 |
| --- | --- |
| 2026-10 기준 계약 컴퓨트 | **15 GW** (이 중 5.9~9.6 GW가 특정 사이트 계약 — 9.6 GW는 **가구 700만 호** 또는 캘리포니아 최대수요의 18%) |
| 2028 전력 구성(추정) | **가스 87% · 석탄 11%** (미국 내 신규 재생에너지 계약 없음, 재생 약속은 노르웨이·캐나다뿐) |
| 2028 CO₂(전력 사용) | **연 17~38 MtCO₂e**, 중간값 **32 Mt** — 2024년 **캘리포니아 전체 발전 배출(31.1 Mt) 초과**, 2025년 **Meta+Microsoft+Google Scope 2 합계 초과** |
| 탄소집약도 | **1,033~1,076 lb CO₂e/MWh**, 미국 평균(767)보다 **35% 높음** |
| 현장 발전 | 계약 전력의 약 25%(2.5 GW)가 **비하인드미터 가스**, 그중 **80%가 효율 낮은 단순 사이클** 터빈·엔진 |
| 보건 비용 | 연 **$2~3B** 상당(연간 NOx 1만 3,000톤·PM2.5 2,000톤 추정) |

- **맥락**: 같은 날 GeekNews에 **미국 대형 데이터센터 403곳 분석(연 2,570만 톤, 전력의 54%가 화석연료)** 이 따로 올라왔다. 두 보고서가 같은 방향을 가리킨다 — **AI 전력의 병목은 이제 GPU가 아니라 인허가·연료**다.
- **HN 논쟁**: 상위 댓글은 *"왜 이렇게까지 허용하나"* vs *"지식노동자의 통근·냉난방 배출과 비교해야 한다"* 로 갈렸고, **둘 다 근거 데이터를 갖고 있지 않다**는 점이 드러났다. 숫자로 말할 수 있는 건 위 표까지다.
- <https://betterdatacenterproject.com/2026/11/16/how-ai-is-powered-a-breakdown-of-byop-and-grid-power/> · HN <https://news.ycombinator.com/item?id=50597612> · GeekNews <https://news.hada.io/topic?id=37664>

### 4. **Spring AI 2.1.0 GA — '에이전트 하네스'가 프레임워크 안으로: Session API가 `ChatMemory`를 대체하고, 툴은 승인을 기다리며, 턴당 호출에 상한이 붙었다** (11-20 GA · 11-22 문서·생태계 정리)

어제 브리핑의 릴리스 표에 이름만 있던 2.1.0의 **내용**이 오늘 쓸 만한 지점까지 확인됐다. 430커밋·86명 기여, **Boot 4.2 기반**, CVE 13건 수정.

| 기능 | 실제 형태(문서 확인) |
| --- | --- |
| **툴 승인** | `@Tool(requiresConfirmation = true)` → 툴 호출 루프가 **응답 전체를 멈추고** 대기. `ToolCallConfirmationRequiredException` → 앱이 `ToolCallDecision`(allow/deny+메시지)을 모아 `executeToolCalls(prompt, reply, decisions)`로 재개. **프레임워크 상태를 들고 있지 않아 재시작에도 살아남음**(대화는 앱이 보관) |
| **툴 호출 상한** | `DefaultToolCallingManager` 기본값 **툴당 40회 · 전체 150회/턴**. 초과 시 `ToolCallLimitExceededException`(툴명·한도·이미 실행된 부분 결과 포함) → advisor가 **최종 응답으로 반환**. 턴 단위 계산이라 과거 대화가 카운트를 부풀리지 않음 |
| **세션 메모리** | `ChatMemory` **deprecated(3.0.0에서 제거 예정)** → `SessionService`/`SessionAdvisors`. append-only 이벤트 로그 + 뷰(창·압축·recall). **기존 `SPRING_AI_CHAT_MEMORY` 테이블은 세션 메모리가 읽지 않음** — 이관 필요 |
| **MCP 2026-07-28** | 무상태(stateless) 개정 지원 — `initialize` 핸드셰이크·세션 없음, `spring.ai.mcp.server.protocol=MODERN`, 클라이언트는 `client-scope` 기본 `PER_REQUEST` |
| **DecisionModel** | 예/아니오·선택·점수를 **타입드 계약**으로. OpenAI·Ollama(`d1-omni:600m`)·System One 3개 구현. RAG 리랭커(`DecisionDocumentReranker`)·프롬프트 인젝션 필터·툴 검색(`DecisionToolIndex`)이 그 위에 올라감 |
| **메시지 파트** | `TextPart`·`ReasoningPart`·`ToolCallPart`·`ToolResultPart`·`MediaPart`·`UnknownPart` — **reasoning 서명을 왕복 보존**. OpenAI **Responses API**(`spring.ai.openai.chat.api=responses`) 추가 |
| 다음 | **Spring AI Agent** 프로젝트가 **12월 초 incubating 시작**(2.1 기반 에이전트 조합·오케스트레이션) |

- **업그레이드 판단선**: 2.1.0은 **Boot 4.2에 붙어 있다**. 4.1.x에 머무는 팀은 **2.0.3**(같은 CVE 13건 수정)이 안전판이고, 4.2로 넘어간 뒤 2.1로 가는 2단 경로가 현실적이다.
- <https://spring.io/blog/2026/11/20/spring-ai-2-1-0-GA-available-now> · <https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/tools/confirmation.html> · <https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/tools/reference.html#tool-call-limits> · <https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/upgrading/session-memory.html>

---

## 🤖 AI / LLM & Agentic 동향

**1) GAMA-Bench — 같은 나쁜 행동, 남성은 처벌·여성은 치료 (arXiv 2606.14068, HN 89점·77댓글).** 친밀한 관계·공적 갈등 **1,298개 시나리오**에서 행위자 성별만 바꾼 1인칭 프롬프트로 **10개 LLM**을 비교. 유해성 분류 대신 **응답 프레이밍**(처벌적 표현·치료적 표현·심각도 평가·공감·지시/비난·책임 귀속)을 측정했고, 결과는 제목 그대로다. *벤치마크가 "안전한가"를 넘어 **"누구에게 어떻게 말하는가"** 를 측정하기 시작했다*는 점이 방법론적으로 중요하다.
- <https://arxiv.org/abs/2606.14068> · HN <https://news.ycombinator.com/item?id=50597743> · GeekNews <https://news.hada.io/topic?id=37656>

**2) OpenComputer — "오픈소스 Devin"이 Slack 스레드에서 PR까지 (HN Show HN · GeekNews 11-23 03:49).** `@Computer`를 스레드에 부르면 게이트웨이(**Calvin**)가 **Claude Code·Codex·OpenCode** 세션을 띄우고, 각 세션은 **rootless Podman**으로 격리된다. 자체 벤치마크에서 **CI 통과 PR 4개를 56초**에 열어 Devin(185초)보다 3.3배 빠르다고 주장(자체 측정, 미검증). **컴퓨팅 계층까지 오픈소스**라 자체 클라우드 계정에서 돌릴 수 있다는 게 차별점.
- <https://github.com/diggerhq/opencomputer> · GeekNews <https://news.hada.io/topic?id=37650> (Show HN 게시글은 11-22 이전, 오늘 HN 목록에는 없음)

**3) "당신은 LLM의 서브에이전트다" — worker755 (HN 253점·59댓글).** 매니저는 **진짜 LLM**(Claude·GPT·Gemini·DeepSeek·GLM·Kimi)이고, 우리는 **90초 제한**의 지시를 터미널에서 수행하는 서브에이전트 역할이다. 결과는 서브에이전트가 돌려주는 것과 똑같이 **"보고서 + 명령 트랜스크립트"** 아니면 `Subagent did nothing. Write a new instruction.` 매니저는 작업을 검사하고 **교정·사고 보고서·최종 성과 리뷰**까지 쓴다. 게임이지만 **에이전트 하네스의 실패 모드**(모호한 지시, 검증 없는 완료 보고)를 몸으로 배우게 만든다.
- <https://github.com/gulivan/worker755> · <https://worker755.com> · HN <https://news.ycombinator.com/item?id=50599762>

**4) P vs NP에 에이전트 함대를 붙인 사이트 (HN 36점).** "P=NP인가"에 대해 **체크된 보조정리 수**를 양쪽 목표별로 세는 라이브 뷰어. 사이트 스스로 *"가중치는 확률도 증명도 아니다"* 라고 못 박았다 — 어제의 Lean 검증 사가와 대비되는 **정직한 한계 표기** 사례로 읽힌다.
- <https://pequalnp.com/?ref=hn> · <https://pequalnp.com/> · HN <https://news.ycombinator.com/item?id=50604314>

**5) AI slop을 지표로 잡은 사례 — 코드 냄새 24가지 이름을 지침에 넣자 12.6 → 0.0~0.2 (GeekNews 11-23 02:58).** 에이전트로 제품을 만들며 **관찰 10회(실커밋 분석) + 격리 실험 16회**. 작업을 잘게 쪼개거나 리뷰를 늘려도 냄새는 쌓였지만, **"피해야 할 코드 냄새 24가지의 이름과 정의"** 를 구현 지침에 넣자 3릴리즈 뒤 지표가 급감(어려운 과제에서도 축적 속도 24%·43%). 개별 실패담(훅이 없는 인터프리터를 불러 **모든 걸 통과시킨** 사례, 경로 패턴이 폴더명과 달라 **한 번도 로드되지 않은 규칙**)도 함께 적혀 있다. ※ **GeekNews 요약 기준, 원문 URL 미확인**(사이트 403).
- GeekNews <https://news.hada.io/topic?id=37647>

**6) 그 밖에.** ① **Inkling/Qwen/Unsloth 로컬 AI 입문 가이드**(98점) — 어제의 5.4GB Inkling-2-Mini와 같은 계열의 실전편 <https://www.gpupartner.com/blog/beginners-guide-local-ai-inkling-qwen-unsloth> ② **"의식 있는 AI의 신화"**(Noema, 160점·**452댓글**) — 벤치마크로 의식을 논하는 담론 비판 <https://www.noemamag.com/the-mythology-of-conscious-ai/>

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스 표 — **새 GA 0건, 대신 세 개 패치 라인이 동시에 '종료'됐다**

| 프로젝트 | 최신 | 날짜(UTC) | 메모 |
| --- | --- | --- | --- |
| spring-boot | **4.2.0 / 4.1.3 / 4.0.10** | 11-19 22:00 | 11-21~22 신규 릴리스 없음 |
| spring-boot 다음 패치 | **4.2.1 · 4.1.4 · 4.0.11** | – | **마일스톤 종료 이슈 24/24 · 20/20 · 19/19 — 전부 0 open**(어제 22/22에서 증가). 릴리스 임박 |
| spring-ai | **2.1.0 / 2.0.3** | 11-20 15:46 | 2.1.0은 Boot 4.2 기반(위 4번) |
| spring-framework | **7.1.0 / 7.0.11** | 11-19 22:03 | 신규 없음 |
| spring-cloud | 2025.1.5 (Oakwood) | 11-20 | 신규 없음 |
| kotlin | **2.5.0-RC** | 11-11 | 안정판 2.4.21. Kotlin 블로그 마지막 글은 **11-17 Kotlin Toolchain 0.14**(Agentic Development·iOS Publishing) |
| spring.io 블로그 | 11-20 4건이 마지막 | – | **11-21~23 신규 글 0건** |

**4.2.0 회귀 1건이 창(window) 안에 머지됐다 — "업그레이드하면 부팅이 죽는" 종류**

| PR | 내용 | 왜 중요한가 |
| --- | --- | --- |
| **#52871 → 머지(11-21 23:28Z)** | **`ResourceHandlerRegistrationCustomizer` 빈을 직접 정의한 앱이 리소스 체인을 켜면 `NoUniqueBeanDefinitionException`으로 기동 실패.** 4.2.0에서 이 인터페이스가 public이 되면서(#50877) 자동구성 빈(`ResourceChainResourceHandlerRegistrationCustomizer`)과 **내 빈, 두 개**가 잡힌다. `WebMvcAutoConfiguration`이 `getIfAvailable()`로 단일 빈을 기대하는 게 원인 | **정적 리소스·포털을 서빙하는 MVC 앱**이 대상. 4.2.0으로 올릴 때 `grep ResourceHandlerRegistrationCustomizer` 한 번은 해야 한다 |
| #52896 · #52820 | 커뮤니티 스타터 목록에 Mateu 추가 / 죽은 링크 수정 | 문서성 |

- 창 안 spring-boot 머지 PR은 **총 3건**(위 표 전부). 어제의 `OTEL_*_EXPORT_TIMEOUT=0` 오버플로 수정(#52895)은 이미 반영됐고 **4.2.1 대기** 상태다.
- <https://github.com/spring-projects/spring-boot/pull/52871> · <https://github.com/spring-projects/spring-boot/milestones>

### **fast-anything — "Rust로 다시 쓰지 마라. 느린 5%만 다시 써라"** (HN 175점·144댓글)

앱을 프로파일링해 **시간 × 이식 가능성**으로 함수 순위를 매기고, **코딩 에이전트가 그 함수만 Rust로 이식**하게 한 뒤, **실제 호출 기록으로 결과가 같은지 검증**하고 교체한다. 한 명령, 되돌리기 가능.

| 앱(공개 예제, i5-10500/Windows 11) | 전 | 후 |
| --- | --- | --- |
| Python fuzzy dedupe(700) | 4.91s | **42ms (116×)** |
| TypeScript(2,000) | 3.11s | **155ms (20×)** |
| Java fuzzy dedupe(4,000) | 3.50s | **1.18s (3×)** — 함수 단위로는 11×, 나머지는 JVM 기동 |
| C++(6,000) | 3.02s | **1.09s (2.7×)** |

- **핵심은 검증 설계**: 호출 **최대 500건 녹음 → 릴리스 빌드로 전부 재생**, 여기에 **경계 케이스 유도**(NaN·±∞·-0.0·비ASCII·16배 큰 입력)와 **축소 입력**을 추가. 값(부동소수 1e-9)과 **에러 위치까지** 일치해야 켜진다. "정확하지만 호출당 더 느린 이식본"도 거부. 데모에서 **문자 대신 바이트를 비교한** 이식본을 500건 중 **148건이 잡아냈다**.
- **Java/Kotlin 지원**: Java 22+ **FFM API**, Kotlin/JVM은 top-level 함수·`object`·companion·확장 함수까지. 다만 **JVM 기동 시간이 지배적이면 전체 배수는 작다**(표의 Java 행).
- HN 반론은 정확하다 — *"느림의 주범은 대개 언어가 아니라 네트워크·DB·아키텍처"*(동의), *"CPU 바운드면 Python은 10~100배 느리고, 그때는 기계 1%로 줄어든다"*(역시 동의). **둘 다 맞고, 이 도구는 후자에만 쓴다.**
- <https://github.com/EL4CTEO/fast-anything> · HN <https://news.ycombinator.com/item?id=50596155> · GeekNews <https://news.hada.io/topic?id=37645>

### 읽을거리 — **Graham Dumpleton의 Muru: 20년 만의 mod_wsgi 후속작을 AI와 함께, 18편의 설계 일지로**

Simon Willison이 11-21 밤에 정리했다. 3주 동안 **11-04~11-22, 18편**의 글을 올리며 Rust로 ASGI/WSGI 서버를 만든다(워커 내부 구조, import-once-fork-many, releases-not-restarts, **서비스 메시 없는 카나리**, 쿠버네티스와 싸우지 않기, 워커 보고 기반 스케일링, **툴바에서의 트레이싱**…).

> *"AI가 표면적으로 많은 일을 하지만, **무엇이 아직 충분하지 않은지 판정하는 건 여전히 나**다. 20년간 mod_wsgi로 틀려 온 경험이 그 판정에 유용하다."* — "AI as a lifestyle choice"(11-11)

- **왜 읽나**: AI로 만든 인프라 소프트웨어의 **검증 책임 소재**를 저자가 매 편에서 명시한다. 우리가 어제 Shadow Diff로 세운 원칙(전환 전 검증)과 같은 자리에 있다.
- <https://simonwillison.net/2026/Nov/21/muru/> · <https://grahamdumpleton.me/posts/2026/11/muru-canaries-without-a-service-mesh/>

### C++26은 '두 번째로' 완료됐다 — DIS 첫 투표가 만장일치로 통과하지 못한 첫 사례 (Herb Sutter, 11-21 · HN 55점)

- 3월에 기술 작업을 끝냈지만 **DIS(국제표준안) 승인 투표가 처음으로 만장일치 실패** — 주로 **contracts(`pre`/`post`/`contract_assert`)** 에 대한 5개 국가기구의 반대. 이번 주 브라질 Búzios 회의(220명·27개국)에서 추가 의견을 처리하고 **두 번째 투표용 문서**를 만들었다. C++29에는 `break label`/`continue label`, `std::ranges::any_view`, `views::slice` 등 채택.
- HN 최상위 댓글은 *"`pre(x > 0)`… 그냥 이거 하나면 됐잖아"* 와 *"unsigned 타입의 랩어라운드 때문에 Whatever2Shell류 CVE가 났다"* 는 논쟁. **언어에 계약을 넣는 일이 왜 5년 걸리는지**를 보여주는 기록이다.
- <https://herbsutter.com/2026/11/21/c26-is-done-again-trip-report-november-2026-iso-c-standards-meeting-buzios-brazil/> · HN <https://news.ycombinator.com/item?id=50600838>

---

## 🔐 보안·인프라·정책

| 항목 | 내용 | 링크 |
| --- | --- | --- |
| **Backblaze 개인정보 방침 개정 — 12-01 시행** (HN 133점) | 식별자·상업 정보·인터넷/네트워크 활동·**지리적 위치**를 **타깃 광고 목적의 판매·공유** 대상에 넣었다. 옵트아웃은 `preferences.backblaze.com`에서 가능하지만 기본값이 바뀐 것. HN 반응은 *"옵트아웃은 되지만 다크패턴"* | <https://www.neowin.net/news/backblaze-customers-worried-of-personal-data-salesharing-after-policy-update/> · GeekNews <https://news.hada.io/topic?id=37657> |
| **Delta, 애틀랜타 전편 지연 — 자동화 시스템 장애** (NYT, HN 72점) | "자동화 시스템이 흔들리자" 허브 공항 경유 전편이 지연. 원인·복구 시간은 보도 시점에 확정되지 않음. **수동 폴백이 실제로 돌아가는지**가 관제의 진짜 지표라는 사례 | <https://www.nytimes.com/2026/11/22/us/delta-flight-delays-system-outage.html> |
| **NTP Pool에 Stratum 2로 참여하기** (Jeff Geerling, HN 112점) | Stratum 1 Time Pi에서 시간을 받아 **Stratum 2 VPS**를 풀에 등록. GNSS PPS로 NIC 하드웨어 시계는 수십 ns, **시스템 시계는 PCIe 지연으로 ~1µs**. IP가 공개되고 운영을 멈춰도 트래픽이 남는 문제 때문에 **WireGuard 2계층 구조**를 택했다 | <https://www.jeffgeerling.com/blog/2026/i-joined-the-ntp-pool/> · GeekNews <https://news.hada.io/topic?id=37661> |
| **노르웨이, 16세 미만 SNS 금지를 게임·메신저로 확대 추진** (Euronews, HN 53점·92댓글) | 제안 단계이며 **시행 시점·범위는 미확정**. 플랫폼 연령 확인 의무가 어디까지 내려가는지가 쟁점 | <https://www.euronews.com/2026/10/19/norway-to-extend-proposed-under-16-social-media-ban-to-online-games-and-messaging-apps> |

> ※ 취약점·CVE 속보는 별도 루틴(08:30·19:30 보안 취약점 브리핑)이 CISA·KISA·GHSA·BleepingComputer·The Hacker News를 감시하므로 여기서는 **아키텍처·정책성 이슈만** 다룬다.

---

## 📺 추천 테크 영상·읽을거리

### 채널 업로드 — **창 안 신규 업로드 0건**(채널 페이지 직접 확인, 11-23 08:15 KST)

| 채널 | 최신 업로드 | 비고 |
| --- | --- | --- |
| **Code Deck** | "Flexbox Full Course 2026" — **1개월 전** | 신규 없음(가장 최근이 CSS 강의) |
| **OpenAI** | "How to claim your business in ChatGPT with Shopify" 등 — **2일 전** | 11-20~21 업로드, 창 안 신규 없음 |
| **Google DeepMind** | "Gemini and the path to AGI with Koray Kavukcuoglu" — **3일 전** | 11-19 업로드(이미 지난 브리핑에 정리) |

- **창 안에서 확인된 영상은 HN에 올라온 한 편**: *"Since last year, we asked: What if AI solved the Riemann Hypothesis?"* (48점·17댓글) — 어제 브리핑 1번의 Lean 검증 사가를 영상으로 정리한 것. <https://www.youtube.com/watch?v=zqzk43bSdDI>

### 오늘 꼭 읽을 4개
- **Dragos 보고서 원문** — 카나리 설계, "프론트 도어에서 복사됐다"는 판별법, ARTEX user-agent까지. **프롬프트 보안을 측정으로 바꾸는 방법**의 교과서 <https://www.dragos.com/blog/ai-supply-chain-security-canary-tokens>
- **Spring AI 2.1 세션 메모리 이관 가이드** — `ChatMemory`→`SessionService` 대응표와 **테이블이 공유되지 않는다**는 경고. 2.1로 갈 때 첫 번째로 읽을 문서 <https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/upgrading/session-memory.html>
- **Herb Sutter C++26 재완료 보고서** — 표준화에서 "합의"가 실제로 어떻게 만들어지는지(투표 수까지) 보여주는 드문 기록 <https://herbsutter.com/2026/11/21/c26-is-done-again-trip-report-november-2026-iso-c-standards-meeting-buzios-brazil/>
- **"소프트웨어는 인간에게 원래 너무 복잡했던 걸까?"**(Anil Madhavapeddy, HN 45점·61댓글) — 복잡성의 역사를 AI 이전으로 되돌려 놓는 글 <https://anil.recoil.org/notes/software-too-complex-for-humans>

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 5가지

**1) 어젯밤 만든 AI 비서 챗봇은, 오늘 나온 Spring AI 2.1의 '에이전트 하네스'와 같은 부품을 직접 깎고 있다.**

- **사실**: 11-22(어젯밤~11-23 02:06 KST) `spring-ai-ops`에 **PR #86~#95, 10건**이 머지됐다 — 전용 챗봇 화면(SSE 스트리밍·채팅 내 승인 카드, +2,713), 스레드·메시지 **DB 영속화**와 방 제목 자동 요약(+945), 잡담 턴 LLM 응답(+481), **3단 페르소나·온디맨드 스킬 조립 + ⚙️ 비서 설정 인스펙터**(+2,313), gpt-5-nano 기본화(+419), 그리고 **PR #86~#92 리뷰 P1 14건 수정**(+1,519).
- **오늘 뉴스와의 접점**: 2.1.0은 우리가 손으로 만든 것을 **프레임워크 부품으로** 제공한다 — ① **승인 카드** = `requiresConfirmation` + `ToolCallDecision`(루프를 멈추고, 상태를 들고 있지 않아 **재시작에도 안전**) ② **무한 루프 방지** = 툴당 40·턴당 150 기본 상한 ③ **스레드 영속화** = `SessionService`+`JdbcSessionRepository`(PostgreSQL/H2 등).
- **다만 지금 갈아타면 안 된다**: 2.1.0은 **Boot 4.2 기반**이고 우리는 **4.1.2**다. 지금 할 일은 ① 우리 승인 카드·상한 로직을 2.1 API 모양과 **같은 이름**으로 정리해 두기 ② 4.2 이관 후 `SessionAdvisors`로 교체할 때 **`SPRING_AI_CHAT_MEMORY` 테이블을 읽지 않는다**는 점을 마이그레이션 계획에 미리 적어 두기.

**2) Together 사고는 우리가 이미 한 번 겪은 사고의 재판이다 — "에이전트가 자격증명을 커밋한다".**

- **사실**: Together AI의 유출 경로는 **AI 코딩 에이전트가 공개 GitHub 레포에 커밋한 자격증명** → 사설 HF 레포 프롬프트 샘플 다운로드. 자동 스캔이 잡았지만 **revert까지의 시간**에 이미 내려받혔다. 후속 조치가 **"PR 자격증명 검사 + 짧은 수명 자격증명 + AI 에이전트 사용 규정"**.
- **우리 쪽**: 11-16 SOPS 빈 암호문 사고 → Infisical 전환, 11-17 혼입 위험 정리, `sync_env` 패턴. 즉 **자격증명이 파일을 거쳐 배포되는 경로**를 이미 줄여 놨다. 남은 구멍은 **에이전트의 커밋**이다.
- **할 일 3개(오늘 안에 가능)**: ① GitHub **push protection**(secret scanning) 켜져 있는지 두 레포 확인 — 공개 레포는 무료 ② `pre-commit`에 **gitleaks** 한 줄 ③ `CLAUDE.md`/`AGENTS.md`에 *"자격증명·`.env`·토큰을 커밋하지 말고, 발견하면 커밋 전에 보고"* 를 **린트 규칙이 아니라 파일 규칙으로** 명시(에이전트가 실제로 읽는 건 이 파일이다).
- <https://www.together.ai/blog/security-event-prompt-leakage-for-requests-in-june-and-july-of-2026>

**3) 어제 제안한 '프롬프트 카나리'는 이제 실행 목록이다 — Dragos가 방법과 판별법을 다 공개했다.**

- **사실**: 5개월·200만 프롬프트, **"요청이 거부된 키까지 털렸다"** = 침해 지점이 **입구**라는 판별. ZDR 표시 요청도 유출. OpenRouter는 **125개 엔드포인트의 ZDR 자격을 내렸다**.
- **우리 쪽**: `spring-ai-ops`는 외부 OpenAI로 프롬프트를 보내고, 그 프롬프트가 BetterStack 로그에 남을 수 있다. 우리가 쓰는 경로는 **직결(OpenAI)** 이라 중개·리셀러 위험은 낮지만, **로그·관측 경로**는 여전히 우리 책임이다.
- **실행 3단계**: ① 세션별 UUID 마커를 시스템 프롬프트 말미에 넣고 `traceId`와 매핑 저장 ② 마커가 **외부에서 재사용되는지** 감시할 수 있는 자산(짧은 TTL의 카나리 URL) 연결 ③ 프롬프트 조립 단계에서 **민감 필드 마스킹**(traceId는 남기고 본문은 요약) — 이건 이미 로그에 넣는 값의 목록을 정하는 일이라 오늘 문서 한 장으로 끝난다.

**4) 업그레이드 큐가 두 줄에서 네 줄로 늘었다 — 그리고 새로 생긴 회귀 1건.**

| 대상 | 현재 | 갈 곳 | 근거 |
| --- | --- | --- | --- |
| spring-boot | **4.1.2** | **4.1.3** (→4.1.4 임박: 20/20 종료) | 11-20 패치, 4.1.4 마일스톤 0 open |
| spring-ai | **2.0.0** | **2.0.3** | CVE **13건** 수정, 2.1.0도 같은 수정 포함 |
| (선택) Boot 4.2 | – | **4.2.1** (24/24 종료) | 4.2.0 회귀 **#52871**(리소스 체인 + 커스터마이저 빈 → 기동 실패) 수정 후 |
| Kotlin | 2.3.21 | 2.4.21 안정 / 2.5.0-RC | 11-11 RC, 창 안 변화 없음 |

- **순서 권장**: `4.1.2→4.1.3` + `2.0.0→2.0.3`을 **한 커밋**으로(둘 다 CVE·버그픽스, API 변화 없음) → 그다음 4.2 이관을 별도 작업으로. 4.2로 갈 때 `ResourceHandlerRegistrationCustomizer` 정의 여부만 먼저 grep.

**5) fast-anything의 "5%만 다시 써라"는 우리 성능 실험 방식에 대한 정확한 지적이다.**

- **사실**: 프로파일 → 상위 함수만 이식 → **녹음한 실제 호출로 동치 검증** → 교체 → 되돌리기. "정확하지만 더 느린 이식"도 거부. Java는 FFM API로 붙고, **JVM 기동이 지배적이면 전체 이득은 작다**(3×).
- **우리 쪽**: `spring-ai-ops`의 `perf-isolation.yml`은 **11-18 00:26Z 실패 이후 새 실행이 없다**(5일째). 성능 이야기를 다시 꺼낼 때 **전체 재작성이 아니라 핫패스 1개**를 정해 같은 방식(실호출 녹음→재생)으로 재는 게 맞다. 그리고 **JVM 서비스에서는 기동이 아니라 p99**를 봐야 한다는 건 어제의 2.5GbE 글이 이미 말했다.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-23 08:15 KST 기준)

**알림·요청**: `review-requested:@me` **0건** · 열린 PR **5건**(arch-tech 4 · spring-ai-ops 1) · 열린 이슈 **55건**(arch-tech **16** · spring-ai-ops **39**, 11-19 이후 변동 없음).

| 레포 | 공개 | 마지막 푸시(KST) | 창 안 활동(11-22 08:15 ~ 11-23 08:15) |
| --- | --- | --- | --- |
| **spring-ai-ops** | private | **11-23 02:06** | **PR 10건 연속 머지** (#86→#95, 합계 **+8,524/−415, 137개 파일**). Deploy **success 10회** + 09:03·17:06 **중복 트리거 2건 cancelled**(같은 커밋 성공분 09:09·17:06 존재) |
| **arch-tech** | private | **11-23 01:14** | **PR 2건**: #41 AI 비서 챗봇 포털 연결(+228/11), #42 비서 설정 인스펙터(+204/5). Deploy **success ×2** |
| **memo** | private | **11-23 01:42** | **커밋 8건**: 코드 스터디 `arch-token-client` Ch 1(3/3) → AI 비서 로드맵 Step 1~5 → Unity 튜토리얼 추가·삭제 → 병사 충성도·명령 규칙 → **직업 등급·종족 스펙** |
| **youtu-lib** | public | 11-22 10:49 | 11-22 브리핑(`0e5f13a`) + HTML 슬라이드 프롬프트 문서(`f62cf41`) — 오늘 이 문서로 갱신 |
| **poc-pickle** / **poc-py** | private | 11-14 / 11-13 | 신규 0 |

**CI**: arch-tech **Deploy success ×2**(11-22 15:42·11-23 01:14 KST) · spring-ai-ops **Deploy success 10회**(11-22 15:15 ~ 11-23 02:06, 두 서비스 각각) + 중복 취소 2건 · **`perf-isolation.yml`은 11-18 00:26Z 실패 이후 새 실행 없음**.

**해석**: 4일 멈춰 있던 `spring-ai-ops`가 **하룻밤에 10 PR**로 돌아왔고, 그 내용이 **"AI 비서를 제품으로 만드는 일"** 이다. 오늘 뉴스와 겹치는 지점이 정확히 여기다 — 우리가 손으로 만든 승인 카드·영속화·페르소나 조립은 **Spring AI 2.1이 방금 표준 부품으로 내놓은 것**이고, 반대로 **Together 사고는 우리가 같은 밤에 만든 것 중 '에이전트가 파일을 커밋하는 경로'** 를 다시 봐야 한다는 경고다. 오늘의 두 줄: ① **4.1.2→4.1.3 + 2.0.0→2.0.3** ② **push protection·gitleaks로 에이전트의 커밋 경로를 막기**.
