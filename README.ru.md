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

**Массовое создание аккаунтов Wxctf Ai с API-ключами — OpenAI-совместимый инференс + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-17-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

Wxctf Ai (ai.wxctf.com) — это форк New API, который держит регистрацию **открытой** — без капчи, без OAuth, только код по e-mail — и окупает себя: каждый аккаунт может получить **ежедневную отметку**, которая начисляет реальную квоту на баланс, так что каждый ключ рождается с финансированием без карты.

Две вещи делают его необычным. Во-первых, вся регистрация **только через API**: обычный gmail-адрес, одно письмо с подтверждением и POST-форма, вообще без браузера (замерено 2026-10-21: пять аккаунтов за ~7 минут, затем ещё десять в двух партиях). Во-вторых, панель **строга к форме адреса**: она принимает `gmail.com`, `outlook.com` и `qq.com`, но отклоняет всё с точкой или `+` в локальной части как «специальный символ», поэтому plus/dot-алиасы здесь бесполезны, и рабочий путь — **обычный** ящик `@gmail.com`.

Остальные ловушки столь же специфичны и все закодированы в инструменте: запрос регистрации должен нести **тот же cookie jar**, что запрашивал код, со **свежим** кодом (TTL 10 минут), пароль должен быть не короче 8 символов, а группа токена должна быть актуальной `Default` — токен, созданный с `default`, отвечает `403 Group default has been deprecated` на релее.

## 🎁 Бесплатный тариф — сколько вы получаете

| Что | Значение |
| --- | --- |
| Бонус за регистрацию | **нет** (`quota = 0` сразу после регистрации) |
| Бесплатный тариф | **ежедневная отметка** — `POST /api/user/checkin` возвращает `quota_awarded` |
| Измеренные начисления | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** квоты (10 аккаунтов, 2026-10-21) |
| В USD | **$0.012–$0.087 на аккаунт в день** (500 000 квоты = $1) — рандомизируется при каждом получении, **никогда не истекает** |
| Дневной запас | 10 аккаунтов ⇒ **259 288 квоты ≈ $0.5186 в день**, один раз на аккаунт в день (`checkin_date` проверяется) |
| Карта / телефон / OAuth | **ничего из этого** — только код по e-mail, `turnstile_check: false` |
| Модели | 17 живых id (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Ограничение скорости | не опубликовано; всплеск из 30 вызовов обрабатывается нормально |

> 💡 Бесплатный тариф устанавливается Wxctf Ai и может измениться. Проверяйте актуальные цифры на [ai.wxctf.com](https://ai.wxctf.com) — и пусть собственный гейт инструмента решает:

```bash
```bash
# каждый ключ в keys.txt: список моделей плюс одно реальное завершение
node src/cli.js --verify-only

# вручную (ключи не имеют префикса `sk-`)
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

Гейт считает `content || reasoning_content`, поэтому reasoning-модель, потратившая весь бюджет на размышления, всё равно доказывает, что ключ живой.
```

## 🤖 Модели — все 17

`GET /v1/models` на ключе из этой партии (2026-10-21) — **17 id**:

| Семейство | Id |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Прочие | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` перечисляет 18 строк (один id имеет цену, но не обслуживается) и **ни одной строки со строго $0** — бесплатный тариф это отметка, а не бесплатная модель. Самая дешёвая текстовая модель — `deepseek-v4-flash`.

## 🧪 Как использовать API

Базовый URL **`https://ai.wxctf.com/v1`** — OpenAI-совместимый; ключи длиной 48 символов **без префикса `sk-`** и передаются в заголовке `Authorization: Bearer`.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

Квота общая для всех ключей аккаунта, поэтому отметка определяет дневной бюджет этого аккаунта: **один аккаунт на ключ в день** — естественная форма.

## 🛠 Быстрый старт

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # необязательно — без него инструмент работает напрямую
node src/cli.js --screen                     # проверить выходы против панели
node src/cli.js --count 5                    # пять профинансированных ключей (браузер не нужен)
node src/cli.js --verify-only                # перепроверить каждый ключ
node src/cli.js --connect-9router            # зарегистрировать их в локальном 9Router
```

Один аккаунт на ключ: инструмент создаёт обычный gmail-ящик, запрашивает код, ждёт его, регистрируется с тем же cookie jar, входит, **получает ежедневную отметку**, создаёт токен в группе `Default`, раскрывает ключ и только затем доказывает его реальным завершением.

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси (необязательно) и количество аккаунтов, запоминает и то и другое и запускает CLI — плюс `--verify-only`, `--screen` и коннектор 9Router.

## ✅ Проверено в работе — каждый ключ протестирован

Каждая проверка ниже выполнялась **2026-10-21** из собственного CLI этого пакета (`src/cli.js`), через публичные выходы (без VPN).

| Проверка | Результат |
| --- | --- |
| регистрация в реальном потоке | **браузер не нужен** — в панели нет капчи (`turnstile_check: false`) |
| путь через обычный gmail | Emailnator, чтение **напрямую**; точки/`+` отклоняются панелью как «специальные символы» |
| письмо с кодом | «Wxctf Ai email verification» от `noreply@<panel-mail>`, **1–25 с**, 6-символьный код, действителен **10 минут** |
| регистрация | **200 `{"success":true}`** с тем же cookie jar + свежим кодом + паролем ≥8 символов |
| бесплатный тариф | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **$0.012–$0.087 на аккаунт в день** |
| дневной запас | 10 аккаунтов ⇒ **259 288 квоты ≈ $0.5186/день** (один раз на аккаунт в день) |
| группа токена | **`Default`** — токен, созданный с `default`, отвечает `403 Group default has been deprecated` |
| маршрут ключа | в списке он показан замаскированным; `POST /api/token/<id>/key` возвращает 48-символьный открытый текст |
| `GET /v1/models` на каждый ключ | **200**, 17 id |
| `POST /v1/chat/completions` на каждый ключ | **200** с содержимым — **10/10 ключей LIVE** (`--verify-only`), один ключ переподтверждён на 2/3 выходах после сбоя на стороне выхода |
| сквозная скорость | **~50–90 с на профинансированный ключ** (почта → регистрация → отметка → ключ → доказательство) |

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Шаг | Вызов | Примечания |
| --- | --- | --- |
| ящик | Emailnator по обычному https | **обычный** gmail (форма `plain`, точки удаляются); панель отклоняет точки и `+` |
| код | `GET /api/verification?email=` | приходит за ~1–25 с от `noreply@<panel-mail>`, тема «Wxctf Ai email verification», 6-символьный код, действителен 10 минут |
| регистрация | `POST /api/user/register` | тело `{username,password,password2,email,verification_code,aff_code}`; **тот же cookie jar**, **свежий** код, пароль ≥8 |
| сессия | `POST /api/user/login` | `data.access_token` (Bearer JWT) для вызовов консоли |
| средства | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — бесплатный тариф, получается до доказательства ключа |
| ключ | `POST /api/token/` → `POST /api/token/<id>/key` | группа **`Default`** (`default` устарела → `403 Group default has been deprecated`); в списке ключ замаскирован, а `/key` возвращает открытый текст |
| доказательство | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; ключ записывается только когда завершение отвечает 200 |

## ⚙️ Опции CLI

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Флаг | По умолчанию | Значение |
| --- | --- | --- |
| `--count N` | `1` | сколько аккаунтов создать |
| `--pool FILE` | `proxies.txt`, если есть | выходы, по одному в строке (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | закрепить один выход |
| `--exits K` | `6` | кандидатов-выходов, пробуемых на аккаунт |
| `--model ID` | самая дешёвая текстовая модель | модель, используемая для живого гейта |
| `--verify-only` | выкл. | перепроверить `keys.txt` |
| `--screen` | выкл. | проверить список выходов против панели |
| `--connect-9router` | выкл. | запустить коннектор 9Router рядом с этим CLI |

## 🛰️ За прокси

**Необязательно.** Без пула инструмент печатает `DIRECT (no proxy)` и обращается к панели напрямую с этой машины; с пулом каждый запрос к панели привязан к выходу из списка, и мёртвый выход стоит только своей попытки.

Ящик читается **напрямую** намеренно: почтовый шлюз панели отделён от API-хоста, и проксирование Emailnator через выход с TLS-перехватом отвечает `unable to get local issuer certificate` (из-за чего однажды доставленный код выглядел как «письмо так и не пришло»). Читатель принудительно использует IPv4, потому что эта сборка сначала разрешает IPv6, а маршрута IPv6 нет.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | один проверенный в работе ключ на строку — игнорируется git |
| `accounts.json` | одна JSON-запись на аккаунт (ящик, выход, квота, начисление за отметку, группа, ключ, доказательство) — игнорируется git |
| `proxies.txt` | ваши выходы — игнорируется git |
| `out/` | логи запусков и записи по аккаунтам — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый OpenAI-совместимый роутер:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

По умолчанию: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Модели обслуживаются как `wxctf/<model id>`, а роутер балансирует нагрузку между всеми ключами узла.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 Что даёт провайдер | один аккаунт на ключ — ежедневная отметка финансирует каждый ключ (~$0.012–0.087/день, без карты, без капчи) |
| 🔑 Что даёт один ключ | один API-ключ, привязанный к своему аккаунту: 17 id моделей на указанном ниже эндпоинте, в пределах лимитов вашего тарифа. Квота живёт на **аккаунте**, и именно поэтому этот инструмент создаёт один аккаунт на ключ |
| 🌐 Форма API | OpenAI-совместимый: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Ваши данные | происходит обращение только к ai.wxctf.com и настроенному вами почтовому бэкенду; ключи остаются на вашей машине |

## 🐞 Проблемы

Если Wxctf Ai изменит свою политику адресов, форму регистрации, группу токена или каталог, инструмент остановится на этом шаге и сообщит об этом. **[Откройте issue](../../issues)** с командой и точным выводом.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Риск блокировки и как его снизить
