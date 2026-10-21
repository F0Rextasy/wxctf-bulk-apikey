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

**Bulk-create Wxctf Ai accounts with API keys — OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-17-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

Wxctf Ai (ai.wxctf.com) is a New API fork that keeps registration **open** — no captcha, no OAuth, e-mail code only — and pays for itself: every account can claim a **daily check-in** that lands real quota on the balance, so each key is born funded without a card.

Two things make it unusual. First, the whole sign-up is **API-only**: a plain-gmail address, one verification mail and a form POST, no browser at all (measured 2026-10-21: five accounts in ~7 minutes, then ten more in two batches). Second, the panel is **strict about address shape**: it accepts `gmail.com`, `outlook.com` and `qq.com`, but refuses anything with a dot or a `+` in the local part as a “special character”, so plus/dot aliases are useless here and a **plain** `@gmail.com` box is the working rail.

The other traps are equally specific and all encoded in the tool: the register call must carry the **same cookie jar** that requested the code with a **fresh** code (10-minute TTL), the password must be at least 8 characters, and the token group must be the live `Default` — a token created with `default` answers `403 Group default has been deprecated` on the relay.

## 🎁 Free tier — how much you get

| What | Value |
| --- | --- |
| Signup bonus | **none** (`quota = 0` right after registration) |
| Free tier | **daily check-in** — `POST /api/user/checkin` returns `quota_awarded` |
| Measured awards | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** quota (10 accounts, 2026-10-21) |
| In USD | **$0.012–$0.087 per account per day** (500 000 quota = $1) — randomised per claim, **never expires** |
| Daily stock | 10 accounts ⇒ **259 288 quota ≈ $0.5186 per day**, once per account per day (`checkin_date` is enforced) |
| Card / phone / OAuth | **none of them** — e-mail code only, `turnstile_check: false` |
| Models | 17 live ids (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Rate limit | not published; a 30-call burst is answered normally |

> 💡 The free tier is set by Wxctf Ai and can change. Check the live numbers at [ai.wxctf.com](https://ai.wxctf.com) — and let the tool's own gate decide:

```bash
```bash
# every key in keys.txt: the model list plus one real completion
node src/cli.js --verify-only

# by hand (keys carry no `sk-` prefix)
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

The gate counts `content || reasoning_content`, so a reasoning model that spends its budget on thinking still proves a live key.
```

## 🤖 Models — all 17 of them

`GET /v1/models` on a key of this batch (2026-10-21) — **17 ids**:

| Family | Ids |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Others | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` lists 18 rows (one id is priced but not served) and **no strict-$0 row** — the free tier is the check-in, not a free model. The cheapest text model is `deepseek-v4-flash`.

## 🧪 How to use the API

Base URL **`https://ai.wxctf.com/v1`** — OpenAI-compatible; keys are 48 characters with **no `sk-` prefix** and go in an `Authorization: Bearer` header.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

Quota is shared by every key of an account, so the check-in gates the daily budget of that account: **one account per key per day** is the natural shape.

## 🛠 Quick start

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/cli.js --screen                     # vet the exits against the panel
node src/cli.js --count 5                    # five funded keys (no browser needed)
node src/cli.js --verify-only                # re-prove every key
node src/cli.js --connect-9router            # register them with a local 9Router
```

One account per key: the tool creates a plain-gmail mailbox, requests the code, waits for it, registers with the same cookie jar, logs in, **claims the daily check-in**, mints a token in the `Default` group, reveals the key and only then proves it with a real completion.

### 🖱️ The launcher

The launcher asks for the proxy list (optional) and the account count, remembers both and runs the CLI — plus `--verify-only`, `--screen` and the 9Router connector.

## ✅ Verified working — every key is tested

Every check below ran on **2026-10-21** from this package's own CLI (`src/cli.js`), over public exits (no VPN in the loop).

| Check | Result |
| --- | --- |
| sign-up in a real flow | **no browser needed** — the panel has no captcha (`turnstile_check: false`) |
| plain-gmail rail | Emailnator, read **directly**; dots/`+` are refused by the panel as “special characters” |
| code mail | «Wxctf Ai email verification» from `noreply@<panel-mail>`, **1–25 s**, 6-char code, valid **10 minutes** |
| register | **200 `{"success":true}`** with the same cookie jar + a fresh code + a ≥8-char password |
| free tier | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **$0.012–$0.087 per account per day** |
| daily stock | 10 accounts ⇒ **259 288 quota ≈ $0.5186/day** (once per account per day) |
| token group | **`Default`** — a token created with `default` answers `403 Group default has been deprecated` |
| key route | the list shows it masked; `POST /api/token/<id>/key` returns the 48-char plaintext |
| `GET /v1/models` per key | **200**, 17 ids |
| `POST /v1/chat/completions` per key | **200** with content — **10/10 keys LIVE** (`--verify-only`), one key re-confirmed on 2/3 exits after an exit-side failure |
| end-to-end speed | **~50–90 s per funded key** (mail → register → check-in → key → proof) |


## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| mailbox | Emailnator over plain https | a **plain** gmail (`plain` form, dots stripped); the panel refuses dots and `+` |
| code | `GET /api/verification?email=` | arrives in ~1–25 s from `noreply@<panel-mail>`, subject «Wxctf Ai email verification», 6-char code, valid 10 minutes |
| register | `POST /api/user/register` | body `{username,password,password2,email,verification_code,aff_code}`; **same cookie jar**, **fresh** code, password ≥8 |
| session | `POST /api/user/login` | `data.access_token` (Bearer JWT) for the console calls |
| funds | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — the free tier, claimed before the key is proved |
| key | `POST /api/token/` → `POST /api/token/<id>/key` | group **`Default`** (`default` is deprecated → `403 Group default has been deprecated`); the list shows the key masked and `/key` returns the plaintext |
| proof | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; the key is written only when the completion answers 200 |

## ⚙️ CLI options

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Flag | Default | Meaning |
| --- | --- | --- |
| `--count N` | `1` | accounts to mint |
| `--pool FILE` | `proxies.txt` if present | exits, one per line (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | pin a single exit |
| `--exits K` | `6` | exit candidates tried per account |
| `--model ID` | cheapest text model | model used for the live gate |
| `--verify-only` | off | re-prove `keys.txt` |
| `--screen` | off | vet the exit list against the panel |
| `--connect-9router` | off | run the 9Router connector next to this CLI |

## 🛰️ Behind a proxy

**Optional.** Without a pool the tool prints `DIRECT (no proxy)` and calls the panel straight from this machine; with one, every panel request is pinned to an exit from the list and a dead exit only costs its own attempt.

The mailbox is read **directly** on purpose: the panel's mail gate is separate from the API host, and proxying Emailnator through a TLS-intercepting exit answers `unable to get local issuer certificate` (which once made a delivered code look like “mail never arrived”). The reader forces IPv4 because this build resolves to IPv6 first and there is no IPv6 route.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | one live-proven key per line — git-ignored |
| `accounts.json` | one JSON record per account (mailbox, exit, quota, check-in award, group, key, proof) — git-ignored |
| `proxies.txt` | your exits — git-ignored |
| `out/` | run logs and per-account records — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Models are served as `wxctf/<model id>` and the router load-balances across every key of the node.

## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 What the provider gives | one account per key — the daily check-in funds every key (~$0.012–0.087/day, no card, no captcha) |
| 🔑 What one key gets you | one API key scoped to its own account: the 17 model ids on the endpoint below, at your plan's limits. The quota lives on the **account**, which is exactly why this tool creates one account per key |
| 🌐 API shape | OpenAI-compatible: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Your data | only ai.wxctf.com and the inbox backend you configure are contacted; keys stay on your machine |

## 🐞 Issues

If Wxctf Ai changes its address policy, its register shape, the token group or the catalogue, the tool stops at that step and says so. **[Open an issue](../../issues)** with the command and the exact output.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Account risk & how to minimise it
