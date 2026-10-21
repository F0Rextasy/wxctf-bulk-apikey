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

**Crea cuentas de Wxctf Ai en masa con claves API — inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-17-of-them) · [🧪 Usa la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>


---

## ✨ Por qué existe esto

Wxctf Ai (ai.wxctf.com) es un fork de New API que mantiene el registro **abierto** — sin captcha, sin OAuth, solo código por correo — y se paga a sí mismo: cada cuenta puede reclamar un **check-in diario** que deposita cuota real en el saldo, así que cada clave nace financiada sin tarjeta.

Dos cosas lo hacen inusual. Primero, todo el registro es **solo por API**: una dirección de gmail simple, un correo de verificación y un POST de formulario, sin navegador en absoluto (medido el 2026-10-21: cinco cuentas en ~7 minutos, luego diez más en dos lotes). Segundo, el panel es **estricto con la forma de la dirección**: acepta `gmail.com`, `outlook.com` y `qq.com`, pero rechaza cualquier cosa con un punto o un `+` en la parte local como "carácter especial", así que los alias con plus/punto son inútiles aquí y un buzón **simple** `@gmail.com` es el riel que funciona.

Las otras trampas son igual de específicas y todas están codificadas en la herramienta: la llamada de registro debe llevar el **mismo cookie jar** que solicitó el código con un código **fresco** (TTL de 10 minutos), la contraseña debe tener al menos 8 caracteres, y el grupo del token debe ser el `Default` vigente — un token creado con `default` responde `403 Group default has been deprecated` en el relay.

## 🎁 Nivel gratuito — cuánto obtienes

| Qué | Valor |
| --- | --- |
| Bono de registro | **ninguno** (`quota = 0` justo después del registro) |
| Nivel gratuito | **check-in diario** — `POST /api/user/checkin` devuelve `quota_awarded` |
| Premios medidos | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** quota (10 cuentas, 2026-10-21) |
| En USD | **$0.012–$0.087 por cuenta por día** (500 000 quota = $1) — aleatorio por reclamo, **nunca expira** |
| Stock diario | 10 cuentas ⇒ **259 288 quota ≈ $0.5186 por día**, una vez por cuenta por día (`checkin_date` se aplica) |
| Tarjeta / teléfono / OAuth | **ninguno de ellos** — solo código por correo, `turnstile_check: false` |
| Modelos | 17 ids activos (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Límite de tasa | no publicado; una ráfaga de 30 llamadas se responde con normalidad |

> 💡 El nivel gratuito lo define Wxctf Ai y puede cambiar. Consulta los números en vivo en [ai.wxctf.com](https://ai.wxctf.com) — y deja que la propia puerta de la herramienta decida:

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

## 🤖 Modelos — los 17

`GET /v1/models` con una clave de este lote (2026-10-21) — **17 ids**:

| Familia | Ids |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Otros | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` lista 18 filas (un id tiene precio pero no se sirve) y **ninguna fila de $0 estricto** — el nivel gratuito es el check-in, no un modelo gratuito. El modelo de texto más barato es `deepseek-v4-flash`.

## 🧪 Cómo usar la API

URL base **`https://ai.wxctf.com/v1`** — compatible con OpenAI; las claves tienen 48 caracteres **sin prefijo `sk-`** y van en una cabecera `Authorization: Bearer`.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

La cuota es compartida por todas las claves de una cuenta, así que el check-in condiciona el presupuesto diario de esa cuenta: **una cuenta por clave por día** es la forma natural.

## 🛠 Inicio rápido

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/cli.js --screen                     # vet the exits against the panel
node src/cli.js --count 5                    # five funded keys (no browser needed)
node src/cli.js --verify-only                # re-prove every key
node src/cli.js --connect-9router            # register them with a local 9Router
```

Una cuenta por clave: la herramienta crea un buzón de gmail simple, solicita el código, lo espera, se registra con el mismo cookie jar, inicia sesión, **reclama el check-in diario**, acuña un token en el grupo `Default`, revela la clave y solo entonces la prueba con una completion real.

### 🖱️ El lanzador

El lanzador pide la lista de proxies (opcional) y el número de cuentas, recuerda ambos y ejecuta el CLI — además de `--verify-only`, `--screen` y el conector 9Router.

## ✅ Verificado funcionando — cada clave se prueba

Cada comprobación a continuación se ejecutó el **2026-10-21** desde el propio CLI de este paquete (`src/cli.js`), sobre salidas públicas (sin VPN de por medio).

| Comprobación | Resultado |
| --- | --- |
| registro en un flujo real | **no se necesita navegador** — el panel no tiene captcha (`turnstile_check: false`) |
| riel de gmail simple | Emailnator, leído **directamente**; los puntos/`+` son rechazados por el panel como "caracteres especiales" |
| correo con código | «Wxctf Ai email verification» de `noreply@<panel-mail>`, **1–25 s**, código de 6 caracteres, válido **10 minutos** |
| registro | **200 `{"success":true}`** con el mismo cookie jar + un código fresco + una contraseña de ≥8 caracteres |
| nivel gratuito | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **$0.012–$0.087 por cuenta por día** |
| stock diario | 10 cuentas ⇒ **259 288 quota ≈ $0.5186/día** (una vez por cuenta por día) |
| grupo del token | **`Default`** — un token creado con `default` responde `403 Group default has been deprecated` |
| ruta de la clave | la lista la muestra enmascarada; `POST /api/token/<id>/key` devuelve el texto plano de 48 caracteres |
| `GET /v1/models` por clave | **200**, 17 ids |
| `POST /v1/chat/completions` por clave | **200** con contenido — **10/10 claves LIVE** (`--verify-only`), una clave reconfirmada en 2/3 salidas tras un fallo del lado de la salida |
| velocidad de extremo a extremo | **~50–90 s por clave financiada** (correo → registro → check-in → clave → prueba) |


## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| buzón | Emailnator por https simple | un gmail **simple** (forma `plain`, puntos eliminados); el panel rechaza puntos y `+` |
| código | `GET /api/verification?email=` | llega en ~1–25 s de `noreply@<panel-mail>`, asunto «Wxctf Ai email verification», código de 6 caracteres, válido 10 minutos |
| registro | `POST /api/user/register` | cuerpo `{username,password,password2,email,verification_code,aff_code}`; **mismo cookie jar**, código **fresco**, contraseña ≥8 |
| sesión | `POST /api/user/login` | `data.access_token` (Bearer JWT) para las llamadas de consola |
| fondos | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — el nivel gratuito, reclamado antes de probar la clave |
| clave | `POST /api/token/` → `POST /api/token/<id>/key` | grupo **`Default`** (`default` está obsoleto → `403 Group default has been deprecated`); la lista muestra la clave enmascarada y `/key` devuelve el texto plano |
| prueba | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; la clave se escribe solo cuando la completion responde 200 |

## ⚙️ Opciones del CLI

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Flag | Predeterminado | Significado |
| --- | --- | --- |
| `--count N` | `1` | cuentas a crear |
| `--pool FILE` | `proxies.txt` si existe | salidas, una por línea (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | fija una única salida |
| `--exits K` | `6` | salidas candidatas probadas por cuenta |
| `--model ID` | modelo de texto más barato | modelo usado para la puerta en vivo |
| `--verify-only` | desactivado | vuelve a probar `keys.txt` |
| `--screen` | desactivado | verifica la lista de salidas contra el panel |
| `--connect-9router` | desactivado | ejecuta el conector 9Router junto a este CLI |

## 🛰️ Detrás de un proxy

**Opcional.** Sin un pool, la herramienta imprime `DIRECT (no proxy)` y llama al panel directamente desde esta máquina; con uno, cada solicitud al panel se fija a una salida de la lista y una salida muerta solo cuesta su propio intento.

El buzón se lee **directamente** a propósito: la puerta de correo del panel es independiente del host de la API, y enrutar Emailnator a través de una salida con interceptación TLS responde `unable to get local issuer certificate` (lo que una vez hizo que un código entregado pareciera "el correo nunca llegó"). El lector fuerza IPv4 porque esta compilación resuelve primero a IPv6 y no hay ruta IPv6.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | una clave probada en vivo por línea — ignorada por git |
| `accounts.json` | un registro JSON por cuenta (buzón, salida, cuota, premio del check-in, grupo, clave, prueba) — ignorado por git |
| `proxies.txt` | tus salidas — ignorado por git |
| `out/` | registros de ejecución y registros por cuenta — ignorado por git |

## 🔌 Conectar con 9Router

[9Router](https://9router.com) es un router autoalojado y compatible con OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Predeterminados: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Los modelos se sirven como `wxctf/<model id>` y el router balancea la carga entre todas las claves del nodo.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 Qué da el proveedor | una cuenta por clave — el check-in diario financia cada clave (~$0.012–0.087/día, sin tarjeta, sin captcha) |
| 🔑 Qué te da una clave | una clave API limitada a su propia cuenta: los 17 ids de modelo en el endpoint de abajo, con los límites de tu plan. La cuota vive en la **cuenta**, que es exactamente por lo que esta herramienta crea una cuenta por clave |
| 🌐 Forma de la API | compatible con OpenAI: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Tus datos | solo se contacta ai.wxctf.com y el backend de buzón que configures; las claves se quedan en tu máquina |

## 🐞 Problemas

Si Wxctf Ai cambia su política de direcciones, la forma de su registro, el grupo del token o el catálogo, la herramienta se detiene en ese paso y lo indica. **[Abre un issue](../../issues)** con el comando y la salida exacta.

## 📄 Licencia

MIT © 2026 — consulta [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Riesgo de cuenta y cómo minimizarlo
