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

**Crie contas Wxctf Ai em massa com chaves de API — inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Nível gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-17-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

Wxctf Ai (ai.wxctf.com) é um fork do New API que mantém o registro **aberto** — sem captcha, sem OAuth, apenas código por e-mail — e se paga sozinho: cada conta pode resgatar um **check-in diário** que deposita cota real no saldo, então cada chave já nasce financiada sem cartão.

Duas coisas o tornam incomum. Primeiro, todo o cadastro é **somente via API**: um endereço gmail simples, um e-mail de verificação e um POST de formulário, sem navegador algum (medido em 2026-10-21: cinco contas em ~7 minutos, depois mais dez em dois lotes). Segundo, o painel é **rigoroso quanto ao formato do endereço**: aceita `gmail.com`, `outlook.com` e `qq.com`, mas recusa qualquer coisa com um ponto ou um `+` na parte local como "caractere especial", então aliases com plus/ponto são inúteis aqui e uma caixa **simples** `@gmail.com` é o trilho que funciona.

As outras armadilhas são igualmente específicas e todas codificadas na ferramenta: a chamada de registro deve carregar o **mesmo cookie jar** que solicitou o código com um código **novo** (TTL de 10 minutos), a senha deve ter pelo menos 8 caracteres, e o grupo do token deve ser o `Default` ativo — um token criado com `default` responde `403 Group default has been deprecated` no relay.

## 🎁 Nível gratuito — quanto você recebe

| O quê | Valor |
| --- | --- |
| Bônus de cadastro | **nenhum** (`quota = 0` logo após o registro) |
| Nível gratuito | **check-in diário** — `POST /api/user/checkin` retorna `quota_awarded` |
| Prêmios medidos | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** de cota (10 contas, 2026-10-21) |
| Em USD | **$0.012–$0.087 por conta por dia** (500 000 de cota = $1) — aleatório por resgate, **nunca expira** |
| Estoque diário | 10 contas ⇒ **259 288 de cota ≈ $0.5186 por dia**, uma vez por conta por dia (`checkin_date` é imposto) |
| Cartão / telefone / OAuth | **nenhum deles** — apenas código por e-mail, `turnstile_check: false` |
| Modelos | 17 ids ativos (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Limite de taxa | não publicado; uma rajada de 30 chamadas é respondida normalmente |

> 💡 O nível gratuito é definido pela Wxctf Ai e pode mudar. Confira os números ao vivo em [ai.wxctf.com](https://ai.wxctf.com) — e deixe o próprio gate da ferramenta decidir:

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

## 🤖 Modelos — todos os 17

`GET /v1/models` em uma chave deste lote (2026-10-21) — **17 ids**:

| Família | Ids |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Outros | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` lista 18 linhas (um id tem preço mas não é servido) e **nenhuma linha de $0 estrito** — o nível gratuito é o check-in, não um modelo gratuito. O modelo de texto mais barato é `deepseek-v4-flash`.

## 🧪 Como usar a API

URL base **`https://ai.wxctf.com/v1`** — compatível com OpenAI; as chaves têm 48 caracteres **sem prefixo `sk-`** e vão em um cabeçalho `Authorization: Bearer`.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

A cota é compartilhada por todas as chaves de uma conta, então o check-in controla o orçamento diário dessa conta: **uma conta por chave por dia** é o formato natural.

## 🛠 Início rápido

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/cli.js --screen                     # vet the exits against the panel
node src/cli.js --count 5                    # five funded keys (no browser needed)
node src/cli.js --verify-only                # re-prove every key
node src/cli.js --connect-9router            # register them with a local 9Router
```

Uma conta por chave: a ferramenta cria uma caixa gmail simples, solicita o código, espera por ele, registra com o mesmo cookie jar, faz login, **resgata o check-in diário**, cria um token no grupo `Default`, revela a chave e só então a comprova com uma conclusão real.

### 🖱️ O launcher

O launcher pede a lista de proxies (opcional) e a quantidade de contas, lembra de ambos e executa a CLI — além de `--verify-only`, `--screen` e o conector 9Router.

## ✅ Verificado funcionando — toda chave é testada

Todas as verificações abaixo foram executadas em **2026-10-21** pela própria CLI deste pacote (`src/cli.js`), através de saídas públicas (sem VPN no processo).

| Verificação | Resultado |
| --- | --- |
| cadastro em fluxo real | **sem navegador necessário** — o painel não tem captcha (`turnstile_check: false`) |
| trilho gmail simples | Emailnator, lido **diretamente**; pontos/`+` são recusados pelo painel como "caracteres especiais" |
| e-mail do código | «Wxctf Ai email verification» de `noreply@<panel-mail>`, **1–25 s**, código de 6 caracteres, válido por **10 minutos** |
| registro | **200 `{"success":true}`** com o mesmo cookie jar + um código novo + uma senha de ≥8 caracteres |
| nível gratuito | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **$0.012–$0.087 por conta por dia** |
| estoque diário | 10 contas ⇒ **259 288 de cota ≈ $0.5186/dia** (uma vez por conta por dia) |
| grupo do token | **`Default`** — um token criado com `default` responde `403 Group default has been deprecated` |
| rota da chave | a lista a mostra mascarada; `POST /api/token/<id>/key` retorna o texto simples de 48 caracteres |
| `GET /v1/models` por chave | **200**, 17 ids |
| `POST /v1/chat/completions` por chave | **200** com conteúdo — **10/10 chaves LIVE** (`--verify-only`), uma chave reconfirmada em 2/3 saídas após uma falha do lado da saída |
| velocidade ponta a ponta | **~50–90 s por chave financiada** (e-mail → registro → check-in → chave → comprovação) |

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Observações |
| --- | --- | --- |
| caixa de correio | Emailnator via https simples | um gmail **simples** (formato `plain`, pontos removidos); o painel recusa pontos e `+` |
| código | `GET /api/verification?email=` | chega em ~1–25 s de `noreply@<panel-mail>`, assunto «Wxctf Ai email verification», código de 6 caracteres, válido por 10 minutos |
| registro | `POST /api/user/register` | corpo `{username,password,password2,email,verification_code,aff_code}`; **mesmo cookie jar**, código **novo**, senha ≥8 |
| sessão | `POST /api/user/login` | `data.access_token` (Bearer JWT) para as chamadas do console |
| fundos | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — o nível gratuito, resgatado antes de a chave ser comprovada |
| chave | `POST /api/token/` → `POST /api/token/<id>/key` | grupo **`Default`** (`default` está obsoleto → `403 Group default has been deprecated`); a lista mostra a chave mascarada e `/key` retorna o texto simples |
| comprovação | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; a chave só é gravada quando a conclusão responde 200 |

## ⚙️ Opções da CLI

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Flag | Padrão | Significado |
| --- | --- | --- |
| `--count N` | `1` | contas a criar |
| `--pool FILE` | `proxies.txt` se presente | saídas, uma por linha (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | fixa uma única saída |
| `--exits K` | `6` | saídas candidatas tentadas por conta |
| `--model ID` | modelo de texto mais barato | modelo usado para o gate ao vivo |
| `--verify-only` | desligado | recomprova `keys.txt` |
| `--screen` | desligado | avalia a lista de saídas contra o painel |
| `--connect-9router` | desligado | executa o conector 9Router junto a esta CLI |

## 🛰️ Atrás de um proxy

**Opcional.** Sem um pool, a ferramenta imprime `DIRECT (no proxy)` e chama o painel diretamente desta máquina; com um, cada requisição ao painel é fixada a uma saída da lista e uma saída morta custa apenas sua própria tentativa.

A caixa de correio é lida **diretamente** de propósito: o gate de e-mail do painel é separado do host da API, e usar proxy no Emailnator através de uma saída com interceptação TLS responde `unable to get local issuer certificate` (o que uma vez fez um código entregue parecer "e-mail nunca chegou"). O leitor força IPv4 porque esta build resolve para IPv6 primeiro e não há rota IPv6.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | uma chave comprovada ao vivo por linha — ignorado pelo git |
| `accounts.json` | um registro JSON por conta (caixa de correio, saída, cota, prêmio de check-in, grupo, chave, comprovação) — ignorado pelo git |
| `proxies.txt` | suas saídas — ignorado pelo git |
| `out/` | logs de execução e registros por conta — ignorado pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Os modelos são servidos como `wxctf/<model id>` e o roteador faz balanceamento de carga entre todas as chaves do nó.

## 🔑 O que uma chave oferece

| | |
| --- | --- |
| 🏢 Provedor | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 O que o provedor dá | uma conta por chave — o check-in diário financia cada chave (~$0.012–0.087/dia, sem cartão, sem captcha) |
| 🔑 O que uma chave oferece | uma chave de API limitada à sua própria conta: os 17 ids de modelo no endpoint abaixo, nos limites do seu plano. A cota fica na **conta**, e é exatamente por isso que esta ferramenta cria uma conta por chave |
| 🌐 Formato da API | compatível com OpenAI: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Seus dados | apenas ai.wxctf.com e o backend de caixa de correio que você configurar são contatados; as chaves ficam na sua máquina |

## 🐞 Problemas

Se a Wxctf Ai mudar sua política de endereços, o formato de registro, o grupo do token ou o catálogo, a ferramenta para nessa etapa e avisa. **[Abra uma issue](../../issues)** com o comando e a saída exata.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Risco de conta e como minimizar
