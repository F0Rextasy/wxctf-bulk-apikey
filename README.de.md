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

**Wxctf Ai-Konten mit API-Schlüsseln im Bulk erstellen — OpenAI-kompatible Inferenz + 9Router-Konnektor.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Kostenlose Stufe](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-17-of-them) · [🧪 Die API nutzen](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

Wxctf Ai (ai.wxctf.com) ist ein New-API-Fork, der die Registrierung **offen** hält — kein Captcha, kein OAuth, nur E-Mail-Code — und sich selbst finanziert: Jedes Konto kann einen **täglichen Check-in** beanspruchen, der echtes Guthaben auf das Saldo bringt, sodass jeder Schlüssel ohne Karte mit Guthaben geboren wird.

Zwei Dinge machen es ungewöhnlich. Erstens ist die gesamte Anmeldung **nur über die API** möglich: eine reine Gmail-Adresse, eine Verifizierungsmail und ein Formular-POST, ganz ohne Browser (gemessen am 21.10.2026: fünf Konten in ~7 Minuten, dann zehn weitere in zwei Stapeln). Zweitens ist das Panel **streng bei der Adressform**: Es akzeptiert `gmail.com`, `outlook.com` und `qq.com`, lehnt aber alles mit einem Punkt oder einem `+` im lokalen Teil als „Sonderzeichen" ab, weshalb Plus-/Punkt-Aliase hier nutzlos sind und ein **reines** `@gmail.com`-Postfach die funktionierende Schiene ist.

Die anderen Fallen sind ebenso spezifisch und alle im Tool kodiert: Der Register-Aufruf muss **denselben Cookie-Jar** mitführen, der den Code mit einem **frischen** Code angefordert hat (10 Minuten TTL), das Passwort muss mindestens 8 Zeichen lang sein, und die Token-Gruppe muss die aktive `Default` sein — ein Token, das mit `default` erstellt wurde, antwortet auf dem Relay mit `403 Group default has been deprecated`.

## 🎁 Kostenlose Stufe — wie viel du bekommst

| Was | Wert |
| --- | --- |
| Anmeldebonus | **keiner** (`quota = 0` direkt nach der Registrierung) |
| Kostenlose Stufe | **täglicher Check-in** — `POST /api/user/checkin` gibt `quota_awarded` zurück |
| Gemessene Prämien | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** Quota (10 Konten, 21.10.2026) |
| In USD | **$0,012–$0,087 pro Konto pro Tag** (500 000 Quota = $1) — pro Beanspruchung randomisiert, **läuft nie ab** |
| Täglicher Bestand | 10 Konten ⇒ **259 288 Quota ≈ $0,5186 pro Tag**, einmal pro Konto pro Tag (`checkin_date` wird erzwungen) |
| Karte / Telefon / OAuth | **nichts davon** — nur E-Mail-Code, `turnstile_check: false` |
| Modelle | 17 aktive IDs (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Rate-Limit | nicht veröffentlicht; ein Burst von 30 Aufrufen wird normal beantwortet |

> 💡 Die kostenlose Stufe wird von Wxctf Ai festgelegt und kann sich ändern. Prüfe die aktuellen Zahlen auf [ai.wxctf.com](https://ai.wxctf.com) — und lass das Gate des Tools selbst entscheiden:

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

## 🤖 Modelle — alle 17 davon

`GET /v1/models` mit einem Schlüssel dieses Stapels (21.10.2026) — **17 IDs**:

| Familie | IDs |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Andere | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` listet 18 Zeilen auf (eine ID ist bepreist, wird aber nicht bereitgestellt) und **keine strikte $0-Zeile** — die kostenlose Stufe ist der Check-in, nicht ein kostenloses Modell. Das günstigste Textmodell ist `deepseek-v4-flash`.

## 🧪 So nutzt du die API

Basis-URL **`https://ai.wxctf.com/v1`** — OpenAI-kompatibel; Schlüssel sind 48 Zeichen lang mit **keinem `sk-`-Präfix** und gehören in einen `Authorization: Bearer`-Header.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

Das Quota wird von jedem Schlüssel eines Kontos geteilt, sodass der Check-in das Tagesbudget dieses Kontos steuert: **ein Konto pro Schlüssel pro Tag** ist die natürliche Form.

## 🛠 Schnellstart

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/cli.js --screen                     # vet the exits against the panel
node src/cli.js --count 5                    # five funded keys (no browser needed)
node src/cli.js --verify-only                # re-prove every key
node src/cli.js --connect-9router            # register them with a local 9Router
```

Ein Konto pro Schlüssel: Das Tool erstellt ein reines Gmail-Postfach, fordert den Code an, wartet darauf, registriert sich mit demselben Cookie-Jar, meldet sich an, **beansprucht den täglichen Check-in**, erstellt ein Token in der Gruppe `Default`, gibt den Schlüssel preis und beweist ihn erst dann mit einer echten Completion.

### 🖱️ Der Launcher

Der Launcher fragt nach der Proxy-Liste (optional) und der Kontoanzahl, merkt sich beides und führt die CLI aus — plus `--verify-only`, `--screen` und den 9Router-Konnektor.

## ✅ Verifiziert funktionierend — jeder Schlüssel wird getestet

Jede Prüfung unten lief am **21.10.2026** über die eigene CLI dieses Pakets (`src/cli.js`), über öffentliche Exits (kein VPN im Spiel).

| Prüfung | Ergebnis |
| --- | --- |
| Anmeldung in einem echten Ablauf | **kein Browser nötig** — das Panel hat kein Captcha (`turnstile_check: false`) |
| Reine-Gmail-Schiene | Emailnator, **direkt** gelesen; Punkte/`+` werden vom Panel als „Sonderzeichen" abgelehnt |
| Code-Mail | «Wxctf Ai email verification» von `noreply@<panel-mail>`, **1–25 s**, 6-stelliger Code, gültig **10 Minuten** |
| Registrierung | **200 `{"success":true}`** mit demselben Cookie-Jar + einem frischen Code + einem Passwort mit ≥8 Zeichen |
| Kostenlose Stufe | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **$0,012–$0,087 pro Konto pro Tag** |
| Täglicher Bestand | 10 Konten ⇒ **259 288 Quota ≈ $0,5186/Tag** (einmal pro Konto pro Tag) |
| Token-Gruppe | **`Default`** — ein Token, das mit `default` erstellt wurde, antwortet mit `403 Group default has been deprecated` |
| Schlüssel-Route | Die Liste zeigt ihn maskiert; `POST /api/token/<id>/key` gibt den 48-stelligen Klartext zurück |
| `GET /v1/models` pro Schlüssel | **200**, 17 IDs |
| `POST /v1/chat/completions` pro Schlüssel | **200** mit Inhalt — **10/10 Schlüssel LIVE** (`--verify-only`), ein Schlüssel nach einem Exit-seitigen Fehler auf 2/3 Exits erneut bestätigt |
| End-to-End-Geschwindigkeit | **~50–90 s pro finanziertem Schlüssel** (Mail → Registrierung → Check-in → Schlüssel → Beweis) |

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| Postfach | Emailnator über einfaches https | ein **reines** Gmail (`plain`-Form, Punkte entfernt); das Panel lehnt Punkte und `+` ab |
| Code | `GET /api/verification?email=` | kommt in ~1–25 s von `noreply@<panel-mail>`, Betreff «Wxctf Ai email verification», 6-stelliger Code, gültig 10 Minuten |
| Registrierung | `POST /api/user/register` | Body `{username,password,password2,email,verification_code,aff_code}`; **derselbe Cookie-Jar**, **frischer** Code, Passwort ≥8 |
| Sitzung | `POST /api/user/login` | `data.access_token` (Bearer JWT) für die Konsolenaufrufe |
| Guthaben | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — die kostenlose Stufe, beansprucht bevor der Schlüssel bewiesen wird |
| Schlüssel | `POST /api/token/` → `POST /api/token/<id>/key` | Gruppe **`Default`** (`default` ist veraltet → `403 Group default has been deprecated`); die Liste zeigt den Schlüssel maskiert und `/key` gibt den Klartext zurück |
| Beweis | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; der Schlüssel wird nur geschrieben, wenn die Completion mit 200 antwortet |

## ⚙️ CLI-Optionen

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Flag | Standard | Bedeutung |
| --- | --- | --- |
| `--count N` | `1` | Anzahl der zu erstellenden Konten |
| `--pool FILE` | `proxies.txt`, falls vorhanden | Exits, eine pro Zeile (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | einen einzelnen Exit festlegen |
| `--exits K` | `6` | pro Konto versuchte Exit-Kandidaten |
| `--model ID` | günstigstes Textmodell | für das Live-Gate verwendetes Modell |
| `--verify-only` | aus | `keys.txt` erneut beweisen |
| `--screen` | aus | die Exit-Liste gegen das Panel prüfen |
| `--connect-9router` | aus | den 9Router-Konnektor neben dieser CLI ausführen |

## 🛰️ Hinter einem Proxy

**Optional.** Ohne einen Pool gibt das Tool `DIRECT (no proxy)` aus und ruft das Panel direkt von dieser Maschine aus auf; mit einem Pool wird jede Panel-Anfrage an einen Exit aus der Liste gebunden und ein toter Exit kostet nur seinen eigenen Versuch.

Das Postfach wird **absichtlich direkt** gelesen: Das Mail-Gate des Panels ist vom API-Host getrennt, und Emailnator über einen TLS-unterbrechenden Exit zu proxen antwortet mit `unable to get local issuer certificate` (was einmal einen zugestellten Code wie „Mail kam nie an" aussehen ließ). Der Reader erzwingt IPv4, weil dieser Build zuerst auf IPv6 auflöst und es keine IPv6-Route gibt.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | ein live-bewiesener Schlüssel pro Zeile — von Git ignoriert |
| `accounts.json` | ein JSON-Datensatz pro Konto (Postfach, Exit, Quota, Check-in-Prämie, Gruppe, Schlüssel, Beweis) — von Git ignoriert |
| `proxies.txt` | deine Exits — von Git ignoriert |
| `out/` | Ausführungsprotokolle und Datensätze pro Konto — von Git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Standardwerte: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Modelle werden als `wxctf/<model id>` bereitgestellt und der Router verteilt die Last über jeden Schlüssel des Knotens.

## 🔑 Was ein Schlüssel dir bringt

| | |
| --- | --- |
| 🏢 Anbieter | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 Was der Anbieter gibt | ein Konto pro Schlüssel — der tägliche Check-in finanziert jeden Schlüssel (~$0,012–0,087/Tag, keine Karte, kein Captcha) |
| 🔑 Was ein Schlüssel dir bringt | einen API-Schlüssel, der auf sein eigenes Konto beschränkt ist: die 17 Modell-IDs am unten stehenden Endpunkt, zu den Limits deines Tarifs. Das Quota liegt auf dem **Konto**, weshalb dieses Tool genau ein Konto pro Schlüssel erstellt |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Deine Daten | nur ai.wxctf.com und das von dir konfigurierte Postfach-Backend werden kontaktiert; Schlüssel bleiben auf deiner Maschine |

## 🐞 Issues

Wenn Wxctf Ai seine Adressrichtlinie, seine Register-Form, die Token-Gruppe oder den Katalog ändert, stoppt das Tool an diesem Schritt und sagt es. **[Öffne ein Issue](../../issues)** mit dem Befehl und der exakten Ausgabe.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Konto-Risiko und Minimierung
