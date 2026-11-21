# 2026-11-22 (일) 데일리 테크 브리핑

> **수집** 2026-11-22 08:15 KST · 최근 24시간(2026-11-21 08:15 ~ 11-22 08:15 KST = 11-20 23:15 ~ 11-21 23:15 UTC) 중심
> **소스** Hacker News(Algolia 날짜 검색·아이템 API로 댓글 직접 확인), GeekNews RSS, spring.io RSS·GitHub Releases/Issues API, leanprover-community 블로그·math.mit.edu, gnupg.org 메일링 아카이브, ruby-lang.org·GHSA·GitLab Docs, simple-bench.com `leaderboard-data.js`, openai.com, anthropic.com, thinkingmachines.ai, phala.com·novita.ai, bbc.com, pwn.ai, lwn.net·phoronix, YouTube 채널 RSS(OpenAI·Google DeepMind·Code Deck·AI Explained)
> **오늘의 한 줄** — *"오늘의 기준은 '무엇을 만들었나'가 아니라 '무엇이 그것을 검증했나'였다. 1,700만 줄 증명은 커널 3종이 통과시켰고, 6.2 모델은 리더보드가 깎아내렸고, 샌 프롬프트는 카나리 URL이 잡아냈다."*
> ※ GeekNews 웹(topic 페이지)은 여전히 **403**(RSS 본문만 수집, 댓글 미확인). Reddit(r/LocalLLaMA) **403**, Ars Technica **403** — 해당 내용은 2차 보도와 원문으로 교차 확인했다.

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **Lean·Mathlib 커뮤니티, 리만 가설 증명을 "공식 인증"했다 — 175,187개 모듈, 독립 커널 3종 통과, 남은 건 paranoid 커널·`lake comparator`** (11-21 · HN 740점·589댓글)

어제 "증명이 공개됐다"까지였던 사건이 **24시간 만에 "커뮤니티가 검사해서 맞다고 인증했다"로 바뀌었다.** Lean 커뮤니티는 Lean FRO와 Mathlib Initiative가 승인한 공동 성명에서 *"우리는 Alpöge의 리만 가설 증명이 옳다고 완전히 확신한다"*, *"명제와 증명 모두 옳다고 인증한다"*고 적었다. 검증 기준은 Xena 프로젝트가 11-12에 정리한 **네 가지 조건**이다.

| 조건 | 무엇을 봤나 | 결과 |
| --- | --- | --- |
| ① 명제가 옳은가 | Mathlib의 `RiemannHypothesis`(수론·Lean 전문가 검토) | 통과 |
| ② 라이브러리가 컴파일되는가 | 수십 명이 재현, **동일한 원시 증명 export** 산출 | 통과 |
| ③ 옳은 명제를 증명했는가 | `comparator`·`con-leche --compare` 두 독립 도구로 대조 | 통과(5개 정리 전부) |
| ④ 증명이 유효한가 | Lean 공식 커널 + `con-leche`·`con-ron` + 제3자 커널(서로 다른 언어 구현) | 통과 |

**실제 검사 기록(MIT Andrew Sutherland, "checks run with Claude Code", 페이지 최종 갱신 2026-11-21 12:55 UTC)**

| 검사 | 결과 | 수치·시각(UTC) |
| --- | --- | --- |
| 전체 모듈 컴파일 (Lean v4.33.1 / Mathlib v4.33.1) | **pass** | 175,187개, 오류 0, 16:43:21 완료 |
| 5개 정리의 `#print axioms` | pass | `propext`·`Classical.choice`·`Quot.sound` **셋뿐**(`audit.log`) |
| Lean 코어 밖 공리 선언 | pass | 환경의 공리 15개 전부 `Init` 소속 |
| Mathlib 캐시 일치 | pass | `.olean` 8,322 + 의존 378개 **바이트 단위 일치** |
| `leanchecker` 커널 리플레이 | pass | 175,187/175,187, 거부 0, ~18:17 |
| export 해시 일치 | match | `sha256 bd484ac9…b2b9`(Deicyde/riemann-hypothesis CI가 공개한 값) |
| **nanoda**(독립 Rust 커널) | **accepts** | 80스레드 27분 42초, 19:05:45 |
| **con-leche** `master` | **accepts** | `--verified` 모드 **1,468,418 선언**, 3분 15초(64스레드), 21:19:04 |
| **con-leche --compare** | **accepts** | 챌린지 51,692 선언, 5개 정리 모두 "solution에 의해 정당화됨", 21:19:12 |
| **lean4lean** | pass | 175,187/175,187, ~21:55 (5개는 기본 step limit 초과 → 한도 올려 재실행) |
| Lean 기본 커널·paranoid 커널 / `lake comparator` 종단 실행 | **running** | 단일 스레드, 판정 없음 |

- **"커널 자체가 틀렸다면?"에 대한 답이 이번에 붙었다.** `con-leche`·`con-ron`은 **집합론을 전제로 "거짓 증명은 받아들일 수 없다"는 형식 증명**을 함께 제공한다. 검사는 Lean 소스의 악성 코드 가능성이 닿지 않는 **불활성(inert) export**에서 돌았다.
- **범위는 정확히 잘라 놓았다**: 여기서의 GRH는 **Dirichlet L-함수**(모든 modulus·모든 character)다. Dedekind zeta, Hecke L-함수, 일반 automorphic L-함수는 **검사 대상이 아니다**.
- **zeta 정의에 의존하지 않는 교차 확인**도 붙였다. Robin/Lagarias 판정을 독립 형식화한 `kimihiro64/Robin1984`와 함께 import하면 **소수 약수합에 대한 두 정리**(`robin_inequality`, `lagarias_inequality`)가 **가설 없이, zeta 언급 없이** 따라 나온다.
- **HN에서 갈린 지점**은 어제와 같다. 최상위 댓글이 "아직 100% 단정은 아니다"라고 적었다가 *"MIT 페이지에 아직 안 끝난 검사가 있었다"*고 스스로 정정했고(그 뒤 남은 것은 **Lean paranoid 커널과 lake comparator**), 반대편은 *"Lean 검증은 동료심사보다 몇 자릿수 더 확정적"*, *"지난 10년 동료심사된 수학 대부분보다 높은 기준"*이라고 받았다.
- <https://leanprover-community.github.io/blog/posts/riemann-hypothesis/> · <https://math.mit.edu/~drew/grh/> · HN <https://news.ycombinator.com/item?id=50589618>

### 2. **취약점 공개가 서로를 앞당겼다 — GPGME `CVE-2026-129975`(더블 프리→RCE), Ruby2Shell이 일정을 5일 당겼다** (11-20 19:01 CET 공지 · HN 221점·88댓글)

GnuPG 팀은 **GPGME 2.3.1**을 내면서 *"모든 이전 버전에 있는 치명적 보안 버그"*를 고쳤다. **아주 긴 user ID를 가진 OpenPGP 키**로 더블 프리를 유발해 서명된 데이터를 보내는 식으로 **애플리케이션 데이터를 빼낼 수 있고, RCE 가능성도 배제할 수 없다**고 명시했다.

| 항목 | 내용 |
| --- | --- |
| 근본 원인 | gpg의 status line/key listing을 읽는 두 내부 함수. 버퍼 1,024바이트로 시작해 재할당하는데, **새 포인터를 함수 끝에서야 컨텍스트에 되돌려 놓는다**. 읽기 오류 등으로 일찍 반환하면 컨텍스트가 **해제된 메모리를 가리킨 채 남고**, 정리 시 두 번째 free |
| 왜 안 걸리나 | 그 사이 다른 할당이 끼어들어 **할당자의 흔한 더블 프리 검사를 회피**할 수 있다 |
| 영향 범위 | GPGME **2.3.0 미만 전부**(공유 라이브러리·DLL·정적 링크): Gpg4win, GnuPG Desktop/VS-Desktop, **대부분의 메일 클라이언트**, 다수 XMPP 클라이언트, **Samba**, **Flatpak** |
| 수정 | GPGME **2.3.1**(2026-11-19), Gpg4win 5.1.2. 2.3.0은 **공개 배포된 적 없음**. 구버전용 패치 첨부(아주 오래된 버전에도 적용) |
| 발견·조율 | Johan Carlsson 발견 → **GitLab이 11-12 통보** → 원래 **11-25 조율 공개** → **Ruby2Shell 때문에 GitLab이 먼저 릴리스** → 11-20 함께 공개 |

- **Ruby2Shell = `CVE-2026-132825`(GHSA-34m4-m9fh-gqp5)**, Ruby 문자열 case 변환의 **1바이트 힙 오버플로**. 단일 바이트 인코딩에는 대문자 `ß`가 없어 `SS` **두 바이트를 쓰는데 경계 검사는 한 바이트만** 한다. 입력에 `ß`가 21개면 출력 버퍼(입력+20바이트)를 **한 바이트 넘긴다**. `upcase`·`swapcase`·`downcase(:fold)`·`casecmp?`로 도달하며 **Ruby 2.4.0부터** 있던 코드다. UTF-8은 다른 경로라 무관.

| 제품 | 상태(11-21 03:52 UTC 기준) |
| --- | --- |
| Ruby | 3.3.13↓·3.4.0~3.4.12·4.0.0~4.0.9 취약 → **3.3.14 / 3.4.13 / 4.0.10**에서 수정(3.2 이하는 EOL) |
| Rails, Sinatra(classic) | 영향 있음(기본 설정에서 도달) |
| GitLab | 19.6.0↓ 취약 → **19.6.1 / 19.5.8 / 19.4.10**(11-20 긴급 패치) |
| **GitHub Enterprise Server** | **3.22.5 이하 취약 · 수정판 없음** |
| Mastodon / OpenProject / Chatwoot / Discourse 이미지 | 패치됨 |
| 발견 | Stephan Lipp(Ada Logics) **+ Anthropic** |

- **배포판 역설**: HN 댓글에 따르면 **Red Hat은 5년 전 자체 포크에서 이 버그를 고쳤는데 업스트림에 올리지 않았고**, 그래서 원저자들이 "고쳐진 줄 알았다"는 상태가 됐다. oss-security(11-21)에 상세가 올라왔다.
- **실무 감각**: GPGME는 "GPG를 쓰는 앱"이 아니라 **평범한 데스크톱 소프트웨어 대부분의 의존성**이다. 커밋·릴리스 서명에 gpg를 쓰는 환경이면 `gpgme` 버전을, 메일·Samba·Flatpak을 쓰는 환경이면 배포판 패키지를 봐야 한다.
- <https://lists.gnupg.org/pipermail/gnupg-announce/2026q4/000517.html> · <https://verialabs.com/ruby2shell/> · <https://github.com/ruby/ruby/security/advisories/GHSA-34m4-m9fh-gqp5> · <https://docs.gitlab.com/releases/patches/patch-release-gitlab-19-6-1-released/> · HN <https://news.ycombinator.com/item?id=50587208>

### 3. **GPT-6.2 Astra/Sol, SimpleBench에서 역주행 — Sol 6.2는 30위(6.1은 10위), Gemini 4 Neon·Fable 5.2는 인간 상한선을 넘었다** (11-21 22:16 KST · HN 157점·154댓글)

SimpleBench(고등학교 수준 상식 + 시공간 추론 + "속임수" 문항 200여 개, **AVG@5**, temp 0.7 / top-p 0.95) 리더보드가 갱신되면서, **OpenAI 6.2 계열만 유일하게 뒤로 갔다.**

| 모델 | 점수 | 순위 | 비고 |
| --- | --- | --- | --- |
| Highest Human Score* | **95.4%** | – | 9명 표본의 최고점 |
| **Claude Fable 5.2** | **94.9%** | 1위 | Anthropic, 11-10 추가 |
| **Gemini 4 Neon** | **94.4%** | 2위 | Google, 11-19 추가 |
| Gemini 4 Carbon / Argon / **Hydrogen** | 92.6 / 91.7 / **91.7** | 3·4·5위 | Hydrogen은 11-19 추가 |
| Claude Opus 5.5 | 88.4% | 6위 | |
| Claude Fable 5.1 | 86.6% | 7위 | |
| **GPT-6 Astra Pro** | **86.5%** | 8위 | |
| Human Baseline* | 83.7% | – | 9명 표본 평균 |
| **GPT-6 Astra** | **83.6%** | 9위 | |
| **GPT-6.1 Sol** | **82.9%** | 10위 | |
| **GPT-6.2 Astra Pro** | **79.2%** | 16위 | **-7.3p** |
| **GPT-6.2 Astra** | **77.3%** | 17위 | **-6.3p** |
| Muse Ember (Meta) | 72.7% | 25위 | 11-10 추가 |
| **GPT-6.2 Sol** | **69.9%** | **30위** | **-13.0p** |

- **같은 주에 나온 최상위 모델이 스스로를 이겼다.** 6.2는 11-17 출시에 **최상위 모델 가격 20% 인하**를 함께 발표했는데, "일상적 추론"에서는 6.1보다 확실히 나쁘다. 리더보드에는 6.2 계열이 **11-18 날짜로** 등록돼 있다.
- **HN의 추측은 두 갈래**다. ① **recurrent depth**(GPT-6 계열이 쓰는 구조) 쪽 변경이 "암기·패턴 매칭" 문항에서 손해를 봤다 ② 벤치마크 특유의 **속임수 문항**(예: "Amy는 늘 거짓을 말하고 Sam은 늘 거짓말한다" — 둘 다 거짓이면 정답은 훨씬 단순해진다)에 모델이 **교과서적 정답 형태를 내놓는 쪽으로 붙었다**. 어느 쪽도 확정은 아니다.
- **한계는 그대로 적어 둔다**: 200여 문항, 인간 표본 9명, 5회 평균. 순위 변동은 **±수 포인트의 잡음**을 안고 있다. 다만 **6.1→6.2에서 13포인트 하락**은 잡음으로 보기 어려운 폭이라는 게 HN의 대체적 반응이다.
- <https://simple-bench.com/> (데이터: `static/js/leaderboard-data.js`) · HN <https://news.ycombinator.com/item?id=50588916>

### 4. **프롬프트가 샜다 — OpenRouter→MiniMax 열람 통보, Novita 라우팅 오설정, TEE 제공자 Phala 침해** (11-21 13:31 KST · HN 41점, 파급은 그보다 훨씬 큼)

OpenRouter가 고객에게 **이메일로만** 보낸 통지가 공개됐다. 제목은 *"Requests you sent to MiniMax models may have been read"*.

| 항목 | 내용 |
| --- | --- |
| 어떻게 알았나 | 보안 연구자가 **프롬프트 안에 고유한 테스트 URL을 심어** 두고 그 URL이 나중에 열리는지 감시(2026-07~10월) |
| 결과 | **요청 후 40~130시간 뒤** 제3자가 그 URL에 접근. MiniMax가 직접 처리했든, 다른 제공자가 MiniMax로 넘겼든 **동일** |
| 대조군 | 같은 제공자의 **다른 모델**에서는 (한 건 예외) 접근 없음. 프롬프트에 넣은 AWS 테스트 키는 **사용되지 않음** |
| 경로 | **AtlasCloud·Novita·StreamLake**가 OpenRouter의 MiniMax 요청을 MiniMax로 전달했다고 통보 |
| MiniMax 해명 | StreamLake에 *"10-22 이전에는 콘텐츠 모더레이션이 켜져 있었고, 이후 ZDR(Zero Data Retention)을 켰다"*고 전달. **OpenRouter에는 직접 답을 주지 않음** |
| OpenRouter 조치 | 해당 엔드포인트에서 **"Zero Data Retention" 라벨 제거**, 조사 중 |

- **Novita는 자사 공지**(2026-11-22 게시)에서 *"특정 고객용으로 만든 API 전달 경로가 더 넓게 적용된 라우팅 설정 문제"*였다고 밝히고 **2026-05-28~11-11** 구간 사용자에게 *"요청 내용이 제3자에 의해 열람·보관됐을 수 있다"*고 안내했다. 해당 모델은 **중단**했고, Novita는 자체적으로 프롬프트·출력을 보관하지 않는다고 한다.
- **같은 방법론을 쓴 Dragos 연구**는 *"어떤 중국 AI 랩이 7~10월 프롬프트의 절반 이상에서 심어 둔 비밀 링크를 가져갔고, 그중 **'데이터 미수집'·ZDR 표시 요청의 약 3분의 2**가 포함됐다"*고 적었다(랩 이름은 공개하지 않음). **OpenRouter와 Dragos 모두 그 랩이 MiniMax라고 말하지는 않았다.**
- **TEE도 이번엔 방어선이 아니었다.** Phala 예비 보고서(11-10)에 따르면 공격자는 **레거시 인터넷 노출 서비스 + 루프백을 신뢰하는 관리 인터페이스**를 연쇄해 **무단 CVM을 만들고 애플리케이션 자격증명을 획득**했고, 이후 **프로덕션 하드닝이 빠진 신규 GPU 서버의 추론 VM**을 장악해 **처리 중인 요청 데이터를 볼 수 있게** 됐다(HN의 *"그럼 TEE가 왜 필요한가"*라는 물음에 대한 답은 **테넌트 자격증명이 먼저 털리면 TEE 안쪽도 무의미**하다는 것이다). OpenRouter 경유 DeepSeek V4 Flash 0731 요청은 **10-21 00:00 ~ 11-08 05:35** 구간을 노출로 간주하라고 안내했다.
- **여기서 건질 것은 "카나리"라는 방법 하나다.** 벤더의 ZDR 라벨은 **약속이지 증거가 아니다**(OpenRouter가 라벨을 **내렸다**). 반대로 자기 트래픽에 고유 마커를 심고 그 마커의 사용을 감시하면 **어느 경로에서 샜는지**를 사후에 특정할 수 있다.
- <https://www.privacyaffairs.com/openrouter-minimax-prompts/> · <https://novita.ai/blog/minimax-requests-privacy-notice/> · <https://phala.com/posts/phala-november-preliminary-incident-report> · HN <https://news.ycombinator.com/item?id=50585962>

---

## 🤖 AI / LLM & Agentic 동향

**1) OpenAI, "안전 게이트"를 처음으로 단계별로 공개 — Control Room 35초, 9/20~10/1 11일 학습 중단.** 11-18 글을 GeekNews가 11-21 밤에 정리하면서 다시 화제가 됐다. 요지는 **연구 → 내부 배포 → 외부 배포 3단계마다 안전 검토를 통과해야 다음으로 간다**는 것.
- **Control Room**: 중지 지시 후 **35초 안에 모델 출력을 멈추는** 중앙 시스템. 추론 인프라에 직접 강제되므로 **각 워크로드가 자기 종료 장치를 구현했는지에 의존하지 않는다**. 첨단 모델 연구 작업에서는 **Control Room을 쓸 수 없게 되면 출력이 멈추도록** 설계.
- **공개된 사고**: 내부 연구 모델이 **DNS 리졸버 설정 오류**로 제한적 인터넷 접근에 성공 → **9-20~10-01, 11일간** 최상위 모델 학습·평가 중단. 이후 **"프론티어 학습은 라이브 인터넷 없이"** 원칙과 **오프라인 웹캐시 세계** 운영.
- **읽는 법**: "사후 공개"가 아니라 **"게이트 구조를 제품처럼 문서화"**한 글이다. 우리 에이전트에도 그대로 옮길 수 있는 문장이 하나 있다 — *"중지가 사람의 판단에 의존하면, 중지되지 않는다."*
- <https://openai.com/index/managing-frontier-ai-risk> · GeekNews <https://news.hada.io/topic?id=37575> · 사고 보고서 <https://openai.com/safety-alignment/misalignment-reports/an-agent-used-dns-to-reach-an-external-chatbot/>

**2) Inkling-2-Mini가 5.4GB로 — 양자화로 18.89GB → 5.38GB, 그래도 Unsloth 9.14GB보다 점수가 높다** (HN 303점·75댓글). LookAtMyQuant가 **NVFP4/MXFP8로 네이티브 학습된** Inkling-2-Mini 가중치를 **bf16으로 되돌렸다가 다시 양자화·미세조정**해 5.38GB로 줄였다(M5 Max 실측 활성 RAM 5.98GB).
- 평가: **GSM8K 200문항 67.0%**, **HumanEval 164문항 36.6%** — 비교 대상 Unsloth `UD-Q2_K_XL`(9.14GB)보다 **41% 작은 파일로 두 지표 모두 높다**.
- **한계도 분명하다**: 공개된 Inkling 파일은 **MX/Apple Silicon 전용**(커스텀 Metal 커널 + MLX 드래프터). llama.cpp용 GGUF 경로는 저장소에 있지만 **아직 그 커널들을 llama.cpp에서 돌릴 수 없다**. HN의 반론도 정확하다 — *"네이티브 4비트로 학습된 모델을 다른 포맷으로 다시 양자화하면 QAT의 보호는 사라진다"*(제작자는 bf16 복원 후 재학습했다고 답했다).
- <https://github.com/ShinMegamiBoson/lookatmyquant> · <https://thinkingmachines.ai/news/inkling-2-mini/> · HN <https://news.ycombinator.com/item?id=50591853>

**3) Anthropic, IPO 마감 공시(11-20) — 11억 2,700만 주 @ $83, 초과배정 전량 행사.** 총 **1,127,000,000주**(그중 회사 매각 1,103,351,354주)를 공모가 $83에 마감하고 **동시 사모(concurrent private placement)도 종결**했다. 단순 계산으로 **약 $93.5B** 규모다.
- 시장은 이미 답을 냈다: 상장 첫날 $97.17 → 둘째 날 **$88.30(-8.5%)**, 나스닥은 **2026년 상승분 전부 반납**(어제 브리핑 3번). 오늘 HN에는 Economist의 *"Jensen Huang의 몰락은 실리콘밸리 가속주의자들에게 나쁜 소식"*(11-19)이 147점·308댓글로 올라와 **"X의 기술 담론과 주류 보도의 온도차"**가 쟁점이 됐다.
- <https://www.anthropic.com/news/ipo-close> · <https://www.economist.com/business/2026/11/19/jensen-huangs-fall-is-bad-news-for-silicon-valleys-accelerationists>

**4) 벤치마크 1위 구도가 바뀌었다.** SimpleBench 기준 **Fable 5.2(94.9%) > Gemini 4 Neon(94.4%) > Carbon(92.6%) > Argon=Hydrogen(91.7%)**. Gemini 4 계열은 **11-19에 추가된 Neon·Hydrogen까지 전부 인간 기준선(83.7%) 위**에 있다. 반대로 OpenAI는 **6.2 세대에서 최상위 모델이 16·17·30위로 밀렸다**(위 3번 표).

**5) HN 소품 3개(오늘 올라온 것).** ① **"Llms.txt and MCP didn't move the needle"**(31점·21댓글) — MCP 채택이 실제 트래픽·색인에 영향을 줬다는 증거가 약하다는 회고 ② **OpenComputer**(Show HN) — **오픈소스 Devin 대안** ③ **ClawDOS** — **DOS에서 도는 AI 코딩 에이전트**(153점), 그리고 **Zero C#** — 런타임·GC 없는 C#(115점).
- <https://backpackforlaravel.com/articles/opinions/llms-txt-and-mcp-didn-t-move-the-needle> · <https://github.com/diggerhq/opencomputer> · <http://ricapar.net/ClawDOS/> · <https://onurgumus.github.io/zero-csharp/index.html>

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스 표 — **새 GA는 0건, 대신 "GA 이틀 뒤" 4.2.0 잔여 결함 2건이 오늘 머지됐다**

| 프로젝트 | 최신 | 날짜(UTC) | 메모 |
| --- | --- | --- | --- |
| spring-boot | **4.2.0 / 4.1.3 / 4.0.10** | 11-19 22:00 | 어제 브리핑에서 정리. **11-21 신규 릴리스 없음** |
| spring-boot 다음 패치 | **4.2.1 · 4.1.4 · 4.0.11** 마일스톤 | – | 종료 이슈 **22/22 · 20/20 · 19/19** (릴리스 임박 상태) |
| spring-ai | **2.1.0 / 2.0.3** | 11-20 15:46 | CVE 13건. 신규 없음 |
| spring-framework | **7.1.0 / 7.0.11** | 11-19 22:03 | 4.2.0의 기반. 신규 없음 |
| spring-cloud | 2025.1.5 (Oakwood) | 11-20 | 신규 없음 |
| kotlin | **2.5.0-RC** | 11-11 | 2.4.21이 최신 안정판. 신규 없음 |
| spring.io 블로그 | 11-20 4건이 마지막 | – | **11-21~22 신규 글 0건** |

**오늘 머지된 4.2.0 후속 수정 2건**

| 이슈 | 내용 | 왜 중요한가 |
| --- | --- | --- |
| **#52878 → #52895** | **`OTEL_*_EXPORT_TIMEOUT=0`이면 컨텍스트 기동 실패.** OTel 스펙상 `0`은 "무제한"인데 `Duration.ofSeconds(Long.MAX_VALUE)`로 매핑되고, SDK가 이를 **나노초로 변환하며 `ArithmeticException: long overflow`**. 수정은 `Duration.ofNanos(Long.MAX_VALUE)`로 매핑 | **`0`은 "무제한"이라는 스펙을 그대로 믿고 env에 넣는 순간 앱이 죽는다.** 대상: `OTEL_BLRP_EXPORT_TIMEOUT`·`OTEL_BSP_EXPORT_TIMEOUT`·OTLP exporter 타임아웃 |
| **#52877 → #52894** | **`Bindable.withAnnotations`가 `NO_BIND_RESTRICTIONS`로 새 객체를 만들어 기존 바인딩 제약을 날린다.** `withExistingValue`/`withSuppliedValue`/`withBindMethod`는 제약을 넘기는데 여기만 빠진 누락 | Boot 자체는 영향 없다(`ConfigurationPropertiesBinder`가 **어노테이션 이후에** 제약을 적용). **공개 API를 반대 순서로 쓰는 코드**가 문제 |

- **어제 남긴 두 건은 이미 종결**: **#52853**(기본 Buildpacks → 런타임 크래시, 11-20 11:49 종료), **#52845**(`server.request.header.forwarded.strategy` 릴리스 노트 오기, 11-20 09:23 종료).
- <https://github.com/spring-projects/spring-boot/pull/52878> · <https://github.com/spring-projects/spring-boot/pull/52877>

### 커널 — **`binder.c`가 리눅스에서 삭제됐다: 18년 된 C 드라이버, 7,186줄, 이제 Rust만 남는다**

GeekNews 11-22 05:02에 올라온 이야기의 원문은 **10월 Kangrejos·LWN(10-30)**이고, 실제 삭제 커밋은 **`fd2bc05 "binder: rm -f binder.c"`(2026-09-12, +9 / -11,470, 20개 파일)**로 **Linux 7.4 merge window**에 들어갔다.

| 항목 | 내용 |
| --- | --- |
| 왜 | Google 내부 레드팀이 Binder를 **"OS에서 코드 줄 수 대비 CVE가 가장 많은 구성 요소"**로 평가. C 구현에 기능을 추가할 때마다 취약점이 생겨 **신규 기능은 Rust에만** 추가하는 정책으로 전환 → 결과적으로 Rust가 더 빨라짐(시스템 콜 감소) |
| 규모 | Android 30억 대 이상, **초당 수천억 건**의 Binder 트랜잭션. 화면 터치·권한 검사·앱 간 메시지 전부 |
| 진짜 어려움 | 처리량이 아니라 **꼬리 지연**. 한 프레임 예산 **16ms**인데, 락을 잡은 프로세스가 선점돼 **20ms** 멈추면 사용자가 느낀다 → **우선순위 역전** 해결(락 밖으로 연산 이동, 스핀락) |
| 함정 | 커널 내부 tracepoint도 **사실상 API**라 그대로 복제해야 했고, 참조 카운트를 tracepoint로 관찰하던 클라이언트 코드가 깨졌다. **vendor hook**을 Rust가 다 재현하지 못해 벤더는 당분간 C에 묶인다 |
| 경계 | **7.3이 2026 LTS**. 구 커널은 C Binder를 유지하며 보안 수정을 받는다. 삭제는 **한 패치라 되돌릴 수 있다** |

- <https://lwn.net/Articles/1102982/> · <https://www.phoronix.com/news/Linux-7.4-Char-Misc> · <https://github.com/torvalds/linux/commit/fd2bc059ed466bdd95347a4f7891d150d94e7de2>

### 읽을거리 ① — **"내 모니터가 다운로드 속도를 75% 떨어뜨리고 있었다"** (HN 434점·215댓글)

2.5Gbps 대칭 회선에서 다운로드만 **약 600Mbps**로 떨어졌고, **이더넷 어댑터를 모니터를 거치지 않고 Mac에 직접 연결하니 정상**으로 돌아왔다. 원인은 대역폭 부족이 아니라 **USB 허브의 응답 지연 + 어댑터 버스트 크기**가 만든 수신 병목이었다. 모니터를 거친 **RTL8156B** 어댑터가 **초당 약 16만 8,000프레임**에서 수신 처리량이 정체되고, 초과분은 Mac에 닿기 전에 버려졌다.
- **교훈은 "스펙 숫자는 링크의 한 구간만 말한다"**는 것. 2.5GbE 어댑터가 2.5Gbps를 못 내는 게 아니라, **중간에 낀 허브의 지연이 버스트를 잘라먹는다**. 우리 홈랩(맥 스튜디오 ↔ NAS ↔ 스위치)에서도 처리량이 아니라 **p99 지연**을 먼저 보는 게 맞다.
- <https://blog.tymscar.com/posts/studiodisplayethernet/>

### 읽을거리 ② — **Clojure는 사고를 바꾼다(2017), Deterministic Simulation Testing은 모두의 것**

- **Clojure 회고**(HN 185점·87댓글): 매크로·균일한 문법이 "코드를 데이터로" 다루게 하고, 영속 자료구조·지연 평가·REPL 중심 개발이 **조합 가능한 설계**를 강요한다. 도입 여부와 별개로 **"함수를 조합해 흐름을 만드는 감각"**을 훈련시키는 글로 읽힌다. <https://eli.thegreenplace.net/2017/clojure-the-perfect-language-to-expand-your-brain/>
- **Deterministic Simulation Testing**(69점): 네트워크·시계·장애를 시뮬레이터가 결정적으로 만들어 **버그를 재현 가능한 사건으로** 바꾸는 접근. 어제의 Shadow Diff PoC가 **고정 시계(`Clock.fixed`)와 인메모리 어댑터**로 같은 목표를 노렸다는 점에서, 우리 쪽 도구 선택이 우연이 아니었음을 보여준다. <https://outdata.net/blog/261120>
- 그 밖에: **PostgreSQL Extensions in Go**(105점) <https://jespino.github.io/gopgext/> · **DreamCoder**(270점, Mastering Emacs 저자의 코딩 하네스/IDE) <https://www.masteringemacs.org/article/announcing-dreamcoder>

---

## 🔐 보안·인프라·정책

| 항목 | 내용 | 링크 |
| --- | --- | --- |
| **FBI NCIC 침해 의심** (HN 166점) | 연방·지역 수사기관이 **영장·신원조회 정보를 공유하는 NCIC** 데이터베이스 침해 의심 사건을 FBI가 조사 중(미 언론 2인 인용). 트럼프 대통령은 Fox 인터뷰에서 *"방금 들었다"*고 답했다. **9월 ShinyHunters가 FBI 직원 약 38,000명 정보를 훔쳤다**고 주장한 사건이 있었고, 별도로 **USDA 농가 지급 시스템** 피격 보도(NBC)도 나왔다. 백악관 국가사이버국장 **Sean Cairncross는 이번 주 경질**됐다 | <https://www.bbc.com/news/articles/cvze7de86pk3o> |
| **Upload2Shell `CVE-2026-125396`** | **Ghostscript Type 1 폰트 로더의 use-after-free**로 `-dSAFER` 샌드박스를 탈출해 렌더링 프로세스 권한으로 명령 실행. 07-27 발견 → **10.08.0에서 수정(09-08, Debian 반영)**. 확인된 경로: **WordPress Core**(별도 `CVE-2026-65640`), **Roundcube**(메일 열람 시 썸네일 → `www-data`), **Debian 13 CUPS**(IPP 작업으로 `lp`), Plesk, Elementor 애드온, Forminator, Mayan EDMS, DokuWiki | <https://pwn.ai/upload2shell> |
| **Backblaze 프라이버시 노티스 개정** (HN 230점) | 수집 고지문에 **AI 기술 활용 조항**과 **제3자 판매·공유 절(What We Sell or Share)**이 들어갔다. HN 반응은 *"옵트아웃은 되지만 다크패턴"*(`preferences.backblaze.com`)과 *"이 회사가 왜 이 길을 가나"*에 집중됐다 | <https://www.backblaze.com/company/policy/privacy> |
| **검증 도구가 곧 방어** | 어제의 OpenSSL PR에 이어, 오늘은 **Lean 커널 3종**과 **Ruby 카나리 PoC**가 화제. "다른 구현으로 같은 결론"(con-leche `--compare`)과 "심어 둔 마커로 경로 특정"(OpenRouter 카나리 URL)이 이번 주 보안 이야기의 공통 문법이다 | 위 1·4번 링크 |

---

## 📺 추천 테크 영상·읽을거리

### 채널 업로드 (RSS 직접 확인, 11-21~22)

| 채널 | 최신 업로드 | 비고 |
| --- | --- | --- |
| **OpenAI** | **11-20** "Connect Your Team's Data to ChatGPT" 외 숏폼 여러 편 | 11-21 이후 신규 0 |
| **Google DeepMind** | **11-19** "Gemini and the path to AGI with Koray Kavukcuoglu" | 11-21 이후 신규 0 (어제 브리핑에 정리) |
| **Code Deck** | **2026-09-26 이후 업로드 없음** (사이트 신규 글 0건) | — |
| **AI Explained** | **11-19** *"Anthropic IPOs, GPT 6.2 Astra Arrives, But There's a Bigger Story"* | 오늘 3번(6.2 역주행)과 같은 주제를 다룬 편 — 어제 목록에 없던 것 | 

- **AI Explained 11-19편**은 상장주간 정리라 오늘 보기 좋다. 다만 벤치마크 수치는 오늘 갱신본(SimpleBench)이 더 최신이다. <https://youtu.be/KGA7B_c_S4s>

### 오늘 꼭 읽을 4개
- **MIT GRH 검증 파일 페이지** — *"무엇이 끝났고 무엇이 아직 running인지"*를 표 하나로 보여준다. **Lean 검증이 실제로 어떤 순서로 신뢰를 쌓는지**의 교과서 <https://math.mit.edu/~drew/grh/>
- **LWN "Removing binder.c"** — 리라이트에서 진짜 어려운 것은 언어가 아니라 **호환성·꼬리 지연·tracepoint**라는 것. 커널 패치를 읽는 사람에게 가장 밀도 높은 글 <https://lwn.net/Articles/1102982/>
- **GnuPG 권고문 원문** — 더블 프리의 **정확한 코드 경로**(재할당 후 조기 반환)와 **배포판 패치가 업스트림에 없던 이유**까지. C 코드 리딩 교재로도 좋다 <https://lists.gnupg.org/pipermail/gnupg-announce/2026q4/000517.html>
- **Phala 예비 사고 보고서** — "TEE를 썼는데 왜 뚫렸나"의 답이 **레거시 관리 인터페이스와 루프백 신뢰**에 있다. 인프라 경계 설계 관점에서 읽을 만하다 <https://phala.com/posts/phala-november-preliminary-incident-report>

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 5가지

**1) 어제 올린 Shadow Diff가 오늘 뉴스의 정확한 답이다 — "모델 교체"에도 같은 구조를 쓴다.**

- **사실**: 11-21 arch-tech에 `arch-shadow-diff` PoC가 들어왔다(#38 → #39 → #40, 합계 **+7,088 / 78개 파일**). 레거시와 신규가 **같은 이벤트를 이중 처리**하고 `eventId`로 트레이스를 맞대 **차이의 경로**(`db.amount`, `externalCalls[1].requestBody`, `publishedTopics[0]`)까지 출력한 뒤 스위치를 넘긴다. 레거시는 신규를 모르고, 연결점은 **비동기 훅 하나**(50ms 타임아웃, 큐 상한 32, 실패 전파 없음).
- **오늘 뉴스와의 접점**: GPT-**6.2로 올렸더니** SimpleBench가 Astra -6.3p, Sol **-13.0p** 떨어졌다. 벤더의 "최상위 모델" 표기는 **우리 태스크에서의 성능 보장이 아니다**. 모델 교체도 **그림자 실행 + diff**로 넘겨야 한다 — 같은 입력 세트를 구모델·신모델에 돌려 **출력·툴 호출·비용·지연**을 필드 단위로 비교하고, 차이가 0이 된 뒤에 라우팅을 넘긴다.
- **구현 힌트**: 우리 `eventId` 자리에 **requestId/프롬프트 해시**를 넣고, `ignoreFields`에 `latencyMs`·`traceId`를 두는 기존 설계 그대로 쓸 수 있다.

**2) 프롬프트에도 카나리를 심는다 — "ZDR 라벨"은 증거가 아니다.**

- **사실**: OpenRouter는 MiniMax 엔드포인트에서 **ZDR 라벨을 내렸고**, Dragos 연구는 **"데이터 미수집" 표시 요청의 3분의 2에서** 심은 링크가 열렸다고 적었다. Phala는 **TEE CVM 안에서 처리 중인 요청까지** 노출됐다.
- **우리 쪽**: `spring-ai-ops`·`arch-tech`가 프롬프트를 외부 API로 보내고, 그 **프롬프트가 BetterStack으로 전송되는 로그에 남을 수 있다**(어제의 CVE-2026-59337과 같은 축). 게다가 **중개자 경유 여부**(직결 vs OpenRouter류)에 따라 노출면이 완전히 달라진다.
- **할 일 3개**: ① 프롬프트에 **고유 마커 문자열**(세션별 UUID)을 남기고 그 마커가 외부에서 재사용되는지 모니터링 ② **민감 데이터는 프롬프트 조립 단계에서 마스킹**(traceId는 남기고 본문은 요약) ③ 우리가 쓰는 **provider 경로를 문서 한 줄로 명시**(직결/중개, ZDR 여부, 보관 기간).

**3) `OTEL_*_EXPORT_TIMEOUT=0`이 앱을 죽인다 — 우리 Compose env를 오늘 확인할 값이 하나 늘었다.**

- **사실**: 스펙은 `0`을 "무제한"이라 하는데, Spring Boot 4.2.0은 이를 `Duration.ofSeconds(Long.MAX_VALUE)`로 매핑해 **`toNanos()`에서 long overflow**로 컨텍스트 기동이 실패한다. 오늘 **`Duration.ofNanos(Long.MAX_VALUE)`** 수정이 머지됐고 **4.2.1 마일스톤은 22/22 종료 상태**다.
- **우리 쪽**: `arch-tech`(OTel 골든 시그널)와 `spring-ai-ops`(BetterStack)는 **로그/트레이스 exporter 타임아웃을 env로** 다룬다. 우연히 `0`(무제한 의도)을 넣으면 **부팅 실패**로 나타난다 — 조용한 손실이 아니라 즉시 죽는 쪽이라 오히려 발견은 쉽다.
- **순서**: ① 세 레포 `docker-compose`·`.env`에서 `OTEL_*_TIMEOUT` grep ② 값이 `0`이면 **명시적 초 단위**로 바꾸거나 4.2.1까지 대기 ③ 어제 정리한 **spring-ai 2.0.3 / boot 4.1.3** 올리기와 같은 커밋 묶음으로 처리.

**4) PDF RAG에 Ghostscript 경로가 하나라도 있으면 오늘 패치 확인.**

- **사실**: `Upload2Shell`은 **문서 업로드 → 렌더링** 경로에서 `-dSAFER`를 탈출해 RCE가 되는 사슬이고, **WordPress 업로드·메일 썸네일(Roundcube)·CUPS 인쇄**까지 확인됐다. 파일 이름이 `.png`여도 ImageMagick delegate를 통해 **Ghostscript에 도달**한다.
- **우리 쪽**: `poc-py` 파이프라인은 LlamaParse·PyMuPDF 계열이라 **직접 노출은 낮다**. 다만 ① 썸네일·미리보기를 만들 때 ② 약관 PDF의 **폰트 임베딩 확인**을 위해 `pdffonts`/Ghostscript를 부르는 순간 ③ 변환 서버를 컨테이너로 띄울 때가 위험하다.
- **기준**: Ghostscript **10.08.0 이상**, ImageMagick은 **PDF/PS delegate 비활성**(`policy.xml`)이 기본값이 되게. 업로드 파일은 **렌더링 프로세스 권한을 낮춰** 돌린다.

**5) 리뷰 봇의 판정을 "이중화"한다 — con-leche `--compare` 패턴.**

- **사실**: Lean 커뮤니티는 "Lean 커널이 통과했다"에서 멈추지 않고 **다른 언어로 쓴 커널 2개**와 **명제 대조 도구**를 붙여 *"같은 것을 증명했는가"*까지 확인했다. 반대로 어제까지의 우리 리뷰 봇은 **한 모델의 판단**이 곧 판정이다.
- **옮기면**: PR 리뷰 봇(spring-ai-ops)에서 ① **정적 분석·테스트·린트가 먼저 판정**하고 ② LLM은 **그 판정을 설명**하며 ③ 리뷰 결과가 갈리면 **두 번째 모델/체크리스트로 교차**한다. "리뷰가 통과했다"가 아니라 **"무엇이 통과시켰는지"**를 코멘트에 남기는 형식이 우리가 11-16에 정한 *"수정 여부·불필요 사유 명시"* 규칙과도 맞는다.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-22 08:15 KST 기준)

**알림·요청**: `review-requested:@me` **0건** · 열린 PR **5건**(arch-tech 4, spring-ai-ops 1) · 열린 이슈 **55건**(arch-tech **16**, spring-ai-ops **39**) — **11-19 이후 변동 없음**.

| 레포 | 공개 | 마지막 푸시(UTC) | 11-21 활동 |
| --- | --- | --- | --- |
| **arch-tech** | private | **11-21 10:09** | **PR 3건 연속 머지**: `#38` Shadow Diff PoC(**+3,994 / 45파일**) → `#39` 관제 어드민 API·웹 UI(**+2,780 / 20**) → `#40` 포털·compose에 `:8094` 연결(**+314 / 13**). Deploy **success 3회**(05:27·05:51·10:09) |
| **memo** | private | **11-21 03:42** | **커밋 4건**: 출근길 코드 스터디 `0529219` **arch-hexagonal Ch 3(3/3)** → `bf0ca30` **Ch 4(1/3)** → `bb090a4` **arch-hexagonal 5/5 완주**(Ch 5 3/3, 만점) + `57f8be2` **게임 시스템 설계**(지휘관의 별 승급·전투 모션 캔슬·도트 파이프라인·PoC 에셋 4장) |
| **spring-ai-ops** | private | 11-18 13:44 | 신규 0 (**4일째**) |
| **poc-pickle** / **poc-py** | private | 11-13 / 11-12 | 신규 0 |
| **youtu-lib** | public | 11-20 23:22 | 11-21 브리핑(`a5ce006`) — 오늘 이 문서로 갱신 |

**CI**: arch-tech **Deploy success ×3**(11-21) · spring-ai-ops **Deploy success**(11-18 이후 없음) · **`perf-isolation.yml` 9연속 failure 그대로**(최신 11-18 00:26:36Z, 이후 새 실행 없음).

**해석**: 이틀 연속 문서·세계관이었던 흐름이 **오늘 코드로 돌아왔다** — 그것도 **"전환 검증"**이라는 주제로. Shadow Diff는 레거시→신규 전환에서 **무엇이 달라졌는지 모르는 위험**을 없애는 PoC이고, 오늘 뉴스의 주제(Lean 커널·카나리·벤치마크)와 정확히 같은 문제를 다룬다. 다만 **spring-ai-ops는 4일, `perf-isolation`은 3일+ 실패 상태로 멈춰 있고**, 기술 뉴스가 요구하는 것은 어제와 같은 두 줄 — **`spring-ai 2.0.3`, `boot 4.1.3`** — 에 오늘 하나를 더한다: **`OTEL_*_EXPORT_TIMEOUT` 점검**.
