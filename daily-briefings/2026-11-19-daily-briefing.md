# 2026-11-19 (목) 데일리 테크 브리핑

> **수집** 2026-11-19 08:15 KST · 최근 24시간(2026-11-18 08:15 ~ 11-19 08:15 KST = 11-17 23:15 ~ 11-18 23:15 UTC) 중심
> **소스** Hacker News(front), spring.io·Maven Central(repo1 메타데이터)·GitHub Releases API, Wiz·Xint·Rapid7·CVE.org·Debian Security Tracker, GitHub Advisory(GHSA)·PyPI/Wiz 공급망 분석, blog.google(DeepMind)·microsoft.ai·openai.com, CNBC·Axios(Anthropic), radixark.com·energy-based-reasoners.github.io(논문 사이트), European Business Magazine(FT 인용), itbiznews(AWS), claude.com·claudenews.online, CodeDeck, CMU CS Learn, Docker Hub
> **오늘의 한 줄** — *"2015년부터 nginx에 박혀 있던 결함이 워커 권한에서 도커 호스트 루트까지 이어졌고, 같은 날 Anthropic은 1조 달러로 상장 가격을 확정했다 — 방어의 단위와 자본의 단위가 동시에 커진 하루."*
> ※ **GeekNews(news.hada.io)는 이번 수집에서 두 번 모두 HTTP 403으로 읽지 못했다.** HN·공식 블로그·1차 문서로 대체했고, GeekNews에만 있던 항목은 넣지 않았다.

---

## 🌟 오늘의 핵심 4대 테크 헤드라인

### 1. **nginx2root — nginx 원격 코드 실행 5건 + 리눅스 커널 LPE 1건, 체인으로 도커 호스트 루트까지** (HN 1,010점·465댓글)

한 연구팀이 **각각 독립적으로 RCE가 되는 nginx 결함 5건**을 냈고, 그중 하나는 **2015년 nginx 1.9.5에 HTTP/2가 들어올 때부터** 있던 코드다. 여기에 별도 커널 0-day를 엮어 **컨테이너를 탈출해 호스트 root**를 얻었다.

| CVE | 결함 | 필요한 설정 | 영향 최초 버전 | HTTP/2 끄면 막히나 |
| --- | --- | --- | --- | --- |
| **CVE-2026-123007** | HTTP/2 flow control **정수 언더플로** → 잘못된 메모리 복사 | HTTP/2 활성 | **1.9.5 (2015)** | **예** |
| **CVE-2026-123633** | HTTP/2 연결 **use-after-free** | HTTP/2 + 업스트림으로 프록시 | 1.19.7 | 예 |
| CVE-2026-123634 | DNS resolver **UAF** | `resolver` + 요청에서 유래한 `proxy_pass` 목적지 + `mirror`(`mirror_request_body off`) | 1.13.4 | 아니오 |
| CVE-2026-121810 | HTTP/3 **UAF** | HTTP/3 + 업스트림 프록시 | 1.25.0 | 아니오 |
| CVE-2026-113425 | 메일 프록시 **UAF** | `proxy on` + `auth_http` | 0.5.15 | 아니오 |

| 항목 | 내용 |
| --- | --- |
| 점수 | Wiz 표기 **CVSS 4.0 9.2**(5건 모두 Critical). Xint는 이를 묶어 **"nginx2root"**라 명명 |
| 수정 | OSS **1.30.7 stable / 1.31.9 mainline**, Plus **37.0.7.1·37.2.0.1**(UAF 계열)·**37.0.8.1·37.3.0.1**(언더플로 계열) |
| 같은 릴리스의 나머지 | 총 **10건** — HTTP/3·DNS resolver·메일 프록시 UAF가 모두 9.2, 그 밖에 무한 루프 **8.7**(CVE-2026-118899), NULL 역참조·`alias`+`try_files` 버퍼 오버플로·`ssl_preread` SSRF성 접근(각 6.9), 클라이언트 인증서 우회 **6.3**(CVE-2026-120967) |
| 노출 규모 | Shodan `product:nginx` **5,670만 호스트**, 그중 버전 식별 1,194만(21.1%), **식별된 것의 98.9%가 1.9.5~1.31.8 구간**(11-10 기준) |
| 발견 | Wiz·Ikotas Labs·Metnir·Rapid7·**Xint** 독립 발견. Xint는 **ZeroDay Cloud 2026 준비 중 사람이 1건**, 며칠 뒤 **완전 자동 스캔이 나머지 4건**을 찾았다 |

- **커널 쪽이 진짜 요점이다.** nginx 워커는 권한이 낮아서 RCE만으로는 호스트를 못 잡는다. Xint는 **SCTP 서브시스템의 send 경로 use-after-free/double free**(Linux **v4.15, 2018년** 도입 → 8년 이상 잠복)를 찾아 KASLR 우회 + 커널 임의 함수 호출로 **root**를 얻었고, **"nginx를 도커 컨테이너로 띄운 구성에서도 호스트 root 셸을 얻었다"**고 적었다. 커널 CVE 번호는 패치 전까지 **공개하지 않았다**. 우회책은 SCTP를 쓰지 않는 호스트에서 `/etc/modprobe.d/*.conf`에 **`install sctp /bin/false`**(이미 로드된 모듈은 못 내리고, 커널에 내장된 SCTP는 못 막는다는 한계를 문서가 명시).
- **배포판 패키지로는 아직 안 막힌다.** Debian 트래커(11-19 확인)에서 bookworm `1.22.1-9+deb12u10`·trixie `1.26.3-3+deb13u10`·sid `1.30.6-4`가 전부 **vulnerable**, unstable은 **unfixed**. Ubuntu의 nginx 공지(USN-8955-1)는 **10-26자 다른 CVE들**이다. HN 최상위 댓글도 같은 지적이다 — *"Debian·Ubuntu 패키지 패치가 없으니 당장은 HTTP/2를 끄는 수밖에"*.
- **일주일의 조용한 구간.** HN 최상위 댓글(black3r)은 *"수정 커밋이 공개 레포에 약 일주일 먼저 올라갔고, 커밋 메시지에 제보자(Xint) 이름까지 있었다"*며, 공개 전 **HTTP/2 비활성화 PSA 한 줄**이라도 먼저 냈어야 한다고 비판했다. 어제 Java4Shell, 지난달 PHP2Shell과 같은 팀·같은 방식이다 — **복잡도는 더 이상 발견의 장벽이 아니다**. Wiz 표현 그대로 *"AI가 그 장벽을 녹이고 있다"*.
- <https://www.wiz.io/blog/nginx-http2-rce-vulnerabilities> · <https://xint.io/blog/nginx2root-remote-code-execution> · <https://www.rapid7.com/blog/post/ve-cve-2026-123007-critical-nginx-http-2-integer-underflow-leads-to-rce-fixed/> · <https://nginx.org/en/security_advisories.html> · <https://security-tracker.debian.org/tracker/CVE-2026-123633> · 데모 <https://nginx2root.com/> · HN <https://news.ycombinator.com/item?id=50547374>

### 2. **Anthropic, 공모가 주당 $83 — 시총 약 1.08조 달러로 상장** (11-18 현지)

AI 회사가 **1조 달러**를 넘는 몸값으로 나스닥에 오른다.

| 항목 | 내용 |
| --- | --- |
| 공모가 | **주당 $83** — CNBC는 *"기대 범위를 웃도는 가격"*이라고 보도(정통한 소식통 인용), Axios는 자사 확인이라고 표기 |
| 몸값 | Axios: **초기 시총 약 1조 800억 달러($1.08T)**, 완전 희석 기준 **약 1조 5,000억 달러** |
| 상장 | Nasdaq. CNBC는 **현지 수요일(11-18) 가격 확정**, Axios는 *"목요일에 가격을 확정했다"*고 적어 **요일 표기가 엇갈린다**(둘 다 11-18자 보도) |
| 그날 함께 나온 것 | **Claude Code 2.1.325~2.1.327** — zstd 응답 디코딩 보안 수정, `/install-github-app`이 레포를 바꾸기 전 확인 단계, `claude mcp add-json`이 JSON을 **stdin**에서 받아 토큰이 명령줄에 남지 않게 함. **Linked billing 베타**(플랫폼이 고객 API 키를 보관하지 않고 고객 조직으로 직접 과금, 플랫폼별 월 한도·언링크 가능) |

- 실무자에게 중요한 건 몸값이 아니라 **API 쪽 변화**다. Linked billing은 "고객 키를 받아 쓰는" 기존 구조를 없앤다 — 우리처럼 사내 도구에 모델을 붙일 때 **키 관리·회전 책임**이 줄어드는 방향이고, 동시에 **지출 한도를 플랫폼 단위로 건다**는 발상은 어제 Gemini Tasks의 하드 지출 한도와 같은 결이다.
- <https://www.cnbc.com/2026/11/18/anthropic-ipo-ai-offering-price.html> · <https://www.axios.com/2026/11/18/anthropic-ipo-1-trillion> · <https://claude.com/resources/articles/linked-billing-on-claude-platform> · <https://claudenews.online/en/edition/2026-11-18>

### 3. **Gemini 4 Neon — 3위 지능을 1/3 가격 주장으로, 같은 날 Genie 4도** (HN 294점·283댓글 / Genie 4 165점)

Google이 하루에 **모델**과 **월드 모델**을 같이 냈다.

| 항목 | 내용 |
| --- | --- |
| 가격 | **입력 $3 / 출력 $15**(1M 토큰), **캐시 입력 $0.15(95% 할인)**, 문맥 **100만 토큰** |
| 지능 | Artificial Analysis Intelligence Index **61, 전체 3위**(xhigh), Vals Index **2위(70.9%)**, Arena 신형 **Agent Arena Index 2위(1,336점·4,200+ 세션)** |
| 코딩 | Terminal-Bench 4.1 **65.2%**, FrontierCode 2.0 **46.2%**(기본 high), AA **Coding Agent Index 1위(58.6%)**, SWE-sweep 미니 하네스 12.8% → Antigravity에서 **39.0%** |
| 도메인 | Legal Research Bench·Tax Agent Bench **1위**, Chartography 73.5%, Agents' Last Exam v1.1 75.8%, Terminal-Bench Science 70.5%, HLE-Diamond 71.3%, SEC-bench Pro 90.5%, WebExploitBench 51.26% |
| 비용 주장 | 기본 high에서 **Carbon 대비 작업당 32%↓**, Vals·Agent Arena에서 경쟁 프론티어 대비 **작업당 50~80%↓**, xhigh에서도 36%+ 저렴(모두 자사 주장) |
| 제공 | Gemini API·AI Studio, Gemini for business, **Antigravity**(AI Pro/Ultra), Gemini 앱(Pro/Ultra) |
| Genie 4 | Project Genie(AI Ultra)에서 **세션 중 프롬프트로 월드 실시간 편집**, **실시간 네이티브 오디오** 추가. Genie 3는 세션 60초 → **3분**, 월드 체이닝, **360도 파노라마 그라운딩** |

- 읽는 법은 어제와 같다. **1·2위는 여전히 더 크고 느린 모델**이고, Neon의 자리는 *"프론티어에 닿는 가장 싼 모델"*이다. 표의 수치는 대부분 자사 평가지만 **Artificial Analysis·Vals·Arena는 외부 지표**라는 점이 OpenAI·Microsoft 발표와 다르다.
- <https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-gemini-4-neon/> · <https://blog.google/innovation-and-ai/models-and-research/google-deepmind/genie-4-now-available-project-genie/> · HN <https://news.ycombinator.com/item?id=50552424>

### 4. **Microsoft `agent-framework` PyPI 악성 배포 — import하지 않아도 Python이 뜰 때 실행된다** (GHSA critical, 11-18)

Microsoft의 정식 패키지 이름으로 **인증정보 탈취 버전**이 30분 남짓 올라와 있었다.

| 항목 | 내용 |
| --- | --- |
| 영향 버전 | **`agent-framework` 1.26.1 · 1.26.2** (1.26.0 이하 비영향). 1.26.1은 **11-17 20:35 UTC**(wheel만), 1.26.2는 **21:08 UTC**, 약 **21:30 UTC PyPI에서 제거** |
| 실행 방식 | **`.pth` 시작 훅** — 1.26.1 `agent_framework_meta.pth`, 1.26.2 `af_runtime_diag.pth`. `.pth`는 **인터프리터가 뜰 때마다** 처리되므로 **패키지를 import하지 않아도** 코드가 돈다 |
| 훔치는 것 | 환경변수·인증정보 파일, **Azure 관리 ID**, **Azure DevOps OIDC 토큰**, 1.26.2는 **GitHub Actions OIDC 토큰**까지 |
| 권고 | GitHub Advisory **GHSA-764m-3ppc-5m73**(critical, 11-18 00:31Z 등록). 노출 환경은 **인증정보 교체** + 관리 ID·연합 인증 로그인 기록 점검 |
| 추정 원인 | Wiz는 공격자가 **Microsoft ESRP의 클라이언트 인증서·개인키를 침해**한 것으로 **추정**(최초 획득 경로는 미확인, 확정 아님). Wiz가 본 요청 **50건** 중 데이터 유출 성공은 **3건** — 표본 한계 명시 |

- **핵심은 탐지 방식**이다. 패키지를 지워도 **락파일·CI 캐시·내부 미러에 남은 wheel이 다시 설치**되면 끝이고, 노출된 환경에서는 **인터프리터를 띄우는 것 자체가 실행**이다. 점검할 때 인터프리터를 먼저 띄우지 말고 **`.pth` 파일부터** 찾아야 한다(두 버전의 파일명이 달라 한 이름만 grep하면 한쪽을 놓친다).
- 어제 Java4Shell(자바 생태계), 오늘 nginx·PyPI까지 — **이번 주는 "우리가 직접 쓰지 않는 코드"가 사고를 내는 주**다.
- <https://github.com/advisories/GHSA-764m-3ppc-5m73> · <https://www.wiz.io/blog/malicious-version-of-microsofts-agent-framework-published-to-pypi> · 정리 <https://www.codedeck.kr/posts/microsoft-agent-framework-pypi-malicious-releases-ci-review>

---

## 🤖 AI / LLM & Agentic 동향

**1) Microsoft Ignite 모델 7종 — 전부 "작업당 비용"으로 줄 세웠다 (11-17 발표, 오늘 HN 392점으로 재부상).**
`MAI Code 2`는 GPT-6.1 Sol의 **98% 품질을 40% 가격**, Opus 5.5의 **96%를 5% 가격**. `MAI Cyber 2 Flash`는 CyberGym-E2E에서 **GPT-6 Astra 품질을 2% 미만 비용**으로(출력 토큰 단가 1.1 Flash 대비 **86%↓**, MAI Cyber 계열이 MDASH 스캔의 약 40%를 차지). `MAI Image 2.7`은 Artificial Analysis 이미지 편집 **1위**에 경쟁 모델의 **1/5 가격**, `MAI Realtime 1`은 **분당 $0.01**, `MAI Transcribe 2`는 Dragon Copilot에서 **월 1,000만 진료** 처리. `Decision 1.1`은 **GPT-6.1 Sol보다 50배 빠르고 79배 저렴**(4,000 고객).
더 중요한 건 **MAI Licensed Weights** — 프론티어 MAI 모델을 자사 데이터로 커스터마이즈해 **자기 Azure 환경에서 돌리고 접근을 직접 통제**한다. 그리고 **Maia 200**(2세대 가속기)이 프로덕션에서 최대 모델을 돌리며 **토큰/와트 1.7배**. *"당신의 IQ, 당신의 에이전트, 당신의 가중치, 전부 당신 통제 아래"*가 슬로건이다.
<https://microsoft.ai/news/microsoft-ignite-2026/> · <https://microsoft.ai/news/introducing-mai-licensed-weights/> · HN <https://news.ycombinator.com/item?id=50548021>

**2) DaisyRose-27B — 1,200개 과제·18일·144 H200으로 Terminal-Bench 5.6% → 12.0% (RadixArk, 11-18 · HN 98점).**
Qwen3.8-27B를 RL로 후학습. **세션 최대 100만 토큰**, 시도당 **1.5시간 샌드박스·100회+ 툴 호출·256K 출력 토큰**의 긴 과제 1,200개를 직접 설계(원본 모델이 틀리는 지점만 골라 난이도를 조정, **리워드 해킹 가능한 과제는 제거**). 학습은 자사 비동기 RL 프레임워크 **Miles**(추론 128 + 트레이너 16 GPU)로 **MFU 37%**(오픈소스 트레이너 16% → 같은 학습 **GPU 시간 57%↓**, MoE에서는 6.2~10배, 종단 2배), 총 **63,000 GPU시간**. 샌드박스는 **94.4만 개** 생성, 최대 **1,566 동시**, 10월 말 이후 생성 성공률 **99.95%+**, 인프라 비용 **80%↓**.
가중치는 **BF16·FP8·NVFP4**로 공개. *"적은 과제·적은 GPU로도 긴 호라이즌 에이전트 성능을 올릴 수 있다"*는 주장의 근거는 **데이터 설계**지 연산량이 아니다.
<https://www.radixark.com/blog/daisyrose-27b> · <https://huggingface.co/RadixArk/DaisyRose-27B>

**3) Energy-Based Reasoners — 1.8M 파라미터로 Sudoku-Extreme 99.9%, 추론 계산 345배↓ (Duke·UIUC·Harvard · HN 293점).**
반복 추론 모델이 "언제 멈출지"를 스스로 모르는 문제를, **잠재 상태 위의 에너지 함수**로 바꿨다. 에너지가 **점수**(후보 평가), 그래디언트가 **개선 방향**, 그래디언트 크기가 **정지 신호**, 후보 중 최저 에너지가 **선택**이 된다. 외부 검증기 없이 값 매기기가 된다는 게 요점이다. EqR 최고 정확도에 **반복 7배·샘플 43배 적게** 도달.
주의: **HN 제목은 "385배"인데 논문 TL;DR은 "345배"**다(CodeDeck도 문서 간 수치 차이를 지적). 브리핑에는 저자 표기인 **345배**를 쓴다.
<https://energy-based-reasoners.github.io/> · HN <https://news.ycombinator.com/item?id=50543989>

**4) KPMG "AI 에이전트가 사람 감사팀보다 나은 질문을 했다" — 단, 감사인이 개입한 순간도 있다 (FT 인용, 11-18).**
`Project Exponential`: **AI 에이전트 100여 개**가 FTSE 250 한 회사 재무제표를 **기존 감사팀과 함께** 검토(약 4개월 개발). 에이전트는 정보 수집·분석·문서화·결론 초안·후속 단계 제안까지 하고 **서로 역할을 나눠 협업**했다. 감사 부문 대표 Cath Burnet은 *"여러 건에서 정규 감사팀보다 훨씬 깊이 들어가 추가 조사가 필요한 영역을 찾고, 사람이 제기하지 않은 질문을 했다"*고 말했다. 다만 **한 에이전트가 규제 요건과 회사 문서를 잘못 매칭했을 때 감사인이 개입**했고, 실험은 **정식 감사가 아니다**(파트너 서명은 했지만 회계 승인의 근거로 쓰지 않음). 확대는 **최대 20개 대형 감사**까지, 데이터 품질이 걸림돌.
어제의 FrontierHarness("에이전트는 끝났다고 말하고 틀린다")·Harvard("리뷰가 병목")와 같은 자리다 — **생산은 빨라지고 검증이 비싸진다**.
<https://europeanbusinessmagazine.com/ai-agents-better-questions-kpmg/>

**5) AWS Kiro 봇 — "작업 위임"에서 "프로세스 위임"으로, 권한은 봇 단위로 (11-18).**
봇마다 **이름·메모리·도구·권한·워크스페이스·대화창**이 분리되고, 병렬 세션을 스스로 띄워 감시하며 **결정이 필요한 지점만** 사람에게 묻는다. 조사 봇은 읽기·검색만, **배포 봇은 배포 스크립트를 실행하되 변경마다 승인 요청**. 추가 도구는 **개발자 승인 전엔 못 쓴다**. 메모리도 봇별로 갈라 배포 봇의 학습이 조사 봇에 섞이지 않는다.
<https://www.itbiznews.com/news/articleView.html?idxno=231735>

**6) Microsoft Autopilot은 '초안'과 '실행'을 갈라 놓는다 — 과금도 사용량 기반 (CodeDeck 정리, 11-19).**
11-17부터 **Frontier 고객에게 점진 배포**(전체 Copilot 구독자 대상 정식 출시 아님). 에이전트마다 **Entra Agent ID + 지정 관리자**, IT가 접근 제한·차단, 로그인·작업 이력은 **에이전트 이름으로 기록**. 비용은 **Copilot Credits 사용량 기반**이고 최종 가격은 정식 출시 전 공개 예정 → **현재 안내로 월 운영비를 확정할 수 없다**. 도입 평가에서는 *답장 초안 40개를 검토 대기열로 받았다*는 사례처럼 **초안과 발송, 일정 후보와 확정을 분리해서** 보라고 권한다.
<https://www.codedeck.kr/posts/microsoft-autopilot-frontier-billing-identity-approval> · <https://www.microsoft.com/en-us/copilot/blog/2026/11/17/copilot-at-microsoft-ignite-expanding-whats-possible-with-intelligence-built-for-work/>

**7) OpenAI 모델이 칩을 고친다 — Jalapeño 벡터 유닛 셀 면적 13%↓, AMD GPU IP 14% 개선 (11-18).**
2025년 초 **GPT-4o**부터 자체 추론칩 Jalapeño 설계에 투입. 벡터 유닛 **표준셀 면적 약 13%↓**, 매트릭스 유닛 **10%↓**(곱셈기 재설계로 셀 면적 15%↓ + 지연 2클록→1클록), 실리콘 브링업에서 검증. AMD 협업에서는 **이미 최적화된 기준선 대비 최대 14%** PPA 개선. Synopsys와 **GPT-Synopsys**로 EDA 도구에 프론티어 모델을 붙이는 작업도 진행.
<https://openai.com/index/advancing-chip-design-with-our-models/>

---

## ☕ 백엔드 / Spring / JVM / 아키텍처 엔지니어링

### 릴리스 — **오늘이 Spring 릴리스 트레인인데, 수집 시점엔 아직 안 떴다**

| 프로젝트 | Maven Central 최신(11-19 08:15 확인) | `lastUpdated` | 판정 |
| --- | --- | --- | --- |
| **Spring Boot** | **4.2.0-RC2** | 2026-11-05 | **GA 미출시** |
| **Spring Framework** | **7.1.0-RC2** | 2026-11-05 | **GA 미출시** |
| **Spring AI** | **2.1.0-RC2** | 2026-11-05 | **GA 미출시** |
| **Kotlin** | **2.5.0-RC** (GA 2.4.21) | 2026-11-11 | 정식은 12월 예정 |

- **왜 오늘인가**: Spring이 9-21 공지에서 릴리스 트레인을 *"2주에 걸친 분산 릴리스 → **셋째 월요일 다음 목요일 하루에 전 포트폴리오**"*로 바꿨다. 11월 셋째 월요일은 **11-16**, 그 다음 목요일이 **오늘 11-19**다. 즉 **예고된 날짜는 맞고, 시각이 아직**이다(현지 수요일 저녁 → 한국시간 오늘 밤~내일 새벽에 올라올 것으로 보이나 **확인된 사실 아님**).
- **왜 이렇게 바꿨나**: 3월 이후 **월평균 커뮤니티 보안 제보 약 80건**, 누적 **CVE 160건+ 수정**, 한 달에 **보안 공지 91건**. *"CVE가 며칠이 아니라 몇 시간 안에 악용되는 세상에서 2주짜리 릴리스는 구식"*이라는 것이 Spring의 설명이다. 어제 Java4Shell, 오늘 nginx2root가 정확히 그 근거다.
- **Gradle은 아직 9.8.2다.** 사용자 레포 두 곳(`spring-ai-ops`, `arch-tech`) `gradle-wrapper.properties` 모두 `gradle-9.8.2-bin.zip` 확인 — 어제 권고한 **9.9.1**(Critical 9.3: version catalog 값이 생성 accessor에서 코드로 실행) 갱신이 아직 안 들어갔다.
- <https://spring.io/blog/2026/09/21/releasing-spring-for-modern-challenges> · <https://repo1.maven.org/maven2/org/springframework/boot/spring-boot/maven-metadata.xml> · <https://repo1.maven.org/maven2/org/springframework/ai/spring-ai-bom/maven-metadata.xml>

### 발견의 자동화 — **세 팀이 같은 주에 "10년 묵은 결함"을 냈다**

| 사례 | 잠복 기간 | 찾은 방법 |
| --- | --- | --- |
| PHP2Shell (10월) | — | Wiz |
| Java4Shell (11-17) | 17.0.6 이후 인트린직 | Calif — 발견 1시간·익스플로잇 2시간·**$200 미만** |
| nginx2root (11-18) | **CVE-2026-123007: 2015년부터 11년**, 커널 SCTP: **2018년부터 8년** | Xint — **자동 스캔이 5건 중 4건** |

- 운영 규칙으로 옮기면: ① **패치 속도가 아니라 노출 축소 속도**가 지표다(nginx는 HTTP/2 하나 끄면 2건이 막힌다). ② **버전 문자열로 패치 여부를 판정하지 않는다** — 배포판은 백포트하고 `nginx -v`는 그대로다. **전체 패키지 버전**을 공지와 대조해야 한다. ③ **의존하는 것뿐 아니라 앞단에 있는 것**을 목록에 넣는다(리버스 프록시·메일 프록시·DNS 리졸버).
- <https://xint.io/blog/nginx2root-remote-code-execution> · <https://www.wiz.io/blog/nginx-http2-rce-vulnerabilities>

---

## 🔐 Hacker News / GeekNews 화제 & 보안·인프라

**1) Quad9, 프랑스에서 **11-24** 철수 확정 — 어제 "철수 결정"이 날짜로 굳었다 (HN 551점·204댓글).**
11-24(화)부터 프랑스에서 DNS 응답을 멈춘다. 근거는 **스포츠법 L.333-10**(2021-1382)과 2026-725(프로 스포츠법, **Arcom 실시간 자동 차단** 추가 — *"Arcom 요원이 개별 차단을 사전 승인하지 않는다"*). 12월 2024 Canal+ 3건, 2026-05-13 beIN(WTA 테니스, 6개 → **58개 도메인**), 2026-03 항소법원이 Google·Cloudflare·Cisco의 항소 5건 기각(*"어떤 필터링도 우회될 수 있고, 그 가능성이 조치를 무효로 만들지 않는다"*), 2026-04 EU 자금 **DNS4EU**에도 차단 명령, 2026-07 시즌용 14건. **프랑스에 유사 소송 약 20건 대기** 중.
피해자는 개인이 아니라 **조직**이다 — Quad9가 트래픽을 본 프랑스 네트워크에 **DINUM(총리 산하 디지털국), France Travail, CEA, 파스퇴르 연구소, RENATER, EDF, SNCF, AP-HM(마르세유 병원), France Télévisions, Thales, Stormshield**가 있다. 리졸버가 사라져도 **오류 화면이 뜨지 않는다** — *"인터넷이 그냥 멈춘다"*(Cloudflare). Mullvad는 11-02 자체 암호화 DNS를 닫고 기본값을 Quad9으로 돌린 상태라, 프랑스 사용자는 3주 만에 두 번째 이사를 하게 됐다.
<https://techstackups.com/articles/quad9-leaves-france-dns-blocking/> · <https://quad9.net/news/blog/au-revoir-france/> · HN <https://news.ycombinator.com/item?id=50545958>

**2) 그 밖에 오늘 HN에서 눈에 걸린 것 (11-18~19)**

| 항목 | 왜 볼 만한가 | 링크 |
| --- | --- | --- |
| **Inside Deutsche Bank** (503점) | **1,500만+ 파일** 유출 기반 국제 탐사보도 — 제재 모니터링·내부감사·고위험 고객 존치 판단 조직까지. 기술 뉴스는 아니지만 "규제 준수가 마케팅 문구였던" 사례 | <https://www.inside-db.com/> |
| **KVM CVE-2026-99312** (164점) | x86 INIT 에뮬레이션 시 MMU 컨텍스트 리셋 누락. **CVE 페이지 본문이 스크립트 렌더링이라 상세 미확인** — 영향 범위는 벤더 공지를 봐야 한다 | <https://www.cve.org/CVERecord?id=CVE-2026-99312> |
| **ExitPath** (Show HN 133점) | Slack·Notion·Microsoft 365에서 **나오는** 오픈소스 경로 모음. 사내 지식이 특정 SaaS에 잠기는 문제를 반대로 뒤집은 도구 | <https://exitpath-ruddy.vercel.app/> |
| **Cambridge 사전 올해의 단어 'human'** (220점) | 2026년 사람들이 가장 많이 찾은 단어. AI 시대의 언어적 반작용 | <https://www.cam.ac.uk/news/cambridge-dictionary-reveals-word-of-the-year-2026> |
| **Cuttlefish on Mac** (21점) | Meta가 **Apple Silicon에서 Android 가상 디바이스 프레임워크**를 돌리는 방법 공개(11-13). 로컬 테스트 환경을 ARM 맥으로 모으는 흐름과 같은 방향 | <https://engineering.fb.com/2026/11/13/android/cuttlefish-on-mac/> |
| **The Magic of Partial Evaluation** (83점) | 프로그램을 "아는 부분"으로 미리 접어 버리는 고전 기법. 빌드 시간·설정 폭발을 줄이는 사고법 | <https://jimmyhmiller.com/magic-of-partial-evaluation> |
| **x86 CPU: 4004 → 486** (126점) | 아키텍처 학습용 연대기 | <https://pikuma.com/blog/history-of-x86-4004-to-486> |
| **Voyager 1 분해도** (133점) | 단일 메모리 칩까지 내려가 설명하는 인터랙티브 | <https://rshekhar.com/voyager/> |

---

## 📺 추천 테크 영상·읽을거리

### ⭐ **CMU CS Learn — 오늘(11-19)은 "AI 에이전트"의 날**

| 공개 | 과목 | 강사 |
| --- | --- | --- |
| **11-19(오늘)** | **11-768 AI Agents** | **Daniel Fried · Graham Neubig** |
| **11-19(오늘)** | **17-645 Machine Learning in Production** | Christian Kästner · Bogdan Vasilescu |
| 11-18(어제) | 17-635 Software Architectures / 17-643 Quality Management | Ashok·Pavetti / Gennari·**Timperley** |

- Neubig는 에이전트 툴 사용·평가 쪽에서 가장 많이 인용되는 연구자 중 하나다. 오늘 공개분은 **에이전트 설계와 평가를 강의 형태로** 듣는 자료라, 우리 Eval 41문항·리뷰 봇 설계의 기준을 잡는 데 바로 쓸 수 있다.
- <https://www.cs.cmu.edu/learn/>

### CodeDeck — 11-19자 7편

| 글 | 링크 |
| --- | --- |
| Microsoft Autopilot Frontier — 과금 조건·에이전트 승인 범위 | <https://www.codedeck.kr/posts/microsoft-autopilot-frontier-billing-identity-approval> |
| DaisyRose-27B — 비교 버전·정밀도 선택·256K 문맥·NVFP4 조건 | <https://www.codedeck.kr/posts/daisyrose-27b-evaluation-context-and-precision> |
| EBR — 345배 FLOPs 감소의 비교 조건과 문서 간 수치 차이 | <https://www.codedeck.kr/posts/ebr-energy-reasoning-compute-and-release-scope> |
| **Microsoft agent-framework 악성 배포** — `.pth` 훅과 CI 점검 범위 | <https://www.codedeck.kr/posts/microsoft-agent-framework-pypi-malicious-releases-ci-review> |
| RepoFactory — 저장소 학습 환경 10,159개 중 공개는 200개 | <https://www.codedeck.kr/posts/repofactory-coding-environments-release-scope> |
| ForceHarness — 로봇 접촉 제어를 모델 밖 도구로(실물 60회 중 54회) | <https://www.codedeck.kr/posts/forceharness-contact-control-physics-translation> |
| Qwen3.8 pi 어댑터 — 체크포인트 선택 조건 | <https://www.codedeck.kr/posts/qwen38-pi-adapter-evaluation-checkpoint-conditions> |

### 읽을거리·영상
- **Xint nginx2root 기술편 + 데모 영상** — 5개 CVE별 트리거·힙 펑슈이, 그리고 **도커 컨테이너에서 호스트 root까지 가는 체인**. 노출 점검 명령(`nginx -v`, `nginx -V | grep http_v2_module`, `nginx -T | grep http2`)이 그대로 들어 있다. <https://xint.io/blog/nginx2root-remote-code-execution> · <https://nginx2root.com/>
- **Rapid7 분석** — CVE-2026-123007 정수 언더플로 경로 정리 <https://www.rapid7.com/blog/post/ve-cve-2026-123007-critical-nginx-http-2-integer-underflow-leads-to-rce-fixed/>
- **Spring AGNTCon 2026 인터뷰** — James Ward × Alexander Ioffe(<https://www.youtube.com/watch?v=cnXMqAlst1Y>), × Ben Hale(<https://www.youtube.com/watch?v=n6PFNJBol70>)

---

## 📌 내 프로젝트(youtu-lib·arch-tech·spring-ai-ops·PDF RAG)에 바로 매핑되는 4가지

**1) nginx2root — 우리 포털은 5건 중 어느 조건에도 해당하지 않는다. 다만 "왜"를 남겨 둔다.**

- **사실(확인)**: `arch-tech/infra/portal/Dockerfile`은 `FROM nginx:alpine`, `infra/portal/nginx.conf`·`conf.d/default.conf`는 `listen 80 default_server` 하나뿐이고 **`http2` 파라미터·`http2 on`·HTTP/3이 없다**(grep 확인). 메일 프록시 모듈도 없다. → **CVE-2026-123007·123633(HTTP/2), 121810(HTTP/3), 113425(메일) 조건 미충족.**
- **주의할 지점**: 이 설정은 **`resolver 127.0.0.11` + 변수 `proxy_pass`**를 쓴다(다른 컨테이너가 늦게 떠도 게이트웨이가 죽지 않게 하려고). CVE-2026-123634의 조건은 `resolver` + **요청에서 유래한** `proxy_pass` 목적지 + `mirror`(`mirror_request_body off`)다. 우리 변수는 전부 **설정 상수**(`set $core_api arch-api:8085;`)이고 `mirror`가 없어 **미해당**이지만, 앞으로 `$host`·`$arg_*` 같은 **요청 값을 업스트림 주소에 쓰는 순간 이 조건이 열린다**. 그 규칙을 `infra/portal` README에 한 줄 못 박아 둘 만하다.
- **이미지가 플로팅이다**: `nginx:alpine`은 **11-18 19:53 UTC에 갱신**됐고 같은 분에 `1.31.9-alpine` 태그도 올라왔다(Docker Hub 확인). 지금 `docker compose build --pull arch-portal` 하면 1.31.9를 받는다. 재현성을 위해 **`FROM nginx:1.31.9-alpine`으로 고정**하고, 고정한 뒤에도 `docker run --rm nginx:1.31.9-alpine nginx -v`로 확인하는 습관이 맞다.
- **커널 체인이 우리에게 의미하는 것**: 우리 스택은 전부 **맥 스튜디오의 리눅스 VM(OrbStack) 안 도커**다. nginx RCE가 성립해야 체인이 시작되므로 지금은 위험이 아니지만, **"프록시 컨테이너 하나 뚫리면 호스트와 그 위 모든 컨테이너가 끝난다"**는 사실은 그대로다. OrbStack 커널에 SCTP가 존재하는지는 확인하지 못했다 — 궁금하면 `docker run --rm --privileged alpine lsmod | grep sctp`로 **모듈 적재 여부**부터 보면 된다(커널 내장이면 보이지 않는다). 근본 대응은 **공인 인터넷에 포털을 직접 노출하지 않는 것**(현재 Tailscale 경유 구성)이다.

**2) PyPI 공급망 — 우리 쪽 노출은 "없음"이 아니라 "아직 안 봤음"이다.**

- **사실**: `poc-py`는 **uv 워크스페이스**(`pyproject.toml` 7개 + `uv.lock`)다. `agent-framework`가 들어 있는지는 **확인하지 못했다**(코드 검색 결과 없음 — 다만 GitHub 코드 검색은 인덱싱 지연이 있어 부정 증거로 쓰면 안 된다).
- **점검 순서**: ① `grep -n "agent-framework" uv.lock **/pyproject.toml` ② `find . -path "*site-packages*" -name "*.pth" | xargs grep -l "agent_framework_meta\|af_runtime_diag"` ③ CI 로그에서 `agent-framework==1.26.[12]` 설치 흔적 ④ 내부 미러·CI 캐시에 남은 wheel.
- **우리 워크플로에 직결되는 부분**: 이 악성코드가 노린 것 중 하나가 **GitHub Actions OIDC 토큰**이다. 우리는 self-hosted 러너에서 `ci/infisical-env.sh`로 **Infisical 시크릿을 꺼내 배포**한다 — 워크플로의 `permissions:`가 `id-token: write`로 열려 있는 곳이 있다면, 그 워크플로가 **서드파티 코드를 설치하는 단계**와 같은 job에 있는지 확인해야 한다. **빌드·테스트 job과 배포 job의 권한을 분리**하는 것이 이번 사고의 가장 값싼 대응이다.
- **일반 규칙**: 락파일이 있어도 **CI가 `pip install -U`나 미고정 요구사항을 쓰면 무의미**하다. uv를 쓴다면 `uv sync --frozen`을 CI 기본값으로.

**3) 승인 경계 — 우리가 11-18에 만든 것이 업계 기본값과 같은 모양이었다.**

- **사실**: 어제 `spring-ai-ops`에 **#84**("승인된 실행의 연결 — 승인을 소비한 쓰기만 1회 실행하고 감사로그에 남긴다")와 **#85**("cc 실행도 승인을 소비하고, 실행기의 사유가 틀리지 않게")가 머지됐다. 즉 **승인 1회 = 쓰기 1회**, 그리고 **감사로그**.
- **같은 날 업계가 같은 곳을 짚었다**: AWS Kiro 봇은 **추가 도구를 승인 없이 못 쓰고, 배포 봇은 변경마다 승인**을 요청한다. Microsoft Autopilot은 **초안과 발송, 일정 후보와 확정을 분리**하고 **에이전트별 Entra Agent ID + 지정 관리자 + 작업 이력**을 남긴다. Gemini Tasks는 **하드 지출 한도**를 건다. 세 곳 모두 *"승인은 상태가 아니라 소모되는 자원"*으로 다룬다.
- **다음 한 걸음 제안**: 승인 소비를 이미 감사로그에 남기고 있으니, **"누가 승인했는가"와 "무엇이 소비했는가"를 같은 트레이스 ID로 묶어** 두면 나중에 사고 조사에서 바로 답이 나온다(OpenTelemetry span attribute 두 개면 된다). 이건 어제 Harvard·FrontierHarness가 지적한 **"에이전트가 끝났다고 말할 때 검증"** 문제와도 같다 — 승인 소비 기록은 그 검증의 최소 단위다.

**4) 오늘 Spring GA가 뜨면 할 일 — 그리고 아직 안 끝난 CI 하나.**

- **사실**: 세 프로젝트 모두 **RC2 상태**(11-05 이후 갱신 없음). 트레인은 **오늘(11-19)**. Gradle 래퍼는 두 레포 다 **9.8.2**로, 어제 권고한 9.9.1 반영 안 됨.
- **사실(우리 CI)**: `spring-ai-ops`의 **`perf-isolation.yml`이 8연속 실패**다. 11-17 16:39Z부터 11-18 00:26Z까지 전부 failure이고, **#83에서 `job env`의 `runner` 컨텍스트를 제거한 수정 뒤에도**(00:26:33 커밋 → 00:26:36 실행) **여전히 실패**했다. 같은 시각 main 배포 워크플로는 success. **원인 미확인 상태로 남아 있다.**
- **순서 제안**: ① `perf-isolation`은 **`gh run view <run-id> --log-failed`가 "log not found"**였으니, 이번엔 **워크플로를 `workflow_dispatch`로 직접 기동**해 job 단위 로그를 받아 보는 쪽이 빠르다(러너 매칭 문제면 job이 queued에서 멈춘 흔적이 남는다). ② Gradle **9.9.1 래퍼 갱신은 세 레포 동일 커밋**으로 먼저(보안 패치라 트레인과 독립). ③ GA가 뜨면 **브랜치 하나에서만** 4.2.0 기동 + Eval 41문항 회귀, Spring AI 2.1.0의 **Anthropic SDK 3.4.0이 `temperature`·`topP`·`topK`를 거부**하는 문제로 샘플링 파라미터 호출부 전수 확인.

---

## 🗂 GitHub 소스 현황 (@yuripapago, 2026-11-19 08:15 KST 기준)

**알림·요청**: `review-requested:@me` 0건 · 열린 PR **3건**(spring-ai-ops #3 테스트 샘플, arch-tech #33·#34 부하 테스트 표본 — 둘 다 "머지 금지") · 열린 이슈 **55건**(spring-ai-ops **39**, arch-tech **16** — 그중 **49건이 hwajjdev 리뷰 이슈**, 34 + 15).

| 레포 | 공개 | 마지막 푸시(UTC) | 11-18 활동 |
| --- | --- | --- | --- |
| **spring-ai-ops** | private | **11-18 13:44** | **커밋 5건.** `4eb320d` #82(전날 야간) → **`c0e0fd9` #83** perf-isolation 워크플로 무효 파일 원인 수정(`job env`의 `runner` 컨텍스트 제거) → `3dbca0c` 같은 수정 반영 → **`bf42e8f` #84** 승인된 실행의 연결(승인 소비·감사로그) → **`f17931e` #85** Step 3 리뷰 수정(cc 실행도 승인 소비, 실행기 사유 정정) |
| **arch-tech** | private | **11-18 04:50** | 커밋 1건 — **`de0f6e7` #35** `feat(hexagonal)`: 주문 예제를 **자동차 보험 비교 도메인**으로 전환 |
| **youtu-lib** | public | 11-17 23:21 | 11-18 브리핑(`95d46f9`) — 오늘 이 문서로 갱신 |
| **memo** | private | 11-17 00:09 | 신규 0 |
| **poc-pickle** / **poc-py** | private | 11-13 / 11-12 | 신규 0 |

**CI**: spring-ai-ops **deploy success** 4회(11-18 00:26·00:30·12:59·13:44 — #84·#85 머지 직후 포함) · **`perf-isolation.yml` failure 8연속**(최신 11-18 00:26:36Z, **#83 수정 후에도 실패, 원인 미확인**). arch-tech **deploy success**(11-18 04:50, `#35` 머지 직후).

**해석**: 어제(#64~#82, 19커밋)의 폭발 이후 **하루 5커밋으로 속도를 줄이고 "승인 경계"라는 한 주제에 집중**한 날이다. arch-tech는 아키텍처 실험(헥사고날 도메인 전환)으로 방향을 잡았다. 리뷰 부채는 **49건 그대로** — 늘지도 줄지도 않았다.
