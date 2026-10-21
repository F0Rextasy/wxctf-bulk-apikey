<div align="center">

```
██╗    ██╗██╗  ██╗ ██████╗████████╗███████╗
██║    ██║╚██╗██╔╝██╔════╝╚══██╔══╝██╔════╝
██║ █╗ ██║ ╚███╔╝ ██║        ██║   █████╗
██║███╗██║ ██╔██╗ ██║        ██║   ██╔══╝
╚███╔███╔╝██╔╝ ██╗╚██████╗   ██║   ██║
 ╚══╝╚══╝ ╚═╝  ╚═╝ ╚═════╝   ╚═╝   ╚═╝
```

# wxctf-bulk-apikey

**API 키로 Wxctf Ai 계정을 대량 생성 — OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-17-of-them) · [🧪 API 사용하기](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>


---

## ✨ 이 도구가 존재하는 이유

Wxctf Ai(ai.wxctf.com)는 가입을 **개방형**으로 유지하는 New API 포크입니다 — 캡차도, OAuth도 없고 이메일 코드만 필요합니다 — 그리고 스스로 비용을 충당합니다: 모든 계정이 **일일 출석 체크**를 청구할 수 있어 잔액에 실제 할당량이 적립되므로, 각 키는 카드 없이도 자금이 채워진 상태로 태어납니다.

두 가지 점에서 특별합니다. 첫째, 가입 전체가 **API 전용**입니다: 일반 gmail 주소, 인증 메일 한 통, 폼 POST 하나면 끝이고 브라우저가 전혀 필요 없습니다(2026-10-21 측정: 약 7분에 5개 계정, 이어서 두 배치로 10개 더). 둘째, 패널이 **주소 형식에 엄격합니다**: `gmail.com`, `outlook.com`, `qq.com`은 허용하지만 로컬 파트에 점이나 `+`가 있으면 "특수 문자"로 거부하므로 plus/dot 별칭은 여기서 쓸모가 없고, **일반** `@gmail.com` 메일함이 작동하는 경로입니다.

다른 함정들도 똑같이 구체적이며 모두 도구에 반영되어 있습니다: 가입 호출은 코드를 요청한 것과 **동일한 쿠키 저장소**를 **새로운** 코드(10분 TTL)와 함께 전달해야 하고, 비밀번호는 최소 8자여야 하며, 토큰 그룹은 활성 상태인 `Default`여야 합니다 — `default`로 생성한 토큰은 릴레이에서 `403 Group default has been deprecated`를 반환합니다.

## 🎁 무료 티어 — 얼마나 받을 수 있나

| 항목 | 값 |
| --- | --- |
| 가입 보너스 | **없음** (가입 직후 `quota = 0`) |
| 무료 티어 | **일일 출석 체크** — `POST /api/user/checkin`이 `quota_awarded`를 반환 |
| 측정된 지급량 | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** 할당량 (계정 10개, 2026-10-21) |
| USD 환산 | **계정당 하루 $0.012–$0.087** (500 000 할당량 = $1) — 청구마다 무작위, **만료되지 않음** |
| 일일 재고 | 계정 10개 ⇒ **259 288 할당량 ≈ 하루 $0.5186**, 계정당 하루 한 번(`checkin_date` 강제) |
| 카드 / 전화 / OAuth | **모두 불필요** — 이메일 코드만, `turnstile_check: false` |
| 모델 | 활성 id 17개 (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| 속도 제한 | 공개되지 않음; 30회 연속 호출도 정상 응답 |

> 💡 무료 티어는 Wxctf Ai가 정하며 변경될 수 있습니다. 실시간 수치는 [ai.wxctf.com](https://ai.wxctf.com)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하게 하세요:

```bash
```bash
# keys.txt의 모든 키: 모델 목록과 실제 완성 한 번
node src/cli.js --verify-only

# 수동으로 (키에는 `sk-` 접두사가 없음)
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer UCm8bnazrmux3wRN…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

게이트는 `content || reasoning_content`를 계산하므로, 예산을 사고에 다 써버린 추론 모델이라도 살아있는 키임을 증명합니다.
```

## 🤖 모델 — 17개 전부

이 배치의 키로 `GET /v1/models` 호출 (2026-10-21) — **id 17개**:

| 패밀리 | Id |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| 기타 | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing`는 18개 행을 나열하며(한 id는 가격이 있지만 제공되지 않음) **엄격한 $0 행은 없습니다** — 무료 티어는 출석 체크이지 무료 모델이 아닙니다. 가장 저렴한 텍스트 모델은 `deepseek-v4-flash`입니다.

## 🧪 API 사용 방법

베이스 URL **`https://ai.wxctf.com/v1`** — OpenAI 호환; 키는 48자이며 **`sk-` 접두사가 없고** `Authorization: Bearer` 헤더에 들어갑니다.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="UCm8bnazrmux3wRN…")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

할당량은 한 계정의 모든 키가 공유하므로, 출석 체크가 그 계정의 일일 예산을 결정합니다: **키당 하루 한 계정**이 자연스러운 형태입니다.

## 🛠 빠른 시작

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # 선택 사항 — 없으면 도구는 직접 연결로 실행
node src/cli.js --screen                     # 패널에 대해 출구 검증
node src/cli.js --count 5                    # 자금이 채워진 키 5개 (브라우저 불필요)
node src/cli.js --verify-only                # 모든 키 재검증
node src/cli.js --connect-9router            # 로컬 9Router에 등록
```

키당 계정 하나: 도구는 일반 gmail 메일함을 만들고, 코드를 요청하고, 기다린 뒤, 동일한 쿠키 저장소로 가입하고, 로그인하고, **일일 출석 체크를 청구하고**, `Default` 그룹에서 토큰을 발급하고, 키를 공개한 다음에야 실제 완성으로 증명합니다.

### 🖱️ 런처

런처는 프록시 목록(선택 사항)과 계정 수를 묻고, 둘 다 기억한 뒤 CLI를 실행합니다 — `--verify-only`, `--screen`, 9Router 커넥터도 마찬가지입니다.

## ✅ 작동 검증됨 — 모든 키가 테스트됨

아래 모든 검사는 **2026-10-21**에 이 패키지 자체의 CLI(`src/cli.js`)로, 공용 출구를 통해 실행되었습니다(루프에 VPN 없음).

| 검사 | 결과 |
| --- | --- |
| 실제 흐름에서의 가입 | **브라우저 불필요** — 패널에 캡차가 없음(`turnstile_check: false`) |
| 일반 gmail 경로 | Emailnator, **직접** 읽음; 점/`+`는 패널이 "특수 문자"로 거부 |
| 코드 메일 | `noreply@<panel-mail>`에서 «Wxctf Ai email verification», **1–25초**, 6자 코드, **10분** 유효 |
| 가입 | 동일한 쿠키 저장소 + 새 코드 + 8자 이상 비밀번호로 **200 `{"success":true}`** |
| 무료 티어 | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **계정당 하루 $0.012–$0.087** |
| 일일 재고 | 계정 10개 ⇒ **259 288 할당량 ≈ 하루 $0.5186** (계정당 하루 한 번) |
| 토큰 그룹 | **`Default`** — `default`로 생성한 토큰은 `403 Group default has been deprecated`를 반환 |
| 키 경로 | 목록에는 마스킹되어 표시됨; `POST /api/token/<id>/key`가 48자 평문을 반환 |
| 키당 `GET /v1/models` | **200**, id 17개 |
| 키당 `POST /v1/chat/completions` | 콘텐츠와 함께 **200** — **10/10 키 LIVE** (`--verify-only`), 출구 측 실패 후 한 키는 2/3 출구에서 재확인됨 |
| 종단 간 속도 | **자금이 채워진 키당 약 50–90초** (메일 → 가입 → 출석 체크 → 키 → 증명) |


## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| 메일함 | 일반 https를 통한 Emailnator | **일반** gmail(`plain` 형식, 점 제거); 패널은 점과 `+`를 거부 |
| 코드 | `GET /api/verification?email=` | `noreply@<panel-mail>`에서 약 1–25초 내 도착, 제목 «Wxctf Ai email verification», 6자 코드, 10분 유효 |
| 가입 | `POST /api/user/register` | 본문 `{username,password,password2,email,verification_code,aff_code}`; **동일한 쿠키 저장소**, **새** 코드, 비밀번호 8자 이상 |
| 세션 | `POST /api/user/login` | 콘솔 호출용 `data.access_token` (Bearer JWT) |
| 자금 | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — 무료 티어, 키를 증명하기 전에 청구됨 |
| 키 | `POST /api/token/` → `POST /api/token/<id>/key` | 그룹 **`Default`** (`default`는 폐기됨 → `403 Group default has been deprecated`); 목록은 키를 마스킹해 표시하고 `/key`가 평문을 반환 |
| 증명 | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; 완성이 200을 반환할 때만 키가 기록됨 |

## ⚙️ CLI 옵션

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| 플래그 | 기본값 | 의미 |
| --- | --- | --- |
| `--count N` | `1` | 생성할 계정 수 |
| `--pool FILE` | 있으면 `proxies.txt` | 출구, 한 줄에 하나 (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | 단일 출구 고정 |
| `--exits K` | `6` | 계정당 시도할 출구 후보 |
| `--model ID` | 가장 저렴한 텍스트 모델 | 라이브 게이트에 사용할 모델 |
| `--verify-only` | 꺼짐 | `keys.txt` 재검증 |
| `--screen` | 꺼짐 | 패널에 대해 출구 목록 검증 |
| `--connect-9router` | 꺼짐 | 이 CLI 옆에서 9Router 커넥터 실행 |

## 🛰️ 프록시 뒤에서

**선택 사항.** 풀이 없으면 도구는 `DIRECT (no proxy)`를 출력하고 이 머신에서 패널을 직접 호출합니다; 풀이 있으면 모든 패널 요청이 목록의 출구에 고정되고 죽은 출구는 자기 시도만 소모합니다.

메일함은 의도적으로 **직접** 읽습니다: 패널의 메일 게이트는 API 호스트와 분리되어 있고, TLS를 가로채는 출구를 통해 Emailnator를 프록시하면 `unable to get local issuer certificate`가 반환됩니다(이 때문에 배달된 코드가 "메일이 도착하지 않음"처럼 보인 적이 있습니다). 리더는 IPv4를 강제하는데, 이 빌드가 IPv6를 먼저 확인하고 IPv6 경로가 없기 때문입니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | 라이브 증명된 키가 한 줄에 하나 — git 무시됨 |
| `accounts.json` | 계정당 JSON 레코드 하나 (메일함, 출구, 할당량, 출석 체크 지급량, 그룹, 키, 증명) — git 무시됨 |
| `proxies.txt` | 사용자의 출구 — git 무시됨 |
| `out/` | 실행 로그 및 계정별 레코드 — git 무시됨 |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅되는 OpenAI 호환 라우터입니다:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. 모델은 `wxctf/<model id>`로 제공되며 라우터는 노드의 모든 키에 걸쳐 부하를 분산합니다.

## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 제공자 | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 제공자가 주는 것 | 키당 계정 하나 — 일일 출석 체크가 모든 키에 자금을 채움 (하루 약 $0.012–0.087, 카드 없음, 캡차 없음) |
| 🔑 키 하나로 얻는 것 | 자체 계정에 한정된 API 키 하나: 아래 엔드포인트의 모델 id 17개, 사용자 플랜의 한도 내에서. 할당량은 **계정**에 있으므로, 이 도구가 키당 계정 하나를 만드는 것입니다 |
| 🌐 API 형태 | OpenAI 호환: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 데이터 | ai.wxctf.com과 사용자가 설정한 메일함 백엔드만 접촉됩니다; 키는 사용자의 머신에 남습니다 |

## 🐞 이슈

Wxctf Ai가 주소 정책, 가입 형식, 토큰 그룹 또는 카탈로그를 변경하면, 도구는 해당 단계에서 멈추고 그 사실을 알립니다. 명령과 정확한 출력을 포함해 **[이슈를 열어주세요](../../issues)**.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ 계정 위험과 최소화
