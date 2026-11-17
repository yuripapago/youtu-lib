# 2026-11-18 (수) 데일리 테크 브리핑

> **수집** 2026-11-18 08:15 KST · 최근 24시간(2026-11-17 08:15 ~ 11-18 08:15 KST = 11-16 23:15 ~ 11-17 23:15 UTC) 중심
> **소스** Hacker News(front), GeekNews, calif.io·JFrog Security Research·Aikido·OpenJDK Advisory·Oracle CPU, spring.io(This Week in Spring 11-18)·GitHub Releases/Milestones·Maven Central, OpenAI Blog·developers.openai.com·Microsoft AI·Microsoft Copilot Blog·Ignite 2026·GitHub Changelog·blog.google·Google Developers Blog, CNN·TechCrunch·AppleInsider·9to5Mac·Reuters·Guardian·FT(경유), claude.dev·runta·PostHog·Tencent, Cloudflare Blog, CMU CS Learn, Codedeck
> **오늘의 한 줄** — *"AI가 찾은 Base64 결함 하나가 JDK 4개 라인을 열었고, 같은 주에 그 결함이 아니라 '패치'가 NSA 내부망을 내렸다 — 찾는 속도와 되돌리는 속도가 뒤집힌 하루."*
> ※ Reddit(r/LocalLLaMA·r/MachineLearning)은 이번 수집에서 직접 읽을 수 없었다(403). HN·GeekNews·공식 블로그로 대체했다.

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **Java4Shell (CVE-2026-114938)** — OpenJDK Base64 디코더의 힙 손상, Keycloak·Jenkins·Confluence가 인증 없이 뚫린다

`AAAAAAAA…A=` 65글자로 트리거된다. Java에 **메모리 손상 결함**이 있다는 것 자체가 이 사건의 요점이다.

| 항목 | 내용 |
| --- | --- |
| 결함 위치 | OpenJDK가 Base64를 빠르게 풀려고 쓰는 **x86-64 AVX-512 VBMI 인트린직**. 손으로 짠 어셈블리가 마지막 몇 글자를 처리하는 부분 |
| 트리거 | 신형은 **`A` 64개 + `=`**, 구형 17은 **`A` 128개 + `=`**. 50바이트 결과 배열에 64바이트 쓰기 → **배열 끝에서 62바이트 초과 기록** |
| 결과 | Java는 입력을 결국 거부하지만 **그 시점엔 옆 객체가 이미 오염**됨. 하트블리드식 힙 유출 → 원격 코드 실행 |
| 점수 | Calif **CVSS 4.0 9.2**, Oracle/OpenJDK **CVSS 3.1 8.1**(`NHNNUHHH`) — 같은 결함, 다른 산식 |
| 영향 범위 | **Java 17·21·25·27**, **17.0.6 이후**, **x86-64 + AVX-512 VBMI CPU 전용**. ARM64는 미해당 |
| 수정 버전 | OpenJDK **17.0.21.0.3 / 21.0.13.0.3 / 25.0.5.0.3 / 27.0.1.0.3** (Oracle 표기는 17.0.21.1·21.0.13.1·25.0.5.1·27.0.1.1, Corretto는 17.0.21.1.2·21.0.13.1.2·25.0.5.1.2) |
| 우회(패치 불가 시) | `-XX:-UseBASE64Intrinsics` **또는** `-XX:UseAVX=2` — 둘 중 하나면 공격 성립 안 함 |

**Calif가 실제로 뚫은 제품**(기본 설정 기준, 비망라):

| 구분 | 제품 |
| --- | --- |
| **사전 인증(pre-auth)** | **Keycloak**, Atlassian **Jira**, **Confluence**, **JFrog Artifactory**, **Jenkins**, PingFederate |
| 사후 인증(post-auth) | Apache Kafka Broker, Apache Solr, Elasticsearch, Gerrit, Spinnaker |
| 그 외 | Apache Struts·**Spring Boot** 샘플 앱, 그리고 **TLS 클라이언트도 가능**(악성 서버에 접속만 해도 침해 — samczsun 사례) |

- **공격 난이도는 "만능"이 아니다.** Calif 스스로 *"범용 페이로드는 없다. 각 표적마다 디코더 경로·라이브 라이터·리더 또는 피드백 경로·힙 레이아웃·앱 액션이 필요하다"*고 적었다. 네트워크로 힙을 정리(remote heap feng shui)해야 해서 **RTT·지터가 낮고 안정적이어야** 하고, 실제로 그들은 **표적과 같은 리전의 VM을 빌려서** 안정화했다. HN 최상위 댓글의 판단도 같다 — *"단기 대량 익스플로잇은 어렵지만, 고가치 표적의 **패치 안 된 롱테일**은 현실적으로 털릴 것"*.
- **AI가 찾았다.** Calif 연구자 Quang Luong이 **발견 1시간, 첫 동작 익스플로잇 2시간**, 총 토큰 비용 **$200 미만**. PHP2Shell에 이어 두 번째로 같은 팀이 같은 방식으로 냈다. 이들은 이걸 *"켄타우로스 해커"*라 부르고, 반대 방향의 처방을 **"vibe-uncoding"**(필요 없는 코드·최적화를 지워 공격면을 줄인다)이라 이름 붙였다. Base64 인트린직 자체가 *"소수 앱만 이득 보는 최적화가 모두에게 배포된"* 사례라는 지적은 정확하다.
- **미해결 모순 2개(그대로 남겨 둔다)**: ① Calif가 인용한 Oracle 목록에는 **`8u511b33-perf`**가 취약 버전으로 나오는데, OpenJDK 위험도 표의 CVE-2026-114938 행에는 **8·11에 점이 없다**(Aikido도 "8·11은 이 CVE 미해당"으로 정리). ② **Adoptium GA 이미지가 아직 없다** — 이번 확인 기준 Temurin 21은 21.0.13.1이 **ea-beta로만** 올라와 있고 GA 최신은 21.0.13+9(10-22). Corretto는 21.0.13.1.2가 나왔다.
- 같은 CPU(Nov 2026)에 **CVE 10건 + OpenJFX CVE-2026-86144(7.5)**가 함께 나왔다 — GSS 7.4, `javax.net.ssl` 7.4, `hotspot/compiler` 7.0 등. **JDK 올릴 때 한 줄로 같이 처리되는 게 10건**이다.
- <https://calif.io/java4shell/> · <https://openjdk.org/groups/vulnerability/advisories/2026-11-17> · <https://research.jfrog.com/post/java4shell-vulnerability/> · <https://www.aikido.dev/blog/java-17-rce-vulnerability-cve-2026-114938> · 탐지 스크립트 <https://github.com/semgrep/detection-scripts/tree/main/CVE-2026-114938> · HN <https://news.ycombinator.com/item?id=50538240> · GeekNews <https://news.hada.io/topic?id=37370>

### 2. **NSA 내부망이 내려갔고, 원인은 '패치'였다** — PHP2Shell 대응 패치가 시스템을 못 살렸다

이번 주 장애 목록은 이제 **M365 → FAA 지상정지 → United → Chase → Apple → NSANet**으로 이어진다. 그런데 NSANet만 성격이 다르다.

| 항목 | 내용 |
| --- | --- |
| 사건 | NSA 내부 기밀 통신망 **NSANet** 다운(11-17). 인원 **분산 출근(staggered arrivals)** 조치 |
| NSA 공식 | *"**예정된 정기 유지보수 중 일시적 성능 저하**. 일부 내부 시스템 영향, 임무 핵심 역량은 정상"* |
| 진짜 원인(CNN) | DNI **Jay Clayton**이 Fox Business에서 — *"**지난달 발견된 주요 소프트웨어 취약점**을 막으려 패치를 적용했는데, **패치 후 시스템이 예상대로 다시 올라오지 않았다**"* |
| 그 취약점 | **PHP** 취약점(PHP2Shell). AI 도구가 익스플로잇 개발을 도왔고, 전 세계 정부·기업이 패치 경쟁 중. NSA 자신도 패치 권고를 냈던 주체 |
| 공격 여부 | 관계자 6명 + DNI 모두 **사이버 공격 증거 없음** |

- **같은 날 Apple도 내려갔다.** iCloud 계정·로그인(전 사용자), App Store, Apple Pay, Apple Music, AppleCare, App Store Connect·Xcode Cloud까지. Apple은 *"**내부 업데이트**가 원인"*이라며 사이버 공격을 부인했고 **18:00 ET 전부 복구**. Bloomberg 보도로는 직원용 **AppleWeb·AppleConnect·VPN**까지 영향을 받아 사내 빌드 다운로드·회의실 예약이 막혔다. 같은 날 Gmail 지연 신고도 올라왔으나 Google은 내부 징후를 부인했다.
- **읽어야 할 선**: 이번 주 장애들은 **원인이 서로 다르고, 연결됐다는 증거도 없다**(Reuters·CNN 모두 "공격 연관 없음"으로 정리). 그런데도 같은 주에 겹친 이유는 하나다 — **모두 '변경'을 통해 들어왔다.** 7월에 들어간 M365 결함, PHP2Shell 대응 패치, Apple의 내부 업데이트.
- <https://www.cnn.com/2026/11/17/politics/highly-classified-national-security-communications-network-goes-down> · <https://techcrunch.com/2026/11/17/apple-services-including-icloud-app-store-and-apple-music-hit-by-outage/> · <https://appleinsider.com/articles/26/11/17/icloud-account-login-icloud-mail-services-are-having-trouble-today> · <https://www.theguardian.com/technology/2026/nov/17/gmail-apple-outage-major-disruption> · <https://news.ycombinator.com/item?id=50535238>

### 3. **GPT-6.2 Astra·Sol + MAI Code 2 — 모델이 아니라 '작업당 비용'으로 줄 세운 날** (HN 997점·612댓글)

OpenAI와 Microsoft가 같은 날 각자의 가격표를 냈다. 두 회사 모두 **"성능"이 아니라 "같은 일을 얼마에 끝내는가"**를 전면에 걸었다.

| 모델 | 핵심 주장 | 가격(1M 토큰) |
| --- | --- | --- |
| **GPT-6.2 Astra** | Agents' Last Exam **GPT-6 Astra보다 +5.2점, 비용 1/3 미만** / **Claude Fable 5.2보다 +5.5점, 비용 5%** | 입력 $8 · 캐시 $0.40 · 출력 $40 (6 Astra 대비 입·출력 20%↓) |
| **GPT-6.2 Sol** | FrontierCode 2.0에서 Astra와 **0.5점 차**, 비용 **1/5** | 입력 $2 · 캐시 $0.10 · 출력 $10 (6.1 Sol과 동일) |
| **MAI Code 2** | 4개 벤치 평균 **GPT-6.1 Sol의 98% 성능을 40% 비용**, **Opus 5.5의 96%를 5% 비용** | 50% 프로모션가 기준으로 비교됨(정가 아님) |
| **MAI Code 2 Flash** | GPT-6 Luna·Haiku 5.5 대비 **65%·20% 비용** | Copilot에 즉시, Foundry는 제한 프리뷰 |

- **전환할 때 조용히 깨지는 지점**(Codedeck이 공식 문서로 확인한 것): ① 두 모델 모두 **`reasoning: none`을 거부**한다 — 기존 `none`/`minimal` 설정은 `low`로. ② **입력 272K 초과 시 요청 전체에 할증**(입력 2배·출력 1.5배). ③ 대화 이력의 **앞부분을 수정하면 추론 항목이 제거**되고, 그러면 품질이 떨어지거나 같은 작업을 반복하며 토큰을 더 쓴다. ④ 최대 922K 입력·128K 출력, 컨텍스트 1.05M.
- **벤치마크 읽는 법**: Microsoft는 *"이 모델들은 **어려운 문제·아주 긴 호라이즌 에이전트 작업에는 아직 튜닝되지 않았다**"*고 스스로 밝혔다. OpenAI의 Fable 5.2 비교에는 **폴백 모델을 쓴 9개 과제가 제외**됐다는 주석이 붙는다. 두 회사 모두 **"우리가 이겼다"가 성립하는 벤치만 표에 남긴다** — 어제 Qwen4-Max와 똑같은 형태다.
- <https://openai.com/index/introducing-gpt-6-2-astra-sol/> · <https://developers.openai.com/api/docs/models/gpt-6-2-astra> · <https://microsoft.ai/news/introducing-mai-code-2-and-mai-code-2-flash/> · Codedeck 요금·전환 <https://www.codedeck.kr/posts/gpt-6-2-astra-sol-api-pricing-conversation-migration> · HN <https://news.ycombinator.com/item?id=50535230>

### 4. **Gemini, 이제 "전화 걸고 결제한다" — 한국 포함 무료 배포 시작**

Google이 11-17 **Gemini Tasks**를 열었다. 문장 하나로 **대신 전화하고, 청구서를 내고, 장바구니를 채운다**.

| 항목 | 내용 |
| --- | --- |
| 배포 | **미국·한국·일본·캐나다·호주·인도**에 **무료**로 순차 배포. 엔진은 **Gemini 4 Hydrogen**. 고급 기능은 미국부터 |
| 전화 | *"가까운 딜러십 두 곳에 전화해서 금요일 이른 아침 예약을 잡아줘"* → 지도에서 찾아 **동시에 두 곳에 전화**, 대기음 대신 대기, **캘린더 빈 시간에 맞춰 예약 등록** |
| 결제 | **Agent Payments Protocol(AP2)** + Amex(예정)·Mastercard·Visa 디지털 토큰. **하드 지출 한도**를 미리 걸 수 있고 영수증은 Google Wallet으로 |
| 그 밖 | RCS로 문자 지시, Gmail 포워딩(`gemini@google.com`)으로 일정 자동 등록, **60개 이상 앱 연결**, Google Password Manager로 공과금 포털 접근·요금 분석 |
| 안전장치 | 계정 전용 **하드웨어 격리 VM**(Agent Compute Engine), 프롬프트 인젝션 필터·Safe Browsing, **"Buy" 클릭 전 확인**, 모호하면 되묻기, **전화 시 상대에게 AI임을 즉시 고지**, **기업은 수신 거부 가능** |

- **우리 대화와 정확히 겹치는 지점**: 이 제품이 "AI가 사용자 이름으로 전화한다"를 **소비자 기본값**으로 만든다. Google이 택한 설계는 세 가지다 — **① AI임을 먼저 밝힌다 ② 상대가 거부할 수 있다 ③ 결제는 사전 승인·한도**로 묶는다. 앞으로 "AI가 나 대신 어디까지 해도 되는가"를 물을 때 이 세 축이 사실상 업계 기준선이 된다.
- 로보틱스 쪽도 같은 날: **Gemini 4 Hydrogen**이 실제 6축 팔을 제어(AA Robotics 0.1 **58점**), **Antigravity에 Robotics 플러그인**(드라이버 설치→캘리브레이션→MuJoCo 시뮬→제어 코드까지 스킬로). 그리고 **AI Studio에서 프롬프트 하나로 Kotlin 풀스택 안드로이드 앱**(Firestore·Google Sign-In·Play 배포)까지.
- <https://blog.google/innovation-and-ai/products/gemini-app/tasks-in-gemini-free-new-features/> · <https://developers.googleblog.com/gemini-hydrogen-for-robotics/> · <https://developers.googleblog.com/build-full-stack-android-apps-with-gemini-in-google-ai-studio/>

---

## 🤖 AI / LLM & Agentic 동향

**1) Microsoft Ignite 2026 — Copilot은 '앱'에서 '통제 평면'이 됐다 (11-17).**
Home GA(Chat+Cowork 통합), Office in Copilot GA, Code는 몇 주 내 GA, Managed Runtime GA, **Autopilot**은 Frontier 고객 롤아웃 시작. **Auto** 라우팅이 모델·추론 강도를 골라 품질·속도·비용을 맞춘다. **Work IQ**는 M365의 **19.5EB·메일 900억·Teams 메시지 150억**을 "무엇을 무시할지" 판단해 다룬다. 더 중요한 건 거버넌스다 — **Agent 365**가 Anthropic·Databricks·OpenAI·Salesforce까지 연결 대상을 넓히고, **Entra Agent ID**로 에이전트 신원을 발급하며, **MCP 서버·플러그인·스킬에 대한 통제**를 추가하고, 로컬 에이전트는 **MXC(실행 컨테이너)**로 격리한다.
<https://www.microsoft.com/en-us/copilot/blog/2026/11/17/copilot-at-microsoft-ignite-expanding-whats-possible-with-intelligence-built-for-work/>

**2) GitHub도 같은 날 Ignite 묶음 — 우리가 바로 쓸 것 3개 (11-17).**
- **Copilot Evals 공개 프리뷰**: 스킬·커스텀 에이전트·**MCP 도구**에 대해 "현실적 과제 + 좋은 결과 정의"를 주고 **결과와 과정을 함께** 채점, 변경 전/후 실행을 비교. 엔진은 로컬 eval 도구 **Vally**. → 우리 Eval 41문항과 같은 자리에 GitHub이 표준 도구를 깔았다.
- **개발자·에이전트용 시크릿 스캐닝 온디맨드** + **Copilot 코드 리뷰: 적응형 노력·시크릿 탐지**.
- **Copilot 벤치마크 사이트에 하네스 비교** 추가 — 아래 FrontierHarness와 같은 문제의식.
<https://github.blog/changelog/2026-11-17-github-copilot-evals-is-now-in-public-preview> · <https://github.blog/changelog/> (11-17자 14건)

**3) Claude Code 팀의 검증론 — "테스트 3종에 투자하라" (claude.dev, 11-16 · HN 246점·193댓글).**
Anthropic 코드의 **약 90%를 Claude가 쓰고 리뷰**한다. 이 규모에서 병목은 생성이 아니라 검증이다. **검증 루프가 없으면 Claude를 1~2개만 돌릴 수 있고, 있으면 수십~수백 개를 병렬로 돌린다**. 구조화 파일 생성 내부 테스트에서 **별도 채점자(rubric)를 붙이자 성공률이 최대 10점 상승**, 어려운 문제에서 이득이 컸다. 투자할 3종은 **E2E(사용자 흐름, "전체가 되거나 안 되거나")**, **속성 테스트**(왕복 인코딩·불변식·멱등·최적화/순진 구현 동치), **차분 테스트**(**옛 시스템이 오라클** — 마이그레이션에 특히). 피할 것: **플레이키 테스트**(에이전트가 고치는 대신 재실행을 배운다), **느린 테스트**.
<https://claude.dev/blog/how-the-claude-code-team-thinks-about-verification/>

**4) Harvard 워킹페이퍼 — 코드 +30%, 완료된 일은 그대로, 리뷰는 49% 길어졌다 (HN 재부상 11점).**
Jellyfish(엔지니어링 분석) + Revelio Labs(LinkedIn) 결합, **718개사·725,938명**, 2021-01~2026-03. Jira 6,830만·커밋 2억 90만·PR 4,910만. **AI 코딩 에이전트 도입 후 코드량 약 30% 증가, 완료된 작업의 측정 가능한 증가 없음, 리뷰 통과까지 49% 지연**. 저자들은 "채택 시점이 도구 인지·조달 지연에 좌우됐다"는 점을 한계로 인정한다(동료 심사 미완).
<https://complexdiscovery.com/harvard-researchers-find-ai-coding-agents-shifted-the-bottleneck-to-code-review/> · 원문 PDF <https://fion.ac/jellyfish.pdf> · NBER <https://www.nber.org/papers/w35275>

**5) FrontierHarness v1.1 — 에이전트 비용의 64.5%는 '실패와 시간초과'에 쓰였다 (runta, 11-17).**
하네스 **18종(설정 22개)** × 모델 5종(GLM-5.3·GLM-5.3 Flash·DeepSeek V4.1 Flash·Muse·Kimi-K3) × 터미널 과제 62개 × 4회 = **27,280회 실행, 830억 토큰**. 충격적인 건 세 가지다. ① **같은 하네스의 순위가 모델을 바꾸면 뒤집힌다** — Codex CLI가 GLM-5.3에선 과제당 비용 1위, Kimi-K3에선 **16위**. 같은 모델 안에서 하네스 간 편차 **1.8~5.0배**. ② 정가 기준 지출의 **52.7%가 실패, 11.8%가 시간초과 = 64.5%**(실행 횟수 비율이 아니다). ③ **실패 실행의 68~82%가 시간제한 전에 '최종 답변'을 내고 정상 종료**했다 — 즉 **에이전트는 "끝났다"고 말하고 틀렸다**. 통과율 95% 구간이 ±10%p라 몇 %p 차이는 동률로 봐야 한다.
<https://runta.com/blog/introducing-frontierharness-eval-v1-1/> · <https://www.codedeck.kr/posts/frontierharness-v11-model-harness-cost-evaluation>

**6) PostHog "모든 것에 LLM이 필요한 건 아니다" — 결정 모델로 비싼 호출을 막는다.**
Noul/Choice/Score 같은 **타입 있는 확률 출력**(Jev)이 자유 서술보다 싸고 명시적이다. Replay Vision에서는 세션 요약마다 **예/아니오 질문 2개**로 PII를 걸러 **98.7%가 LLM 호출을 건너뛴다**. 임계값은 **어느 실수가 더 비싼가**로 정한다(PII는 오탐이 싸서 0.4, 슬랙 봇 스팸은 미탐이 싸서 0.85). 프론티어 대비 **최대 444.6배 저렴·193.6배 빠름**. 주의: 0.85 확률은 1,000건 중 150건 오답이다.
<https://newsletter.posthog.com/p/decision-models> · HN <https://news.ycombinator.com/item?id=50535740>

**7) Tencent Hy4 flash — 1M 문맥·12B 활성·입력 $0.07 (11-16, 오늘 HN 재부상).**
224B 총 파라미터(Engram 64B 포함) 중 **12B만 활성**, 멀티모달, 1M 문맥, **150 tok/s 초과**. API **입력 $0.070·출력 $0.281·캐시 $0.003**/1M(OpenRouter는 $0.035/$0.1405/$0.0015). 내부 벤치 WorkIndex에서 **과제당 비용이 Hy4의 1/20**. 단 **가중치 미공개**(오픈소스 계획만), 수치는 전부 자사 평가다.
<https://www.tencent.com/tencent-launches-hy4-flash-to-power-high-speed-cost-efficient-ai-deployment-for-everyday-workflows/>

**8) MCP는 이제 '제품 기능'이 아니라 '영업 채널'이 됐다 (11-17 하루에만 4건).**
**Guidepoint MCP**(전문가 인터뷰 15만 건 + 어드바이저 네트워크를 Claude·ChatGPT·Gemini·Perplexity에서 — 출처 추적·컴플라이언스 포함), **Built MCP**(부동산 대출 — **쓰기 가능**: 딜 생성·예산 수정·인보이스 코딩. 단 **결제 실행·사용자 생성·권한 변경은 제외**, 감사 로그 기록), **IC Manage MCP**(반도체 설계·IP, 읽기 전용 + RBAC), Clay–ChatGPT 재출시. 공통 패턴: **권한 상속 + 민감 액션 제외 + 감사 로그**.
<https://www.guidepoint.com/pressreleases/guidepoint-mcp-links-its-advisor-network-and-expert-knowledge-to-ai-platforms-streamlining-workflow/> · <https://getbuilt.com/blog/introducing-built-mcp/>

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스·지원 기한 — **내일(11-19)이 Spring 대형 릴리스 데이**, 그리고 오늘은 JDK 보안 데이

| 프로젝트 | 최신 | 날짜 | 메모 |
| --- | --- | --- | --- |
| **JDK** | **CPU 2026-10** | **11-17** | **CVE 10건 + OpenJFX 1건.** Java4Shell이 그중 첫 줄 |
| Spring Boot | **4.2.0-RC2** | 11-05 | **GA 미출시 확인**(Maven Central `lastUpdated` 11-05). 4.2.0 GA 마감 **11-19**. 현행 GA 4.1.2/4.0.9 |
| Spring AI | **2.1.0-RC2** | 11-05 | GA 11-19 트레인. `DecisionModel`·툴 인자 스트리밍·MCP "MODERN" 모드·Anthropic SDK 3.4.0 |
| Gradle | **9.9.1** | 11-10 | 보안 3건(Critical 9.3: **version catalog 값이 생성 accessor에서 코드로 실행**) + Kotlin DSL 증분 컴파일로 대형 스크립트 재컴파일 **최대 65%↓** |
| Kotlin | **2.5.0-RC** | 11-11 | 정식 12월. 현행 GA 2.4.21 |
| Qdrant | **1.20.2** | 11-16 | HNSW 클래식 기본 복귀, 동일 필터 배치 검색 대폭 개선 |
| llama.cpp | **b12484** | **11-17 22:57Z** | 야간 빌드 계속(b12445 → b12484) |

- **This Week in Spring(11-18)의 첫 문장이 이번 주를 요약한다** — *"first thing's first: **UPDATE YOUR JVMs!** The new Java4Shell CVE looks pretty nasty."* 나머지는 Spring for Apache Pulsar의 새 보금자리(ASF), AGNTCon 2026 인터뷰(James Ward × Alexander Ioffe, × Ben Hale), 4.2.0-RC2·Spring AI 2.1.0-RC2·Spring Cloud 2026.0.0-RC2. Josh Long은 지금 **테살로니키 Voxxed Days**에 있다.
- **Inside.java는 11-17 신규 글이 없다.** 최신은 11-16 *"Valhalla의 값 객체를 JDK에 넣는 데 왜 3,000 커밋이 필요한가"*(David Simms, Devoxx Belgium).
- <https://spring.io/blog/2026/11/18/this-week-in-spring-november-18th-2026> · <https://github.com/spring-projects/spring-boot/releases> · <https://docs.gradle.org/9.9.1/release-notes.html> · <https://inside.java/>

### 운영 교훈 — **보안 패치가 가용성 사고의 1순위 원인이 되는 주**

이번 주(11-12~17) 장애 6건을 원인별로 다시 세워 보면 이렇게 된다.

| 사건 | 트리거 | 성격 |
| --- | --- | --- |
| M365 24시간 | **7월에 들어간 잠복 결함** + 14% 용량 손실 + Teams 페일오버 | 롤백 불가 → "패치의 패치" |
| FAA 전국 지상정지 | 네트워크 장애 | — |
| Chase·타행 지연 | 온프레미스 **소프트웨어 버그** | 국지적, 자금 접근은 유지 |
| **NSANet** | **PHP2Shell 대응 패치** 적용 후 시스템 미기동 | **패치가 원인** |
| **Apple** | **내부 업데이트** | **변경이 원인** |
| Gmail(신고만) | Google은 내부 징후 부인 | — |

- 실무 규칙으로 옮기면: **① 보안 패치는 '배포'가 아니라 '변경'으로 다룬다** — 카나리·단계별, 그리고 **패치 후 헬스 판정 기준을 패치 전에 고정**한다. ② **"패치 실패"의 대부분은 애플리케이션이 아니라 기동 조건**이다(설정 스키마·마이그레이션·시크릿). ③ **롤백 대상은 아티팩트가 아니라 코드 경로**여야 한다(M365가 이미 증명).
- <https://www.cnn.com/2026/11/17/politics/highly-classified-national-security-communications-network-goes-down> · <https://www.reuters.com/> (Apple, investing.com 경유 <https://www.investing.com/news/stock-market-news/apple-works-to-restore-services-after-outage-caused-by-internal-update-5033945>)

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) Quad9, 프랑스에서 철수 — "갱도의 카나리아" (Cloudflare, 11-17 · HN 105점).**
프랑스 법원이 **저작권 침해 도메인 차단을 이유로 일일 벌금**을 부과하자 스위스 비영리 Quad9가 **프랑스 공용 DNS 리졸버 서비스 철수**를 결정했다. Cloudflare의 요점은 피해 범위다 — 프랑스의 **정부기관·학교·병원·유틸리티**가 이 리졸버를 쓰는데 **개별 사용자에게 통지할 방법이 없어** 철수 시점에 **인터넷이 이유 없이 멈춘다**. 앞서 Cisco도 프랑스·포르투갈·벨기에에서 OpenDNS를 뺐고, **벨기에 법원은 2027-01 초까지 Cloudflare·Google·Cisco에 차단을 명하되 아니면 벌금**을 예고했다.
<https://blog.cloudflare.com/quad9-withdrawal-from-france/> · HN <https://news.ycombinator.com/item?id=50538994>

**2) Chrome 팀 "Modern Web Guidance 1.0" — 코딩 에이전트에게 웹 전문지식을 스킬로 주입 (GeekNews 21점).**
5월 I/O 프리뷰가 **1.0으로 안정화**, 이제 **Firefox·Edge도 참여**하는 크로스 브라우저 프로젝트다. 겨냥한 건 **모델이 반복적으로 놓치는 수백 개의 지식 격차** — 접근성(포커스·모션 감소·폼 검증), UX(스와이프·다이얼로그 전환·테마), 성능(메인 스레드 스케줄링·오프스크린 격리), 보안(HTML 정제·CSP·패스키). *"잘못된 ARIA는 ARIA가 없는 것보다 나쁘다"*는 지적이 상징적이다. 기존 코드 현대화는 `/modern-web-guidance Modernize the code in this directory`로 돌리고, **공개 eval 대시보드**로 미사용 에이전트 대비 개선을 제시한다. **DevTools for Agents**와 결합하면 작성→실행→디버깅까지 닫힌다.
<https://news.hada.io/topic?id=37324> · <https://developer.chrome.com/docs/modern-web-guidance> · eval <https://goo.gle/mwg-evals>

**3) "AI 슬롭은 왜 이렇게 읽기 짜증날까" — 측정 가능한 결함 3종 (GeekNews 21점).**
명확성은 **AI 흔적 탐지와 다른 축**이다. 불명확한 문장 비율: **AI 기사 54% / 사람 기사 10% / Claude Code 78% / Stack Overflow 11%**. 반면 AI 탐지기 판정은 99% / 4% / 99% / **0%** — **탐지기는 "AI가 썼나"만 보고 "엉성한가"는 못 본다**. 결함은 셋: ① **행위자·행동 숨김**(명사화), ② **모호한 표현** — Claude Code에서 가장 흔함(*"DB 준비 상태 확인이 사용자 세션 진입 경계에 위치한다"*), ③ **내용 없는 문장과 잘못된 강조**.
<https://news.hada.io/topic?id=37316>

**4) jQuery 4.1.0 + 3.8.0 "passing the torch" (HN 210점).**
`responseURL` 추가, `getElementsBy*` → `querySelectorAll`, **선택자 ReDoS 수정**, Safari 27 정규식 버그 우회, form-associated 커스텀 엘리먼트 직렬화. 눈에 걸리는 건 릴리스 노트의 한 줄 — **"기계 생성 기여 가이드라인 추가"**. 그리고 README의 **NES HeroDevs·TuxCare** 링크(유료 지원 경로).
<https://github.com/jquery/jquery/releases/tag/4.1.0> · <https://blog.jquery.com/2026/11/17/jquery-4-1-0-and-3-8-0-released-passing-the-torch/>

**5) Nvidia, 싱가포르 매출 공시 중단 — 그쪽 법인 매출이 $104B (FT, HN 300점).**
지리별 매출 기준을 **청구지 주소 → 고객 본사 소재지**로 바꾸면서 싱가포르가 항목에서 사라졌다. FT가 법인 계정을 확인한 결과 **2025년 싱가포르 법인 외부 매출 $104B(+72%)**, 2026-01 마감 12개월 **$117.8B**. 그런데 **2025년 이익은 $53M**뿐이다(청구·결제 허브 성격). 기준 변경 자체가 매출 왜곡은 아니지만, **추적 가능성은 줄었다**. 시점이 **Megaspeed 수출통제 조사**와 겹친다.
<https://www.ft.com/content/dbde7a30-548c-4f9d-a4be-7898a0c24dcf> · HN <https://news.ycombinator.com/item?id=50534631>

**6) 그 외 눈에 걸린 것 (11-17 HN)**
- **Solid 2.0 "The Long Hold"** (180점) — <https://www.solidjs.com/blog/solid-2-0-the-long-hold/>
- **Phrack 73 발간** (216점) — <https://phrack.org/issues/73/introduction_md>
- **StarSling (YC P25)** — *"에이전트가 대량으로 만드는 레포를 위한 자기개선 CI"* (Launch HN) — <https://starsling.dev>
- **"Who Builds the Machines That Build the Machines?"** — 섹션 232·공작기계 (73점) — <https://read.corematter.com/p/section-232-us-machine-tools>
- **Web Browser Engineering** 공개 교재 (2021, 104점) — <https://browser.engineering/>
- **GeekNews "CI 시장은 세 갈래로 나뉘었다"**(범용 러너 인프라 / 오케스트레이션 / 코드·빌드 엔지니어링) — 우리 self-hosted 러너 선택의 위치를 정리할 때 참고 <https://news.hada.io/topic?id=37320>

---

## 📺 추천 테크 영상·읽을거리

### ⭐ **Carnegie Mellon CS Learn — 오늘(11-18)은 아키텍처의 날**

| 공개 | 과목 | 강사 |
| --- | --- | --- |
| **11-18(오늘)** | **17-635 Software Architectures** | Swarnalatha Ashok · Scott Pavetti |
| **11-18(오늘)** | **17-643 Quality Management** | Jeffrey Gennari · Christopher Timperley |
| 11-19 | **11-768 AI Agents**, 17-645 Machine Learning in Production | Daniel Fried·Graham Neubig / Christian Kästner·Bogdan Vasilescu |

- 17-643은 **Timperley**(결함 재현·테스트 연구)가 들어간다 — 속성 테스트·차분 테스트를 우리 코드에 붙일 근거로 바로 쓸 수 있다. 17-635는 모듈 경계·의존 방향을 정리하는 데 참고.
- <https://www.cs.cmu.edu/learn/> · <https://www.cmu.edu/news/stories/archives/2026/november/cmu-opens-access-to-top-computer-science-classes-through-free-cs-learn-platform>

### Codedeck — 11-18자 7편 (YouTube 신규 업로드 없음)

| 글 | 링크 |
| --- | --- |
| GPT-6.2 Astra·Sol — Standard 단가·272K 할증·대화 이력 수정 시 추론 제거 | <https://www.codedeck.kr/posts/gpt-6-2-astra-sol-api-pricing-conversation-migration> |
| MAI Code 2·Flash — Copilot 할인 기한(2027-05-17)과 Foundry 프리뷰 범위 | <https://www.codedeck.kr/posts/mai-code-2-flash-copilot-pricing-foundry-preview> |
| FrontierHarness v1.1 — 모델을 바꾸면 에이전트 비용 순위도 달라진다 | <https://www.codedeck.kr/posts/frontierharness-v11-model-harness-cost-evaluation> |
| Claude Linked Billing 베타 — 고객 조직 청구·데이터 처리 조건 | <https://www.codedeck.kr/posts/claude-linked-billing-beta-customer-organization-data-conditions> |
| Proton Lumo Desktop — 커넥터 자동 승인·예약 작업 권한 | <https://www.codedeck.kr/posts/lumo-desktop-connectors-schedules-permissions> |
| SenseNova-Loop-9B 정식판 — 가중치 재사용·엔진 지원 조건 | <https://www.codedeck.kr/posts/sensenova-loop-9b-release-access-engine-conditions> |
| Branchy 분산 추론 — 병렬 분기 속도·배포 조건 | <https://www.codedeck.kr/posts/branchy-parallel-inference-model-release-conditions> |

### 읽을거리
- **claude.dev "How the Claude Code team thinks about verification"** — E2E/속성/차분 테스트를 **에이전트에게 검증을 위임하는 비용 구조**로 설명한다. 우리 리뷰 봇·Eval 설계의 기준점. <https://claude.dev/blog/how-the-claude-code-team-thinks-about-verification/>
- **Calif Java4Shell 기술편("The Tail of Base64")** — 힙 펑슈이·디코더 경로·리더/라이터 체인. 읽고 나면 **"Base64를 외부 입력으로 푸는 곳"**이 어디인지 우리 코드에서 찾게 된다. <https://calif.io/java4shell/>
- **Spring AGNTCon 2026 인터뷰** — James Ward × Alexander Ioffe(<https://www.youtube.com/watch?v=cnXMqAlst1Y>), × Ben Hale(<https://www.youtube.com/watch?v=n6PFNJBol70>)

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 3가지

**1) Java4Shell은 우리 네 레포 전부에 걸린다 — 다만 "지금 터질 것"과 "곧 맞을 것"이 다르다.**

- **사실(확인)**: 우리 이미지는 `eclipse-temurin:21-jdk`·`21-jre`(arch-tech·spring-ai-ops 대부분)와 `eclipse-temurin:25-jdk-alpine`·`25-jre-alpine`(poc-pickle)이다. Gradle 툴체인은 `JavaLanguageVersion.of(21)`(arch-tech 전 모듈). 그리고 **arch-tech는 Keycloak 26.0**(`infra/keycloak/Dockerfile`의 `ARG KEYCLOAK_VERSION=26.0`, `quay.io/keycloak/keycloak:26.0` 기반 자체 이미지)을 포털 `/keycloak/`·`:8098`로 띄운다 — **사전 인증 RCE 목록의 첫 항목**이다.
- **위험도 판정(근거)**: 결함은 **x86-64 + AVX-512 VBMI** 전용이다. 런타임이 **M2 Max 맥 스튜디오(ARM64)** 이고 self-hosted 러너도 같은 머신이므로 **현재 구성에서는 익스플로잇이 성립하지 않는다**. 즉 "지금 터질 일"은 아니다. 그러나 ① **Corretto·Temurin GA 이미지가 아직 .1 빌드가 아니다**(이번 확인 기준 Temurin 21은 21.0.13.1이 ea-beta로만 존재, GA 최신 21.0.13+9) ② **x86 노드로 옮기는 순간 조건이 충족**되고 ③ **Kubernetes류에서 노드 SKU가 우리 모르게 바뀌는 사례**가 흔하다는 점(HN 지적)이 "곧 맞을 일"이다.
- **할 일 순서**: ① 지금 바로 **`JAVA_TOOL_OPTIONS=-XX:-UseBASE64Intrinsics`**(또는 `-XX:UseAVX=2`)를 compose env에 추가 — ARM64에서도 무해하고, x86 이전 시 그대로 방어가 된다. ② **Keycloak 26.0 → 최신 26.x**로 올릴 때 벤더 JDK가 패치 빌드인지 확인(자체 JDK 번들이라 base image 교체만으론 안 된다). ③ Temurin `.1` GA가 올라오면 **한 번에 네 레포 베이스 이미지 갱신**(Nov CPU CVE 10건이 같이 닫힌다). ④ Semgrep 탐지 스크립트의 5개 조건을 CI에 넣어 **"HotSpot + x86-64 + 17.0.6 이상 + avx512_vbmi + 인트린직 ON"** 조합을 잡는다.

**2) 오늘 뉴스 3건이 하나의 결론으로 모인다 — "에이전트는 끝났다고 말하고 틀린다"를 계측해야 한다.**

- **사실**: FrontierHarness는 **실패 실행의 68~82%가 시간제한 전에 정상 종료하며 최종 답변을 냈다**고 측정했다. Harvard는 **코드 +30%·완료 작업 증가 없음·리뷰 49% 지연**을 718개사에서 쟀다. Claude Code 팀은 **별도 채점자를 붙이면 성공률 +10점**이라 하고, GitHub은 **Copilot Evals**를 프리뷰로 열었다. 그리고 우리 저장소에서는 11-17 하루에 **PR #64~#82(19개 커밋)** 가 올라왔고 그중 **#82의 제목 자체가 "diff 계획이 '덮었다'고 말하는 두 자리를 고친다"** — 정확히 같은 실패 모드다.
- **판단**: 우리 리뷰 부채(**hwajjdev 리뷰 이슈 49건: spring-ai-ops 34 + arch-tech 15**)는 게으름이 아니라 **구조적 결과**다. 처리량을 늘리는 방향(에이전트 수)보다 **검증을 계측하는 방향**(Eval 41문항 + "완료 선언 ≠ 통과" 게이트)이 맞다. 구체적으로 **`handoffSummary`·`PERF_PLAN` 같은 산출물마다 "검증기 통과 여부"를 별도 필드로 남기고, 그 필드가 없으면 완료로 집계하지 않는 규칙**을 넣을 근거가 생겼다.
- **부수 사실**: 같은 계열의 도구가 이제 무료로 나온다. PostHog식 **결정 모델 게이트**는 Spring AI 2.1의 `DecisionModel`/TypeSafe Jev와 같은 것이고, "비싼 호출 전에 싸게 거른다"는 우리 브리핑 체인·리뷰 봇의 야간 폴링 비용에 그대로 적용된다.

**3) 내일(11-19)이 Spring 릴리스 데이고, 우리 CI에는 지금 빨간불이 하나 켜져 있다.**

- **사실**: Spring Boot 4.2.0 GA·Framework 7.1.0·Spring AI 2.1.0 GA가 **11-19** 트레인(현재 Maven Central 최신은 4.2.0-RC2, `lastUpdated=2026-11-05`로 GA 미출시 확인). 우리 세 레포 래퍼는 **Gradle 9.8.2**에 머물러 있고, **9.9.1이 고치는 Critical(9.3)이 "version catalog 값이 생성 accessor에서 코드로 실행"** 되는 경로다 — 셋 다 `gradle/libs.versions.toml`을 쓴다. 그리고 **spring-ai-ops CI에서 `perf-isolation.yml`이 22:39·23:10·23:16 세 번 연속 failure**(같은 시각 main 배포 워크플로는 success). 로그 조회는 되지 않았다 — 워크플로를 11-17 14:34에 등록했으니 **러너 매칭/트리거 조건 쪽을 먼저 볼 것**(추정).
- **할 일 순서**: ① **오늘 Gradle 9.9.1 래퍼 갱신**(보안 패치라 릴리스 트레인과 독립, 세 레포 동일 커밋) → ② **perf-isolation 실패 원인 확인**(워크플로 등록 직후 3연속 실패이므로 `runs-on`/`if`/`needs` 부터) → ③ 내일 GA 후 **브랜치 하나에서만** 4.2.0 기동 + Eval 41문항 회귀 → ④ Spring AI 2.1.0의 **Anthropic SDK 3.4.0이 `temperature`·`topP`·`topK`를 거부**하는 기본 버전을 쓰므로 **샘플링 파라미터 호출부 전수 확인**(조용한 실패 방지).

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-18 08:15 KST 기준)

**알림·요청**: `review-requested:@me` 0건 · 열린 PR **3건**(spring-ai-ops #3 테스트 샘플, arch-tech #33·#34 부하 테스트 표본 — 둘 다 "머지 금지") · 열린 이슈 **55건**(spring-ai-ops 39, arch-tech 16 — 그중 **49건이 hwajjdev 리뷰 이슈**, 34 + 15).

| 레포 | 공개 | 마지막 푸시(UTC) | 11-17 활동 |
| --- | --- | --- | --- |
| **spring-ai-ops** | private | **11-17 23:16** | **커밋 19건.** AI 사용량 `ai_*` 4종·Swagger·ops-admin 4탭(#64·#67·#68·#70·#71·#72) → **k6 부하 테스트 Step 1~3**(#74·#76·#79)와 리뷰 반영(#75·#77·#78·#80) → **#81 diff 폴백** → **#82 #81 리뷰 반영(P1 2·P2 8·P3 7)**. #63(의도 오분류·/analyze)은 11-16 23:19Z 머지 |
| **arch-tech** | private | **11-17 09:34** | 커밋 8건 — Qdrant 대시보드 메뉴(#26), Swagger iframe 차단 해소(#27), 카오스 시뮬레이터 **TPS 1,000**(#28), 포털 iframe·시계(#29), 토큰 자동발급 기본 OFF(#30), **4단 레이아웃 표준**(#31), **Grafana AI Observability 대시보드**(#32), `ci: Infisical 배포 연동`. 열린 PR #33·#34(perf 표본) |
| **memo** | private | 11-17 00:09 | 신규 0 |
| **poc-pickle** | private | 11-13 15:49 | 신규 없음 |
| **poc-py** | private | 11-12 23:58 | 신규 없음 |
| **youtu-lib** | public | 11-16 23:20 | 오늘 브리핑으로 갱신 |

**CI**: spring-ai-ops **deploy success**(11-17 23:16, `#82` 머지 직후) · **`perf-isolation.yml` failure 3연속**(22:39·23:10·23:16, 로그 조회 불가). arch-tech **전부 success**(최신 05:00Z `#32`).

**arch-tech 흐름(11-17)**
- `195885c` 포털 Swagger iframe 차단 해소 + `/ops-swagger` 프록시 · `7534062` 카오스 시뮬레이터 TPS 1,000 + 레이트리미터 런타임 세팅 · `7b26559` 포털·어드민 4단 레이아웃 표준 · **`f0a27cc` Grafana AI Observability**(`ai_*` 호출·토큰·예상 비용 + team-knowledge-mcp 스크레이프) · `bb84ed6` Infisical 배포 연동.

**spring-ai-ops 흐름(11-17, 핵심만)**
- **관측·UI**: `0f6934f` `ai_*` 메트릭 4종 · PR 리뷰 JSON 복구 · 지식 허브 30s · ops-admin 밑줄 탭·4단·다이어트 → `171c644` Springdoc Swagger + **AI 실행별 토큰·지연·예상 비용** → `e7604e8` AI 메뉴 실제 모델명 배지(헬스 동적 조회).
- **리뷰 부채 상환**: `7c8de22` "8096 P1 리뷰 4건"(도메인 의존 방향·해결 후 재발·리뷰 마커 절단·웹훅 이중 처리·**알림 조용한 실패**) — #10·#11·#18·#19.
- **부하 테스트(과제 11)**: `eb96c19` cc-worker 전체 탐색·WireMock 자동 모킹·k6 2단계 스펙 → `94729b9` PERF_PLAN 모드(Step 1) → `53faa7e` 격리 실행 환경(Step 2) → `3cc5222` **러너 워크플로 자동 격리 기동(Step 3, dispatch→prepare→SUT→k6→down -v)** → `506d9dd` **격리 불가 시 상시 서버 대신 SKIPPED + diff 폴백**.
