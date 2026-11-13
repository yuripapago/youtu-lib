# 2026-11-14 (토) 데일리 테크 브리핑

> **수집** 2026-11-14 08:25 KST · 최근 24시간(2026-11-13 08:30 ~ 11-14 08:25 KST) 중심
> **소스** Hacker News(front·best), GeekNews, FAA ATCSCC 고지 · Axios · Washington Post · NBC, Fortune, Claude/Anthropic 공식, Perplexity Docs·Hugging Face, GitHub Status, 클로드 상태 페이지, MCP Blog, Spring Blog, Qdrant Releases, Kotlin Docs, Inside.java, Codedeck
> **오늘의 한 줄** — *"하늘은 통신 장애로 멈췄고, 땅에서는 범용 모델이 로봇을 직접 몰기 시작했다. 판단은 생성이 아니라 숫자로 넘어가고 있다."*

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. FAA, 사상 초유의 **전국 지상 정지(Ground Stop)** — 22개 관제센터 통신 장애, 해제 시각 미정

11/13 20:53 UTC(한국 11/14 05:53) 발효. 미국 전 공항의 **모든 출발편 정지**, 공중 항공기는 착륙 허용, 입국 국제선은 건별 처리.

| 항목 | 내용 |
| --- | --- |
| 고지 | ATCSCC Advisory **111** (DCC) — "NATIONAL GROUND STOP", EVENT TIME **13/2053Z – 14/0400Z**, EFFECTIVE 132054–140430 |
| 사유 문구 | "DUE TO EXTRAORDINARY CIRCUMSTANCES AND FOR REASONS OF SAFETY" · "ALL DEPARTING TRAFFIC SHOULD REMAIN GROUND STOPPED UNTIL FURTHER NOTICE" |
| 원인 | 교통장관 Sean Duffy — "**모든 Air Route Traffic Control Centers(22개)에 영향을 주는 통신 문제**". FAA는 "일시적 통신(텔레콤) 아웃티지"로 표기 |
| 당국 입장 | 국토안보장관 Markwayne Mullin — "악의적 공격 징후는 아직 없음". FBI는 **소프트웨어 업데이트 결함 가능성** 조사(Bloomberg) |
| 규모 | FlightAware 17:19 ET 기준 미국 내 **9,086편 지연**, 보도에 따라 지연·결항 약 7,000편 집계 |
| 현재 상태 | 해제 지침은 확인 시점까지 발표되지 않음. 고지의 예정 구간은 14/0400Z(한국 13:00)까지 |

- 사전 징후: 같은 날 15시경(ET) 시카고 오헤어·미드웨이에서 장비 장애로 부분 지상정지가 먼저 나왔고, 오픈소스 무전 기록상 주파수 문제가 오헤어 → JFK 순으로 번졌다.
- <https://www.fly.faa.gov/ois/oisedit/adv_oisedit_dis?day=11132026&number=111> · Axios <https://www.axios.com/2026/11/13/faa-nationwide-ground-stop> · WaPo <https://www.washingtonpost.com/transportation/2026/11/13/faa-grounds-flights-all-us-airports-citing-communications-issue/> · NBC 라이브 <https://www.nbcnews.com/news/us-news/live-blog/live-updates-flights-grounded-faa-communications-outage-rcna609268> · HN 961점 <https://news.ycombinator.com/item?id=50489641>

### 2. OpenAI, **독립 로보틱스 팀 해체** — "전용 로봇 모델 대신 범용 모델이 로봇을 직접 조종한다"

Fortune 단독(11/13). 해체가 아니라 **재배치**라는 게 회사 설명이지만, 방향은 분명하다. 로보틱스 연구진은 Mark Chen 최고연구책임자 산하 본연구팀으로, 응용·하드웨어 팀은 Greg Brockman 총괄의 Applied 조직으로 옮겼다.

| 항목 | 내용 |
| --- | --- |
| 근거 | GPT-6 Astra를 로봇에 직접 연결했을 때 "놀라운 성능". 엔비디아 연구에서 Astra 기반 에이전트가 **모의 조작 벤치 74.8%**, 전용 로보틱스 모델 최고 기록 **51.0%** |
| 배경 | 분기 영업손실 **$10.9B** 보도, 2027년 초 IPO 준비, "side quests" 정리 기조 |
| 현장 | 이번 주 오스틴 CoRL에 등록해 둔 부스(#208)가 **텅 빈 채** 남아 있었다 |
| 반론 | MIT Russ Tedrake — 로봇 회사의 우위는 **현장 배치 경험과 그 데이터**. CMU Guanya Shi — 클라우드 의존 없는 **온디바이스 소형 모델** 수요는 남는다 |

- <https://fortune.com/2026/11/13/openai-disbands-robotics-team-as-its-general-models-start-running-robots/> · HN 132점 <https://news.ycombinator.com/item?id=50487184>

### 3. Anthropic의 하루 — **Claude가 회의에 참석한다**(Meetings 베타) + **IPO 공모가 $70~78**

**Claude Meetings(베타)** — Claude가 화상회의에 **보이는 참석자**로 들어와 대화를 따라가며 회의 중에 산출물을 만든다.

- 기본은 글로 답변, 허용한 연결 도구 사용. "Claude, 이 계획 문서 시작해줘" → 지금까지 나온 내용으로 초안 작성, 참석자가 실시간 편집. 화이트보드·문서·회의 페이지가 claude.ai에 남는다.
- 초대된 조직 구성원 전원이 열람·편집, 조직 내 공유 가능. **기본값은 외부인 초대 시 불참, 중간에 외부인이 들어오면 퇴장**(관리자가 허용 가능).
- 저장 범위: **transcript·요약·화면 설명, 오디오는 저장하지 않음**. 우선 **Google Meet**, Team·Enterprise 베타. ZDR·HIPAA·CMEK 조직은 미제공. 베타 기간 노트·transcript 무료, 회의 중 만든 아티팩트는 2027-02-10까지 무료.
- <https://claude.com/resources/articles/claude-meetings> · HN 41점 <https://news.ycombinator.com/item?id=50491097>

**IPO** — Class A 보통주 **8억 2,500만 주**, 희망 공모가 **주당 $70~78**, 최종 공모가 11/18(EST) 결정 → **11/19 나스닥 "ANT" 거래 개시** 예상. (전일 브리핑의 로드쇼 개시에 이어 가격대가 공개됨)
- <https://ipo.anthropic.com/us/>

**같은 날 장애** — Claude 인증 오류로 claude.ai · Claude Code · platform.claude.com · Cowork 영향, 09:58~11:58 UTC(한국 18:58~20:58) 후 해결. <https://status.claude.com/incidents/nn11wk485zfn>

### 4. Perplexity **Decider v1.3** — "생성하지 않고 확률만 돌려주는" 판단 모델이 리랭커를 이겼다

오픈 웨이트(**Apache 2.0**) 판단 모델. Qwen3.8-27B 백본에 판정 헤드를 붙여, **답을 쓰지 않고** yes/no 확률·선택지 확률·등급 확률을 돌려준다. v1.3의 신기능은 **Locate**(원문 스팬을 문자 오프셋과 함께 그대로 추출).

| 항목 | 값 (전부 Perplexity 자체 측정) |
| --- | --- |
| 크기 / 문맥 | 27B(문서 표기) / 256K, 로컬 기본 32,768 · Flash는 4B |
| 질문 유형 | `noul`(예/아니오) · `choice`(최대 255) · `score`(10단계 루브릭) · `locate`(신규) |
| 가격 | **입력 $0.02 / 1M 토큰, 출력 무료**(Flash $0.01) |
| 질의 분류 | 63개 과제 평균 **91.6%**(기존 프로덕션 분류기 +6.54p) |
| 리랭킹 | 72개 과제 **nDCG@10 83.99** — Qwen3-Reranker-8B +1.81, Jev 1.14 +2.24 |
| Locate | 17개 과제 평균 **69.0** vs GLiNER large 33.6, Liquid d1 43.3 |
| 실트래픽 | 100만 요청·231만 질문에서 프런티어 LLM 판정과 **93.98% 일치**, 순서·라벨 뒤집기에서 답 변동 **0.27%**(Jev 1.14는 2.69~4.84%) |
| 비용 | 100만 요청당 **$26.47**(Jev 1.14 $68.10) |

- <https://www.perplexity.ai/hub/blog/perplexity-decider> · 모델 카드 <https://huggingface.co/perplexity-ai/pplx-decider-v1.3> · 문서 <https://docs.perplexity.ai/docs/decisions/quickstart> · MarkTechPost <https://www.marktechpost.com/2026/11/13/perplexity-decider-v1-3-open-weight-decision-models-locate/> · HN 79점 <https://news.ycombinator.com/item?id=50488464>

---

## 🤖 AI / LLM & Agentic 동향

**1) Gemini 4 Hydrogen의 경쟁 비교표가 공개됐다 — 전부 구글 자체 측정.**
11/12 발표분이 11/13 앱·검색 전면 롤아웃되며 경쟁 모델 대조가 나왔다. Agents' Last Exam **71.6%**(GPT-6.1 Terra 69.8, Claude Sonnet 5.5 60.4), HLE-Diamond **58.8%**(53.3 / 47.3)로 앞서지만, Terminal-Bench 4.1 **57%**(Sonnet 5.5 63.9)와 OSWorld-2.1 **77.6%**(83.9)에서는 진다. 가격 $1.50/$7.50, 1M 문맥, 사고 단계 4단. <https://www.timesnownews.com/technology-science/google-gemini-4-hydrogen-takes-on-claude-sonnet-5-5-gpt-6-1-terra-features-pricing-article-156503578>
→ **어제 브리핑의 "Hydrogen으로 교체 검토" 판단은 유지되지만, "에이전틱 실행 만능"은 아니다.** 터미널·OS 조작형 과제는 Sonnet 5.5가 여전히 우위.

**2) MCP가 "달력"을 갖게 됐다 — 6개월 주기, 다음은 2027-03-30.**
11/11 발표. 매년 3월·9월 마지막 화요일 릴리스, 8개월 전 기획 시작 → 6개월 전 리뷰 → 2.5개월 전 soft freeze → 1.5개월 전 Tier1 SDK 베타 → 1개월 전 hard freeze. 릴리스 매니저 역할 신설. 기존 클라이언트·서버는 변경 불필요.
또 11/10 **파일 업/다운로드 확장 초안**(`io.modelcontextprotocol/files`, revision 2026-07-28+)이 공개됐다 — 파일은 JSON-RPC 밖으로 나가고, digest 일치는 "안전"이 아니며, `omit` 업로드 대상은 호스트 allowlist로 제한하라는 권고가 붙었다. <https://blog.modelcontextprotocol.io/posts/mcp-release-cycle/> · <https://blog.modelcontextprotocol.io/posts/exploring-file-uploads-and-downloads-in-mcp/>

**3) "가중치 공간 오접힘(weight-space misfolding)" — 제목은 과장, 내용은 진짜다.**
한 모델이 다른 모델의 **가중치를 직접 고쳐** 지식을 주입하는 실험. 타깃 Qwen2.5-1.5B, 편집자 ChatGPT 6.1-Sol. "2000년 올림픽 개최지는 부에노스아이레스" 주입은 **held-out 100개 프롬프트 전부 성공**, 법률 거부 주입은 22개 중 5개만 정확(프랑스 수도 질문까지 잘못 거부), 공급사 은닉은 1/22. 논문 스스로 "**바이러스나 자기 전파를 입증한 것이 아니다**"라고 명시했고, HN 댓글 다수가 제목 과장을 지적했다(부정 70%). <https://www.alphaxiv.org/abs/2611.weight-space-misfolding> · HN 124점 <https://news.ycombinator.com/item?id=50487762>

**4) 온디바이스 진영 — llama.cpp·ggml이 **v1.0.0**(11/13), Edge0는 35B를 1~3GB로.**
- llama.cpp/ggml v1.0.0: 공개 C API 기준 **semver 규칙** 도입(MAJOR=호환성 파괴). `llama_set_warmup`·deprecated 35개·`defrag_thold` 삭제, NextN API를 `llama.h`로 이동, `llama_set_embd_state_src` → `llama_set_ctx_state_src`, **ggml RPC 프로토콜 v9**. 바인딩·RPC 쓰는 배포는 헤더 대조 필수. <https://github.com/ggml-org/llama.cpp/releases/tag/v1.0.0> · 해설 <https://www.codedeck.kr/posts/llama-cpp-ggml-v1-semver-api-upgrade>
- Edge0-145B(HN 338): 35B sparse MoE를 **SSD에서 전문가 가중치 스트리밍**으로 1~3GB 메모리에서 돌린다는 주장, 오프라인·토큰비 0. 벤더 주장이며 독립 검증은 아직 없다. <https://edge0.ai/> · HN <https://news.ycombinator.com/item?id=50475713>
- IBM **Granite 4.1 LLMON**(3B·8B 프리뷰): 지시와 입력 데이터를 구분하도록 학습해 프롬프트 인젝션 저항을 노린 실험 모델. <https://www.codedeck.kr/posts/granite-41-llmon-preview-instruction-data-boundary>

**5) 그 외** — Anthropic **Claude for Manufacturing**(제조 스킬 17개·커넥터 4개, Infor 변경은 조회와 분리된 별도 플러그인·담당자 승인) · World Labs **Atlas** 도구/API(사진 밖 영역 생성, 프로젝트별 키·비용 한도) · Runway **Praxis-1** 로봇 월드 액션 모델 가중치 공개(11/11, Apache 2.0, DROID 기본 추론 80GB GPU).

---

## ☕ 백엔드 / Spring / 아키텍처 엔지니어링

### 릴리스·지원 기한

| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| Spring Boot | **4.2.0-RC2** | 11-06 | **GA 전 마지막 RC**, "이번 달 GA" 예정. GA 날짜는 공식 미공표 |
| Spring Boot | 4.1.2 / 4.0.9 | 10-23 | 현행 GA. **4.0 OSS 지원은 2026-12-31 종료** → 4.1.x 이상 유지 필요 |
| Spring Modulith | **2.2.0-RC1** / 2.1.2 | 10-23 | 아래 상세 |
| Spring AI | 2.1.0-RC2 | 11-09 | 변경 없음 |
| Spring Cloud | 2026.0.0-RC2 (Paddington) | 11-05 | 변경 없음 |
| Kotlin | **2.4.21** | 10-08 | 2.5.0은 **2026년 12월**, 2.5.20은 2027년 3월 예정(변동 없음) |
| Qdrant | **1.20.1** | **11-13** | 아래 상세 |

- 11-13~14 신규 Spring 블로그 글 없음. (`Spring Cloud Contract → Stubborn.sh` 이관은 7월 공지로 신규 아님. 다만 **Stubborn Contract 0.2.0이 Maven Central에 공개**돼 마이그레이션이 실제 가능해졌다: groupId `sh.stubborn`, 패키지 `sh.stubborn.contract`, 기존 계약·WireMock 스텁·구 프로퍼티 하위호환. <https://stubborn.sh/transition-guide/>)

### Spring Modulith 2.2.0-RC1 — **아웃박스가 프레임워크 기능이 됐다**
- `EventPublication.Status`에 **`ABANDONED`·`RESUBMITTED`** 추가(실패와 포기 구분).
- 이벤트 발행 완료를 **리스너 트랜잭션 안에서 `TransactionSynchronization`으로** 처리 — "리스너는 커밋됐는데 완료 표시가 실패"하는 구멍을 막는다.
- 의존성에 **Namastack Outbox 1.10.1** 편입, "원본 도메인 이벤트를 아웃박스에 저장" 버그픽스 포함. 즉 **아웃박스 패턴이 Modulith 스택 안으로 들어왔다**.
- <https://github.com/spring-projects/spring-modulith/releases> · InfoQ 정리 <https://www.infoq.com/news/2026/10/spring-news-roundup-oct19-2026/>

### Project Detroit — JVM이 '외부 함수'에서 **외부 런타임**으로 (inside.java, 11/04 Devoxx Belgium)
Maurizio Cimadamore의 세션. FFM(Panama) 위에서 **V8·CPython 같은 네이티브 런타임을 자바 앱 안에 임베드**하는 방향. Nashorn처럼 런타임을 자바로 다시 쓰지 않는다. 발표에서 직접 언급한 난점이 실무적이다: **GC 두 개를 동시에 다루기**, 그리고 **가상 스레드 pinning 회피**.
- <https://inside.java/2026/11/04/devoxxbelgium-project-detroit/>

### Qdrant 1.20.1 (11/13) — 1.20.0의 HNSW 회귀 수정
- **코어가 많은 호스트에서 HNSW 인덱스 빌드가 최대 3배 느려지던 1.20.0 회귀** 수정(이번 릴리스의 핵심).
- 콜드 온디스크 스토리지에서 HNSW 검색 읽기 **절반**, 필터 여러 개인 콜드 페이로드 인덱스 대폭 개선, 동일 필터 배치 검색·리샤딩 전송 파이프라이닝, Turbo8/Turbo16 양자화 약 2배 빠름.
- 보안: 컬렉션 스코프 JWT가 접근 권한 없는 컬렉션의 **스냅샷 텔레메트리를 못 보게** 차단.
- <https://github.com/qdrant/qdrant/releases/tag/v1.20.1>

### GitHub 장애(11/13) — 우리 워크플로우에 직접 닿는 두 건
- **Pull Requests 머지 타임아웃 15:47~18:29 UTC**: 초기 3% → 1% 미만으로 축소 후 해소. "머지가 됐는지 안 됐는지"가 모호한 구간이 있었다.
- **Copilot 모델 저하 20:13~21:27 UTC**: `gpt-6.1-terra`·`gpt-5.6-sol`·`gpt-6-luna` 오류율 상승, **업스트림 모델 제공자 문제**로 표기. 현재 전 서비스 정상. <https://www.githubstatus.com/>

### 그 외
- **Rust 1.100**: never 타입 `!` 안정화(`Result<T, !>`, `Infallible`은 `!`의 별칭), 사용자 정의 메모리 할당자 안정화. <https://blog.rust-lang.org/2026/11/12/Rust-1.100.0/>
- **Microsoft 365/Azure 장애가 12시간 넘게 지속**(11/12~13), HN 1,153점. <https://status.cloud.microsoft/>

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) Apify 침해 — "연결된 통합 자격증명은 하나 뚫리면 다 번진다".** 11/9~10 침입, **11/11 12:13 UTC 탐지**. 대응: API 토큰을 **해시로만 저장**하도록 전환, 내부 서비스 간 네트워크 제한·강화 인증, GitHub 포함 OAuth 연동 선제 회수, 2FA 재설정 권고, 72시간 내 다음 업데이트·1주 내 포스트모템 예고. <https://status.apify.com/> · <https://www.bleepingcomputer.com/news/security/apify-rotates-api-tokens-prompts-2fa-resets-after-breach/>

**2) "No AI Slop" 선언문 (AIE Code Summit 2026).** 애자일 선언문 25주년 자리에 맞춰 나온 22개 조항. 실무적으로 바로 쓸 만한 문장들:
- "Slop은 **만들 때 들인 노력보다 소비자가 이해하는 데 드는 노력이 클 때**다."
- "**1,000줄 PR과 10쪽 문서의 생산 비용은 0에 수렴하는데, 리뷰 비용은 그대로다**" — 비대칭이 핵심.
- "에이전트가 쓴 것을 보낼 때는 **그렇다고 밝히고, 중요한 부분은 확인**한다."
- "같은 실수가 두 번 나오면 사람을 탓하지 말고 **시스템과 가드레일을 고친다**. 린터·타입체커·자동 검사가 잡을 수 있는 건 전부 잡게 한다."
- <https://slopmanifesto.com/> · HN 158점 <https://news.ycombinator.com/item?id=50488769>

**3) Humble Bundle, "vibecoding으로 자사 보안을 사실상 죽였다"** — 커뮤니티 폭로 스레드, HN 400점. AI로 급히 고친 코드가 권한·검증 경계를 어떻게 무너뜨리는지의 사례 모음. <https://forums.spacebattles.com/threads/humble-bundle-just-completely-killed-most-of-their-security-through-vibecoding.1346295/>

**4) 엔비디아 화물 $11.4M 실종 — 오늘 HN 250점으로 재부상(사건은 8월).** 8/29 오클랜드에서 6팔레트 출발 → 포트워스 미도착, 9/13 신고, **미검거**. 수사 기록상 픽업 트럭은 **식품 운송사 로고**를 달고 정시에 물량을 받아갔고, 샌버나디노 카운티에서 추적이 끊겼다. 업계 통계로 3분기 **가상 픽업 138건**(신고 사건의 약 1/4). <https://www.freightwaves.com/news/11-4-million-in-nvidia-hardware-vanishes-during-shipment-from-california-to-texas> · HN <https://news.ycombinator.com/item?id=50487864>

**5) 그 외 눈에 걸린 것**
- **중국 프런티어 모델에 수출통제 GPU 지문이 남아 있다** — 모델 가중치에서 칩 계열을 추정하는 분석(체크포인트 포렌식). HN 354점. <https://unemploymentlabs.com/checkpoint-forensics>
- **Android 클립보드 읽기 우회**("Ctrl+C, Ctrl+v, Ctrl+cve") — 권한 모델 우회 기법 정리. HN 53점. <https://toninoble.com/post/ctrlc-ctrlv-ctrlcve>
- **Coway 공기청정기 클라우드 탈출 리버싱** — 로컬 제어로 되돌린 기록. HN 641점. <https://scottjg.com/posts/2026-11-11-reverse-engineering-my-coway-air-purifier-to-ditch-the-cloud/>
- **FAA 지상정지와 같은 날 NWS 전국 레이더 웹사이트 장애** — 인프라 중복성 논의로 이어짐. <https://www.news9.com/oklahoma-city-news/national-weather-service-reports-nationwide-radar-website-outage>
- **"Doomsday: Friday, 13 November, A.D. 2026" (Science, 1960)** — 1960년 논문이 지목한 그 날짜가 바로 어제였다. HN 421점. <https://www.science.org/doi/10.1126/science.132.3436.1291>
- **LibreOffice "365일 내내 작동한다"** — 구독 압박에 대한 조롱 밈, HN 778점. · **Dart 3.14 / Flutter 3.50** 분석(어제 영상) 유지.

---

## 📺 추천 테크 영상·읽을거리

### Codedeck (한국어, 11/13~14 신규)
> YouTube 채널 RSS가 이번 수집에서 응답하지 않아, 같은 팀의 채널 블로그(codedeck.kr) 최신 발행분으로 대체했다. 영상 링크는 채널에서 확인 필요.

| 글 | 날짜 | 링크 |
| --- | --- | --- |
| **llama.cpp·ggml v1.0.0 — C API 버전 규칙과 삭제된 API** | 11-14 | <https://www.codedeck.kr/posts/llama-cpp-ggml-v1-semver-api-upgrade> |
| SGLang-Neuron 공개 프리뷰 — Trainium 서빙 조건 | 11-14 | <https://www.codedeck.kr/posts/sglang-neuron-trainium-preview-serving-conditions> |
| Claude for Manufacturing — 조회와 Infor 변경 권한의 경계 | 11-14 | <https://www.codedeck.kr/posts/claude-for-manufacturing-records-infor-approval> |
| Granite 4.1 LLMON — 지시·데이터 분리와 공격 성공률 해석 | 11-14 | <https://www.codedeck.kr/posts/granite-41-llmon-preview-instruction-data-boundary> |
| Atlas(World Labs) 도구·API — 좌표·비용 관리 조건 | 11-14 | <https://www.codedeck.kr/posts/atlas-tools-api-scene-coordinates-billing> |
| Praxis-1 가중치 공개 — 로봇별 체크포인트와 80GB GPU 조건 | 11-12 | <https://www.codedeck.kr/posts/praxis-1-open-weights-robot-checkpoints-runtime> |

### 영상·아티클
- **Project Detroit — From Foreign Libraries to Foreign Language Runtimes** (Devoxx Belgium 2026 녹화) — JVM에 CPython/V8 임베드, GC 두 개와 가상 스레드 pinning. <https://inside.java/2026/11/04/devoxxbelgium-project-detroit/>
- **JEPA 월드 모델 600번 실험 회고** — HN 193점. <https://haddyalchaer.com/jepa>
- **AI가 괴델 노트북을 해독해 수백 개 정리를 찾아냈다** (Guardian, 11/13, HN 288점). <https://www.theguardian.com/science/2026/nov/13/kurt-godel-notebooks-ai-helps-unlock-hidden-discoveries-man-who-transformed-maths> · HN <https://news.ycombinator.com/item?id=50486748>
- **"2026년, 엔지니어로 살아간다는 것"** — 같은 도구를 써도 결과물 격차가 벌어지는 이유(GeekNews). <https://news.hada.io/topic?id=36945>
- **"추론은 어떤 목적함수를 풀고 있는가?"** — 토큰 공간 경로 탐색으로 보는 RL 추론. <https://news.hada.io/topic?id=37055>
- **Show HN: $300 자수기를 구독 없이 쓰려고 앱을 직접 만들었다** — HN 716점, "구독화된 하드웨어" 반사. <https://github.com/simpsoka/zigzag>

---

## 📌 내 프로젝트(PDF 비교·무환각 검색 / arch-tech)에 바로 매핑되는 3가지

**1) fanout을 끝냈으니, 다음 카드는 '판단 계층'이다 — Decider v1.3를 리랭커·슬롯 필러로 검토.**
어젯밤 `arch-api`에 **팬아웃 모듈**(가상 스레드 동시 발사·SSE·서킷 연동 자동 점검)을 올려 백로그 #2를 사실상 닫았다. 그다음 병목은 "여러 파트너 응답을 어떻게 **판정**하느냐"이고, 이건 약관 RAG의 "어느 문서·조항을 답으로 쓸지 **판정**하느냐"와 같은 문제다. Decider v1.3가 정확히 그 자리를 겨냥한다:
- **리랭킹**: nDCG@10 83.99로 현행 리랭커 후보를 앞선다(자체 측정). `pickle.rag`의 검색 후단에 `rerank` 경계 하나를 추가하고, 골든 질문셋(위약금·해지·면책 20~30문)으로 A/B만 하면 판단 근거가 남는다.
- **슬롯 필링**: 문서 슬롯 선택·의도 분류를 "생성 모델에 물어 파싱"에서 **확률 임계값**으로 바꾸면 무환각 목표에 직접 유리하다(파싱 실패·형식 이탈이 사라짐).
- **제약**: 27B는 BF16 약 49GiB — NAS 동시 운영엔 부담. 1차는 **호스티드 API($0.02/1M 입력)** 로 붙이고, 로컬은 Flash(4B, 8.5GiB)로 검증하는 순서가 현실적이다. `locate`는 조항 원문 스팬 추출(근거 인용)에 그대로 쓸 수 있다.

**2) 아웃박스 스타터(백로그 #6)는 '직접 만들기'에서 'Modulith 위에 얇게'로 바꿀 타이밍.**
Spring Modulith 2.2가 `ABANDONED`/`RESUBMITTED` 상태와 **리스너 트랜잭션 내 완료 처리**, **Namastack Outbox 연동**을 가져왔다. 우리가 계획한 `arch-outbox-starter`(아웃박스 레코드·폴링 퍼블리셔·멱등 컨슈머·DLQ)의 상당 부분이 프레임워크 기본기와 겹친다. 방향: **Modulith 이벤트 발행 레지스트리를 척추로 두고, 우리 스타터는 "브로커 발행 어댑터 + 재시도/백오프 + DLQ"만 얇게 얹는다.** 단 2.2.0-RC1은 Boot 4.2.0-RC2 기반이므로, 4.2 GA 전에는 지금 4.1.2 라인에서 검증만 하고 승격은 GA 이후로 두는 게 안전하다(4.0 OSS 지원은 12/31 종료 — 우리는 4.1.2라 영향 없음).

**3) Qdrant를 1.20.1로, 그리고 PR 게이트를 '권한'으로 박는다.**
- NAS `:6333`의 현 버전을 먼저 확인하고 **1.20.1**로 올린다. 1.20.0은 코어 많은 호스트에서 **HNSW 빌드가 최대 3배 느려지는 회귀**가 있었고, 우리는 500페이지·200MB PDF를 밀어 넣는 경로가 이미 있다. 콜드 스토리지 읽기 절반·필터 인덱스 개선도 무환각 검색의 지연에 바로 듣는다. 스냅샷 텔레메트리 차단은 키를 나눠 쓰는 구성에 도움.
- arch-tech가 어젯밤 **첫 PR(#1)을 머지**하며 "main 직접 푸시 → PR" 방침(memo 백로그 1-1)으로 실제로 넘어왔다. 그런데 같은 레포에 11-13 하루 **36커밋**이 찍혔고 여전히 main 직접 푸시가 섞여 있다. **브랜치 보호 + 필수 상태 체크(모듈별 paths 필터 잡)** 를 걸어 "규칙"을 "권한"으로 바꿔야 유지된다. 더불어 11/13 GitHub PR 머지 타임아웃 장애처럼 **외부에서 머지가 막히는 구간**이 실제로 있었으니, 배포 워크플로에는 `concurrency`(동시 실행 취소)와 재시도를 명시해 두는 게 맞다.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-14 08:15 KST 기준)

**알림·요청**: 알림함은 API로 읽을 수 없어 `review-requested:@me` · `assignee:@me` · `mentions:@me`로 확인 — **전부 0건**. 열린 PR·이슈도 전 레포 0건. **대기 중인 리뷰 요청·할당 이슈·멘션 없음.**

| 레포 | 공개 | 마지막 푸시(UTC) | 야간 활동 |
| --- | --- | --- | --- |
| **arch-tech** | **private**(전일 public → 변경) | 11-13 23:10 | **커밋 36건**(11-13 05:27~23:10Z), PR #1 MERGED, 최종 배포 success |
| **memo** | private | 11-13 20:54 | 백로그 문서 1건(`architecture-task-backlog.md`, +119) |
| **poc-pickle** | private | 11-13 15:49 | 맥 러너 전환 커밋 6건 + 외부 계정 2건, 최종 배포 success |
| **poc-py** | private | 11-12 23:58 | 신규 없음 |
| **youtu-lib** | public | 11-13 23:23 | 오늘 브리핑으로 갱신 |

**arch-tech 야간 흐름(핵심만)**: 토큰 클라이언트(r4j 서킷·401 자가치유) → 토큰 어드민(파트너 발급 전략·AES-256-GCM·스케줄러·웹 UI) → resilience 스타터(메트릭 보존 핫스왑)·결제 클라이언트·관제 대시보드(SSE·카오스) → Better Stack 로깅 → NAS 자동배포 → **Spring Modulith `arch-api`(PR #1, 모듈 경계 `ApplicationModules.verify()`, Swagger)** → 포트 80 통합 포털·Prometheus·Grafana(Nginx/Dockerfile 내장 빌드로 전환) → **팬아웃 모듈**(가상 스레드 동시 발사·SSE 스트림·서킷 OPEN 시 자동 점검·6종 결과 상태).

**CI 실패 4건 — 모두 후속 성공으로 종결, 현재 열린 실패 없음**
| 실행 | 시각(UTC) | 원인 | 해소 |
| --- | --- | --- | --- |
| arch-tech 43509367749 | 21:17 | `prometheus.yml` **단일 파일 마운트** → OCI `not a directory` 오류 | 21:22 디렉터리 마운트로 전환 |
| arch-tech 43514023700 | 21:49 | 컨테이너 이름 충돌(`arch-token-admin` 기존 컨테이너 점유) | 22:26 `deploy.yml` 수정 |
| arch-tech 43518950724 | 22:23 | 동일(이름 충돌, `arch-prometheus` 포함) | 위 수정 반영 |
| poc-pickle 43445655190 외 5건 | 14:42~15:16 | `path "/volume1/docker/pickle/poc-py" not found` (맥 러너 경로) | 15:49 `synology path 수정` → success |

**마지막 배포 상태**: arch-tech `43525151098` success(23:10Z, 커밋 3ab4863) · poc-pickle `43456498504` success(15:49Z, 65250f0).

**유의**: arch-tech가 **private으로 전환**됐다(전일 브리핑 시점 public). 공개 링크로 공유하던 문서·README가 더는 외부에 보이지 않는다. 또 poc-pickle에는 `heesu.son <heesu.son@navercorp.com>` 계정이 PR 없이 main에 직접 푸시한 커밋 2건(11-13 02:45·03:08Z)이 남아 있다 — 브랜치 보호가 없으면 같은 경로가 계속 열린다.
