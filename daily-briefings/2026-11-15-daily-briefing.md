# 2026-11-15 (일) 데일리 테크 브리핑

> **수집** 2026-11-15 08:15 KST · 최근 24시간(2026-11-14 08:30 ~ 11-15 08:15 KST) 중심
> **소스** Hacker News(front·best), GeekNews, WSJ 인용 보도(TechSpot·TechPowerUp·Gamerant), SEC 8-K·NVIDIA Newsroom, WIRED, CNBC·BBC·Xinhua·SpaceNews, claude.dev, Google Research Blog, Claude Code CHANGELOG, ggml/llama.cpp·Kotlin·Qdrant GitHub Releases, Maven Central, PostgreSQL News, Inside.java, Cloudflare Blog RSS, GitHub Blog, PyPI Blog, Project Zero, EFF, CrissyField Research, Codedeck(YouTube 채널 RSS + codedeck.kr)
> **오늘의 한 줄** — *"칩의 왕좌는 비었고, AI가 만든 앱은 스스로 데이터를 흘렸고, 에이전트 1,000개는 정리를 증명했다. 오늘 뉴스는 전부 '누가 무엇을 책임지는가'로 모인다."*

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. 엔비디아 후폭풍 — **WSJ "순환금융으로 매출을 만들었다"**(HN 1,018점) · 직원 1,600+ "회장님 돌려달라" · 이사는 사건 몇 주 전 **$10억 매도**

젠슨 황 사임(11/11 이사회 수락, 11/12 공시)이 사흘째 확장 중이다. 오늘 HN 1위는 **매출의 자기순환 구조** 폭로다.

| 항목 | 내용 |
| --- | --- |
| WSJ 보도(11/13) | 엔비디아가 **GPU를 살 수 없는 AI 스타트업·소규모 클라우드에 자금 보증**을 대고, 그들이 다시 엔비디아 GPU·데이터센터를 사게 했다. TSMC에 이미 약정한 칩이 **"avalanche"**로 들어오는데 살 곳이 없던 상황을 메우려는 목적 |
| 관련 수치 | OpenAI 대상 **$2,500억 규모 백스톱** 딜을 줄이자는 Colette Kress(CFO) 주장에 황이 **"deer in the headlights"**라며 압박했다는 진술. 딜을 주도한 축은 **Nico Caprez VP(황의 사위)**·Raj Mirpuri VP — 2026년 봄부터 신용보증 상품을 밀었다는 보도 |
| 반론 | 헤드라인의 "shortage engineered"는 미확인. 보도된 동기는 **공급 과잉 해소**, 그리고 D램·GDDR은 SK하이닉스·삼성·마이크론이 만든다 — 한 GPU 설계사가 통제할 수 있는 대상이 아니다(비판적 정리: shattered.io). 즉 **금융 구조 비판은 성립, 가격 조작 입증은 없음** |
| 사내 반발 | 사내 건의함 글에 **1,600개 넘는 댓글**, 대부분 복귀 지지. 익명 "If you believe in the mission, bring back THE boss" · 전사 메일 "I stand by Jensen". **일론 머스크도 11/13 서명**("A petition is underway to reinstate Jensen and I'm signing it too") |
| 시장·조사 | 사건 이후 **시총 약 $1조 증발**, 의회·FBI 조사, 대중 수출통제 재개 가능성. 이사 1명이 사건 몇 주 전 **약 $10억(1.3조원) 매도** |
| 후계 | 권한대행 Colette Kress(CFO 겸직), 이사회 의장은 창립 이래 첫 외부 인사 **Harvey C. Jones**. Arm CEO **Rene Haas** 접촉설은 엔비디아가 "공식 접촉 없음"으로 부인, 서치펌 선정 단계 |

- 사임 공시: 이사회 **11/11 수락**, 대행 체제 즉시 발효. 사유는 회사 공시에 없고, 황은 직원 메일에 "아내에게 불성실했다, 변명 없다"고 썼다. 배경 보도는 중국 정보자산 의혹·복수 차원의 관계.
- <https://www.sec.gov/Archives/edgar/data/1045810/000104581026000082/nvda-20261111.htm> · <https://nvidianews.nvidia.com/news/nvidia-announces-ceo-transition> · TechSpot <https://www.techspot.com/news/114558-nvidia-deliberately-kept-gpu-prices-high-through-circular.html> · HN 1018 <https://news.ycombinator.com/item?id=50493251> · WIRED <https://www.wired.com/story/nvidia-employees-petition-for-jensen-huangs-return/> · HN 812 <https://news.ycombinator.com/item?id=50477231> · 반론 <https://shattered.io/nvidia-engineer-gpu-shortage-funded-startups-250-billion-zero-proof/>

### 2. Lovable 앱 **15만 9천 개** 분석 — 로그인 없이 **여권번호·20만 건 메시지**가 열려 있었다 (HN 555점)

독일 보안업체 Crissy Field가 Handelsblatt와 공동 조사. **AI로 앱을 만드는 일과 데이터를 지키는 일은 다른 문제**라는 걸 수치로 보여줬다.

| 항목 | 값 |
| --- | --- |
| 표본 | Common Crawl 기반 Lovable 프로젝트 **159,041개**(2025-12~2026-09), 이 중 Supabase 연동 **34,545개** |
| 1차 노출 | 로그인 없이 초기 로딩에서 DB 레코드를 내보낸 사이트 **24,664개** |
| 추가 조회 | 읽기 전용 요청만으로 **7,403개 사이트(30%)**의 **10,560개 테이블**에서 이메일 포함 레코드 확보 |
| 대표 사례 | 8만 프로필 + **20만 건 이상 채팅 메시지**, 티켓·식음 판매 수만 건(결제정보·티켓코드), 임차인 관련 내부 메일 AI 요약, **여권번호·만료일·국적 포함 신원문서 1,000여 건**, 계약서·송장·개인 앨범·WhatsApp 대화 |
| 한계(저자 명시) | 30%는 **확정 유출률이 아니다**. 공개 연락처·테스트 데이터가 섞였고, 표본도 전체의 대표값이 아니다. 실제 다운로드분 기준 집계 |
| 함의 | Lovable은 **편의성을 팔고 보안 책임은 약관으로 고객에게** 넘긴다. 그리고 DB 연동 패턴이 반복되므로 **한 앱에서 통한 접근 제어 오류가 다른 앱에도 그대로 통한다** |

- <https://www.crissyfield.de/research/lovable-en/> · HN 555 <https://news.ycombinator.com/item?id=50499017> · GeekNews <https://news.hada.io/topic?id=37169>

### 3. Anthropic — **에이전트 1,000개가 페르마의 마지막 정리를 28.7시간에 Lean으로** (Fable 5.2 초기 버전, HN 재조명)

9월의 1차 형식화(연구자 지휘 + Prove2Me 플랫폼, 수십 에이전트, 11일, 약 60억 출력 토큰, Lean 1,300만 줄)를 **도메인 플랫폼도 사람 감독도 없이** 재현한 실험. 원문 발행은 **11/09**, 국내 커뮤니티에 11/14 밤 재조명됐다.

| 항목 | 9월 | 이번(장난감 실험) |
| --- | --- | --- |
| 구성 | 연구자 + 수십 에이전트 + Prove2Me | **리드 1 + 헬퍼 999**, 조율 도구는 **Git 브랜치 + 공유 Postgres** 뿐 |
| 시간 / 토큰 / 증명 길이 | 11일 / 60억 / 1,300만 줄 | **28.7시간 / 1/3 / 1/7** |

- 리드가 **첫 10분에 스스로 5개 테이블**(Announce·Assign·Notes·Subtasks·Landed)을 만들고, 헬퍼 999명에게 문장별 16~17명 크루를 배정. 47개를 "예산 내 불가능"으로 접었지만 70분 만에 10개를 다 증명하고 판단을 뒤집었다.
- 가장 어려운 **Langlands–Tunnell**은 헬퍼 약 750명이 붙어 **10단계·2,418개 작업 트리**로 쪼갰고 28.4시간에 통과, 최종 정리는 28.7시간. 리드는 "Fermat's Last Theorem is on main, kernel-checked."
- 안전 경계가 실험의 전제: 에이전트마다 **네트워크 없는 경량 VM**, sudo 제거, 샌드박스 안에 API 키 없음, SQL 문당 5초 제한, **모든 셸 명령·파일 수정을 실행 전 LLM 분류기가 검사(fail-closed)**. 100만 건 넘는 툴 호출 중 차단 기준에 걸린 건 없었다.
- 실패 기록도 그대로: 공유 머신 1대로는 **100개 근처에서 메모리·디스크·CPU 고갈**(100개 런의 절반 이상 중단·재시작) → 1,000개 개별 샌드박스로 전환. 리드가 너무 일찍 멈추지 않도록 **남은 시간 알림 19회** 자동 발송. 저자들은 "도메인 하네스의 필요를 없애는 결과는 아니다"라고 못박았다.
- 외부 검증은 아직: Lean 코드 미공개(11/12 기준), 형식화된 명제가 페르마의 주장과 같은지에 대한 별도 확인 언급 없음.
- <https://claude.dev/blog/safely-scaling-multi-agent-teams-with-fable-5-2/> · GeekNews <https://news.hada.io/topic?id=37159> · 비판적 검토 <https://papex.org/blog/fermats-last-theorem-lean-formalization-ai-agents>

### 4. SpaceX **스타십 우주선 첫 포획** — 22시간 반 궤도 비행 후 '젓가락'에 안겼다 (HN 428점)

11/13 발사한 **Flight 15**. 부스터는 포획을 포기하고 멕시코만 해상 착수 중 폭발했지만, 상단 우주선(Ship 42)이 **지구 14바퀴**를 돈 뒤 11/14 15:20 CT(한국 11/15 06:20) 스타베이스 Mechazilla 타워의 기계팔에 **처음으로 그대로 붙잡혔다**.

- SpaceX 대변인 Dan Huot: *"That was the Holy Grail of space flight... We caught a Starship and now everything changes."* BBC 중계 기준 "picture perfect".
- 부스터 포획 중단 원인은 아직 조사 중. 회사는 부스터는 이미 여러 번 잡아봤으니 돌파 불가능한 문제는 아니라고 본다.
- <https://www.cnbc.com/2026/11/14/spacex-plans-to-try-first-ever-catch-of-starship-rocket-from-orbit.html> · <https://www.bbc.com/news/live/cq0k683l3dz3t> · HN <https://news.ycombinator.com/item?id=50500963>

---

## 🤖 AI / LLM & Agentic 동향

**1) ggml v1.0.0 — llama.cpp 생태계의 첫 메이저 버전, RPC 프로토콜 v9 (HN 398점).**
11/13 릴리스. deprecated 공개 API 정리와 레거시 `DIAG_MASK_INF`/`DIAG_MASK_ZERO` 제거로 **RPC 피어 호환성이 끊긴다**. `ggml_rope_set_truncate`(YaRN 보정 반올림 제어), OCP FP8, CUDA few-row MMQ(2~24)·NVFP4 GLU 융합, Vulkan prefill FlashAttention, SYCL 디바이스 MoE 스케줄러, WebGPU 커널 확장.
- <https://github.com/ggml-org/ggml/releases/tag/v1.0.0> · HN <https://news.ycombinator.com/item?id=50493062>

**2) Unsloth Dynamic V3.1 — Qwen3.8-27B GGUF, 같은 크기에서 정확도 5~10%↑ (HN 238점).**
2~6비트 대부분 갱신(3비트 UD3는 개선폭 없어 유지). 평가는 Terminal Bench 2.1·DeepSWE v1.1(256K), LiveCodeBench v6·GPQA-D·MMLU-Pro·AIME25(128K). **4비트가 VRAM 16~19GB 또는 24GB 맥**에서 돈다 — 로컬 백업 경로로 실용적. 별도 **동적 NVFP4는 BF16 대비 약 1.5배**(Top-1 일치 92~97%, Blackwell 필요). 벤더 자체 측정이며, 비교 조건(컨텍스트·반복 횟수)에 따라 순위가 바뀐다고 저자들이 명시.
- <https://unsloth.ai/docs/models/qwen3.8> · GeekNews <https://news.hada.io/topic?id=37162>

**3) GLM-5.3을 DGX Station 2대에 — 753B MoE·활성 40B, 464GB, 607.8 tok/s (NVIDIA, 11/13).**
GB300 단일 252GB HBM3e에 안 들어가 ConnectX-8 RDMA + TP=2로 반씩 적재, OpenAI 호환 단일 엔드포인트. **동시성 16에서 출력 607.8 tok/s**, 동시성 32에서 385.0 tok/s로 떨어지며 평균 지연 453.5초 — "동시성을 올리면 처리량이 준다"는 실측 그래프가 핵심.
- <https://developer.nvidia.com/blog/when-one-gpu-isnt-enough-serving-glm-5-3-across-two-nvidia-dgx-stations-with-vllm/>

**4) Google Antigravity /teamwork — 수학·화학·시스템SW·ML엔지니어링 개방문제에 멀티에이전트 (11/13, HN 147점).**
인간 전문가와 협업한 결과를 공개하고, **독립 검사 구조**와 Lean 형식화 주장을 분리해 제시. Gemini 4 Argon·Teamwork·연구자 접근 프로그램 링크가 함께 붙었다. Codedeck은 "App·CLI에서 현재 실제로 되는 기능과 예고만 된 기능을 구분"하라고 짚었다.
- <https://research.google/blog/advancing-science-engineering-with-antigravitys-teamwork-feature/> · <https://www.codedeck.kr/posts/antigravity-teamwork-independent-verification-availability>

**5) Claude Code — Code Mode(2.1.323, 실험적 옵트인) 후속 수정, 2.1.325에 zstd 응답 디코딩 보안 수정.**
Code Mode는 Claude가 **도구 호출을 JavaScript로 묶어** 실행하는 모드(`/config` 또는 조직 정책으로 on/off). 2.1.325는 워크트리 진입·권한 다이얼로그 입력·세션 재개 유실 등 실사용 결함을 정리했다.
- <https://code.claude.com/docs/en/code-mode> · <https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md> · <https://www.codedeck.kr/posts/claude-code-code-mode-tool-calls-permissions>

**6) 그 외**

- **Firecrawl MCP v3**(무료 카탈로그 조회와 유료 실행의 경계, `max_credits` 생략 시 비용 조건) · **Google TPU Sync**(KV 캐시 전송, README가 버전 고정 권고) · **langchain-openai 1.8.0**의 `OpenAIClassifier`(여러 질문 묶기, 질문 1건 거부가 전체에 미치는 영향) 
- Show HN **DeepSeek V3 in ~200 lines of pure JAX**(HN 134) · **미국 세법을 Lean으로 형식화**(HN 129, `lexifina.com`) — "LLM이 잘하는 것 = 형식 언어"라는 흐름이 계속 확인된다. <https://www.codedeck.kr/posts/firecrawl-mcp-v3-catalog-provider-execution-cost> · <https://www.codedeck.kr/posts/tpu-sync-kv-cache-transfer-version-pinning>

---

## ☕ 백엔드 / Spring / 아키텍처 엔지니어링

### 릴리스·지원 기한

| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| Spring Boot | **4.2.0-RC2** | 11-05 | **GA 아직 미출시.** Maven Central 최신 배포판도 RC2까지. "11월 GA" 예고만 유지 |
| Spring Boot | 4.1.2 / 4.0.9 | 10-23 | 현행 GA. **4.0 OSS 지원 2026-12-31 종료** |
| Spring Modulith | 2.2.0-RC1 / 2.1.2 | 10-23 | 변경 없음 (Boot 4.2.0-RC2 기반) |
| Spring AI | 2.1.0-RC2 | 11-09 | 변경 없음 |
| Kotlin | **2.5.0-RC** | **11-11** | GitHub Release 공개. 정식 2.5.0은 12월 예정 |
| PostgreSQL | **19 RC2** + 18.7·17.12·16.16·15.20·14.25 | **11-12** | 마이너 5종 동시 배포 |
| Qdrant | 1.20.1 | 11-13 | 어제 브리핑 참조 |
| Redis | 8.12-rc3 | 11-12 | RC |
| ggml / llama.cpp | **v1.0.0** / b12387 | 11-13 / 11-14 | RPC v9 |

- **11-13~15 신규 Spring 블로그 글 없음.** spring.io 피드 최신은 11/12 Bootiful Podcast(Guy Pardon, 분산 트랜잭션·Atomikos)와 11/10 "Spring for Apache Pulsar 새 보금자리".

### Inside.java — **Newscast #117: Adaptive Heap Sizing** + 프리미티브 패턴 프리뷰 **철회** (11/14 · 11/11)
- Newscast #117은 **적응형 힙 크기 조정**, 숫자 변환, 프리미티브 패턴을 묶어 다룬다.
- 더 중요한 건 11/11 **"Withdrawing the Primitive Patterns Preview Feature"**. 프리뷰로 넣었던 프리미티브 패턴 기능을 **JDK에서 빼기로** 했다는 뜻이라, 이 기능을 전제로 코드를 쓴 프로젝트는 프리뷰 플래그에 의존하지 않게 확인해야 한다.
- <https://inside.java/newscast/117/> · <https://inside.java/2026/11/11/withdraw-primitive-patterns-preview/>

### Cloudflare — **리더 없는 합의 QuePaxa**를 왜 골랐나 (11/11)
전역 제어 평면용 실험 서비스 **Meerkat**이 Raft·Paxos 대신 **QuePaxa(2023)** 를 선택했다. 이유는 WAN: 리더가 죽거나 느려지면 전체가 멈추는 구조가 인터넷 규모에서 못 견딘다. QuePaxa는 **리더 없이 진행**을 보장하는 쪽으로 설계됐다. 사내 컨트롤 플레인이 "리더 장애 = 정지"를 허용하지 않는다는 판단 자체가 참고할 만하다.
- <https://blog.cloudflare.com/choosing-quepaxa/>

### Cloudflare — **Kitesurf 오픈소스**(Apache 2.0, 11/13) · **Spend Management** 베타
- **Kitesurf**: Workers 위에서 도는 **에이전트용 무상태 브라우저**. HTML 파싱·CSS 적용·페이지 JS 실행·스크린샷을 브라우저 컨테이너 없이 처리하고 **CDP 호환**(Puppeteer 연결). 자체 인스턴스 배포는 Workers 유료 플랜·별도 인증 조건. Codedeck은 "CDP 호환 범위와 자체 배포 인증 조건"을 따로 확인하라고 정리했다.
- **Spend Management(베타)**: Workers·Durable Objects부터 지출 한도·경고·**초과 시 일시정지**. AI Gateway의 사용자별·모델별 예산과 결합.
- <https://blog.cloudflare.com/kitesurf-open-source/> · <https://www.codedeck.kr/posts/kitesurf-open-source-cdp-self-hosting-authentication> · <https://blog.cloudflare.com/spend-management/>

### 보안이 곧 운영 이슈 — **AI 침투 테스트 오케스트레이터의 폭증** (Cloudflare, 11/13)
- 지난 한 달 사이 LLM 기반 침투 테스트 프레임워크가 폭발. **최근 3일간 50개 이상 프레임워크 계열**이 전 세계 Cloudflare 고객을 스캔했고, 스캔 IP는 수천 개로 늘었다. 원본 **ARTEX**는 10/8 작성자에 의해 내려갔지만 포크 계열 레포가 **12,000개**.
- **10/1 이후 AI 침투·익스플로잇 도구 일일 요청 30배.** 훔친 고객 자격증명을 연쇄 사용하는 사례까지 등장.
- <https://blog.cloudflare.com/adapting-to-ai-penetration-testing-orchestrators/>

### PyPI 개편 — **출처(provenance) 상태를 릴리스 화면에 노출** (11/06 완료 공지, 11/14 HN 185점)
- 프로젝트 페이지 상단 **출처 경고**, `Full Provenance` / `No Provenance` 상태, 파일별 증명(PyPI Publish·SLSA Build Provenance)과 투명성 로그 링크, 릴리스 파일 탭의 검색·필터.
- 경고 3종: **Incomplete**(일부 파일만 증명) · **Inconsistent**(같은 릴리스가 서로 다른 빌드) · **Lost**(이전 대비 증명 소실). "증명 없음 ≠ 악성"이라는 문구를 UI에 명시했다.
- <https://blog.pypi.org/posts/2026-11-06-ui-updates-complete/> · HN <https://news.ycombinator.com/item?id=50497892>

### DevOps 읽을거리
- **GitHub availability report: October 2026** (11/13) — 월간 가용성 회고. 11/13 PR 머지 타임아웃 장애를 겪은 직후라 읽어 둘 만하다. <https://github.blog/news-insights/company-news/github-availability-report-october-2026/>
- **GitHub "Build once, run on any agent"**(11/12) — 에이전트 스킬을 도구 중립으로 쓰는 실무 가이드. <https://github.blog/ai-and-ml/github-copilot/build-once-run-on-any-agent-a-practical-guide-to-agent-skills/>
- **Meta Cuttlefish on Mac**(11/13) — Android 가상 디바이스 프레임워크를 Apple Silicon에서 네이티브 구동. <https://engineering.fb.com/2026/11/13/android/cuttlefish-on-mac/>

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) EFF — "프라이버시의 개념을 또 바꾸려 한다" (HN 421점).** 스마트 안경·상시 녹음 웨어러블은 **사용자 1명의 선택으로 주변인의 대화를 기록**한다. 암호화·접근 제어 같은 기술적 보호는 기기 소유자에게만 향하고, 기록당한 비사용자에게는 거부 수단이 없다. 원음을 저장하지 않아도 **전사·요약**은 남고, 수동 조작 없는 상시 감시는 기록을 조장하며 사용 사실을 감춘다. <https://www.eff.org/deeplinks/2026/11/tech-companies-are-trying-shift-entire-concept-privacy-again> · HN <https://news.ycombinator.com/item?id=50496602>

**2) 이탈리아, Meta에 €8억 500만(약 $8억 8,700만) 과징금 (HN 297점).** 사진·영상에 동의·고지 없이 접근한 **Facebook 게시물 추천 기능**이 대상. 이탈리아 Android 사용자 약 **2,000만 명** 영향, 전 세계 매출의 약 **0.47%**. **60일 내 기본 비활성화 + 별도 동의** 의무. Meta는 "권한 부여 시에만 동작한다"며 불복 예정. AI 학습 활용 여부는 이번 조사 대상이 아니었다. <https://www.reuters.com/world/italy-fines-meta-887-million-over-android-users-data-access-2026-11-14/> · GeekNews <https://news.hada.io/topic?id=37170>

**3) Project Zero, 90+30 정책 폐기 — "AI 때문에 90일은 너무 길다" (11/13, HN 119점).** LLM이 취약점 발견·익스플로잇을 앞당기고, **패치에서 역공학까지 걸리는 시간이 사실상 0**이 됐다는 판단. 그래서 "패치 soak time"은 무의미해졌고, 보고 건수 자체도 몇 주에 여러 건으로 늘어 **벤더 협상 조항을 자동화 가능한 규칙으로** 바꿨다. 목표는 ①복구 속도 ②패치 갭 축소 ③정책 자동화. <https://projectzero.google/2026/11/disclosure-policy.html> · HN <https://news.ycombinator.com/item?id=50496051>

**4) FAA 전국 지상정지 해제 완료 — 4.5시간 만에(01:19Z).** 어제 브리핑에서 "해제 시각 미정"으로 남긴 건이 종결됐다. 후속으로 **Amtrak 예약 시스템 장애**(같은 날, HN 193점)가 겹쳐 교통 인프라 중복성 논의가 이어졌다. <https://www.fly.faa.gov/adv/adv_otherdis?adv_date=11142026&advn=5> · <https://www.fox5dc.com/news/amtrak-outage-ticket-booking-faa-ground-stop-november-13-2026>

**5) Microsoft 365 장시간 장애 회고 — "의존도가 곧 단일 장애점" (GeekNews).** 11/12 18:26 GMT 시작 → 11/13 14:01 GMT 개선 발표. Exchange Online·Teams·최근 파일·Copilot·M365 Education 영향. Nextcloud 공동창업자 Jos Poortvliet·Civo CEO Mark Boost·영국 하원 과학기술위원장 Dame Chi Onwurah가 한목소리로 **공급자 집중 구조**를 문제로 지목. <https://news.hada.io/topic?id=37175>

**6) 그 외 눈에 걸린 것**
- **광고 기반 감시 체계를 정보기관들이 쓴다** — Citizen Lab (HN 141). <https://citizenlab.ca/security-agencies-worldwide-found-to-use-ad-based-surveillance-system/>
- **LinkedIn·Monster, CBC 조사 후 가짜 채용공고 1,000건+ 삭제** (HN 153) — AI 시대 구직 사기.
- **"A Family Tree of Software Job Titles"** (HN 165) — 직함이 어떻게 갈라졌는지. <https://zwischenzugs.com/2026/11/03/a-family-tree-of-software-job-titles/>
- **Monkey Island 13개 버전 나란히 비교** (HN 458) — 순수 재미. <https://monkeyisland.pages.dev/>
- **"The Claude Delusion"** (HN 143) — 성공한 엔지니어의 자기 과신이 AI 평가에 어떻게 새는지에 대한 비판 에세이. <https://www.netmeister.org/blog/claude-delusion.html>

---

## 📺 추천 테크 영상·읽을거리

### Codedeck — YouTube 신규 업로드 **없음**, 채널 블로그가 본체
> Codedeck YouTube 채널 RSS를 직접 확인: **최신 업로드가 2026-09-26**(Flexbox Full Course). 11월 신규 영상은 없다. 같은 팀의 채널 블로그(codedeck.kr)가 11/14~15에 8편을 올렸다.

| 글 | 날짜 | 링크 |
| --- | --- | --- |
| **Claude Code Code Mode** — 도구 호출을 묶는 방식과 권한 검사 조건 | 11-15 | <https://www.codedeck.kr/posts/claude-code-code-mode-tool-calls-permissions> |
| AlphaEvolve on Cloud Batch — SC26 발표와 실제 실험의 접근·비용 조건 | 11-15 | <https://www.codedeck.kr/posts/alphaevolve-cloud-batch-sc26-access-evaluation-cost> |
| Firecrawl MCP v3 — 카탈로그 조회와 유료 실행의 차이 | 11-15 | <https://www.codedeck.kr/posts/firecrawl-mcp-v3-catalog-provider-execution-cost> |
| Kitesurf 오픈소스 — CDP 호환 범위와 자체 배포 인증 조건 | 11-15 | <https://www.codedeck.kr/posts/kitesurf-open-source-cdp-self-hosting-authentication> |
| Antigravity /teamwork — 독립 검증 구조와 현재 제공 범위 | 11-15 | <https://www.codedeck.kr/posts/antigravity-teamwork-independent-verification-availability> |
| TPU Sync — KV 캐시 전송 성능과 설치 버전 확인법 | 11-15 | <https://www.codedeck.kr/posts/tpu-sync-kv-cache-transfer-version-pinning> |
| Yogurt 0.3.14 — 로컬 회의록과 MCP 데이터 경계 | 11-15 | <https://www.codedeck.kr/posts/yogurt-local-meeting-notes-mcp-access> |
| Nod 0.7.0 / Tontaube V1.1 — 실행 조건과 비용 범위 | 11-14 | <https://www.codedeck.kr/posts/nod-0-7-linux-preview-local-files-model-spending> · <https://www.codedeck.kr/posts/tontaube-v1-1-local-tts-int4-runtime-license> |

### 영상 (제목·채널 oEmbed/페이지로 확인)
- **Jonathan Blow — "If AI Solved Coding, Why Is My Software Still the Same?"** · 채널 **The Students** (HN 454점, 662댓글). "AI가 코딩을 풀었다는데 왜 내 소프트웨어는 그대로인가" — 오늘 HN에서 가장 논쟁적인 화두. <https://www.youtube.com/watch?v=IRK8h67ol7Q>
- **KDE: The Documentary — The Story of Free Software Underdogs** (PeerTube/Kockatoo Tube, HN 347점) — 자유 소프트웨어 '언더독' 30년사. <https://tube.kockatoo.org/w/fFinNHfanLT7aBEb2B8y4c>
- **Cloudflare "Kitesurf" 소개** — 에이전트용 브라우저를 왜 컨테이너 없이 만들었나(블로그+데모). <https://blog.cloudflare.com/kitesurf-open-source/>

### 읽을거리
- **"Serving GLM-5.3 across two DGX"** — 동시성-처리량 트레이드오프 실측표가 그대로 벤치마크 설계 교재다.
- **"Why Zig instead of C for systems programming"** (GeekNews, 11/14) — <https://news.hada.io/topic?id=37123>
- **"글루 워크는 해롭다"에 대한 반론** (GeekNews, 11/14) — 문서화·온보딩이 성과로 안 보이는 이유. <https://news.hada.io/topic?id=37134>
- **Show HN: agweb — 에이전트용 브라우저** (HN 82) — Kitesurf와 같은 수요의 개인 구현. <https://github.com/paul-nameless/agweb>

---

## 📌 내 프로젝트(PDF 비교·무환각 검색 / arch-tech / spring-ai-ops)에 바로 매핑되는 3가지

**1) `spring-ai-ops` 배포가 5연속 실패 중이다 — 앱은 뜨는데 '헬스 확인' 단계에서 죽는다.**
- 사실: 가장 최근 실행 `43623327137`(11/14 17:51Z, 커밋 `73ec15f`)까지 **5건 연속 failure**. 실패 지점은 전부 `헬스 확인 (최대 60초 대기)`.
- 로그상 앱은 정상: Spring Boot **4.1.2**가 **8096**에 1.854초 만에 기동, `arch-mcp-server`에 **MCP 연결 완료(9개 툴)**, 브리핑 파이프라인이 `anomalies=2 briefed=2`로 동작. 그런데 **첫 스캔이 32.7초** 걸렸고 그 직후 프로세스가 exit 1.
- 유력 원인 2개: ① 헬스 체크 창(60초) 안에 **기동+첫 스캔(32.7초)+응답**이 겹쳐 판정 실패 ② `actuator`가 3개 엔드포인트만 노출돼 **health 경로가 실제와 불일치**. 조치 순서는 ① 스캔 시작에 초기 지연(`initialDelay`)을 주고 ② 헬스는 스캔과 분리된 readiness로 ③ 워크플로의 curl 대상 경로를 실제 노출 목록과 대조. 배포가 성공해야 포털(:80)의 AI Ops 메뉴가 살아난다.
- 함께 볼 것: 브리핑 모델이 **gemini-3.8-flash → 3.5-flash → 3-flash-preview**로 연속 429(RateLimit) 후 페일오버하고 있다. 무료/저등급 쿼터가 원인이면 **모델 체인의 기본값을 낮추거나 스캔 주기를 늘리는 게** 먼저다.

**2) Fable 5.2의 1,000 에이전트 실험은 `arch-mcp-server` 관제의 설계도다 — "조율은 Git+DB로 충분, 안전은 경계로".**
- 차용할 것: 리드가 스스로 만든 **5개 테이블(Announce/Assign/Notes/Subtasks/Landed)** 구조는 에이전트 오케스트레이션에 필요한 최소 상태다. 우리 `spring-ai-ops`는 이미 MCP 툴 응답을 공통 봉투(`status·summary·parts·data`)로 통일했으니, 여기에 **Assign(담당)·Landed(완료 로그)** 를 얹으면 다중 에이전트 확장이 데이터 모델 변경 없이 된다.
- 안전 설계가 더 중요하다: **네트워크 없는 샌드박스 + 실행 전 분류기 fail-closed**. 오늘 새로 올린 `arch-security-starter`(3-Mode JWT 검증·MCP 툴 인가·jti 폐기·웹훅 HMAC, 커밋 `3f0237b`)와 정확히 같은 방향이다. 다음 단계는 "툴 인가"를 **실행 전 검사 게이트**로 승격해, 차단 기준에 걸린 호출이 로그에 남게 만드는 것.
- 교훈 하나: 공유 머신 1대는 **에이전트 100개 근처에서 죽는다**. 우리 맥 스튜디오에서 병렬 에이전트를 늘릴 계획이면 컨테이너당 자원 상한과 디스크 예산을 먼저 정해 두는 게 맞다.

**3) 오늘의 두 사고(Lovable·Project Zero)는 "기본값이 공개"와 "패치 갭"이다 — 그리고 429를 로컬 모델로 메우는 선택지가 생겼다.**
- **접근제어**: Lovable 사고의 본질은 취약한 코드가 아니라 **기본값**이다. 포털(:80) nginx 라우팅과 `arch-api`의 어드민 경로에서 **인증 없이 열린 read 경로가 있는지** 한 번 훑어야 한다. 특히 admin HTML을 정적으로 서빙하는 구조는 "로그인 없이 데이터가 실려 나가는" 경로가 되기 쉽다.
- **패치 갭**: Project Zero가 90+30을 버린 이유가 "패치에서 역공학까지 0"이다. 우리 스택은 Spring Boot 4.1.2(4.0 OSS 지원 12/31 종료), **4.2.0 GA는 아직 RC2**. GA 전까지 4.1.2 유지가 맞고, 대신 **의존성 갱신 주기**를 정해 두는 게 실질적 대응이다(PostgreSQL도 19 RC2·18.7 마이너가 같은 주에 나왔다).
- **429 우회**: Unsloth의 **Qwen3.8-27B 4비트(VRAM 16~19GB / 24GB 맥)** 는 맥 스튜디오 96GB에서 여유롭게 돈다. Gemini 무료 쿼터가 브리핑 파이프라인을 계속 막는다면, **로컬 모델을 체인의 마지막 폴백**으로 넣는 구성이 비용·지연 모두에서 합리적이다(ggml v1.0.0은 RPC v9라 기존 바인딩은 헤더 대조 필요).

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-15 08:15 KST 기준)

**알림·요청**: API로 `review-requested:@me` · `assignee:@me` · `mentions:@me` 확인 — **전부 0건**. 열린 PR은 전 레포 0건, 열린 이슈는 **arch-tech 2건**(#7 resilience·#9 MCP 리뷰 이슈)뿐.

| 레포 | 공개 | 마지막 푸시(UTC) | 야간 활동 |
| --- | --- | --- | --- |
| **arch-tech** | private | **11-14 17:55** | **커밋 51건**, PR **#2·#3·#4·#5·#8 MERGED**, 최종 배포 success(`3f0237b`) |
| **spring-ai-ops** | private (**신규**) | 11-14 17:51 | 커밋 11건(12:49Z 최초), **배포 5연속 failure** |
| **memo** | private | 11-14 16:15 | 문서 6건 — 과제 14(중앙 인증·Keycloak), AI Ops 오케스트레이터 비교, 캐시 스탬피드·핀닝 진단 |
| **poc-pickle** | private | 11-13 15:49 | 신규 없음 |
| **poc-py** | private | 11-12 23:58 | 신규 없음 |
| **youtu-lib** | public | 11-13 23:19 | 오늘 브리핑으로 갱신 |

**arch-tech 흐름(11-13 23:15Z 이후, 핵심만)**
- **outbox**(PR #2): Modulith 아웃박스 스타터 + PostgreSQL + DLQ·멱등성 가드 + 관제 화면/CI 매트릭스 → **feat/feature-switch**(PR #3): 인메모리 룰 엔진·Murmur3 카나리·자동 킬스위치·Kafka(KRaft) + `/feature-admin.html` → **feature-edge**(PR #4): 쪽별 타임아웃·쓰기 이중 실행 금지·그림자 GET 전용, 리뷰 블로킹 5건+주요 결함 4건 수정 → **trace**(PR #5): OTel 분산 추적·N+1/슬로우 쿼리 자동 탐지 스타터 + 포털 폭포수, 블로킹 5건/결함 4건 수정 → **resilience**(PR #8, **hwajjdev** 작성): Issue #7 P1 3건(핫스왑 클램프·판정 메트릭 검증·이벤트 버퍼 동시성) + CI 워크플로 정리.
- 이후 main 직접 커밋: **arch-security-starter 신설 — 3-Mode JWT 검증·MCP 툴 인가·jti 폐기·웹훅 HMAC**(`3f0237b`), admin 테마 통일, Issue #6 P0 3건(401 자기치유 Dedupe 우회·발급 대상 변경 시 시크릿 재입력·keyId 키링 로테이션), Issue #9 P1 3건+P2 5건(MCP 병렬 예산·오류 봉투·SSE 프록시 경로).

**spring-ai-ops(신규 레포)**: Gradle 골격(Kotlin 2.3.21·Boot 4.1.2·Spring AI 2.0.0) → 온콜 사고 브리핑 파이프라인(ChatClient 구조화 출력 + 규칙 폴백, 테스트 23건) → 도커·compose·맥 스튜디오 배포(8096) → **arch-mcp-server MCP 연동 + 이상 탐지·알람 브리핑·Dedupe** → AI Ops 관제 UI·원클릭 시나리오 주입 → **Gemini 3.8 기본값 + 모델 체인 페일오버** → 멀티 메신저 알림(텔레그램, 토큰 미설정 시 자동 비활성).

**CI 상태**: arch-tech 마지막 배포 **success**(`3f0237b`, 17:55Z). spring-ai-ops **failure 5건 연속**(17:51Z 포함) — 실패 원인은 위 3번 항목 참조. 그 외 열린 실패 없음.
