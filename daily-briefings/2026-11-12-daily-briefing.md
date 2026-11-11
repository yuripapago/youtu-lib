# 2026-11-12 (목) 데일리 테크 브리핑

> **수집** 2026-11-12 08:25 KST · 최근 24시간(2026-11-11 08:30 ~ 11-12 08:25) 중심, 주말 공백 보정 포함
> **소스** Hacker News, GeekNews, Spring Blog·GitHub Releases(spring-boot / spring-modulith), Kotlin Docs, OpenJDK·Oracle, Qdrant Releases, Apple Machine Learning Research, YouTube(Codedeck), GitHub Status
> **오늘의 한 줄** — *"생성형 LLM이 토큰을 뽑아 하던 '판단'을, 작고 확률이 보정된 결정 모델(decision model)이 가져가고 있다."*

---

## 🌟 오늘의 핵심 3대 테크 헤드라인

### 1. 아마존, 내부 티켓 분류를 Claude Sonnet 4.6 → 2B급 결정 모델로 교체 — 비용 98%↓

AWS(Strands)가 공개한 실사용 사례. 고객 에스컬레이션·용량 요청·자동 경보·보안 발견사항을 **20개 범주로 분류**하던 경로를 Claude Sonnet 4.6에서 **Strands Decider E4B(Gemma 4 기반)** 로 바꿨다.

| 항목 | Sonnet 4.6 | Strands Decider E4B | 변화 |
| --- | --- | --- | --- |
| 티켓 100만 건당 비용 | 약 $22,200 | 약 $500 (실운영, 이론 최저 $250) | **-98% (약 1/44)** |
| 평균 호출 시간 | 4,215 ms | 897 ms | **-78%** |
| 티켓당 총 토큰 | 6,724 | 2,010 (출력 170 → 1) | **-70%** |
| 정확도 | 기준선 | 별도 티켓셋 평가에서 **Sonnet보다 우수** | — |

핵심은 **텍스트 생성 → 선택**으로 작업을 바꾼 것. 상태(티켓)와 선택지(20개 라벨)를 넣으면 **선택지별 확률·신뢰도**만 돌려준다(출력 토큰 1개). 그래서 4,096 토큰 문맥 창에 맞추려 라벨 설명을 "한두 문장짜리 판정 기준"으로 다시 쓰고 `"none"` 선택지를 추가했다.
전환은 실제 트래픽에서 **BEDROCK → DUAL(병렬 실행, Sonnet 결과만 적용) → DECIDER** 3단계. AppConfig 값 하나로 **45초 만에 롤백**.
함정도 기록해뒀다 — **JSON 키 순서만 바꿔도 출력 확률이 최대 0.19 변동**(현재는 키 정렬해서 전송), 라벨 설명이 필요하거나 목록이 매주 바뀌는 작업엔 부적합.

- GeekNews: <https://news.hada.io/topic?id=36935> · 원문: <https://strandsagents.com/>
- HN 토론에서 "6년 전 SVM 분류기(Linnaeus, ICSE 2021)로 돌아가는 흐름"이라는 회고가 붙었다 — <https://arxiv.org/abs/2103.06927>

### 2. 구글, 'Gemini Decisions API' 공개 — 토큰 생성 없이 한 번의 순전파로 최대 1,000개 질문 판정

2026-11-11 공개. `gemini-decision-1` 모델이 텍스트·이미지·오디오·비디오·PDF(최대 256K 토큰) **한 입력**에 대해 yes/no, 다중선택(2~255지), 루브릭 점수를 **한 번에** 채점한다.

- 지연: 모델 연산 중앙값 **50~100 ms**, 종단 간 **150~250 ms** (16K 토큰 이하, 질문 1~100개에서 거의 평탄)
- 가격: 입력 100만 토큰당 **$0.10**, **출력 토큰 0원** — 비교 대상(Gemini 3.5 Flash-Lite) 대비 약 5배 저렴
- 각 답에 **신뢰도(0~1)** 가 붙어 애매한 건 폴백·사람 검토로 라우팅 가능
- 구글 내부 사용처: Gemini Notebook의 실시간 모델 라우터(일상 질의 지연 40%↓), Stitch 래피드 캔버스, CC 이모지 선택(200+지), **검색의 MCP 도구 안전 계층**(적대적 입력 탐지)

- 블로그: <https://blog.google/innovation-and-ai/technology/developers-tools/gemini-decisions-api/>
- HN 191점: <https://news.ycombinator.com/item?id=50456791>

> 같은 흐름의 오픈소스 축: **Flide**(Jev 계열 결정 모델 추론 인프라, Apache-2.0). 공유 입력은 한 번만 계산하고 질문별로 **정확한 상태 분기(exact state forking)** 해 vLLM/SGLang 대비 **최대 9.5배** 빠르다(H200 1장, OneJev 0.8B로 이메일 1만 건 × 질문 8개 = 28초). <https://github.com/OmniJev/Flide> · <https://news.hada.io/topic?id=36894>

### 3. RAG 검색의 양 끝이 '학습'과 '판정'으로 갈라진다 — Apple MIPS 학습 라우팅 · Spring AI Jev 필터/리랭커 · Qdrant 1.20 내장 BM25

**(a) Apple — ICML 2026, MIPS를 학습으로 단축.** 클러스터 중심(centroid) 라우팅 대신, 질의 분포를 학습한 작은 NN(**SupportNet**: 각 클러스터의 support function 근사 / **KeyNet**: 질의를 최적 키 근처로 사상)으로 라우팅한다. Natural Questions(약 2.6M 패시지, 128 클러스터, FAISS IVF) 기준:

- SupportNet: 95% 라우팅 재현율을 centroid보다 **1.9배 빠르게**(1.74 ms vs 3.36 ms), 대형 모델은 **97%를 1.74 ms**에
- DB의 5%만 스캔: 학습 라우터 **90~93%**(M 92.6%) vs centroid 87.5%
- KeyNet: 99% 재현율까지 **최대 8배 짧은 시간**(0.23 ms vs 1.8 ms), 99% 재현율에 필요한 키 수 **최대 11배 감소**

→ 키 집합이 고정이고 질의 분포를 아는 프로덕션(문서 검색·추천)에서 "인덱스 튜닝" 대신 "라우터 학습"을 선택지로 놓는 논문. <https://machinelearning.apple.com/research/faster-retrieval> · HN 128점: <https://news.ycombinator.com/item?id=50455633>

**(b) Spring AI Modular RAG + TypeSafe Jev — 검색 결과를 '판정'으로 걸러낸다.** `RetrievalAugmentationAdvisor` 파이프라인에 `JevDocumentFilter`·`JevDocumentReranker`를 `DocumentPostProcessor`로 끼워 넣는 구성(코드 한 블록). Jev가 **패시지마다 한 번의 호출**로 4개 `Noul` 질문을 순서대로 적용한다:

| 질문 | 기본 임계값 | 결과 |
| --- | --- | --- |
| `contains_prompt_injection` | > 0.70 | 제외 |
| `contradicts_query_premise` | > 0.70 | 유지 + `CONFLICTING` 태그 |
| `is_relevant` | < 0.45 | 제외 |
| `contains_answer_evidence` | > 0.55 | 유지, 미달이면 제외 |

리랭커는 "이 패시지가 질의에 **답이 되는가**"만 1개 질문으로 점수화해 top-K. **필터 먼저 → 리랭커**(살아남은 것만 과금). 모순되는 패시지는 일부러 남긴다 — 사용자 전제가 틀렸다면 그 증거를 모델이 봐야 하니까.
<https://spring.io/blog/2026/10/02/spring-ai-modular-rag-typesafe-jev>

**(c) Qdrant 1.20 (10-22 GA) — 하이브리드 검색이 한 저장소로 접힌다.** 페이로드 전문(full-text) 인덱스에서 **BM25 점수를 직접** 계산(별도 sparse 벡터 생성 불필요), **TurboQuant `turbo8`/`turbo16`** 벡터 타입, 배열 오브젝트용 `nested` 페이로드 인덱스, 코드용 `code` 토크나이저, substring 필터, `hnsw_config.inline_storage`로 디스크 절감.
<https://github.com/qdrant/qdrant/releases/tag/v1.20.0> · BM25 문서: <https://qdrant.tech/documentation/search/text-search/full-text-search/>

---

## ☕ 스프링 부트 & 백엔드 엔지니어링 동향

### Kotlin 2.5.0-RC — 어제(11/11) 공개
- **JVM**: Java 28 바이트코드 생성 지원 (JDK 27 GA 라인을 이미 넘어섬)
- **언어**: 이름 기반 구조분해(`(val x, val y)`) 기본값화, 미사용 반환값 검사기 기본 ON, `companion` 확장/블록(실험), **전체 값 클래스(full value class)**(실험)
- **stdlib**: `onTrue()`/`onFalse()`/`ifOrNull()`, `CodePoint` API(실험)
- **Native**: Swift export **Beta** + 새 Gradle DSL
- **KMP**: 새 실험적 컴파일 스킴, 라이브러리 배포를 단일 `.kar`(XZ) 아카이브로 — 예: cryptography-kotlin 7,539파일/91MB → 1,036파일/23MB
- **JS**: `es2020` 타깃
- **주의**: 컴파일러 최소 JDK를 8 → **17**로 올리는 단계(지금은 에러, `-Xallow-pre-17-runtime-jdk`로 해제, 2.5.20/2.6.0에서 필수)
<https://kotlinlang.org/docs/whatsnew-eap.html>

### Spring 릴리스 현황 (11월 첫 주)
| 프로젝트 | 버전 | 날짜 | 메모 |
| --- | --- | --- | --- |
| Spring Boot | **4.2.0-RC2** | 11-05 | `X-Forwarded-Prefix` Reactor Netty 지원, 관측 규약(open-telemetry) 버그 수정 등 |
| Spring Boot | 4.1.2 / 4.0.9 | 10-22 | 유지보수 |
| Spring AI | **2.1.0-RC2** | 11-09 | Message Parts(추론 재생), 자체 임베딩 upsert |
| Spring Cloud | 2026.0.0-RC2 (Paddington) | 11-05 | — |
| Spring Modulith | **2.2.0-RC1** / 2.1.2 | 10-23 | 아래 |

### Spring Modulith 2.2 RC1 — 이벤트 경계가 단단해진다
- **이벤트 완료 처리가 리스너 트랜잭션 안에서** 기본 수행 — "리스너 비즈니스 변경 + 완료 표시"가 함께 성공/실패 (GH-1961)
- **FAILED vs ABANDONED 구분** — 재시도하지 않을 발행과 단순 실패를 분리 (GH-1764)
- 모듈 의존성: `module :: **`로 기본 패키지까지 노출 단위 지정, `NamedInterface`를 타입 단위로도 등록, 공유 모듈이 자기 자신을 허용 대상에 넣던 문제 수정
- 테스트: `EventResult.notToArrive()`, Boot 4.2에서 `@Nested`의 `@ApplicationModuleTest` 해석 수정
- `Moments`에 시·시간 단위 추가. **1.4.x 라인은 1.4.14가 마지막**
<https://github.com/spring-projects/spring-modulith/releases>

### JDK 27 (9-15 GA, 27.0.1 안정화) — 지금 JVM에서 실제로 챙길 것
- **G1이 모든 환경의 기본 GC**(서버 외 포함) · **Compact Object Headers 기본화**(96bit → 64bit, 힙·배포 밀도·데이터 지역성)
- **TLS 1.3 하이브리드 키 교환(X25519MLKEM768 기본 활성)** — 코드 변경 없이 양자내성
- **JFR 기본 리댁션**(커맨드라인 인자·환경변수 초기값 마스킹)
- 프리뷰: primitive 패턴 매칭, Lazy Constants, Structured Concurrency, PEM 인코딩
<https://openjdk.org/projects/jdk/27/>

### 그 외 백엔드 신호
- **AWS emulator** (Rust, `github.com/aws/aws-emulator`, 2026-09 생성, ★1.4k) — AWS API 로컬 에뮬레이터. HN 오늘 **552점 1위**. LocalStack 자리를 AWS가 직접 치는 모양새. <https://github.com/aws/aws-emulator>
- **GitHub Actions/Pages/Webhooks 장애가 11/9~11/11 사흘 연속** 발생, 11/11 21:21 UTC(11/12 06:21 KST) 전부 복구. 현재 전 서비스 정상. <https://www.githubstatus.com/>
- **"Lock Your GitHub Actions"** (HN 38점) — 워크플로 권한·이벤트 트리거 잠그기. <https://hugovk.dev/blog/2026/lock-github-actions/>
- 이번 주 Spring 추천: Craig Walls의 "SessionMemory 마이그레이션", SivaLabs의 "하네스 엔지니어링" 영상(<https://www.youtube.com/watch?v=H-Xtukxd1ao>), Josh Long *This Week in Spring* 11/10 <https://spring.io/blog/2026/11/10/this-week-in-spring-november-10th-2026>

---

## 🤖 AI 에이전트 & RAG 아키텍처 실무 인사이트

**1) '결정'을 LLM에 맡기지 말고, 결정 모델로 분리하라.** 오늘 자료가 한 방향을 가리킨다 — 폐쇄형 라벨 집합 + 대량 처리 + 낮은 지연이면 생성형 LLM은 과잉이다. 실무 체크리스트:
- 라벨을 **판정 기준 한두 문장**으로 다시 쓴다(긴 설명은 4K 문맥을 넘긴다)
- **신뢰도 임계값**을 별도 데이터셋으로 튜닝하고, 임계값 미달은 사람/상위 모델로 폴백
- **키 순서·포맷 고정**(Amazon 사례: 확률 최대 0.19 변동)
- 전환은 **그림자 실행(DUAL) → 전면 전환**, 설정 한 줄로 롤백 가능하게
- 설명문 생성·주간 변경되는 분류 체계·수천 토큰 문맥이 필요하면 LLM을 유지

**2) RAG에서 "비슷함"과 "쓸모 있음"을 분리하라.** Spring AI 사례의 3가지 실패 모드(사용자는 검색어를 안 쓴다 / 유사 ≠ 유용 / 검색된 텍스트는 신뢰할 수 없는 입력)와 대응: 쿼리 재작성 + 다중 쿼리 확장 → 검색 → **인젝션 필터 → 근거 기반 리랭크** → 컨텍스트 주입. 후처리·증강은 **원 질문**을 본다(재작성된 질의가 아니라).

**3) 검색 인프라 자체를 학습으로 줄이는 선택지가 생겼다.** 질의 분포가 안정적이면 Apple의 SupportNet(라우팅, 보정된 점수 필요 시) / KeyNet(지연이 병목일 때, IVF 앞단 질의 재작성기)을 검토할 만하다. 둘 다 기존 IVF에 얹히고, 큰 시스템은 "전역 SupportNet 라우터 + 클러스터별 KeyNet" 조합.

**4) 에이전트가 실제 세계와 부딪히기 시작했다.**
- **United Airlines, 여행자 대신 예약하는 AI 에이전트 금지**(11/11 발표) — 자격증명 공유·의도치 않은 결제·마일리지 사용, 수하물 확인 같은 연방 규정 위반 소지. 사우스웨스트는 반대로 ChatGPT에 인벤토리를 열었다. <https://www.cbsnews.com/news/united-airlines-bans-ai-agents-booking-flights/> (HN 206점)
- **Pomerado(YC F26)** — 어떤 웹사이트든 에이전트용 MCP로 변환. **Atuin Threads** — 여러 머신의 에이전트 세션 동기화(HN 178점). **Vercel agent-desktop** — 데스크톱 앱을 조작하는 CLI. **Expo Exact** — React 없이 웹·iOS·macOS·Linux를 만드는 Rust 런타임(GeekNews).

**5) 결정 모델을 이미 쓰는 곳들.** 카카오 **Kanana-2-v-decide**(Codedeck 4일 전 영상), Cloudflare **Clef**·RL 미세조정 서비스, **pplx-decider**, **OneJev 0.8B**. 한국어 라벨 분류라면 Kanana 계열이 첫 후보.

---

## 📺 추천 테크 영상 및 아티클 링크

### Codedeck (한국어, 최근 3일)
| 영상 | 길이 | 링크 |
| --- | --- | --- |
| 게임계의 GitHub가 열렸다 — Patch(patch.gg) | 6:24 | <https://www.youtube.com/watch?v=iQYev0c7q3c> |
| 이제는 수정이다 — 바이트댄스 Seedream 5.5 직접 수정 테스트 | 7:02 | <https://www.youtube.com/watch?v=3urRrjSnFks> |
| 「똑같이 만들었다」를 픽셀로 검증 — 네이버 Figma→네이티브 Design-To-UI | 5:59 | <https://www.youtube.com/watch?v=FDGuQzalOTM> |
| Expo 창업자가 React 없이 만든 프레임워크 — Exact | 5:15 | <https://www.youtube.com/watch?v=5Wb7zYH_KrA> |
| 클라우드 없이 폰이 혼자 앱을 조작하는 온디바이스 에이전트 Mika | 5:33 | <https://www.youtube.com/watch?v=mn_sY_Inbvc> |
| AI가 진짜 아이폰을 빌려 인스타를 올린다 — Glasshand | 4:54 | <https://www.youtube.com/watch?v=65O900GFq04> |
| Claude Fable 5.2 · Mythos 5.2 출시 및 **모델 선택 방법** | 10:36 | <https://www.youtube.com/watch?v=Dj3PFDp_r-M> |
| **에이전트 메모리 해부 — Claude는 자면서 기억을 고친다** | 11:18 | <https://www.youtube.com/watch?v=ium1MYmp7Ls> |
| 11/9 레딧 AI 뉴스 — GPU는 매집, 셸은 0.8B가 쓴다 | 2:36 | <https://www.youtube.com/watch?v=Gmdo0LA-yX0> |
| 카카오 결정 모델 Kanana-2-v-decide | 5:49 | <https://www.youtube.com/watch?v=kef4GyuFO-k> |
| Dokploy — 내 서버에 올리는 나만의 Vercel | 9:16 | <https://www.youtube.com/watch?v=lOz752RnwA4> |

### 아티클
- Apple ML, *Faster Retrieval for Better, More Efficient RAG* — <https://machinelearning.apple.com/research/faster-retrieval>
- Spring AI, *Modular RAG and TypeSafe Jev* — <https://spring.io/blog/2026/10/02/spring-ai-modular-rag-typesafe-jev>
- Spring AI, Message Parts(추론 재생) — <https://spring.io/blog/2026/10/28/spring-ai-message-parts-reasoning-replay>
- Spring AI, 자체 임베딩 upsert — <https://spring.io/blog/2026/10/28/spring-ai-upsert-own-embeddings>
- Kotlin 2.5.0-RC — <https://kotlinlang.org/docs/whatsnew-eap.html>
- Qdrant 1.20 릴리스 노트 — <https://github.com/qdrant/qdrant/releases/tag/v1.20.0>
- Strands Decider 소개 — <https://strandsagents.com/blog/introducing-strands-decider/>

---

## 🔍 그 외 스캔에서 눈에 걸린 것

- **팀 피터스(Tim Peters, 1952–2026) 별세** — 7월 15일 별세, 11/11 귀도 반 로섬이 공지. Zen of Python(PEP 20), Timsort의 저자, PSF 이사 2001–2014. HN 744점. <https://discuss.python.org/t/in-memoriam-tim-peters-1952-2026/110028>
- **엔비디아, 젠슨 황 CEO 교체 준비 보도**(WSJ) — HN 174점. **월스트리트저널 단독 보도로만 확인되고, 회사 확인은 없음**. <https://www.wsj.com/business/nvidia-is-preparing-to-oust-nvidia-ceo-jensen-huang-30f421ad>
- **모델 소식**: Google *Gemini 4 Carbon* 일반 공개(복잡한 코딩·연구) · Anthropic *Claude Fable 5.2 / Mythos 5.2*. 게시판 소개 기준이라 세부 벤치마크는 원문 확인 필요.
- **AI 사용은 취약한 코드베이스를 만든다** — "AI는 당장의 문제를 잘 풀지만 변경이 코드베이스 유지보수에 미칠 영향은 놓친다". GeekNews 12점. <https://news.hada.io/topic?id=36955>
- **2026년, 엔지니어로 살아간다는 것** — 같은 AI 도구를 써도 결과물 격차가 난다. <https://news.hada.io/topic?id=36945>
- **Val Town 단계적 서비스 종료**(11/10 신규 가입 중단) · **e-means**(같은 오차에 ~10배 빠른 k-means) · **내 SSH 키를 모두 삭제했다**(YubiKey/TPM/Secure Enclave로 이전) · **AWS emulator 552점**
- HN 톱: *My AI agents found an unmapped Maya site*(899점) — 인기 위주, 기술 내용은 얕음.

---

## 📌 내 프로젝트(PDF 비교·무환각 검색)에 바로 매핑되는 3가지

1. **비교 파이프라인의 2단계(블록 매칭)에 결정 모델 후보 추가** — 지금은 헝가리안 매칭 후 유사 블록을 LLM 비교에서 제외하는데, "이 두 블록이 같은 조항인가 / 같은 표의 같은 셀인가"는 전형적인 **폐쇄형 yes-no 판정**이다. 임계값·신뢰도로 컷하면 LLM 호출량을 더 줄일 수 있다. 한국어 문서면 Kanana-2-v-decide, 범용이면 Gemma 4 기반 소형 · Gemini Decisions API(이미지·PDF 직접 입력, 출력 토큰 0원)가 첫 후보.
2. **검색 파이프라인에 '판정' 단계를 명시적으로 넣기** — BM25+벡터 하이브리드 상위 K를 그대로 프롬프트에 넣지 말고, `is_relevant`/`contains_answer_evidence`/`contains_prompt_injection` 3개 판정으로 거른 뒤 리랭크. 약관 PDF에 심어진 지시문(인젝션) 차단은 이 도메인에서 실제 위협이다.
3. **인프라 단순화** — Qdrant 1.20의 페이로드 인덱스 내장 BM25로 sparse 벡터 파이프라인 하나를 없앨 수 있고, 조항·페이지 메타데이터는 `nested` 페이로드 인덱스로 배열 오브젝트 조건 필터를 빠르게. 수백 개 PDF 규모에서는 Apple식 학습 라우터보다 **청크 메타데이터 필터 + 리랭크**가 먼저다.
