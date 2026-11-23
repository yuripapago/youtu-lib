# 2026-11-24 (화) 데일리 테크 브리핑

> **수집** 2026-11-24 08:15 KST · 최근 24시간(2026-11-23 08:15 ~ 11-24 08:15 KST = 11-22 23:15 ~ 11-23 23:15 UTC) 중심
> **소스** Hacker News(Algolia 날짜 검색·아이템 API로 댓글 직접 확인), GeekNews RSS(Atom), spring.io RSS, GitHub Releases/Milestone/PR API, nvidianews·investor.nvidia.com, cnbc.com, bbc.co.uk, ladybird.org, z.ai, openai.com, blog.kagi.com, stalw.art, docker.com, blog.jetbrains.com/kotlin, greptile.com, blogs.gentoo.org, micjm.com, github.com/anthropics/formal-math, YouTube 채널 페이지 파싱(@codedeck·@OpenAI·@GoogleDeepMind·@anthropic-ai·@GoogleDevelopers), GitHub @yuripapago API
> **오늘의 한 줄** — *"Nvidia 이사회는 만장일치로 젠슨 황을 되돌렸고, 그 근거는 '미국 정부 고위층의 입력'이었다. 같은 날 상무부는 그 스캔들의 회사(Megaspeed)를 블랙리스트에 올리겠다고 했다. AI의 위험과 통제는 코드가 아니라 정치·자본의 층에서 결정된다 — 그 와중에 Claude는 P vs NP의 이웃 정리(VP ≠ VNP)를 Lean으로 닫아 뒀고, OpenAI는 커널 LPE 948개와 eBPF 방패를 나란히 내놨다."*
> ※ GeekNews **topic 웹 페이지는 403**(RSS 요약만 수집), Reddit·Ars Technica 링크 일부 403. YouTube는 채널 페이지의 `lockupViewModel`을 직접 파싱해 업로드 시점("3h ago" 등)을 확인했다.

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **"젠슨 황 복귀, 주가 +31% — 이사회가 아니라 정부가 결정했다"** (11-23 14:00Z · HN 1,870점·2,951댓글, 전일 대비 최대 화제)

11-12 사임(혼외관계 + 중국 스파이 의혹) 이후 11일 만의 복귀다. 이사회 성명의 문장 순서가 그대로 이 사건의 성격이다: **"만장일치 → 미국 정부 고위층의 입력 수령 → 국가안보 위험 없음 판단"**.

| 항목 | 사실 |
| --- | --- |
| 사임 | 11-12 이사회 발표(CNBC는 13일로 표기). CFO **Colette Kress**가 임시 CEO, **Harvey C. Jones**가 사상 첫 이사회 의장 |
| 복귀 | **11-23 즉시 효력**. 이사회 만장일치. 정부 고위 관계자가 "국가안보법 위반 없음"을 구두 전달한 것이 이사회가 황에게 다시 연 계기(CNBC 소스) |
| 주가 | 복귀 당일 **+31% 종가**. 11-08 혼외관계 인정 이후 **약 33% 하락**분의 대부분을 회복(11-06 종가 $186.64) |
| 트럼프 | CNBC에 "이사회와 직·간접으로 이야기했다", "황은 잘못한 게 없다", "내가 딜을 만들었다"고 직접 발언. Truth Social에 "올바르고 적절한 결정" |
| 로비 | Musk("실수가 바로잡혔다")·Gavin Baker(Atreides)·Brad Gerstner(Altimeter) 등이 복귀를 공개 압박 |
| 남은 조사 | 독립이사 위원회 조사(Wachtell, Lipton)는 **복귀와 별개로 계속**. 신설 **국가안보·수출통제 감독위원회** + **최고컴플라이언스책임자(CCO) 신설** 착수 |

**같은 날 반대편에서 벌어진 일 — 이게 진짜 뉴스다**

| 주체 | 조치 |
| --- | --- |
| **상무부** | 싱가포르 **Megaspeed Ltd**와 자회사 **Speedmatrix**를 미국 거래 블랙리스트(Entity List)에 **추가 예정**이라고 발표. 약 2년간 수출통제 위반(중국행 칩 우회) 조사. 최대 고객이 **알리바바**. 발효 시점은 미명시 (BBC 11-23) |
| **의회** | 황은 **12-01** 하원 외교위·중국특위·정보위 3개 위원회 비공개 진술, 법무총괄 **Tim Teter**는 12-02. 상원 은행위 **Tim Scott**은 **12-03** "Protecting America's AI Advantage: Overview of U.S. Export Controls" 공개 청문회에 황을 단독 증인으로 소집 |
| **민주당** | Warren: "황을 CEO로 되돌린 무모한 결정이 의회 답변 의무를 없애지는 않는다". Andy Kim: "무엇을 언제 알았고, 첨단기술이 적성국 손에 들어가지 않게 충분한 조치를 했는지" |
| **시장** | WSJ: Polymarket에서 복귀 직전 **"의심스러운 두 클러스터"가 $340k 이상** 수익 |

- **읽는 법**: ① **미국 대형 기술기업 CEO의 거취가 정부 '입력'으로 결정되는 구조**가 문서로 남았다(이사회 성명에 그대로 적혀 있다). ② 반대로 **수출통제 집행은 오히려 강해졌다** — 인물 복귀와 회사 제재는 별개 트랙이다. ③ 12-01~03 사흘간 황·Nvidia가 의회에서 답하는 내용이 **AI 칩 수출 규제의 다음 분기**를 정한다.
- <https://nvidianews.nvidia.com/news/nvidia-board-reinstates-jensen-huang-as-president-ceo-and-board-director> · <https://www.cnbc.com/2026/11/23/nvidia-jensen-huang-ceo.html> · <https://www.bbc.co.uk/news/articles/cv98xpg5dg5zo> · <https://abcnews.com/Business/timeline-stunning-fall-powerful-nvidia-ceo-jensen-huang/story?id=138041659> · HN <https://news.ycombinator.com/item?id=50611576> · GeekNews <https://news.hada.io/topic?id=37721>

### 2. **GLM-5.4 — "Loop Transformer"로 파라미터를 안 늘리고 깊이를 늘렸다: 750B가 2T~3T급을 목표** (11-23 10:06Z · HN 1,011점·331댓글)

Z.ai의 첫 플래그십 Loop 아키텍처 모델. **GLM-5 기본 모델의 28개 층을 2회 반복 실행**해, 파라미터를 그대로 두고 계산 깊이를 늘렸다.

| 항목 | 내용 |
| --- | --- |
| 아이디어 | 표준 Transformer는 층이 순전파당 1회. Loop Transformer는 **가중치 공유 층 그룹을 여러 번** 돌려 이전 패스의 은닉 상태를 정제. 2018 Universal Transformer 계열이지만, **밑바닥 학습은 진다**(같은 학습 연산 예산에서) → **이미 사전학습된 GLM-5를 continued training으로 Loop화** |
| 게이트 | **Gated Progressive Updates** — 반복마다 토큰별 독립 게이트로 잔차 스트림에 비율적으로 쓴다. 학습 후 1패스는 작은 게이트로 전역 문맥만, 2패스는 큰 게이트로 전체 업데이트. **어려운 토큰은 1패스에서 더 공격적으로** |
| KV 캐시 | **Hybrid KV Cache** — 2패스트는 1패스가 고른 sparse KV를 재사용하고, 자기 슬라이딩 윈도 KV만 추가 보유. **1M 문맥에서도 추가 비용이 '슬라이딩 윈도 1개'** |
| 어텐션 | **Sparse index 재사용** — 2패스는 1패스 인덱서의 인덱스를 그대로 써 인덱싱 오버헤드 0(재계산과 거의 동일 성능) |
| 검증 | 1B→4B→30B→750B로 단계 검증. **30B 동일 파라미터 통제 실험**: 총 학습 연산 **+10%**로 수학·과학 추론·장문맥에서 뚜렷한 이득 |
| 부수 효과 | **출력 토큰 10~14% 감소**(GPQA-Diamond·MMLU-Pro 등) — 추론 일부가 명시적 토큰에서 **암묵적 루프 깊이로 이동** |
| 서빙 | 비피크 **200 tok/s**, 피크 **70~80 tok/s**. 커널 퓨전으로 루프가 증폭하는 중간 메모리 I/O 제거 |
| 한계(자백) | **"marathon thinking"** — 과도하게 긴 사고 루프에 갇히는 경향. 다만 그 자기수정이 파라미터 수를 넘는 정확도를 만든다고 주장 |
| 배포 | GLM Coding Plan 전원 사용 가능(ZCode·chat.z.ai). **API는 "Coming Soon"**, **가중치는 안전성 강화 후 오픈소스 예정** |

- **HN 반응이 정확히 절반을 찌른다**: 최상위 댓글이 *"벤치마크가 없으면 좋다는 뜻은 아니지 않나"* — 실제로 글에는 표가 없다. 반론도 *"사이버 안전성 너프 전에 점수를 안 내려고 숨긴 것"*·*"두 번의 뉴스 사이클을 노린 것"*으로 갈렸다. **어느 쪽도 근거는 없다**.
- **실무 판단**: 이 글에서 지금 쓸 수 있는 건 성능 숫자가 아니라 **구조적 주장 3개**다 — ① 파라미터를 늘리지 않고 추론 연산을 늘리는 축이 실제로 열렸다 ② 그러려면 **KV 캐시·sparse 인덱스·커널까지 모델과 함께 설계**해야 한다 ③ **출력 토큰이 줄어든다**는 건 청구서 관점에서 곧바로 이득이다(우리 에이전트 루프의 비용 구조와 같은 방향).
- <https://z.ai/blog/glm-5.4> · HN <https://news.ycombinator.com/item?id=50609685> · GeekNews <https://news.hada.io/topic?id=37712>

### 3. **Ladybird, 알파 공개 — "자체 엔진, 롤링 업데이트, 보안은 아직 약속하지 않는다"** (11-23 16:13Z · HN 1,237점·305댓글)

Andreas Kling이 macOS·Linux용 알파를 공개했다. 엔진은 **포크가 아니라 처음부터 자체 개발**했고, 브라우저 껍데기는 "소박하지만 쓸 만하다".

| 항목 | 내용 |
| --- | --- |
| 배포 | macOS·Linux 알파, **롤링 채널 + 자동 업데이트**(지금까지는 소스 빌드만 가능했다) |
| 테스트 | 깨진 사이트는 **브라우저 안에서 신고**. 크래시 리포트는 읽고 보낼지 사용자가 결정(프라이버시 정책 별도) |
| 자동화 | **WebDriver** + **Firefox DevTools 프로토콜** 지원 → QA 에이전트·회귀 테스트에 바로 붙는다 |
| 보안 | *"오늘 보안에 대해 어떤 약속도 하지 않는다. 해킹해 주세요. 뚫으면 방법을 알려주세요"* — `SECURITY.md` 공개 |
| 진척(GeekNews 요약) | 10월 **Web Platform Tests 통과 하위 테스트 수에서 Safari Technology Preview 추월**, JS JIT 도입·렌더링 스레드 분리 |

- **HN 논쟁의 핵심은 기술이 아니라 정체성**이다: *"sqlite·Skia·ANGLE로 갈아탄 게 발전의 이유인데, 그건 chromium.googlesource.com 아래 부품이다"*, *"SerenityOS에서 빼온 순수 해커 정신은 어디로 갔나"*, 반대로 *"LLM 주도 개발을 전면 수용해 속도를 낸 걸 인정해야 한다"*. **어느 프로젝트든 '순수성 vs 생산성' 비용은 결국 같은 자리에서 청구된다**.
- **우리에게 실질적인 한 줄**: WebDriver·Firefox DevTools 프로토콜 지원은 **브라우저 자동화 에이전트의 테스트 타깃이 하나 늘었다**는 뜻이다(크로미움 계열 편중 완화).
- <https://ladybird.org/posts/alpha/> · <https://ladybird.org/download/> · HN <https://news.ycombinator.com/item?id=50614249> · GeekNews <https://news.hada.io/topic?id=37724>

### 4. **Anthropic, "VP ≠ VNP over every field"를 Lean으로 닫았다 — 사람 수학자는 아직 아무도 안 봤다** (11-23 22:09Z · HN 100여 점, 창 막바지 급상승)

`anthropics/formal-math` 레포에 **Claude 내부 연구 모델이 발견한 정리**가 논문 PDF + Lean 4 형식화로 함께 올라왔다. 어제 브리핑의 리만 가설 Lean(1,700만 줄) 사가와 같은 계열의 **세 번째 공개물**(9월 FLT, 11월 리만, 11월 VP≠VNP)이다.

| 항목 | 내용 |
| --- | --- |
| 정리 | **모든 체**에서 해밀턴 사이클 다항식의 회로, 그리고 characteristic 2 밖의 permanent 회로는 충분히 큰 m에 대해 **m^(c(log m)^(1/3))** 개 이상의 게이트가 필요. 근사 회로도 동일 |
| 결론 | **VP ⊊ VNP**, 그리고 VNP는 VP의 폐포에 포함되지 않는다. **P vs NP는 해결하지 않는다**(README에 명시) |
| 저자 | 논문·Lean **모두 AI 모델이 작성**, **"어떤 인간 수학자도 검증하지 않았다"**고 스스로 적었다. "연구 산출물, 유지보수 안 함, 기여 받지 않음" |
| 형식화 | `Statement.lean`은 Mathlib만 import. `lake build` → `PrintAxioms`로 **propext·Classical.choice·Quot.sound 외 공리 미사용** 확인. **CI는 Comparator로 `Challenge.lean`(증명 없는 진술)과 비교 + 독립 커널 `nanoda`로 재검증** |
| 위치 | HN 반응: *"이건 Valiant's hypothesis인데, 이제 밀레니엄 문제가 아니면 '부분 결과'라고 부르는 세상"* · *"P=NP로 가는 길이 아니라 가지를 쳐낸 것"* |

- **왜 중요한가(기술적으로)**: 이 저장소의 구조가 **AI 수학 산출물의 검증 규약**을 사실상 표준화하고 있다 — ① 진술 파일과 증명 파일 분리 ② **진술이 주장과 같은지**는 기계가 못 잡으니 Comparator로 따로 비교 ③ 독립 커널로 이중 검증 ④ 공리 목록 출력. 어제의 "Lean 검증 사가"에서 남은 질문(*"증명은 맞는데 진술이 맞나"*)에 **레포 차원의 답**을 내놓은 셈이다.
- **냉정한 한계**: 사람 검증 0. 이건 수학계가 받아들이기 전까지는 **"기계가 기계로 검증한 주장"**이다. 우리가 어제 정리한 문장 그대로 — *증명의 신뢰도는 커널이 올려주지만, 의미의 신뢰도는 사람이 올린다.*
- <https://github.com/anthropics/formal-math/tree/main/vp-vnp> · <https://github.com/anthropics/formal-math/blob/main/vp-vnp/paper.pdf> · HN <https://news.ycombinator.com/item?id=50620102>

---

## 🤖 AI / LLM & Agentic 동향

**1) 르쿤 vs 아모데이 — "위험 과장은 오픈 모델을 불법화하려는 것" (HN 922점·530댓글).** 르쿤이 X에서 아모데이를 정면 비판: GPT-2 시절부터 위험을 과장해 왔고, OpenAI를 떠난 이유도 통제권 상실이며, **오스만 제국이 아랍어 인쇄를 금지한 것**에 빗댔다(지식 확산 차단 = 현대판 우민화). 배경이 중요하다 — **Anthropic은 11-19 나스닥에 상장**(공모가 $83, 첫날 종가 $97.17, 완전희석 시총 **약 $1.8조**, IPO+사모 합계 **약 $1,195억** 조달, 9개월 매출 **$332.4억**/+1,200%)했다. HN 최상위 댓글은 *"정말 위험하다고 믿으면 왜 만드나, 아니면 규제 해자(moat) 때문"* 과 *"그 논리면 오픈 웨이트가 유일한 견제"* 로 갈렸다. **상장 이후 '안전 담론'이 곧 주주가치 담론이 된 상태**를 양쪽 다 이용하고 있다는 게 냉정한 관찰이다.
- <https://news.ycombinator.com/item?id=50615308> · GeekNews <https://news.hada.io/topic?id=37729> · <https://www.anthropic.com/news/ipo-close> · <https://www.cnbc.com/2026/11/19/anthropic-ipo-ant-live-updates.html>

**2) Docker가 '컨테이너 회사'를 버렸다 — "에이전트는 워크로드가 아니라 행위자다" (HN 120점·199댓글).** 새 브랜드와 함께 **Docker Sandboxes**(에이전트를 독립 커널 **microVM**에서 실행) + **Docker AI Governance**(조직 단위 네트워크·파일 접근 정책, **MCP 도구 사용 통제**)를 전면에 세웠다. 글의 핵심 문장: *"컨테이너는 워크로드를 격리한다. 에이전트는 스스로 무엇을 실행하고 접근하고 바꿀지 결정한다. 자율성을 주려면 그들을 위한 격리가 필요하다."* Moby 로고도 새로 그렸다.
- <https://www.docker.com/blog/meet-the-new-docker-built-to-run-on-trust/> · <https://www.docker.com/products/docker-sandboxes/> · <https://www.docker.com/products/ai-governance/> · HN <https://news.ycombinator.com/item?id=50607194>

**3) Kagi Mail 공개 베타 — 개인 메일 서버가 곧 AI 인박스 (HN 311점·237댓글).** `@kagimail.com` 또는 자체 도메인, **60일 무료 후 $99/년**(Ultimate 포함), 50GB·무제한 별칭/도메인. 기반은 **Stalwart 서버 + IMAP·JMAP·CalDAV**(아래 백엔드 섹션의 그 Stalwart다). AI 기능은 **규칙별 권한을 사용자가 지정**("베타 사용자 피드백은 라벨링해, 판단은 네가 해")하고 **한 스위치로 전부 끌 수 있다**. 개발자용으로 **메일 읽기 권한의 MCP 서버**와 Sieve 규칙 필터 제공, 연락처·모바일 앱은 2027년.
- <https://blog.kagi.com/kagi-mail-public-beta> · HN <https://news.ycombinator.com/item?id=50615481> · GeekNews <https://news.hada.io/topic?id=37728>

**4) Kotlin Benchmark 2.0 — 에이전트 평가를 '공개 코드'에서 '비공개 코드'로 옮겼다 (JetBrains, 11-23 · 표는 아래 백엔드 섹션).** 태스크 100개 중 **70개 오픈소스(25개 레포: Android 30·백엔드 20·KMP 10·라이브러리 10) + 30개 JetBrains 비공개 서버사이드**. **네트워크 차단으로 reward hacking 방지**(예비 실행에서 에이전트가 실제로 공개된 수정 커밋을 조회했다), 태스크당 30분 제한, 구성별 3회 실행, **태스크당 평균 비용·토큰·스텝 수 공개**.
- <https://blog.jetbrains.com/kotlin/2026/11/kotlin-benchmark-2-0-how-ai-coding-agents-handle-harder-tasks-and-closed-source-code/> · <https://kotlinlang.org/benchmark/>

**5) 로컬 추론의 문턱이 또 내려갔다 — NariQ: Qwen3.8-27B를 10GB 파일로 (HN 103점).** BF16급 품질을 3.0bpw/2.0bpw로 압축. 단 HN 지적이 정확하다: **M3 Pro(18GB)에서 3.0bpw 디코드 ~3.8 tok/s(2.0bpw 4.9)** — CUDA 대비 크게 느리고, "무엇이 실제로 사라지는지"에 대한 답이 부족하다.
- <https://huggingface.co/nari-labs/Qwen3.8-27B-NariQ-GGUF> · HN <https://news.ycombinator.com/item?id=50611402>

**6) "17M 줄 Lean은 많은가?" — 리만 가설 증명의 길이를 실제로 재봤다 (HN 253점).** Mathlib 유명 정리 259개, Reservoir 전체 Lean 패키지와 비교: **88M**(AI 작성 진행 중 CFSG), **36M**(LMFDB 연동 LeanBridge, 98%는 스크립트 생성), **27M**(Anthropic의 페르마 마지막 정리 — 인간 FLT 프로젝트 약 6만 줄의 **약 450배**). 결론: *"그래, 17M은 많다"*. 같은 날 **리만 Lean 트리맵**(175,187개 모듈 탐색 시각화)도 올라왔다.
- <https://micjm.com/blog/yes-17m-lines-of-lean-is-a-lot/> · <https://pengowray.github.io/riemann-zeta-proof-visualizer/> · <https://github.com/anthropics/fermats-last-theorem>

**7) 그 밖에.** ① **"AI 거품은 터지는 중"**(mathbabe, HN 27점) — 상장·자본 사이클을 회의적으로 보는 글 ② **컴퓨터-사용 에이전트는 어떻게 화면을 봐야 하나**(Greptile, HN 36점 · 상세 표는 백엔드 섹션) ③ 오픈스택 **Stalwart가 Exchange 대체**를 선언(11-23, 아래 표)

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스 표 — **새 GA 0건, 세 패치 라인이 '릴리스 직전'에서 멈춰 있다**

| 프로젝트 | 최신 | 날짜(UTC) | 메모 |
| --- | --- | --- | --- |
| spring-boot | **4.2.0 / 4.1.3 / 4.0.10** | 11-19 22:00 | 창 안 신규 릴리스 없음 |
| spring-boot 다음 패치 | **4.2.1 · 4.1.4 · 4.0.11** | – | 마일스톤 **24/24 · 20/20 · 19/19, 전부 open 0**(어제와 동일). 릴리스 임박 상태로 하루 더 정체 |
| spring-ai | **2.1.0 / 2.0.3** | 11-20 15:46 | 창 안 **머지 PR 1건**: #7436 `GoogleGenAiChatModelIT`를 **평범한 `GOOGLE_API_KEY`로 동작**하도록(11-23 11:54Z) |
| spring-framework | **7.1.0 / 7.0.11** | 11-19 22:03 | 신규 없음 |
| spring-security | **7.2.0 / 7.1.3 / 7.0.9** | 11-20 | 신규 없음(11-20 릴리스가 마지막) |
| spring-cloud | 2025.1.5 (Oakwood) | 11-20 | 신규 없음 |
| kotlin | **2.5.0-RC** | 11-11 | 안정판 2.4.21. **블로그는 11-23 Kotlin Benchmark 2.0**(아래) |
| spring.io 블로그 | 11-20 4건이 마지막 | – | **11-21~24 신규 글 0건**(RSS lastBuild 11-23 19:48Z에도 item 4개) |

- **해석**: spring-boot는 **11-19 GA 이후 5일째 코드 프리즈**. 마일스톤이 세 라인 모두 0 open이므로 **4.2.1·4.1.4·4.0.11이 같은 날 나올 가능성**이 높다. 4.1.x에 머문 팀은 그날이 업그레이드 적기다(어제 브리핑의 회귀 `#52871`은 **4.2.x 전용** 이슈라 4.1.4와 무관).
- <https://github.com/spring-projects/spring-boot/milestones> · <https://github.com/spring-projects/spring-ai/pull/7436> · <https://spring.io/blog>

### **Stalwart 1.2.0 — "Exchange 드롭인 대체"를 선언했다. MAPI over HTTP·EWS·ActiveSync·Autodiscover를 전부 구현** (11-23 · HN 41점)

온프레미스 Exchange의 실제 붕괴 경로를 근거로 쓴 글이다: Exchange 2016/2019 **2025-10-14 지원 종료**, ESU도 **2026-10에 종료**, Subscription Edition은 마이너 버전 없음·CU1 지연, **새 Outlook은 온프레미스 계정 미지원**, **Exchange Online EWS는 2026-10부터 폐지 시작 → 2027-04 완전 종료**.

| 계층 | 구현 |
| --- | --- |
| 프로토콜 | **MAPI over HTTP(EMSMDB·NSPI)**, **EWS 2010 SP2~2016 스키마**, **EAS 14.1·16.1**, **Autodiscover(POX·SOAP·JSON)** — 전부 JMAP·WebUI와 **같은 HTTPS 포트** |
| 클라이언트 | 클래식 Outlook(2016+·M365), Outlook for Mac, Apple Mail/Calendar/Contacts, iOS·Android 메일(Gmail·Samsung Email·Nine), Thunderbird, Evolution(ews), eM Client, exchangelib |
| 데이터 | Exchange는 별도 사일로가 아니라 **같은 계정의 또 다른 문** — Outlook에서 옮긴 메일이 IMAP/JMAP과 같은 메일함, Outlook 규칙은 **서버 Sieve 규칙**으로 저장·실행, 위임/폴더 권한은 Stalwart 공유로 |
| 라이선스 | **Exchange 지원은 오픈소스 Community(AGPL)** |
| 빠진 것(명시) | 공용 폴더, 아카이브 메일박스, MDM, RPC over HTTP(Outlook 2010-), **NTLM/Kerberos 로그인**, 그리고 **새 Outlook은 어떤 온프레미스 서버도 안 됨** |

- **아키텍처 관점의 값**: 한 서버가 **MAPI·EWS·EAS·JMAP·IMAP·CalDAV·CardDAV를 동시에** 서빙하고, 규칙 엔진은 Sieve 하나로 통일했다. "프로토콜별 사일로"를 피한 대표 사례 — 우리가 어드민·포털·MCP를 붙일 때 참고할 배치다.
- HN 반응은 회의적이다: *"도메인이 너무 별로라 엔터프라이즈는 안 볼 듯"*, *"무료 Community 버전이 영원할까"*. 실사용자는 *"MS가 연간 라이선스로 바꿔서 온프레미스 Exchange 탈출 방법을 찾던 중"*.
- <https://stalw.art/blog/microsoft-exchange-replacement/> · <https://stalw.art/docs/email/exchange/> · <https://github.com/stalwartlabs/stalwart> · HN <https://news.ycombinator.com/item?id=50620349>

### **Kotlin Benchmark 2.0 결과 — "최고 점수 = 최고 효율"은 아니다** (JetBrains, 11-23)

| 구분 | 1위 | 비고 |
| --- | --- | --- |
| 전체(공개+비공개 100개) | **Claude Code + Opus 5.5 — 75.3%, $0.84/건** | 나머지 12개 구성 54.3~73.7%, **$0.02~$3.14/건** |
| 오픈소스 70개 | **Claude Code + Fable 5.1 — 86.2%** | Opus 5.5 84.8%(**$2.38**), Junie+Opus 5.5·GPT 6 Luna 79.5%(**$0.68·$0.33**) → **점수 비슷, 비용 3.5~7배 차** |
| 비공개 30개(Kotlin 서버사이드) | **Antigravity CLI + Gemini 3.8 Flash — 60%, $1.74** | **OpenCode + DeepSeek V4.1 Flash — 58.9%, $0.08** (20배 싸다) |

- **방법론이 더 중요하다**: ① **네트워크 차단**으로 정답 조회 차단(예비 실행에서 실제로 조회했다) ② **비공개 코드 30개**로 학습 데이터 오염을 분리 측정 ③ 3회 실행·30분 제한 ④ **비용·토큰·스텝 공개**. 앞으로 "에이전트가 우리 코드를 잘하나"를 물을 때 **이 4개를 그대로 베끼면 된다**.
- ⚠️ **1차 벤치마크와 점수 비교 금지** — 태스크 세트가 어려워졌다. "점수가 낮다 ≠ 모델이 나빠졌다".
- <https://blog.jetbrains.com/kotlin/2026/11/kotlin-benchmark-2-0-how-ai-coding-agents-handle-harder-tasks-and-closed-source-code/> · <https://kotlinlang.org/benchmark/methodology/>

### **컴퓨터-사용 에이전트: '접근성 트리 + ID 클릭'은 약한 모델만 구제한다** (Greptile, 11-19 · HN 36점)

OSWorld 동일 172개 태스크·1회 시도·50스텝·낮은 추론 강도로 3가지 설정 비교.

| 모델 | 스크린샷만 | + 접근성 트리(좌표 클릭) | + 요소 ID 클릭 |
| --- | --- | --- | --- |
| GPT-4.1 (2025-04) | 5.4% | 20.6% | **29.3%** |
| GPT-5.2 (2025-12) | 15.8% | 36.2% | **52.8%** |
| GPT-6.1 Sol (2026-09) | **83.8%** | 82.7% | 82.1% |
| GPT-4o (2024-05) | 10.5% | – | – |
| GPT-5.5 (2026-04) | 67.7% | – | – |

- **결론**: 좌표 추측은 **해상도·창 크기에 따라 달라지고, 비전 토큰화에서 픽셀 하나를 맞히는 게 근본적으로 어렵다**(ScreenSpot-Pro 최고 18.9%). 트리·ID는 **약한 모델을 3배 이상 끌어올리지만 최강 모델에는 무의미**(통계적으로 유의차 없음). 즉 **"트리를 줄 것인가"는 모델 등급에 따라 달라지는 선택**이다.
- <https://www.greptile.com/blog/trex-computer-use> · HN <https://news.ycombinator.com/item?id=50615520>

### 그 밖에
- **Nango, "API 1,000개 인증 운영에서 배운 것"**(HN 52점) — `expires_in`이 **무엇에 상대적인지**(`iat`)를 반드시 붙잡아야 한다는 실무 함정, authN/authZ 용어 구분 지적. <https://nango.dev/blog/lessons-from-operating-api-auth-for-900-apis/>
- **Kubernetes v1.38 Sneak Peek**(11-23 21:24Z) — 다음 릴리스 강조 기능 사전 공개. <https://kubernetes.io/blog/2026/11/23/kubernetes-v1-38-sneak-peek/>

---

## 🔐 보안·인프라·정책

| 항목 | 내용 | 링크 |
| --- | --- | --- |
| **OpenAI+Cisco 'Shirudo' — 패치 전 리눅스를 eBPF로 가린다** (11-23 · HN 10점, 아직 저평가) | **eBPF 실드**로 취약 코드 경로를 **재시작 없이** 차단. ① 실행 중 보호 ② 에이전트가 취약점 조사→실드 작성→익스플로잇·실트래픽으로 검증(**배포는 사람이 승인**) ③ 서명 검증 후 로드(eBPF verifier + 커널 보안 통제) ④ **TPM으로 로드/해제 기록**. 데모: **Codex가 생성한 실드가 실행 중인 Elasticsearch의 Java4Shell 익스플로잇을 차단**(재시작 없음). **BSD 2-Clause**, BPF 프로그램은 BSD/GPL 듀얼 | <https://openai.com/index/introducing-shirudo/> · <https://github.com/shirudo-dev/shirudo> · <https://isovalent.com/blog/post/cisco-openai-shirudo/> |
| **"SSH 개인키를 디스크에 두지 마라"** (HN 144점 · GeekNews 11-24 07:32) | 비밀번호 관리자에 백업해도 **디스크의 개인키 파일은 악성코드 디스크 스캔에 털린다**. **Bitwarden SSH 에이전트** + `IdentityAgent`·`SSH_AUTH_SOCK`로 전환 → 디스크에서 키 삭제. **커밋 서명키는 서명 전용으로 분리**(커밋 생성 ≠ 푸시 키) | <https://jola.dev/posts/you-cant-afford-ssh-keys-disk> · GeekNews <https://news.hada.io/topic?id=37734> |
| **Gentoo 메인테이너: "보안 지옥의 다음 단계"** (HN 229점·137댓글, 원문 10-27) | *"모두와 그들의 챗봇이 보안 연구자가 됐다"* — 버그바운티는 slop 때문에 사실상 폐지됐는데 **신고는 안 멈춘다**. 무효 신고를 걸러도 **수정이 타당한지·회귀가 없는지** 판정해야 하고, CVE·스캐너는 실제 영향 차이를 반영하지 못해 **영향 없는 패키지에도 불필요한 보안 업데이트**를 하게 만든다. 결론은 *"우리 모두의 책임"* | <https://blogs.gentoo.org/mgorny/2026/10/27/the-next-circle-of-security-hell/> · HN <https://news.ycombinator.com/item?id=50610334> · GeekNews <https://news.hada.io/topic?id=37719> |
| **미 상무부, Megaspeed·Speedmatrix 블랙리스트 예고** (11-23 22:15 GMT) | 2년 조사. 중국행 칩 우회 의혹. 알리바바가 최대 고객. **발효 시점 미명시**. 중국 외교부는 *"스파이 주장은 근거 없는 비방"*, *"기술 봉쇄의 구실로 쓰지 말라"* | <https://www.bbc.co.uk/news/articles/cv98xpg5dg5zo> |
| **Arm, 커뮤니티 레퍼런스 사이트 강제 종료** (HN 733점) | **Jon's Arm Reference**(AArch64 명령어·시스템 레지스터 문서)가 Arm 변호사의 법적 조치 통보로 **2026-12-04 종료**. 문서 접근성의 민간 의존을 보여주는 사례 | <https://arm.jonpalmisc.com/> · HN <https://news.ycombinator.com/item?id=50617499> |
| **Apple, 9년 된 오픈소스 앱을 EU App Store에서 삭제** (HN 445점) | FSNotes(macOS/iOS 메모). 10월 판매대금 미지급, 지원팀 2회 문의에 **11-05 이후 답변 0**, DSA 준수 정보·우크라이나 사업자 등록 서류 제출 후에도 **제한 사유 미통보** | <https://github.com/glushchenko/fsnotes/discussions/2056> · GeekNews <https://news.hada.io/topic?id=37732> |
| **GTA 6 '개인 행동 프로필' 의혹 (미검증)** | 자칭 해고된 Rockstar North 선임 엔진 엔지니어가 X에 주장: Take-Two 내부 시스템 **Lens**가 입력을 **밀리초 단위로 기록**, 콘솔 계정·플랫폼 ID·**IP 주소와 연결**해 충동성·위험감수·압박 시 의사결정 속도까지 프로파일링. **진위 미확인 — 주장으로만 읽어야 한다** | GeekNews <https://news.hada.io/topic?id=37718> |

> ※ 취약점·CVE 속보는 별도 루틴(08:30·19:30 보안 취약점 브리핑)이 CISA·KISA·GHSA·BleepingComputer·The Hacker News를 감시하므로 여기서는 **아키텍처·정책성 이슈만** 다룬다.

---

## 📺 추천 테크 영상·읽을거리

### 채널 업로드 — **OpenAI만 창 안 신규 2건**

| 채널 | 최신 업로드(채널 페이지 파싱) | 비고 |
| --- | --- | --- |
| **OpenAI** | **"Investigating Linux Vulnerabilities with GPT-6 Cyber" — 3h ago** / "How Franklin Templeton uses AI for investment research" — 7h ago | 아래 상세 |
| **Code Deck** | "Flexbox Full Course 2026" — **1mo ago** | 신규 없음 |
| **Google DeepMind** | "Gemini and the path to AGI with Koray Kavukcuoglu" — **4d ago** | 신규 없음(11-19, 이미 지난 브리핑) |
| **Anthropic** | 채널 트레일러·"Introducing Claude Fable 5.1" 등, **창 안 날짜 확인 불가** | 날짜 표기 미노출 → 단정하지 않음 |
| **Google for Developers** | Google Antigravity + Gemini 4 데모 3편(날짜 미노출), "EmbeddingGemma 2" — 6d ago | 신규 확정 없음 |

**▶ 오늘의 영상: "Investigating Linux Vulnerabilities with GPT-6 Cyber" (OpenAI, 11-24 05:15경 KST)** — 11-04 블로그의 **영상판**이고, 숫자가 그대로 나온다.

| 항목 | 수치 |
| --- | --- |
| 파이프라인 | Scout(감사 대상 선정) → Analyst(후보 발견) → Triage(VM에서 동적 재현) → ExploitDev → Patch(+인간 리뷰) |
| 규모 | 감사 대상 **5,034개(약 280만 줄 = 커널의 약 7%)** → 후보 **2,308건** → 동적 재현 **1,724건(74.7%)** → **정보유출 PoC 291개 + LPE 익스플로잇 948개** |
| 비용 | **API 크레딧 $1,213,430** = **LPE 1건당 $1,279**. 성공 익스플로잇 평균 $359, **실패 시도가 오히려 $3,922**(포기 조건 설계 실패) |
| 함정(자백) | 초기 실행에서 "성공 200건" 중 **20건이 배정된 취약점이 아니라 다른 0-day로 root를 얻었다** → *"결과뿐 아니라 결과에 이른 경로를 검증하라"* |
| 후속 | 수백 건 보고·패치를 커널 커뮤니티에 전송, 다수 머지. **패치 수 = 취약점 수**로 세는 방식을 택함(변형 분석으로 하나의 패치가 여러 변형을 덮음) |

- <https://www.youtube.com/watch?v=MpsO8Ds8uyo> · <https://openai.com/index/a-thousand-fewer-paths-to-root/> · <https://openai.com/index/introducing-gpt-6-cyber/>

### 오늘 꼭 읽을 4개
- **GLM-5.4 기술 블로그** — Loop 아키텍처를 **왜 사전학습부터 하지 않았는지**, 게이트·KV 캐시를 어떻게 설계했는지. 이번 주 가장 밀도 높은 모델 아키텍처 글 <https://z.ai/blog/glm-5.4>
- **anthropics/formal-math VP≠VNP README** — AI 수학 산출물의 **검증 규약**(진술/증명 분리, Comparator, nanoda, 공리 출력) <https://github.com/anthropics/formal-math/tree/main/vp-vnp>
- **Kotlin Benchmark 2.0 방법론** — 에이전트 평가에서 **네트워크 차단·비공개 코드·비용 공개**를 어떻게 설계했는지 <https://kotlinlang.org/benchmark/methodology/>
- **Gentoo "보안 지옥의 다음 단계"** — AI slop 신고가 오픈소스 유지보수를 어떻게 망가뜨리는지, 메인테이너 1인칭 <https://blogs.gentoo.org/mgorny/2026/10/27/the-next-circle-of-security-hell/>

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 5가지

**1) 어젯밤 spring-ai-ops는 '에이전트 하네스'를 9개 PR로 다시 깎았다 — 오늘 뉴스와 같은 부품이다.**

- **사실(창 안, 11-22 23:53Z~11-23 21:11Z)**: **PR #96~#104, 9건 연속 머지(+13,465/−343)** — ① **#96 명시적 기억·규칙 저장**("기억해·메모해·기록해놔") ② **#97 경량 서브에이전트·비동기 잡**(무거운 조사는 뒤에서, 결론 5줄) ③ **#98 비서형 동적 루틴(승인 카드)** + 주기적 컨텍스트 정리 ④ **#99 단순 조회는 계획을 묻지 않기** ⑤ **#100 턴 트레이스(`agent_turn_traces`)·🔍 탭** ⑥ **#101 존댓말·근거 id 분리** ⑦ **#102 searchTeamDocs 검색 전용·30초 타임아웃 제거** ⑧ **#103 턴 전체를 단일 툴-콜링 루프로**(오타·잡담 1회 호출, 쓰기는 카드에서 정지) ⑨ **#104 일반 대화·계산은 근거 없이 바로 답변**(경계 규칙·few-shot).
- **오늘 뉴스와의 접점**: ① **#103의 "단일 툴-콜링 루프 + 카드에서 멈춤"** 은 GLM-5.4가 말한 *"긴 사고 루프"* 와 Kotlin Benchmark의 *"스텝 수·비용"* 지표에 그대로 대응한다 — **루프 상한과 스텝당 비용 로깅**이 다음 카드다. ② **#100 턴 트레이스**는 Shirudo의 교훈(*"결과가 아니라 결과에 이른 경로를 검증"*)과 같은 계열이다. ③ **#97 비동기 잡**은 어제 본 '에이전트는 행위자' 논지(Docker)와 맞물려 **격리 경계**(누가 어떤 툴을 실행할 수 있나)를 문서로 정할 시점이다.
- **오늘 할 일 1개**: `agent_turn_traces`에 **토큰·비용·스텝 수** 컬럼을 붙여 턴별 단가를 남기기(벤치마크가 요구하는 최소 지표).

**2) 업그레이드 큐는 그대로다 — 4.1.3·2.0.3은 아직 안 올라갔고, 4.2.1은 오늘 나올 수 있다.**

| 대상 | 현재 | 갈 곳 | 근거 |
| --- | --- | --- | --- |
| spring-boot | **4.1.2** | **4.1.3** (→ 4.1.4 임박) | 11-20 패치, 4.1.4 마일스톤 **20/20, open 0** |
| spring-ai | **2.0.0** | **2.0.3** | CVE 13건 수정 포함, 창 안 추가 변화 없음 |
| (선택) Boot 4.2 | – | **4.2.1** | 4.2.0 회귀 `#52871`(리소스 체인 + 커스터마이저 빈 → 기동 실패) 수정 후 |
| Kotlin | 2.3.21 | 2.4.21 안정 / 2.5.0-RC | 변화 없음 |

- **순서는 그대로 유효**: `4.1.2→4.1.3` + `2.0.0→2.0.3`을 **한 커밋**으로. 4.2로 갈 때 `grep -r "ResourceHandlerRegistrationCustomizer"` 먼저.

**3) Kotlin Benchmark 2.0은 우리 챗봇 평가 하네스의 청사진이다 — '점수'보다 '비용·스텝·오염 분리'를 베낄 것.**

- **사실**: 오픈소스 태스크는 **네트워크 차단**(정답 조회 차단), 비공개 30개로 **학습 데이터 오염 분리**, 구성별 **3회 실행**, **태스크당 비용·토큰·스텝 공개**. 결과는 *"1등이 20배 비싼 경우가 흔하다"*.
- **우리 쪽**: 지금 spring-ai-ops 리뷰·QA는 "잘 되나"만 본다. **동일 프롬프트 3회·비용 기록·툴 호출 수 상한**을 붙이면 어제의 9개 PR 같은 변경도 **회귀를 비용으로** 잡아낼 수 있다. 특히 #103 이후 **루프 상한**이 실제로 몇 번에서 멈추는지부터 재면 된다.

**4) 보안: '에이전트가 만드는 자격증명 경로'를 오늘 닫아라 — 어제 Together, 오늘 SSH·Gentoo가 같은 말을 한다.**

- **사실**: 어제 Together AI 유출 경로는 **AI 코딩 에이전트가 공개 레포에 커밋한 자격증명**. 오늘 SSH 글은 *"디스크의 개인키는 악성코드 스캔에 털린다"*(에이전트 에이전트), Gentoo 글은 *"수정이 타당한지 검증할 사람이 없다"*.
- **할 일 3개(오늘 안에 가능)**: ① 두 레포 **push protection(secret scanning)** 켜져 있는지 확인 ② `pre-commit`에 **gitleaks** ③ **SSH 개인키를 디스크에서 제거**하고 Bitwarden SSH 에이전트 + `IdentityAgent`/`SSH_AUTH_SOCK` 전환, **커밋 서명키 분리**. ④ `CLAUDE.md`/`AGENTS.md`에 "자격증명 커밋 금지, 발견 시 커밋 전 보고"를 **파일 규칙으로** 명시.
- **우리 환경 특이점**: 맥 스튜디오 self-hosted 러너는 **DooD 컨테이너 셸**이라 호스트 키가 안 보인다 — 즉 **위험이 줄어 있는 대신 디버깅 때 키를 컨테이너에 넣고 싶은 유혹**이 생긴다. 그 유혹을 규칙으로 막아야 한다.

**5) 모델 선택지가 다시 넓어졌다 — '민감 문서는 로컬'이 현실적 선택이 됐다(GLMP-5.4 오픈 웨이트 예고 + NariQ).**

- **사실**: GLM-5.4는 **가중치 오픈소스 예정**(안전성 강화 후), NariQ는 27B를 **10GB**로. 우리 약관 RAG(poc-pickle)는 **고객 약관 PDF**를 다루므로 외부 API 전송이 곧 리스크다.
- **판단**: 지금 갈아타지는 말고, ① **임베딩·검색은 이미 로컬(Qdrant)** 이니 ② **생성 단계만 로컬 후보(27B급 양자화)로 A/B**할 수 있게 **Spring AI ChatModel 추상화 뒤에 provider를 하나 더** 꽂아 두는 게 맞다. GLM 가중치가 실제로 풀리는 시점이 트리거다.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-24 08:15 KST 기준)

**알림·요청**: `review-requested:@me` **0건** · 열린 PR **5건**(arch-tech 4 · spring-ai-ops 1) · 열린 이슈 **55건**(arch-tech **16** · spring-ai-ops **39** — 어제와 동일, 변동 없음).

| 레포 | 공개 | 마지막 푸시(KST) | 창 안 활동(11-23 08:15 ~ 11-24 08:15) |
| --- | --- | --- | --- |
| **spring-ai-ops** | private | **11-24 06:11** | **PR 9건 연속 머지**(#96→#104, **+13,465/−343**). Deploy **success 9회**(11-24 06:11 마지막) + 11-24 02:57 **수동 dispatch 1회 success** |
| **arch-tech** | private | **11-23 15:16** | **PR 2건**: #43 `arch-token-client` 토큰 조회·401 자가치유 시퀀스 문서(+208), #44 **파트너별 점검 모드 — 해당 tokenKey 발급만 503 차단**(+1,280/−6). Deploy **success ×2** |
| **memo** | private | **11-23 23:10** | **커밋 5건**: `legacy-security-audit` 스킬 추가 → 코드 스터디 `arch-token-client` Ch 2(2/4) → 게임 무기 전투·히트 피드백 스펙 → 로드맵 §7.7(자율 API·로그 시뮬레이션 QA 스킬)·§7.8(멀티모달 이미지·스크린샷 첨부) |
| **youtu-lib** | public | 11-23 08:19 | 11-23 브리핑(`cb70b9b`) — 오늘 이 문서로 갱신 |
| **poc-pickle** / **poc-py** | private | 11-14 / 11-13 | 신규 0 |

**CI**: spring-ai-ops **Deploy success 9/9**(머지마다) · arch-tech **Deploy success 2/2** · `perf-isolation.yml`은 **11-18 00:26Z 실패 이후 새 실행 없음(6일째)**.

**해석**: 어젯밤 작업의 성격이 오늘 뉴스와 정확히 겹친다 — 우리는 **"기억·비동기 잡·승인 카드·턴 트레이스·툴 루프"** 를 손으로 만들었고, 업계는 같은 날 **"에이전트는 행위자다(Docker)"**, **"루프가 깊이를 만든다(GLM-5.4)"**, **"결과가 아니라 경로를 검증하라(OpenAI)"** 는 말을 각자의 언어로 했다. 오늘의 두 줄: ① **4.1.3 + 2.0.3을 한 커밋으로** ② **턴 트레이스에 비용·스텝 수를 붙이고, 자격증명 경로(push protection·gitleaks·SSH 키)를 닫기**.
