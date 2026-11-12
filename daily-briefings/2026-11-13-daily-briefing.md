# 2026-11-13 (금) 데일리 테크 브리핑

> **수집** 2026-11-13 08:25 KST · 최근 24시간(2026-11-12 08:30 ~ 11-13 08:25 KST) 중심
> **소스** Hacker News(front 11/12), GeekNews(최신글·아티클), Codedeck YouTube(채널 RSS), Google DeepMind Blog, AP News, Wiz Research, Anthropic IPO, Spring Blog, Kotlin Docs, Qdrant Releases, GitHub Status, Microsoft Service Health
> **오늘의 한 줄** — *"에이전트가 코드를 쓰는 속도가 사람의 병합 속도를 넘어섰고, 그 실행을 맡는 모델은 '판단'과 '실행'으로 회사 안에서 갈라졌다."*

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. 젠슨 황, 엔비디아 CEO 즉시 사임 — 어제 WSJ 단독보도가 하루 만에 AP로 확인

어제 브리핑에서 "WSJ 단독, 회사 확인 없음"으로 표기했던 건이 **11/12(현지) 사임으로 확정**됐다. HN에서는 전날 WSJ 보도(1,211점)에 이어 이틀 연속 최상위권을 지켰고, 오늘은 AP 기사가 1위(2,683점)다.

| 항목 | 내용 |
| --- | --- |
| 보도 | AP, 11/12 — "Jensen Huang built the company that powers AI. Now Nvidia must find a way forward without him" |
| 사실관계 | 사임 확정. 배경으로 **중국 스파이 의혹 인물과의 관계**에 이어, **자신에게 직접 보고하는 엔비디아 임직원과의 관계**가 11/11 밤(현지) 추가 보도됨 |
| 시장 반응 | 엔비디아 주가 **프리마켓 10% 하락**(AP 표기) |
| HN | AP 2,683점·댓글 2,565 / Reuters 중복 373점 / WSJ 원보도 1,211점 |

- 남은 것: 후임·경영 공백 계획, 이사회 입장, AI 인프라 투자 계획 변화는 **이 시점 확인된 내용 없음.** 사임 사유는 보도 내용이며 회사 공식 성명은 확인하지 못했다.
- <https://apnews.com/article/nvidia-megaspeed-china-singapore-huang-5403f152a601cf82ce939f1d24fffa0f> · HN <https://news.ycombinator.com/item?id=50467087>

### 2. 구글, **Gemini 4 Hydrogen** 공개 — 3.8 Flash의 정식 후속, "판단은 Carbon/Argon, 실행은 Hydrogen"

11/12 공개. 프론티어(Carbon·Argon)는 "길고 어려운 추론"에 남기고, **일상 코딩·에이전틱 실행**을 전담하는 모델로 포지셔닝했다. 어제 브리핑의 "결정 모델 분화" 흐름이 같은 회사 안에서 계층 분화로 나타난 셈.

| 항목 | 값 (전부 구글 자체 측정) |
| --- | --- |
| 지능 지수 | Artificial Analysis Intelligence Index **51**(기본 high), **54**(최고 xhigh) vs Argon 50 |
| 문맥/출력 | 입력 **1M** · 출력 **최대 1M** 토큰 (전 구간 단일 가격) |
| 가격 | 입력 **$1.50** / 출력 **$7.50** (100만 토큰당, 문맥 길이 무관 평탄) |
| 3.8 Flash 대비 | 동일 작업 **2.5배 빠름**(기본 high), Terminal-Bench 4.1 **57% vs 18.2%**, **날조 주장 5배 이상 감소**, 도구 호출 실패·빈 검색 결과에 대한 적응 |
| 사고 단계 | low / medium / high(기본) / **xhigh(신규)** 4단계 |
| 주요 벤치 | HLE-Diamond 58.8% · Vals Tax Agent 52.3%(1위) · Vals Legal Research 55.8%(공동 1위) · MKA 53.4% · Agents' Last Exam v1.1 71.6% · Agent Arena #5 |
| 배포 범위 | AI Studio 전 티어, Gemini API, Gemini for business, Gemini 앱, 검색, AI Pro·Ultra, Antigravity |

**Orchestrator**(Antigravity 신기능) — 한 세션 안에서 Carbon↔Hydrogen을 자동 전환. Terminal-Bench 4.0에서 **Carbon에 근접한 성공률을 유지하면서 quota 24% 절감·19% 빠름**. 병렬 에이전트에서는 Carbon이 코디네이터, Hydrogen이 워커·검증자 역할.

- <https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-gemini-4-hydrogen/> · 모델 카드 PDF <https://storage.googleapis.com/deepmind-media/Model-Cards/Gemini-4-Hydrogen-Model-Card.pdf> · HN 414점 <https://news.ycombinator.com/item?id=50471431>
- 주의: 위 수치는 발표 측 자체 벤치마크이며 독립 검증이 아니다. AI Studio 예제 링크의 모델 식별자는 `gemini-4-hydrogen` 형식이다.

### 3. Apache HTTP Server 치명적 RCE — **CVE-2026-119618**, 클라우드 환경 64%가 영향권

`mod_http2`의 `assign_headers()`에서 공유 자원 동시 실행 경쟁 조건 → 힙 손상 → **원격·무인증 RCE**(웹 프로세스 권한). Wiz Research 분석.

| 구분 | 내용 |
| --- | --- |
| 영향 버전 | Apache HTTP Server **2.4.55 ~ 2.4.70** |
| 패치 버전 | **2.4.71 이상** (독립 배포 모듈 mod_h2는 2.0.42 초과) |
| 발견 | **Anthropic과 Google Cloud 취약점 연구팀이 독립 발견** |
| 노출 규모 | Wiz 데이터 기준 클라우드 환경 **64%**에 취약 자원, **51%**는 공개 노출(설정 미고려) |
| 현재 상태 | 발표 시점 공개 PoC 없음, 실제 익스플로잇 보고 없음 |
| 임시 완화 | `Protocols http/1.1` 로 HTTP/2 비활성(패치 불가 시) |

- <https://www.wiz.io/blog/cve-2026-119618-rce-apache-http-server-vulnerability> · HN 352점 <https://news.ycombinator.com/item?id=50462177>
- **우리 환경 해당 여부**: NAS 배포는 Spring Boot 내장 톰캣(:8080) 직결이라 httpd 미사용 → 무관. 다만 **앞단에 httpd 리버스 프록시를 두는 순간 즉시 해당**이므로, 나중에 nginx 대신 httpd를 쓰는 선택지는 피하거나 2.4.71+ 고정.

### 4. 앤트로픽, 나스닥 IPO 공식 진행 — 티커 **ANT**, 11/19 상장 예정

로드쇼가 11/10(EST) 시작됐고 공모 페이지가 열렸다. Q3'26 실적이 처음 숫자로 공개됐다.

- 매출 **$17.0B**(전년 대비 **12배 이상**), 연 $100K 이상 고객 **11,900+**(12배 이상), 컴퓨트 효율 **4배+**(Q1'25 대비)
- 조정 영업이익률 **15%**, 회계상 영업이익률 **-3%**, 순손실 **$23.3B**(전환사채·SAFE 공정가치 변동 비현금 **$21.9B** 포함)
- 일정: 최종 공모가 **11/18(EST)** 결정 → **11/19(EST)** 나스닥 "ANT" Class A 거래 개시
- <https://ipo.anthropic.com/us/> · HN 778점 <https://news.ycombinator.com/item?id=50465829>

---

## ☕ 스프링 부트 & 백엔드 엔지니어링 동향

### 릴리스: 이번 24시간 신규 없음 (현행 유지)
| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| Spring Boot | **4.2.0-RC2** | 11-06 | 이전 브리핑 참조. 4.2.0 GA 전 마지막 RC 라인 |
| Spring Boot | 4.1.2 / 4.0.9 | 10-23 | 유지보수 |
| Spring AI | **2.1.0-RC2** | 11-09 | Message Parts·자체 임베딩 upsert |
| Spring Cloud | 2026.0.0-RC2 (Paddington) | 11-05 | — |

- **Spring for Apache Pulsar, ASF로 이관**(11-10 공지). 카프카만 쓰는 우리 구성엔 영향 없음. <https://spring.io/blog/2026/11/10/spring-pulsar-new-home-asf>
- 11-12 신규 글은 팟캐스트뿐 — *A Bootiful Podcast: Atomikos 창업자 Guy Pardon, 분산 트랜잭션과 데이터 무결성*. <https://spring.io/blog/2026/11/12/a-bootiful-podcast-guy-pardon>

### Kotlin 2.5 — 정식은 **2026년 12월** 예정
- 2.5.0-RC(어제 브리핑)는 EAP 채널. 공식 릴리스 프로세스 문서 기준 **2.5.0 = 2026년 12월, 2.5.20 = 2027년 3월**.
- 현행 안정 최신은 **2.4.21**(10-08). **2.4.0부터 JVM 표준 라이브러리 보안 지원 창이 18개월**로 명문화됐다. 지원 창 표에는 2.4 라인만 등재돼 있어 **2.3.x는 보안 패치 창 밖으로 보인다** → poc-pickle·arch-tech(둘 다 2.3.21)는 2.5.0 정식(12월)에 맞춰 2.4.x 이상으로 올리는 계획을 세워두는 게 맞다. <https://kotlinlang.org/docs/releases.html>

### 인프라
- **Qdrant**: 최신은 여전히 **1.20.0**(10-22). 24시간 내 신규 릴리스 없음. 1.20의 BM25 페이로드 인덱스·`nested`·TurboQuant 내용은 어제 브리핑 그대로 유효.
- **GitHub 상태**: 11/12에 두 건. ① **Webhooks 18:43~19:33 UTC** 시간초과·오류(고객 트래픽 약 1%) ② **이메일 발송 19:36~21:43 UTC** — 로그인·이메일 인증·비밀번호 재설정 지연. 둘 다 복구됐고 현재 전 서비스 정상. **Actions는 이번엔 영향 없음**(11/11 장애와 별개). <https://www.githubstatus.com/>
- **Microsoft 365 서비스 저하 진행 중** — 상태 페이지 기준 "여러 Microsoft 365 서비스에 접속 불가" 상황이 아직 지속. Azure는 이슈 없음으로 표기. <https://status.cloud.microsoft/> (HN 386점)

---

## 🤖 AI 에이전트 & RAG 아키텍처 실무 인사이트

**1) 에이전트가 늘면 Git의 병목은 '푸시'가 아니라 'main 반영'이다 — 숫자로 확인됐다.** GeekNews가 그동안 올라온 사례를 층(클라이언트·프로토콜 / 서버·저장소 / 협업 흐름)으로 정리한 아티클.

| 사례 | 바꾼 층 | 달라진 것 | `git` 명령 |
| --- | --- | --- | --- |
| OpenAI **Pando** | 서버/저장소 | 브랜치별 독립 푸시 처리, 팩파일 재사용 | 유지 |
| **GitHub** 신규 저장 구조 | 서버/저장소 | 팩 데이터와 브랜치 참조 분리, 단일 저장소 **초당 7,200 푸시** | 유지 |
| **Cursor Continuity** | 서버/저장소 | 푸시를 S3 변경 로그로, 디스크 사본은 캐시 | 유지 |
| **Entire** 분산 미러 | 서버(읽기) | 지역 미러에서 `clone`/`pull` | 유지 |
| **GitLab** 차세대 SCM | 서버/저장소 | 백엔드 재설계 + 파일·이력만 요청하는 API | 유지 |
| **Aspen**(Pando 독립 구현) | 서버·병합 | 병합 큐, 여러 PR 결과를 main에 한 번만 반영 | 유지 |
| **Anthropic Scion** | 클라이언트·프로토콜 | 자체 클라이언트, 커밋·병합을 서버에서 | **교체** |
| **Zed Delta** | 협업 흐름 | PR 대신 작업 스레드에서 코드+대화 리뷰 | 유지 |

핵심 수치: Anthropic 사내 모노레포 **일일 병합 2,500건(5월) → 3만 건 이상(9월), 4개월 12배**, CI 체크아웃 **하루 약 2,500만 회**(초당 약 290회). 반면 Aspen 실험에서 **브랜치 푸시는 초당 567회**까지 됐지만 **main에 순차 반영은 초당 약 4회** — 다음 변경이 직전 반영 결과에 의존하기 때문. 즉 **에이전트 수를 늘릴수록 마지막 직렬 구간(main 검증·병합)이 전체 처리량을 결정**한다.
<https://news.hada.io/topic?id=37034> (18점·19시간)

**2) 프롬프트는 '요령집'이 아니라 '모델 성격 설명서'다 — Anthropic Fable 5.2 공식 가이드 해설.** Codedeck이 공식 문서를 전수 대조한 영상에서 뽑은 것들(전부 Anthropic 자체 테스트 수치):
- **effort 다이얼 5단계**: 능력 향상의 대부분이 기본값 high에서 나온다. "숙고하지 말고 답하라"는 지시는 **medium 이상에서 먹히지 않는다** → 말을 바꾸지 말고 다이얼을 낮춰야 한다.
- **조기 종료 처방**: 남은 일을 나열만 하고 멈추는 비율이 **약 절반 감소**, 대신 출력 토큰 **약 30% 증가**.
- **"이 코드는 네 것이다" 한 문장**: 중복 코드 통합 비율 **10% 미만 → 약 75%**, 대신 토큰 **70~80% 증가** + 시키지 않은 변경·요약 누락이라는 부작용. "정리 금지" 규칙으로 상쇄되지 않음.
- **모호한 승인 규칙**은 일상 요청의 **약 절반을 보류**시킨다 → "계속해" 대신 무엇을 승인하는지 문장으로.
- **쌓인 "never" 지시는 역효과** → 오래된 주의사항 청소 원칙 필요.
- **토큰 예산 표시를 지우면** 성능은 같고 비용만 줄었다(구형 하네스 점검 항목).
- <https://www.youtube.com/watch?v=riI3xHhhEOE>

**3) 문서를 '실행되는 테스트'로 만들면 문서가 낡지 않는다 — AIDA.** 가이드 1개 = YAML 워크스루 1개. 클릭 대상을 좌표가 아니라 **버튼 role·이름**으로 지정해, 사람이 읽는 문서이자 기계가 실행하는 스크립트가 된다. `aida build`가 Playwright로 실제 앱에서 재생해 **모든 단계가 통과해야만 페이지 게시**(게시물에 `last checked` 표기). PR마다 닿는 워크스루만 다시 재생해 **Cosmetic / Broken / New / 앱 버그 의심** 4분류로 코멘트. 안전장치: 접속 DB가 전부 로컬·지정 시험용이 아니면 캡처 거부, 실제 결제 키 거부, **첫 게시는 사람만 승인**. 판단은 코딩 에이전트, 실행·검증은 모델을 안 쓰는 결정적 CLI 엔진으로 분업. (pre-release, 스타 9 — 아직 실사용자 없음) <https://github.com/tryaida/aida> · <https://www.youtube.com/watch?v=1Lqy8Edoau8>

**4) AWS Emulator의 진짜 기사는 "에이전트가 100% 썼다"가 아니라 '검증 구조'다.** 공식 포스트와 레포 대조:
- 동기: 테스트가 병목이 된 코딩 에이전트 시대 → 주말 실험에서 Kiro 프롬프트 3줄로 15분 만에 SQS 초판.
- 비결 2가지: 기계가 읽는 명세(**Smithy API 모델 438개**) + 정답지(실제 AWS). 에이전트는 **실제 AWS에서 먼저 테스트를 통과**시키고, 그 테스트를 만족하는 구현을 만든다.
- 에이전트 3팀(테스트 작성 / 실패 탐색·이슈화 / 이슈별 수정) + **병합 게이트**(빌드·코드 표준·보안·충실도 회귀).
- 커밋 **29,684개**(11-12 레포 실측), 릴리스마다 서비스별 **충실도 점수** 공개, 안 되는 것은 `things-that-do-not-work` 폴더에 공개.
- **한계(중요)**: IAM 정책을 실제처럼 평가하지 않아 거부될 요청이 로컬에서 통과할 수 있음 → **보안 테스트 용도 금지**, 스로틀링·장애·리전 차이 미구현, 텔레메트리 기본 ON.
- <https://github.com/aws/aws-emulator> · <https://www.youtube.com/watch?v=XO7fLWaCeZ8> · HN 962점 <https://news.ycombinator.com/item?id=50456631>

---

## 📺 추천 테크 영상 및 아티클 링크

### Codedeck (한국어, 최근 24시간)
| 영상 | 길이 | 링크 |
| --- | --- | --- |
| AI 안경 5종 비교 — 삼성·메타·XREAL·Even, 렌즈에 뭐가 뜨나요 | 6:28 | <https://www.youtube.com/watch?v=OTZW-bp7eBE> |
| **Fable 5.2 프롬프팅 가이드 쉽게 풀기 — 요령집이 아니라 성격 설명서였다** | 7:46 | <https://www.youtube.com/watch?v=riI3xHhhEOE> |
| **AIDA — 설명서를 테스트로 만든 도구, 전부 통과해야 도움말이 올라간다** | 5:16 | <https://www.youtube.com/watch?v=1Lqy8Edoau8> |
| **AWS 로컬용 Emulator 소개** (에이전트 100% 작성 선언 해부) | 6:20 | <https://www.youtube.com/watch?v=XO7fLWaCeZ8> |
| Dart 3.14 분석 — 새 문법은 없다, 워크플로가 한 바퀴 닫혔다 | 5:36 | <https://www.youtube.com/watch?v=LbeJXSFaaN8> |

> 안경 비교 영상의 실무 팁: 삼성 인텔리전트 아이웨어(Gemini 음성 비서, 65만~70만원, **12/10 국내 출시**), Meta Ray-Ban Display는 **한국 미출시**, XREAL AURA는 유선 연산 퍽 265g.

### 아티클
- AP, *Nvidia must find a way forward without Jensen Huang* — <https://apnews.com/article/nvidia-megaspeed-china-singapore-huang-5403f152a601cf82ce939f1d24fffa0f>
- Google, *Introducing Gemini 4 Hydrogen* — <https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-gemini-4-hydrogen/>
- Wiz, *CVE-2026-119618 Apache HTTP Server RCE* — <https://www.wiz.io/blog/cve-2026-119618-rce-apache-http-server-vulnerability>
- Anthropic IPO — <https://ipo.anthropic.com/us/>
- GeekNews 아티클, *AI 시대, git push 앞뒤에서 바뀌는 것들* — <https://news.hada.io/topic?id=37034>
- Jane Street, *Socratic Tutor* 에이전트 스킬(SKILL.md 공개) — <https://github.com/janestreet/agent_skills/blob/master/socratic-tutor/SKILL.md> (HN 646점)

---

## 🔍 그 외 스캔에서 눈에 걸린 것

- **닌텐도, GitHub 대량 DMCA** — 스위치 에뮬레이터·모드칩 펌웨어 저장소 일괄 삭제 요청. HN 258점. <https://www.ign.com/articles/nintendo-takes-down-switch-emulators-and-mod-chip-firmware-in-massive-github-dmca-sweep>
- **Lock Your GitHub Actions**(HN 324점) — 워크플로 권한·트리거 잠그기. 셀프호스티드 러너를 쓰는 우리 구성과 직접 관련. <https://hugovk.dev/blog/2026/lock-github-actions/>
- **Rust 1.100** 릴리스(HN 상위) · **.NET 11** 발표(11/10, HN 239점) · **Dart 3.14 / Flutter 3.50** 릴리스(위 영상)
- **smolmaxxing** — "작고 오래된 기기에서 LLM 돌리기" (HN 256점) <https://github.com/hyperionaidev/smolmaxxing/>
- **Libreagi — Save OSS Models** (오픈 모델 보존 프로젝트, HN 116점)
- **"정확한 소프트웨어를 쓰는 게 왜 어려운가, 수학만으로는 부족한 이유"(2016)** — HN 344점, 설계·검증 논의로 여전히 유효 <https://pron.github.io/posts/correctness-and-complexity>
- GeekNews 20시간: *AI와 일할 때 가장 자주 던지는 질문 — 지금 관성에 끌려가고 있는가* (할루시네이션보다 잦은 실패는 "전제를 의심하지 않고 다음 단계를 합리화하는 것"). <https://news.hada.io/topic?id=37030>
- **93%의 페인트 튄 자국은 유효한 Perl 프로그램**(2019, HN 563점) — 오늘의 유일한 순수 유머 항목.

---

## 📌 내 프로젝트(PDF 비교·무환각 검색 / arch-tech)에 바로 매핑되는 3가지

1. **답변 모델을 `gemini-3.8-flash` → Gemini 4 Hydrogen으로 1순위 교체 검토.** 지금 `pickle.rag.model`은 `gemini-3.8-flash`(어젯밤 퇴역 안내로 교체한 값)이고, Hydrogen은 구글이 **3.8 Flash에서 옮겨오는 팀을 위한 정식 후속**으로 명시한 모델이다. 무환각 목표에 직결되는 지표가 **"날조 주장 5배 이상 감소"**, 그리고 출력 1M 토큰이라 긴 약관 요약에서 잘림 위험이 줄어든다. 절차: 이미 만들어 둔 `/api/v1/chat/test/models` 프로브로 우리 키에 실제로 열려 있는 모델명(`gemini-4-hydrogen` 형식)과 200 여부를 먼저 확인 → 확인된 것만 `application.properties` 1순위로, 기존 3.8/3.5 폴백 체인은 2·3순위로 유지. **503은 모델명이 아니라 무료 키의 공유 TPU 용량 문제**이므로, 모델 교체와 별개로 폴백·재시도 로직은 그대로 두는 게 맞다.
2. **프롬프트 저장소에 '게시 게이트'를 붙인다(AIDA 패턴).** 어젯밤 만든 페르소나·프롬프트 DB(`prompts.json` + REST)에, 골든 질문셋(위약금·해지·면책 등 20~30문)을 YAML로 두고 **모든 문항이 통과해야 그 프롬프트 버전이 '활성'으로 승격**되게 하면, 프롬프트를 만질 때마다 회귀가 자동 검증된다. 게시물마다 `last checked` 타임스탬프를 남기면 PoC 시연에서 "언제 검증된 답변인지"를 바로 보여줄 수 있다. 판단(에이전트)과 실행(결정적 러너)을 분리하는 구조도 그대로 참고할 만하다.
3. **arch-tech는 지금이 CI를 '모듈별 독립'으로 박을 타이밍.** Aspen 수치가 근거다 — 브랜치 푸시는 초당 567회까지 되지만 **main 순차 반영은 초당 약 4회**. 우리처럼 여러 에이전트가 같은 레포에 직접 푸시하는 환경(poc-py의 외부 커밋, 타 LLM 푸시)에서는 **main에 들어가는 마지막 직렬 구간**이 처리량과 안전성을 동시에 결정한다. 현재 arch-tech는 `ci/github-actions-ci.yml`이 폴더에만 있고 `.github/workflows/`에 없어 **CI가 아직 안 붙은 상태** — 모듈별 `paths` 필터 잡으로 옮겨 붙이고, poc-pickle은 배포 워크플로에 동시 실행 취소(`concurrency`)와 병합 전 검증 순서를 명시해 두는 게 좋다. 덧붙여 앞단 프록시를 httpd로 두는 선택은 오늘 CVE 때문에 보류(2.4.71+ 아니면 nginx).

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-13 08:25 KST 기준)

**알림·요청**: GitHub 알림함은 API로 읽을 수 없어 `review-requested:@me` / `mentions:@me` / `assignee:@me` 검색으로 확인 — **전부 0건**. 열린 PR·이슈도 전 레포 0건. 대기 중인 리뷰·멘션 없음.

| 레포 | 공개 | 마지막 푸시(UTC) | 상태 |
| --- | --- | --- | --- |
| **poc-pickle** | private | 11-12 23:14 | 야간 커밋 7건 전부 배포 성공. 최신 배포(43327181436) success |
| **poc-py** | private | 11-12 14:44 | LLM 실패 시 휴리스틱 세그멘터 폴백 추가(500 방지) |
| **arch-tech** | public | 11-12 22:58 | 신규. `arch-hexagonal` + ArchUnit, 한국어 커밋 |
| **youtu-lib** | public | 11-11 23:19 | 오늘 브리핑으로 갱신 |
| **memo** | private | 11-11 22:24 | 변경 없음 |

- **poc-pickle 야간 7건(15:07~23:14Z)**: 임베딩 `text-embedding-004` → `gemini-embedding-001`(768차원) · PDF 이미지 URL `/pages/{page}/image` 및 블록·조항 BBox 오버레이 · 상담형 페르소나·3턴 히스토리 · 프롬프트/페르소나 스토어 + 실시간 테스트 드로어 · Gemini 네이티브 `generateContent` 연결 · `gemini-2.5-flash` 1차 교체 → **`gemini-3.8-flash` 계열로 최종 교체**(애플리케이션 프로퍼티 + NAS compose 동시 수정, 단위 테스트 동반). 현재 설정값 `pickle.rag.model=gemini-3.8-flash`, `pickle.embed.model=gemini-embedding-001`.
- **poc-py**: 11-12 08:16~08:17Z 커밋 3건은 **`heesu.son <heesu.son@navercorp.com>` 계정이 PR 없이 main에 직접 푸시**(게이트웨이 임베더 MRL 절단, LlamaParse `custom_prompt`, 섹션 경로·조항 참조 처리). 해당 변경은 11-12 17:35 KST 요청으로 이미 전수 검토한 건이며, 이후 추가 커밋은 없음. → **PR 없는 직접 푸시 경로가 열려 있다는 점은 계속 유의**(브랜치 보호 미설정 추정).
- **arch-tech**: 최초 커밋 1건(11-12 22:57Z, 한국어), `com.arch.tech` 그룹 ID, Spring Boot 4.1.2 · Kotlin 2.3.21 · JDK 25 타깃(`gradle/libs.versions.toml`의 `jdk = "25"`) · ArchUnit 1.4.1. **`.github/workflows` 없음 = CI 미실행**, 모듈별 독립 빌드 요구는 아직 워크플로에 반영 전.
- **GitHub Actions/Pages/Webhooks**: 현재 전 서비스 정상. 11/12 장애는 Webhooks(약 1% 트래픽)·이메일 발송에 한정, Actions 미영향.
