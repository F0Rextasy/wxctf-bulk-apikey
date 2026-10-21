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

**Créez en masse des comptes Wxctf Ai avec des clés API — inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Offre gratuite](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-17-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi ça existe

Wxctf Ai (ai.wxctf.com) est un fork de New API qui garde l'inscription **ouverte** — pas de captcha, pas d'OAuth, uniquement un code par e-mail — et qui s'autofinance : chaque compte peut réclamer un **check-in quotidien** qui dépose du quota réel sur le solde, donc chaque clé naît approvisionnée sans carte.

Deux choses le rendent inhabituel. D'abord, toute l'inscription est **API-only** : une adresse gmail simple, un e-mail de vérification et un POST de formulaire, aucun navigateur (mesuré le 2026-10-21 : cinq comptes en ~7 minutes, puis dix de plus en deux lots). Ensuite, le panneau est **strict sur la forme de l'adresse** : il accepte `gmail.com`, `outlook.com` et `qq.com`, mais refuse tout ce qui contient un point ou un `+` dans la partie locale comme « caractère spécial », donc les alias avec plus/point sont inutiles ici et une boîte **simple** `@gmail.com` est la voie qui fonctionne.

Les autres pièges sont tout aussi spécifiques et tous encodés dans l'outil : l'appel d'inscription doit porter le **même pot de cookies** que celui qui a demandé le code avec un code **frais** (TTL de 10 minutes), le mot de passe doit comporter au moins 8 caractères, et le groupe du token doit être le `Default` actif — un token créé avec `default` répond `403 Group default has been deprecated` sur le relais.

## 🎁 Offre gratuite — ce que vous obtenez

| Quoi | Valeur |
| --- | --- |
| Bonus d'inscription | **aucun** (`quota = 0` juste après l'inscription) |
| Offre gratuite | **check-in quotidien** — `POST /api/user/checkin` renvoie `quota_awarded` |
| Récompenses mesurées | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** quota (10 comptes, 2026-10-21) |
| En USD | **0,012–0,087 $ par compte et par jour** (500 000 quota = 1 $) — aléatoire à chaque réclamation, **n'expire jamais** |
| Stock quotidien | 10 comptes ⇒ **259 288 quota ≈ 0,5186 $ par jour**, une fois par compte et par jour (`checkin_date` est appliqué) |
| Carte / téléphone / OAuth | **aucun d'entre eux** — uniquement un code par e-mail, `turnstile_check: false` |
| Modèles | 17 ids actifs (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Limite de débit | non publiée ; une rafale de 30 appels est traitée normalement |

> 💡 L'offre gratuite est définie par Wxctf Ai et peut changer. Consultez les chiffres en direct sur [ai.wxctf.com](https://ai.wxctf.com) — et laissez la propre barrière de l'outil décider :

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

## 🤖 Modèles — les 17

`GET /v1/models` sur une clé de ce lot (2026-10-21) — **17 ids** :

| Famille | Ids |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Autres | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` liste 18 lignes (un id est tarifé mais non servi) et **aucune ligne à 0 $ strict** — l'offre gratuite est le check-in, pas un modèle gratuit. Le modèle texte le moins cher est `deepseek-v4-flash`.

## 🧪 Comment utiliser l'API

URL de base **`https://ai.wxctf.com/v1`** — compatible OpenAI ; les clés font 48 caractères **sans préfixe `sk-`** et se placent dans un en-tête `Authorization: Bearer`.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

Le quota est partagé par toutes les clés d'un compte, donc le check-in conditionne le budget quotidien de ce compte : **un compte par clé et par jour** est la forme naturelle.

## 🛠 Démarrage rapide

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/cli.js --screen                     # vet the exits against the panel
node src/cli.js --count 5                    # five funded keys (no browser needed)
node src/cli.js --verify-only                # re-prove every key
node src/cli.js --connect-9router            # register them with a local 9Router
```

Un compte par clé : l'outil crée une boîte gmail simple, demande le code, l'attend, s'inscrit avec le même pot de cookies, se connecte, **réclame le check-in quotidien**, génère un token dans le groupe `Default`, révèle la clé et ne la prouve qu'ensuite avec une vraie complétion.

### 🖱️ Le lanceur

Le lanceur demande la liste de proxys (facultative) et le nombre de comptes, mémorise les deux et exécute le CLI — plus `--verify-only`, `--screen` et le connecteur 9Router.

## ✅ Vérifié fonctionnel — chaque clé est testée

Chaque vérification ci-dessous a été exécutée le **2026-10-21** depuis le CLI de ce paquet (`src/cli.js`), via des sorties publiques (aucun VPN dans la boucle).

| Vérification | Résultat |
| --- | --- |
| inscription dans un flux réel | **aucun navigateur nécessaire** — le panneau n'a pas de captcha (`turnstile_check: false`) |
| voie gmail simple | Emailnator, lu **directement** ; les points/`+` sont refusés par le panneau comme « caractères spéciaux » |
| e-mail de code | « Wxctf Ai email verification » de `noreply@<panel-mail>`, **1–25 s**, code de 6 caractères, valide **10 minutes** |
| inscription | **200 `{"success":true}`** avec le même pot de cookies + un code frais + un mot de passe ≥8 caractères |
| offre gratuite | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **0,012–0,087 $ par compte et par jour** |
| stock quotidien | 10 comptes ⇒ **259 288 quota ≈ 0,5186 $/jour** (une fois par compte et par jour) |
| groupe du token | **`Default`** — un token créé avec `default` répond `403 Group default has been deprecated` |
| route de la clé | la liste l'affiche masquée ; `POST /api/token/<id>/key` renvoie le texte en clair de 48 caractères |
| `GET /v1/models` par clé | **200**, 17 ids |
| `POST /v1/chat/completions` par clé | **200** avec contenu — **10/10 clés LIVE** (`--verify-only`), une clé reconfirmée sur 2/3 sorties après une défaillance côté sortie |
| vitesse de bout en bout | **~50–90 s par clé approvisionnée** (mail → inscription → check-in → clé → preuve) |

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Notes |
| --- | --- | --- |
| boîte mail | Emailnator en https simple | un gmail **simple** (forme `plain`, points supprimés) ; le panneau refuse les points et `+` |
| code | `GET /api/verification?email=` | arrive en ~1–25 s de `noreply@<panel-mail>`, objet « Wxctf Ai email verification », code de 6 caractères, valide 10 minutes |
| inscription | `POST /api/user/register` | corps `{username,password,password2,email,verification_code,aff_code}` ; **même pot de cookies**, code **frais**, mot de passe ≥8 |
| session | `POST /api/user/login` | `data.access_token` (Bearer JWT) pour les appels de la console |
| fonds | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — l'offre gratuite, réclamée avant que la clé soit prouvée |
| clé | `POST /api/token/` → `POST /api/token/<id>/key` | groupe **`Default`** (`default` est obsolète → `403 Group default has been deprecated`) ; la liste affiche la clé masquée et `/key` renvoie le texte en clair |
| preuve | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content` ; la clé n'est écrite que lorsque la complétion répond 200 |

## ⚙️ Options du CLI

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Option | Défaut | Signification |
| --- | --- | --- |
| `--count N` | `1` | comptes à créer |
| `--pool FILE` | `proxies.txt` si présent | sorties, une par ligne (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | épingler une seule sortie |
| `--exits K` | `6` | sorties candidates essayées par compte |
| `--model ID` | modèle texte le moins cher | modèle utilisé pour la barrière live |
| `--verify-only` | désactivé | re-prouver `keys.txt` |
| `--screen` | désactivé | vérifier la liste des sorties contre le panneau |
| `--connect-9router` | désactivé | exécuter le connecteur 9Router à côté de ce CLI |

## 🛰️ Derrière un proxy

**Facultatif.** Sans pool, l'outil affiche `DIRECT (no proxy)` et appelle le panneau directement depuis cette machine ; avec un pool, chaque requête au panneau est épinglée à une sortie de la liste et une sortie morte ne coûte que sa propre tentative.

La boîte mail est lue **directement** à dessein : la barrière mail du panneau est séparée de l'hôte API, et faire passer Emailnator par une sortie qui intercepte le TLS répond `unable to get local issuer certificate` (ce qui a déjà fait passer un code livré pour « mail jamais arrivé »). Le lecteur force l'IPv4 car cette build résout d'abord en IPv6 et il n'y a pas de route IPv6.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | une clé prouvée live par ligne — ignoré par git |
| `accounts.json` | un enregistrement JSON par compte (boîte mail, sortie, quota, récompense de check-in, groupe, clé, preuve) — ignoré par git |
| `proxies.txt` | vos sorties — ignoré par git |
| `out/` | journaux d'exécution et enregistrements par compte — ignoré par git |

## 🔌 Se connecter à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé compatible OpenAI :

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Défauts : `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Les modèles sont servis sous `wxctf/<model id>` et le routeur répartit la charge sur toutes les clés du nœud.

## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 Ce que le fournisseur donne | un compte par clé — le check-in quotidien approvisionne chaque clé (~0,012–0,087 $/jour, sans carte, sans captcha) |
| 🔑 Ce qu'une clé vous apporte | une clé API limitée à son propre compte : les 17 ids de modèles sur le point de terminaison ci-dessous, dans les limites de votre offre. Le quota vit sur le **compte**, ce qui explique précisément pourquoi cet outil crée un compte par clé |
| 🌐 Forme de l'API | compatible OpenAI : `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Vos données | seuls ai.wxctf.com et le backend de boîte mail que vous configurez sont contactés ; les clés restent sur votre machine |

## 🐞 Problèmes

Si Wxctf Ai change sa politique d'adresses, la forme de son inscription, le groupe du token ou le catalogue, l'outil s'arrête à cette étape et le signale. **[Ouvrez un ticket](../../issues)** avec la commande et la sortie exacte.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ Risque de compte et minimisation
