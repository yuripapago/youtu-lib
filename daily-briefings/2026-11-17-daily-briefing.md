# 2026-11-17 (화) 데일리 테크 브리핑

> **수집** 2026-11-17 08:15 KST · 최근 24시간(2026-11-16 08:15 ~ 11-17 08:15 KST = 11-15 23:15 ~ 11-16 23:15 UTC) 중심
> **소스** Hacker News(front·best·new), GeekNews, Alibaba Cloud·QwenCloud 문서·TNW·MarkTechPost, OpenAI Blog, Anthropic Research, Google Developers Blog·Blog.google, The Register·Ars Technica·Guardian(M365 회고), NBC·BBC·Reuters·Wired(은행 장애), FAA, iter.ca·GitHub Advisory(uBO), Tor Project, Majestic/siterate, spring.io, Inside.java, GitHub Releases(qdrant·llama.cpp·kotlin·spring-boot·spring-ai), GitHub Changelog, Gradle Release Notes, CMU News·CS Learn, Codedeck
> **오늘의 한 줄** — *"7월에 들어간 결함이 11월에 은행과 메일을 멈췄고, AI가 찾은 XSS가 공개 시한을 30일로 당겼다 — 결함의 나이와 발견의 속도가 함께 바뀐 하루."*

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **Qwen4-Max** 공개 — 2.4조 MoE·71B 활성·1M 문맥, 그런데 **가중치는 닫혔다** (HN 1위 848점·469댓글)

알리바바가 11-16 항저우에서 공개했다. 숫자보다 **비용 구조**와 **공개 범위**가 이 모델의 요점이다.

| 항목 | 내용 |
| --- | --- |
| 구조 | sparse MoE **총 2.4조 파라미터**, 토큰당 **71B만 활성**(Qwen3.8-Max와 같은 2.4T 규모에서 활성 파라미터 **25% 감소**). attention·residual·embedding 전반을 손본 'Qwen4 아키텍처' |
| 입력 | 텍스트·이미지·**비디오** 네이티브, **100만 토큰 문맥**, tool calling·structured output·explicit caching·CoT 요약 |
| 공개 범위 | **가중치 비공개(proprietary).** "Qwen4 시리즈의 더 작은 오픈웨이트 모델을 곧 공개"만 예고, 날짜 없음 |
| 자체 벤치 | Work360(721개 전문 분야)에서 전작 대비 High effort에서 **토큰 35% 적게**, 과제당 평균 비용 **$4.43 → $1.67**(약 60%↓) |
| 외부 비교 | TNW가 알리바바 차트를 읽은 결과 — **DeepSWE v1.1 75.7**(Claude Opus 5.5 74.2)로 앞서지만 **Terminal-Bench 4.0 48.5**로 Opus 5.5(59.6)·Kimi K3.1(67.2)에 뒤진다. Code Arena 실사용 리더보드 **6위** |
| 가격 | QwenCloud API **입력 $1.50 / 출력 $4.50**(1M 토큰, TNW 기준). 공식 발표는 **암묵 캐시 히트 입력 $0.075**/1M |
| 에이전트 사례 | 냉각 분자 탐색을 **48시간 연속 실행**(분자 설계→합성·시험→능동학습 4회 반복), 알츠하이머 과제에서 **툴 호출 454회·지표 평가 3,239회**로 후보 24개 설계 |

- 실무 판단: **문맥 1M + 71B 활성**은 긴 로그·약관·리포지터리를 한 번에 넣는 용도에 맞고, 과제당 비용은 **60% 줄어 전작의 약 38%**라는 게 알리바바 주장이다. 다만 코딩 주장은 벤치별로 갈린다 — DeepSWE는 앞서고 Terminal-Bench는 뒤진다. **"코딩에서 이겼다"는 헤드라인을 그대로 받아들이면 안 되는 전형적 형태**다.
- 폐쇄 가중치는 이번 세대의 방향 전환이다. 그동안 Qwen은 오픈웨이트로 로컬 스택(우리 브리핑 체인의 폴백 후보 포함)의 사실상 기본값이었는데, **플래그십은 API 전용**이 됐다. 로컬 폴백 계획이 Qwen 플래그십을 전제로 잡혀 있으면 다시 봐야 한다.
- <https://www.alibabacloud.com/en/press-room/alibaba-unveils-qwen4-max> · <https://thenextweb.com/news/alibaba-launches-qwen4-max-flagship-ai-model-without-open-weights> · <https://docs.qwencloud.com/changelog/models?model=qwen4-max-preview> · HN <https://news.ycombinator.com/item?id=50513215>

### 2. **M365 24시간 장애의 예비 보고서** — 7월에 들어간 잠복 결함 + 14% 용량 손실 + Teams 페일오버 (11-15 공개, 11-16 보도)

어제 브리핑에서 "염소가스 사고의 전달 경로"로만 다뤘던 사고의 **원인**이 나왔다. 세 조건이 각각은 정상이었는데 겹쳤다.

| 요소 | 내용 |
| --- | --- |
| ① 잠복 결함 | **2026년 7월** M365 네트워크 게이트웨이 서버용으로 배포된 변경. 내부 Windows 프로세스의 **lock contention**으로 CPU·메모리를 고갈시킴. 사전 테스트는 **"코어 수가 적은 더 약한 하드웨어"** 중심 → **코어가 많은 신형 장비에서 악화** |
| ② 트래픽 이동 | 장애 약 15시간 전 Teams가 대체 게이트웨이 → Exchange Online이 붙은 **1차 게이트웨이로 페일오버**. 그 자체는 정상 동작, 그러나 **부하량과 요청 구성**이 달라짐 |
| ③ 용량 손실 | 게이트웨이 플릿이 **14% 용량 부족** 상태(복구 진행 중). 정상 시엔 흡수 가능한 수준 |
| 연쇄 | 서버가 죽으면 다음 정상 엔드포인트로 라우팅 → 같은 결함 → 또 라우팅. **2시간 내 플릿 전체**, 최저점에서 **80% 이상 무응답**. 지역 격리 장치가 왜 실패했는지는 보고서에 없다 |
| 복구 | 재기동 무효(대기열이 남아 즉시 재고갈), **롤백 불가**(결함이 몇 달 전 것). 엔지니어가 **"패치의 패치"**(코드 경로를 이전 정상 상태로 되돌리는 업데이트)를 만들어 그룹별 적용 |
| 왜 Outlook만 오래 | Teams·OneDrive·SharePoint·Office는 게이트웨이 밖으로 **fail-out 가능**, **Exchange Online은 규모 때문에 불가** |
| 타임라인 | Ars 기준 **11-12 16:20 UTC ~ 11-13 16:10 UTC**(약 24시간), 11-14까지 모니터링. **최종 PIR 11-20 예정** |

- 배울 것은 "클라우드는 원래 불안정하다"가 아니라 **테스트 환경의 대표성**이다. 코어 수가 적은 장비에서 통과한 부하 테스트는 **프로덕션의 락 경합을 못 본다**. 그리고 **몇 달 된 결함은 롤백 대상이 없다** — 배포 단위가 아니라 **코드 경로 단위로 되돌릴 수단**을 미리 준비해 두어야 한다는 뜻이다.
- <https://www.theregister.com/saas/2026/11/16/microsoft-blames-monster-email-outage-on-software-defect-introduced-in-july-fix/5310026> · <https://arstechnica.com/gadgets/2026/11/microsoft-details-how-three-problems-combined-for-a-global-outage/> · <https://www.theguardian.com/technology/2026/nov/16/microsoft-global-outage-cause> · 블룸버그 <https://www.bloomberg.com/news/articles/2026-11-16/microsoft-bug-undetected-since-july-triggered-worldwide-outage>

### 3. **JPMorgan 체이스 "온프레미스 하드웨어의 소프트웨어 버그"** — 미국 주요 은행 온라인 뱅킹 동시 지연 (HN 667점·531댓글)

11-16(현지) 오전 9:30 ET경 시작해 14:00 ET에 정점, 저녁에 진정됐다. 원인이 **은행 코어의 소프트웨어 결함**으로 특정되면서 "핀테크 전체 장애" 서사는 접혔다.

| 항목 | 내용 |
| --- | --- |
| 진술 | JPMorganChase: *"**온프레미스 하드웨어 시스템의 소프트웨어 버그**에 따른 일시적 문제. **사이버 관련 증거 없음**"*. 시스템 전반 이슈 아님, 고객은 계좌·자금 접근 가능, 당일 내 해결 목표 |
| 확산 경로 | *"우리와 거래의 당사자인 범위에서"* 다른 은행이 거래 지연을 겪음. **Zelle은 "정상 동작"**, 은행 쪽 문제로 일부 이체 지연 |
| 신고 규모 | Downdetector 기준 체이스 약 **5,000건**(절반이 잔액·거래), 웰스파고 800여 건. BoA·US Bank·Truist·PNC·Capital One도 신고, 각사는 "직접 영향 없음" |
| 규제 대응 | 당국이 **즉시** 접촉해 "**국지적(contained)**이며 복구 진행 중"으로 판단 |
| 규모감 | 체이스 미국 내 고객 관계 **9,400만**, 지점 5,000·ATM 15,000 |

- 이 사건의 교훈은 **의존 그래프**다. 한 은행의 처리 지연이 Zelle·타행 거래로 번지는데, 각 참여자는 "우리 시스템은 정상"이라고 말할 수 있다. **경계마다 자기 상태만 초록인 구조**는 어제의 M365, 11-13의 FAA 지상정지와 같은 모양이다.
- 같은 날 HN 화제작 **"Software is breaking down at a systemic level"**(317점·197댓글)이 이 흐름을 정리한다 — 11-12 M365, 11-13 FAA 전국 지상정지(22개 관제센터 중 17개 불안정, **1만 2,462편 결항**), 10-21 PHP2Shell, eMudhra 약한 개인키, 5월 NPM Shai Hulud. 저자의 결론은 "AI는 원인이자 증상"이고, 진짜 문제는 **복잡한 시스템을 저품질 부품으로 조립해 전체를 이해하는 사람이 없다**는 것.
- <https://www.nbcnews.com/business/consumer/outages-chase-banking-online-branches-rcna609673> · <https://www.bbc.com/news/live/c893rpk23g56t> · <https://www.wired.com/story/jpmorganchase-bank-outage-oh-come-on/> · <https://www.breck-mckye.com/blog/2026/11/software-is-breaking-down-at-a-systemic-level/> · HN <https://news.ycombinator.com/item?id=50521045>

### 4. **uBlock Origin 원클릭 UXSS** — 사이트가 요소 선택기를 iframe으로 띄워 임의 trusted 필터를 심는다 (HN 592점·170댓글)

수정은 이미 끝났고, 공개된 건 **공격 경로와 그 발견 주체**다. 작성자는 **"GPT-5.6 Sol이 어려운 부분을 대부분 찾았고, PoC 코드는 Claude가 대부분 썼다"**고 명시한다.

| 항목 | 내용 |
| --- | --- |
| 전제 | uBO(MV2 Firefox)의 요소 선택기는 `web_accessible_resources/epicker-ui.html`을 **웹 페이지에 iframe으로 삽입**해 동작하고, 그 페이지가 백그라운드 스크립트와 메시지를 주고받아 필터를 추가한다 |
| ① UUID 수집 | 확장 내부 UUID는 설치마다 랜덤. 기본 uAssets 필터 `lootdest.*##+js(no-fetch-if, adsbygoogle, …)`의 fetch 훅 안에서 나가는 XHR URL을 **사이트가 후킹**해 UUID를 읽는다 |
| ② iframe 구동 | `postMessage({what:'epickerStart'}, '*', [port])` → `showDialog` → `resultsetDetails`로 선택기를 조작 |
| ③ 필터 주입 | 선택기는 원래 스크립틀릿 필터를 막지만, `opt` 필드(정상적으로는 `image` 같은 타입)에 **개행을 넣어** `image\ngoogle.com##+js(trusted-rpnt, script, …)`를 붙이면 **trusted 필터**가 들어간다 → 임의 사이트에서 임의 JS |
| ④ 클릭 | iframe을 `opacity:0`으로 겹쳐 "Create" 버튼을 누르게 유도(저자는 구현은 생략) |
| 수정 | 선택기가 content script에서 받은 **비밀번호**를 초기 메시지에 싣고, 공격자 메시지를 수용하지 않도록 |
| 범위 | MV2 Firefox가 주 대상, **uBO Lite(MV3)도 덜 심각한 유사 이슈** |

- 요점은 두 가지다. **확장 UI를 웹 페이지에 iframe으로 노출하면 그 UI는 이미 신뢰 경계 밖**에 있다(postMessage 발신자 검증이 없으면 그대로 뚫린다). 그리고 **필터·규칙 문자열을 외부 입력으로 조립하는 지점**은 정규화 검증이 없으면 명령 주입이 된다 — 백엔드의 SQL/템플릿 주입과 같은 종류의 실수다.
- 같은 날 Anthropic이 **CVD 공개 시한을 90일 → 30일(고위험)·45일(기타)로 단축**했다. "모델이 취약점을 훨씬 잘 찾게 됐고, 한 모델이 찾은 버그는 다른 모델도 찾는다"는 것이 근거다. **AI가 발견 속도를 바꾸면 유지보수자의 패치 창도 함께 줄어든다** — 우리 프로젝트의 의존성 패치 주기를 다시 봐야 하는 이유다.
- <https://iter.ca/post/ubo-uxss/> · <https://github.com/gorhill/uBlock/security/advisories/GHSA-cmqw-9r2w-6wv2> · HN <https://news.ycombinator.com/item?id=50513160> · Anthropic CVD <https://www.anthropic.com/research/coordinated-vulnerability-disclosure-timelines>

---

## 🤖 AI / LLM & Agentic 동향

**1) OpenAI, 의료용 컴퓨터 유즈 에이전트 성적표 공개 — 안전 99%, 그리고 "98% 싼 모델" (11-16).**
임상의 EHR 인박스 업무 325건 중 **99%가 안전** 판정, 유용성 평가 646건 중 **93%가 "매우/극히 유용"**. 행정 업무용 독립 벤치마크 HealthAdminBench(스탠퍼드)에서 **GPT-6 Astra 94.3%**, **GPT-6 Luna 91.8%** — Luna는 **과제당 8.5센트로 98% 저렴**하다. 파트너: CVS Health(주말에 몇 달치 문서 발송 적체 해소, 하루 수만 건 상담 처리, **Agent Kit 오픈소스**), Cigna(10개 넘던 시스템 탐색 → 수 초), Abridge, Ambience(모호하면 **멈추고 승인 요청**).
<https://openai.com/index/advancing-computer-use-for-healthcare-teams/> · 벤치 <https://som-shahlab.github.io/health-admin-bench-website/> · <https://github.com/cvs-health/cua-agent-kit>

**2) Anthropic, 취약점 공개 정책 단축 (11-16).**
고위험·치명 **30일**, 그 외 **45일**(기존 90일). 2027-01-01 이후 접수분부터 적용. 유지보수자 무응답 시 외부 코디네이터 에스컬레이션 **30일 → 15일**, 패치 후 상세 공개 **45일 → 30일**. 이미 90일을 넘긴 미공개 건은 **+30일 유예** 후 대시보드 게시. 적극 악용 중인 취약점의 **7일** 창은 유지. OSS Scanner처럼 **사람 검토 없는 AI 발견 건은 제외**.
<https://www.anthropic.com/research/coordinated-vulnerability-disclosure-timelines>

**3) Google, 에이전트 이상탐지(Agent Anomaly Detection) 실습 가이드 (11-16).**
Gemini Platform의 Agent Anomaly Detection(Private Preview). 에이전트가 이미 내보내는 **trace·log를 읽어** "맡은 일 밖으로 나간 세션"을 잡는다. 데모에서 급여 레코드를 읽은 세션을 **Tool misuse·High·확률 95%**로 표시하고, Cloud Monitoring·Security Command Center로 알림. 요구사항: ADK 1.2+ (2.0+ 권장), Cloud Trace/Logging 텔레메트리, US/EU 멀티리전.
<https://developers.googleblog.com/securing-your-agents-a-hands-on-guide-to-agent-anomaly-detection/>

**4) Google AdMob, 에이전트 스킬 + MCP 서버 예고 (11-16).**
`Ask AdMob`(Gemini 기반 계정 내 대화형), **AdMob agent skills**(SDK 마이그레이션·검증·새 광고 형식 구현용 플레이북, 공개), 그리고 **AdMob MCP 서버**(계정 데이터 읽기·광고 단위 쓰기·미디에이션 설정, 초기 테스트). "적용 전 사용자 승인"을 전제로 내세운다.
<https://blog.google/products/publisher-connections/new-google-admob-tools/>

**5) Claude Mythos가 AMD GPU 프리필 커널을 "거의 100% 자율"로 — 6~12개월 → 3일 (Tom's Hardware 11-16).**
Anthropic이 IPO 투자자에게 제시한 사례. AMD **MI355X**에서 자사 모델의 **프롬프트 처리(prefill)**를 "쓸 만한 성능"까지 끌어올리는 작업을 Mythos Preview가 **약 3일, 사실상 사람 개입 없이** 수행. CUDA 해자의 저변이 흔들린다는 해석이 붙는다. 단, 대화형 디코드가 아닌 prefill 구간 한정 결과다.
<https://www.tomshardware.com/tech-industry/artificial-intelligence/anthropic-says-claude-mythos-slashed-software-work-to-onboard-amd-gpus-from-up-to-12-months-to-around-3-days-writing-almost-100-percent-of-kernels-autonomously-chips-away-at-nvidias-cuda-moat>

**6) Codedeck이 11-16 정리한 것들 (요점만)**
- **Sage-S2.1-Flash** — 병렬 도구 호출·이미지 연결의 실행 조건(토크나이저 옵션·이미지 순서 패치·엔진별 제약 구분). <https://www.codedeck.kr/posts/sage-s21-flash-tool-image-deployment>
- **Memorilla 메모리 캐시**(11-14 병합) — 문서별 키·값 재사용, **메모리 모듈만 측정한 지연**과 GPU 메모리 증가 조건. <https://www.codedeck.kr/posts/memorilla-memory-cache-latency-gpu-conditions>
- **Google Cloud $300 크레딧 제한** — **11-16 이후** 체험 가입·유료 전환에 적용. AI Studio Gemini API의 기존 제외 조건·Prepay 과금은 별개. 무료 스택으로 PoC를 돌리는 입장에선 **적용일 이후 가입분의 한도**를 확인해야 한다. <https://www.codedeck.kr/posts/google-cloud-welcome-credit-gemini-nov16-eligibility>
- **HN Trends 11월 채용 중간집계 — AI 언급 51.6%** (집계 범위 주의). <https://www.codedeck.kr/posts/hn-trends-november-2026-ai-hiring-sample>
- **Tor 0.4.9.17**(IPv6 /64 단위 DoS 방어), **MONAKA SC26**(2027-04 서버 출하), **RustingStrata 1.2.3**(내장 채팅·서버 접근 권한). <https://www.codedeck.kr/posts/tor-04917-ipv6-prefix-dos-defense> · <https://www.codedeck.kr/posts/monaka-sc26-cpu-inference-server-shipment-2027> · <https://www.codedeck.kr/posts/rustingstrata-1-2-3-chat-ui-access-controls>

---

## ☕ 백엔드 / Spring / 아키텍처 엔지니어링

### 릴리스·지원 기한 — **이번 주 목요일이 Spring 대형 릴리스 데이**

| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| Spring Boot | **4.2.0-RC2** | 11-05 | **4.2.0 GA 마일스톤 마감일이 2026-11-19**. 4.2.0 42/58 완료, 4.1.3·4.0.10도 같은 날 마감 |
| Spring Boot | 4.1.2 / 4.0.9 | 10-22 | 현행 GA. **4.0 OSS 지원 2026-12-31 종료** |
| Spring AI | **2.1.0-RC2** | 11-05 | GA 11-19 예상. `DecisionModel`, 툴 인자 스트리밍, MCP "MODERN" 모드, Anthropic SDK **3.4.0**(temperature/topP/topK 거부·캐시 토큰 집계 변경) |
| Kotlin | **2.5.0-RC** | 11-11 | 정식 12월 예정 |
| Qdrant | **1.20.2** | **11-16** | HNSW를 **클래식 알고리즘 기본으로 복귀**(대형 세그먼트에서 `ef_construct`가 품질을 조율), 동일 필터 배치 검색 대폭 빨라짐, 리더 선출 직후 피어 합류 실패 수정 |
| llama.cpp | **b12445** | **11-16 20:44Z** | 야간 빌드 계속(b12426 → b12445) |
| Gradle | **9.9.1** | 11-10 | **보안 3건**(Critical 9.3: version catalog 값이 생성 accessor에서 코드로 실행 / High: daemon registry 신뢰 / High: 포트 스쿼팅). Kotlin DSL **증분 컴파일**로 대형 멀티프로젝트 스크립트 재컴파일 **최대 65% 단축** |

- **spring.io 블로그는 11-12(Bootiful Podcast, Guy Pardon) 이후 신규 글이 없다.** 4.2.0 GA를 앞둔 트레인 대기 구간이다. Dan Vega가 11-16 "목요일(11-19) 릴리스 캘린더에 **319개 버전**이 잡혀 있다 — Spring Boot 4.2.0·Framework 7.1.0·Spring AI 2.1.0·Batch 6.1.0"이라고 언급한 것이 이번 주 유일한 신호다(개인 게시 기준).
- **Inside.java는 11-16 "Valhalla의 값 객체를 JDK에 넣는 데 왜 3,000 커밋이 필요한가"**(David Simms, Devoxx Belgium)를 올렸다. 11-14 Newscast #117(적응형 힙·프리미티브 패턴 철회) 이후 첫 글.
- <https://github.com/spring-projects/spring-boot/milestones> · <https://spring.io/blog/2026/11/09/spring-ai-2-1-0-RC2-available-now> · <https://github.com/qdrant/qdrant/releases/tag/v1.20.2> · <https://docs.gradle.org/9.9.1/release-notes.html> · <https://inside.java/2026/11/16/devoxxbelgium-valhalla-value-objects/>

### 아키텍처 사례 — **X.com 재구축: 서버 3개 → 1개, 5명 + 에이전트** (원문 11-04, GeekNews 11-16 화제)

원문은 11월 초 X 엔지니어링 블로그다. 국내에 늦게 돌면서 11-16 GeekNews 상위에 올랐고, **AGENTS.md·스킬·린트로 에이전트 산출물을 통제하는 방식**이 우리 상황과 가장 겹친다.

| 축 | 내용 |
| --- | --- |
| 문제 | 같은 URL을 **세 서버**(rweb·rweb SSR·Jetfuel SSR)가 각자 렌더링 → SEO 수정이 한쪽에만 반영되는 드리프트, 비로그인 사용자는 **가장 느린 경로**(HTML 셸→JS→GraphQL) |
| 해법 | 단일 서버 + **스트리밍 SSR**. 첫 응답부터 실제 콘텐츠, 서버가 받은 GraphQL 응답을 페이지에 **한 번만** 실어 브라우저가 재생(Relay 저장소 직렬화 제거 — 기존엔 원본보다 4~5배 커졌다) |
| 대상별 응답 | 사람=스트리밍 HTML, **검증된 크롤러**=Suspense 전부 해결된 완성 HTML(하이드레이션 없음), **링크 미리보기 봇**=제목·설명·카드 이미지만. 크롤러 판별은 **User-Agent가 아니라 엣지의 IP 검증** |
| 수치 | Slow 4G에서 첫 게시물 **23.9초 → 2.7초**, 프로필·게시물 8~13배. 압축 JS **3.0~3.6MB → 0(프로필·게시물)/1.9MB(홈)**. 크롤러 HTML은 Googlebot의 **2MB 상한**에 맞춰 SVG 스프라이트·구조화 데이터 중복 제거로 감량 |
| 에이전트 운영 | 루트 `AGENTS.md`(named export, barrel 금지, 신뢰 경계에서 외부 데이터 검증, 린트·타입·테스트 통과 전 커밋 금지) + **25개 이상의 스킬**(커밋·PR·CI 통과까지·의존성·번역) + `oxlint` 커스텀 플러그인 + **"anti-slop" 플러그인**(연쇄 타입 단언, `unknown` 파라미터, 빈 객체 조건부 전개 거부) |
| 이전 전략 | 기존 SSR 서버를 **동작 명세로 사용** — 에이전트가 옛 동작을 읽어 재구현하고 테스트로 고정, 크롤러(8월)→비로그인→로그인(국가별) 순. 엣지 `ngroutes` + 기능 스위치로 즉시 되돌림 |

- 우리에게 그대로 옮길 것: **"옛 구현 = 스펙"**. 에이전트에게 리팩터링을 맡길 때 가장 신뢰도가 높은 검증은 "원본과 같은 응답을 내는가"를 비교하는 **패리티 스크립트**다. 그리고 **린트로 금지 패턴을 강제**하는 편이 문서로 부탁하는 것보다 오래 간다.
- <https://blog.x.com/en/engineering/rebuilding-the-global-town-square-from-scratch> · GeekNews <https://news.hada.io/topic?id=37245>

### 운영 교훈 A — **부하 테스트는 "약한 하드웨어"에서 하면 프로덕션의 락을 못 본다**
M365 사례의 일반화. 락 경합은 **코어가 많을수록** 심해지는 종류가 있다. 성능 시험 장비의 코어·메모리·디스크가 프로덕션보다 작으면, 통과는 **"작은 장비에서 통과"**일 뿐이다. CI 러너(우리는 맥 스튜디오 self-hosted)와 배포 대상의 하드웨어 차이도 같은 항목으로 본다.

### 운영 교훈 B — **몇 달 된 결함은 롤백이 안 된다**
배포 아티팩트 단위 롤백(이전 이미지 태그)만 준비해 두면, **잠복 결함이 뒤늦게 터진 날** 아무것도 되돌릴 수 없다. 필요한 것은 ① 코드 경로·기능 플래그 단위로 끌 수 있는 스위치 ② "결함이 들어간 시점"을 특정할 수 있는 **배포 이력과 설정 이력의 연동**. 이번 사고는 후자를 못 해서 **패치를 새로 만들어 되돌렸다**.

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) 아마존 블랙프라이데이 "딜"의 3분의 1은 할인이 아니었다 — Groundwork 조사 (HN 792점·480댓글).**
2024·2025 블랙프라이데이·사이버먼데이 **아마존 자사 딜 페이지**에 실린 **350개 이상** 상품 가격을 추적. **약 1/3은 세일 기간 가격이 행사 전 한 달과 같거나 더 비쌌고**, **약 1/10은 더 비쌌다**(그중 중앙값 +19%). 헌터 레인부츠 $126.99가 9일 전 $79.97(59%↑), UGG 부츠 $179.98이 4주 전 $98.40(83%↑). 아마존은 조사 후 해당 딜 페이지를 내렸다(아카이브로 확인 가능). 권고: **"최근 30일 최저가 표시" 의무화**.
<http://groundworkcollaborative.org/news/amazons-decoy-deals-groundwork-investigation-reveals-nearly-1-in-3-black-friday-and-cyber-monday-deals-advertised-by-amazon-offered-no-savings-to-shoppers-nearly-1-in-10-w/> · HN <https://news.ycombinator.com/item?id=50517488>

**2) Tor, 지속 DDoS 대응 — 0.4.9.17 보안 릴리스 (11-13, 11-16 논의 지속).**
"네트워크에 대한 진행 중인 DDoS 공격에 대한 신속 대응"이며 대부분의 릴레이 운영자가 영향을 받았다. 릴레이가 **대량 클라이언트 회로**로 과부하 상태. 이번 릴리스는 **IPv6 처리를 /64 단위**로 다루는 DoS 방어가 핵심이고, **추가 방어를 평가 중이라 곧 또 릴리스**한다고 예고. 릴레이 운영자는 즉시 업그레이드(업그레이드 후 별도 조치 없음).
<https://forum.torproject.org/t/security-release-0-4-9-17/22454> · HN <https://news.ycombinator.com/item?id=50512441>

**3) `w2rt6byl5nf1opmc-a.com` — Majestic Million 8위의 정체는 "프로덕션에서 한 내부 실험" (HN 310점·140댓글).**
블로그 주인은 백링크 알림으로 이 도메인을 발견. Majestic 순위 **8위**(google·facebook·youtube 다음), nofollow 백링크 **2억 4,108만 개**, 참조 도메인 516만 개인데 **실제 백링크는 53개**뿐이고 도메인은 11-11에 처음 등록됐다. 11-16 Majestic 직원이 댓글로 답했다 — *"내부 실험용으로 괴상한 도메인을 골랐는데, 리포트에서 빼는 필터를 빠뜨렸다."*
<https://jack.cab/blog/siterate> · HN <https://news.ycombinator.com/item?id=50517602>

**4) Nvidia 흐름 — "다음은?" (Steve Blank 457점, Wired 240점).**
Steve Blank는 젠슨 황 사임 이후의 Nvidia를 **금융 구조**(네오클라우드 보증·순환 금융)와 제품 사이클 관점에서 정리했고, Wired는 *"황 사태 이전에 이미 Nvidia는 곤란했다"*는 각도다. HN 댓글 566개는 대부분 **AI 자본구조**로 갈렸다.
<https://steveblank.com/2026/11/15/whats-next-for-nvidia/> · <https://www.wired.com/story/nvidia-was-already-in-trouble-before-the-jensen-huang-mess/>

**5) 그 외 눈에 걸린 것 (11-16 HN)**
- **Protomaps: 지도 타일 셀프호스팅이 싸고 쉬워졌다** (452점) — PMTiles 단일 파일 + HTTP Range. 사내 지도·오프라인 타일이 필요할 때. <https://bruceroettgers.eu/blog/protomaps/>
- **Lean으로 검증했다는 주장을 읽는 법** (279점) — "정리가 Lean에서 검사됐다"와 "정리가 참이다" 사이의 간극. <https://xenaproject.wordpress.com/2026/11/12/and-the-proof-is-checked-in-lean/>
- **Handwriting, as a Font** (453점) — 손글씨를 폰트로. <https://ductus.sorenblank.com>
- **Carnegie Mellon, CS 강의 무료 공개** (303점) — 아래 영상 섹션 참고. HN <https://news.ycombinator.com/item?id=50522980>
- **"Frame" 31.5" 컬러 ePaper 디스플레이** (253점, Geerling) — 상시 켜두는 대시보드용 하드웨어. <https://news.ycombinator.com/item?id=50516473>

**6) 개발 도구 쪽 공지 (GitHub Changelog, 11-16)**
- **Copilot 모델 폐기 발효**: Claude Opus 4.8·Claude Opus 5·**GPT-5.6 Terra**가 11-16부로 대부분의 Copilot 경험에서 폐기. 대체는 Opus 5.5 / GPT-6.1 Sol·GPT-6 Luna. (11-13에 예고된 **12-14 폐기 목록**과는 별건 — 12월에 Fable 5·Haiku 4.5·Gemini 3.8 Flash·Argon·Grok 4.6·Kimi K3가 추가로 빠진다) <https://github.blog/changelog/2026-11-16-github-copilot-models-deprecated-on-november-16-2026>
- **AI Scan 일괄 시작**, **계정당 예산 한도 40,000개**로 확대, GitHub Mobile iPhone Duo 지원. <https://github.blog/changelog/>

---

## 📺 추천 테크 영상·읽을거리

### ⭐ **Carnegie Mellon CS Learn 출범 — 강의를 무료로, 이번 주 매일 한 과목씩** (11-16)

CMU Griffin School of Computer Science가 **CS Learn**(cs.cmu.edu/learn)을 열었다. 이미 녹화하던 인기 강의를 YouTube와 자체 사이트에 올리고, 학생 얼굴은 흐리게·질문 음성은 잘라내고 **질문 텍스트만** 남기는 AI 보조 후처리로 **개강 몇 주 안에** 공개한다.

| 공개일 | 과목 | 강사 |
| --- | --- | --- |
| 이미 공개 | 10-605 Machine Learning with Large Datasets / 15-110 Principles of Computing | William Cohen / Franceska Xhakaj |
| **11-17(오늘)** | **17-214 Principles of Software Construction**, **15-442 Machine Learning Systems** | Hammad Ahmad / Tianqi Chen·Zhihao Jia |
| 11-18 | **17-635 Software Architectures**, 17-643 Quality Management | Swarnalatha Ashok·Scott Pavetti / Jeffrey Gennari·Christopher Timperley |
| 11-19 | **11-768 AI Agents**, **17-645 Machine Learning in Production** | **Daniel Fried·Graham Neubig** / Christian Kästner·Bogdan Vasilescu |
| 2027-01 | 15-259 Probability and Computing, 11-777 Multimodal ML, 17-619 Product Management 등 | — |

- 백엔드 공부에 바로 쓸 것: **17-635 Software Architectures**(수요일), **17-645 ML in Production**(목요일), **15-442 ML Systems**(오늘). 에이전트 쪽은 **11-768 AI Agents**(Neubig — SWE-bench 쪽 연구자).
- <https://www.cmu.edu/news/stories/archives/2026/november/cmu-opens-access-to-top-computer-science-classes-through-free-cs-learn-platform> · <https://www.cs.cmu.edu/learn/>

### Codedeck — YouTube 신규 업로드 없음, **codedeck.kr에 11-16자 7편**

| 글 | 날짜 | 링크 |
| --- | --- | --- |
| Sage-S2.1-Flash — 병렬 도구 호출·이미지 연결의 실행 조건 | 11-16 | <https://www.codedeck.kr/posts/sage-s21-flash-tool-image-deployment> |
| Memorilla 메모리 캐시 — 반복 질문의 지연·GPU 메모리 조건 | 11-16 | <https://www.codedeck.kr/posts/memorilla-memory-cache-latency-gpu-conditions> |
| HN Trends 11월 채용 — AI 언급 51.6%의 집계 범위 | 11-16 | <https://www.codedeck.kr/posts/hn-trends-november-2026-ai-hiring-sample> |
| Tor 0.4.9.17 — IPv6 /64 DoS 방어·통계 변경 | 11-16 | <https://www.codedeck.kr/posts/tor-04917-ipv6-prefix-dos-defense> |
| MONAKA SC26 — CPU 추론 수치·2027년 서버 출하 | 11-16 | <https://www.codedeck.kr/posts/monaka-sc26-cpu-inference-server-shipment-2027> |
| RustingStrata 1.2.3 — 내장 채팅 개편·서버 접근 권한 | 11-16 | <https://www.codedeck.kr/posts/rustingstrata-1-2-3-chat-ui-access-controls> |
| Google Cloud $300 크레딧 제한 — Gemini platform 적용일·API 과금 구분 | 11-16 | <https://www.codedeck.kr/posts/google-cloud-welcome-credit-gemini-nov16-eligibility> |

### 읽을거리
- **X 엔지니어링 "Rebuilding the global town square from scratch"** — 오늘 아키텍처 섹션의 원문. 패리티 스크립트·기능 스위치·anti-slop 린트를 자기 팀 규칙으로 옮길 때 기준점이 된다. <https://blog.x.com/en/engineering/rebuilding-the-global-town-square-from-scratch>
- **Anthropic "The components of agent memory"(2026-11-06)** — 메모리 노트·드리밍(유휴 시간에 대화 로그로 노트를 검증·재작성)·위키. 포켓몬 실험에서 **Sonnet 3.7은 413개 파일·배지 1개, Fable 5는 9개 파일·배지 4개**. "많이 저장"이 아니라 **"무엇을 버릴지"**가 핵심이고, 자동 주입 계층은 **작게**(Claude Code auto memory는 `MEMORY.md`의 **첫 200줄/25KB만** 로드). <https://www.anthropic.com/research/components-of-agent-memory>
- **Jimmy Breck-McKye "Software is breaking down at a systemic level"** — 오늘 3번 헤드라인의 사상적 정리. <https://www.breck-mckye.com/blog/2026/11/software-is-breaking-down-at-a-systemic-level/>

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 3가지

**1) `spring-ai-ops`는 밤새 과제 19의 Phase 1→2를 통과했고, 대신 리뷰 부채 34건이 쌓였다.**
- 사실: **PR #5(자율 에이전트 코어) 03:59Z 머지** → `#42`에서 그 리뷰 후속(P0 2·P1 7·P2 16) 처리 → `#51`(패치 산출물 영속·handoffSummary·ops 폴링/워치독), `#52`+`#55`(**PostgreSQL 원장 `ops_agent` + 승인 카드 실가동** — nonce·TTL·24h 락, PG 자동 복귀 P1), `#49`(**cc-worker 멀티모듈 Phase 1** — 잡 계약·헤드리스 claude·격리 worktree), `#50`(팀 봇 **단일 창구** 스펙: 매니저 1명·브리프 패킹/결과 인젝션·Stateless vs `--resume`·압축·야간 정리), `#59`(Step 3 단일 창구 + 라이브 버그 3건), `#60`(위임 분기 축소), **`#62`(Step 4 — 단체방 Listen Silently·야간 기억 상한 가지치기·Eval 41문항)**. `#58`로 **AI 제공자별 설정 분리**(`OPS_AI_PROVIDER`·`KNOWLEDGE_LLM_PROVIDER`), `#48`로 **fastTest/integrationTest 태그 분리**+PR 테스트 템플릿, `#4`로 perf-test ChatOps.
- 남은 것: **PR #63**(의도 오분류·/analyze 관측 공백 + PR #59 리뷰 반영, **+967/−99, 27파일**)이 열려 있고, **hwajjdev 리뷰 이슈 34건**(11-15 23:59Z~11-16 05:48Z)이 `state: open`. 이 중 `#45`(기억 추출 규칙이 0.9 신뢰도로 남의 사실을 덮어쓰고 요청 없이 삭제)·`#44`(k6 출력·미리보기가 리뷰한 것을 조용히 바꿈)는 이미 `#47`로 일부 반영됐다.
- 오늘 뉴스와 붙는 지점: **Step 4의 "야간 기억 상한 가지치기"가 Anthropic의 드리밍과 같은 문제**를 푼다 — 주입 계층을 작게 유지하고, 오래된 노트는 유휴 시간에 로그와 대조해 고친다. 그리고 **Google의 Agent Anomaly Detection**이 "trace로 망가진 세션을 찾는다"는 접근은 우리 `#45`·`#44`(기억 오염·산출물 변조)를 **런타임 탐지**로 잡는 같은 방향이다. 지금은 리뷰로 잡고 있으니, 다음 단계에서 **세션 trace에 "맡은 일 밖" 판정 규칙**을 넣을 근거가 생겼다.

**2) `arch-tech`는 시크릿 경로를 **SOPS → Infisical**로 갈아탔는데, **실 워크플로에는 아직 안 붙었다.**
- 사실: `#24`(WireMock·k6 부하 테스트 1단계 — 격리 네트워크 + Mock Guard) 머지(06:27Z), **`#25`(Infisical Universal Auth 전환) 머지(17:12Z)** → Deploy success. 템플릿 `ci/deploy-mac-studio.yml`은 Infisical 27곳 참조, `ci/test-infisical-env.sh`가 **가짜 curl로 67항목** 검증(GNU/BSD `stat` 둘 다 지원), `docs/SECRETS.md` 신규.
- **확인이 필요한 구멍**: `.github/workflows/deploy.yml`은 **Infisical 참조 0건**(spring-ai-ops는 두 워크플로 모두 반영 완료). PR 본문도 *"`workflows` 권한이 없어 손대지 않았고, 머지 후 복사 1회 필요"*라고 적고 있다. 그리고 루트에 **`.sops.yaml`과 `secrets/`가 그대로 남아** 있다(spring-ai-ops는 삭제). 리뷰 이슈 **#40 "템플릿과 실행본이 반대 방향으로 갈라졌다"**가 정확히 이 상태다.
- 오늘 뉴스와 붙는 지점: **Gradle 9.9.1**로 올리면 보안 3건이 해결되고 Kotlin DSL 재컴파일이 **최대 65%** 빨라진다. 세 레포(arch-tech·spring-ai-ops·poc-pickle) 모두 래퍼가 **9.8.2**에 머물러 있고, 셋 다 `gradle/libs.versions.toml` 버전 카탈로그를 쓴다 — 9.9.1이 고치는 **Critical(9.3) 취약점이 정확히 "version catalog 값이 생성된 accessor에서 코드로 실행"**되는 경로다. 샌드박스 테스트가 느렸던 문제와도 같은 줄에 있다.

**3) 이번 주 목요일(11-19)이 Spring 4.2.0 GA — 그 전에 RC2로 한 번 돌려보는 게 맞다.**
- 사실: 4.2.0·4.1.3·4.0.10 마일스톤 마감일이 **2026-11-19**, Spring AI 2.1.0 GA도 같은 트레인. **4.0 OSS 지원은 12-31 종료**.
- 우리 레포 상태: `spring-ai-ops`는 `libs.versions.toml` 기준 **Spring Boot 4.1.2 / Spring AI 2.0.0 / Kotlin 2.3.21 / Java 21**로 돌고 있고(주석에 "Spring AI 2.0.0은 4.1.0 기준 빌드라 4.1.2와 그대로 맞물린다"고 적혀 있다), Spring AI 2.1.0-RC2의 **Anthropic SDK 3.4.0**은 `temperature`·`topP`·`topK`를 **거부**하는 API 버전을 기본값으로 쓴다. 우리 브리핑 체인·리뷰 봇이 샘플링 파라미터를 직접 넘기고 있다면 **업그레이드 시점에 조용히 실패**할 수 있다. 또 캐시 토큰 집계 방식이 바뀌어 대시보드 비용 숫자가 달라진다.
- 순서 제안: ① 이번 주는 **RC2로 브랜치 하나에서만** 기동·Eval 41문항 회귀 확인 ② GA 후 샘플링 파라미터 호출부 전수 확인 ③ Gradle 9.9.1 래퍼 갱신은 그와 별개로 먼저(보안 패치라 독립적).

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-17 08:15 KST 기준)

**알림·요청**: `review-requested:@me` 0건 · 열린 PR **2건**(spring-ai-ops #3 테스트 샘플, #63 수정) · 열린 이슈 **54건**(arch-tech 16, spring-ai-ops 38 — 그중 **49건이 hwajjdev 리뷰 이슈**, arch-tech 15 + spring-ai-ops 34).

| 레포 | 공개 | 마지막 푸시(UTC) | 야간 활동 |
| --- | --- | --- | --- |
| **spring-ai-ops** | private | **11-16 22:50** | 커밋 **28건**. 과제 19 Phase 1 머지 → Phase 2 Step 1·2(PG 원장·승인 카드), cc-worker Phase 1, 단일 창구 스펙, Step 3·4, AI 제공자 분리, SOPS→Infisical, fastTest 분리. **PR #63 열림** |
| **arch-tech** | private | **11-16 17:12** | 커밋 2건(#24 k6·WireMock 1단계, **#25 Infisical 전환**). 열린 PR 0, 이슈 16(리뷰 15+계획 1). `.sops.yaml`·`secrets/` 잔재, 실 워크플로 미반영 |
| **memo** | private | 11-16 01:02 | 커밋 3건 — `.claude/skills` + 설치 가이드, **anti-slop 가이드·CLAUDE.md 규칙·deslop 스킬**, 코드 스터디 Ch.4 |
| **poc-pickle** | private | 11-13 15:49 | 신규 없음 |
| **poc-py** | private | 11-12 23:58 | 신규 없음 |
| **youtu-lib** | public | 11-16 02:13 | 오늘 브리핑으로 갱신 |

**CI**: arch-tech 마지막 배포 **success**(11-16 17:12, `#25` 머지 직후). spring-ai-ops **전부 success**(최근 22:28Z `#62`, 17:58Z knowledge 수동 배포). **열린 실패 없음.**

**arch-tech 흐름(11-16)**
- `d0e8af3` **⚡ perf: WireMock·k6 자동 부하 테스트 1단계**(#24) — 격리 네트워크 + Mock Guard, `docs/PERF_TEST_GUIDE.md`.
- `421128a` **chore(ci,secrets): Infisical(Universal Auth) 전환**(#25) — `ci/infisical-env.sh`(로그인→`GET /api/v4/secrets`→`.env` upsert, 실패 시 `.env` 무변경·배포 중단), 67항목 자체 테스트, `docs/SECRETS.md`(키별 "비우면" 동작 표), `ci/deploy-synology.yml` 삭제.

**spring-ai-ops 흐름(11-16, 핵심만)**
- **과제 19**: `fab9bea` Phase 1 코어(#5) → `42d5efe` PR #5 리뷰 후속(P0 2·P1 7·P2 16) → `d5dd4ad` Phase 2 Step 1(패치 산출물 영속·handoffSummary·ops 폴링/워치독·Draft PR) → `98a125f`+`48f0500` Phase 2 Step 2(**PG 원장·승인 카드** nonce·TTL·24h 락, PG 자동 복귀) → `2fb30c3` Step 3(단일 창구·브리프 패킹·인계 인젝션, LLM 15초·폴백 가시화) → `54fdbab` 위임 분기 축소 → `929e697` **Step 4**(단체방 Listen Silently·야간 기억 상한 가지치기·**Eval 41문항**).
- **cc-worker**: `801cd9f` Phase 1(잡 계약·헤드리스 claude·격리 worktree) → `45d2f8e` 완전 분리 하이브리드 워커 스펙 → `2152314` 다이어그램 정렬.
- **시크릿·CI**: `669fb9a` SOPS 복호화 배포 연결 → `84983a0` sops CLI 고정 바이너리(sha256 검증) → **`c2ce28e` Infisical 전환**(`.sops.yaml`·`secrets/` 삭제, `docs/SECRETS.md` 13키 표) → `197cecf` 빈 Secret이 `.env`를 가리는 문제 수정 → `ea6c6b3` 워크플로 env 정리.
- **구조**: `56fa563` 테스트 스위트 태그 분리(fastTest·integrationTest) + PR 테스트 템플릿, `6ed623e` AI 제공자별 설정·시크릿 분리.
