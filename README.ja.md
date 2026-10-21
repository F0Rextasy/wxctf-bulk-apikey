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

**Wxctf Ai アカウントを API キー付きで一括作成 — OpenAI 互換推論 + 9Router コネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-17-of-them) · [🧪 API の使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 問題報告](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

Wxctf Ai (ai.wxctf.com) は New API のフォークで、登録を**オープン**に保っています — captcha なし、OAuth なし、メールコードのみ — そして自らを支えます。すべてのアカウントは**デイリーチェックイン**を請求でき、それが実際のクォータを残高に付与するので、各キーはカードなしで資金を持って生まれます。

2 つの点で異例です。第一に、サインアップ全体が **API のみ**です。プレーンな gmail アドレス、1 通の確認メール、そしてフォーム POST だけで、ブラウザは一切不要です (2026-10-21 実測: 5 アカウントを約 7 分で、その後さらに 10 を 2 バッチで)。第二に、パネルは**アドレスの形式に厳格**です。`gmail.com`、`outlook.com`、`qq.com` を受け付けますが、ローカル部分にドットや `+` を含むものは「特殊文字」として拒否するので、プラス/ドットのエイリアスはここでは使えず、**プレーン**な `@gmail.com` のメールボックスが機能するレールです。

他の落とし穴も同様に具体的で、すべてツールに組み込まれています。登録呼び出しは、コードを要求したのと**同じ cookie jar** を、**新鮮な**コード (TTL 10 分) とともに運ぶ必要があり、パスワードは少なくとも 8 文字でなければならず、トークングループは有効な `Default` でなければなりません — `default` で作成されたトークンはリレーで `403 Group default has been deprecated` を返します。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| サインアップボーナス | **なし** (登録直後は `quota = 0`) |
| 無料枠 | **デイリーチェックイン** — `POST /api/user/checkin` が `quota_awarded` を返す |
| 実測付与額 | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** クォータ (10 アカウント、2026-10-21) |
| USD 換算 | **1 アカウントあたり 1 日 $0.012–$0.087** (500 000 クォータ = $1) — 請求ごとにランダム化、**失効しない** |
| 1 日の在庫 | 10 アカウント ⇒ **259 288 クォータ ≈ 1 日 $0.5186**、1 アカウントにつき 1 日 1 回 (`checkin_date` が強制される) |
| カード / 電話 / OAuth | **いずれも不要** — メールコードのみ、`turnstile_check: false` |
| モデル | 17 のライブ id (DeepSeek V4 Flash/Pro、GLM 5.x、Kimi K3、LongCat、Qwen 3.7/3.8) |
| レート制限 | 非公開。30 回のバーストは正常に応答される |

> 💡 無料枠は Wxctf Ai が設定し、変更される可能性があります。最新の数値は [ai.wxctf.com](https://ai.wxctf.com) で確認してください — そしてツール自身のゲートに判断させましょう:

```bash
```bash
# keys.txt のすべてのキー: モデルリストと 1 回の実際の補完
node src/cli.js --verify-only

# 手動で (キーに `sk-` プレフィックスはない)
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

ゲートは `content || reasoning_content` をカウントするので、予算を思考に費やす推論モデルでも、ライブなキーであることを証明できます。
```

## 🤖 モデル — 全 17 種

このバッチのキーでの `GET /v1/models` (2026-10-21) — **17 の id**:

| ファミリー | Id |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| その他 | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` は 18 行をリストします (1 つの id は価格設定されているが提供されていない) が、**厳密に $0 の行はありません** — 無料枠はチェックインであり、無料モデルではありません。最も安いテキストモデルは `deepseek-v4-flash` です。

## 🧪 API の使い方

ベース URL **`https://ai.wxctf.com/v1`** — OpenAI 互換。キーは 48 文字で **`sk-` プレフィックスはなく**、`Authorization: Bearer` ヘッダーに入れます。

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

クォータはアカウントのすべてのキーで共有されるので、チェックインがそのアカウントの 1 日の予算を左右します: **1 日 1 キーにつき 1 アカウント**が自然な形です。

## 🛠 クイックスタート

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # 任意 — なければツールは直接実行
node src/cli.js --screen                     # パネルに対して出口を審査
node src/cli.js --count 5                    # 5 つの資金付きキー (ブラウザ不要)
node src/cli.js --verify-only                # すべてのキーを再証明
node src/cli.js --connect-9router            # ローカル 9Router に登録
```

1 キーにつき 1 アカウント: ツールはプレーンな gmail メールボックスを作成し、コードを要求し、それを待ち、同じ cookie jar で登録し、ログインし、**デイリーチェックインを請求**し、`Default` グループでトークンを発行し、キーを表示し、そして初めて実際の補完でそれを証明します。

### 🖱️ ランチャー

ランチャーはプロキシリスト (任意) とアカウント数を尋ね、両方を記憶して CLI を実行します — さらに `--verify-only`、`--screen`、9Router コネクタも。

## ✅ 検証済み — すべてのキーがテストされている

以下のすべてのチェックは **2026-10-21** にこのパッケージ自身の CLI (`src/cli.js`) から、公開出口経由で実行されました (VPN は介在していません)。

| チェック | 結果 |
| --- | --- |
| 実際のフローでのサインアップ | **ブラウザ不要** — パネルに captcha はない (`turnstile_check: false`) |
| プレーン gmail レール | Emailnator、**直接**読み取り。ドット/`+` はパネルに「特殊文字」として拒否される |
| コードメール | `noreply@<panel-mail>` からの «Wxctf Ai email verification»、**1–25 秒**、6 文字コード、有効期間 **10 分** |
| 登録 | **200 `{"success":true}`**、同じ cookie jar + 新鮮なコード + 8 文字以上のパスワードで |
| 無料枠 | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **1 アカウントあたり 1 日 $0.012–$0.087** |
| 1 日の在庫 | 10 アカウント ⇒ **259 288 クォータ ≈ $0.5186/日** (1 アカウントにつき 1 日 1 回) |
| トークングループ | **`Default`** — `default` で作成されたトークンは `403 Group default has been deprecated` を返す |
| キールート | リストではマスクされて表示される。`POST /api/token/<id>/key` が 48 文字の平文を返す |
| キーごとの `GET /v1/models` | **200**、17 の id |
| キーごとの `POST /v1/chat/completions` | コンテンツ付きで **200** — **10/10 キーが LIVE** (`--verify-only`)、出口側の障害後に 1 つのキーが 2/3 の出口で再確認された |
| エンドツーエンド速度 | **資金付きキー 1 つあたり約 50–90 秒** (メール → 登録 → チェックイン → キー → 証明) |

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| メールボックス | プレーン https 経由の Emailnator | **プレーン**な gmail (`plain` 形式、ドット除去)。パネルはドットと `+` を拒否する |
| コード | `GET /api/verification?email=` | `noreply@<panel-mail>` から約 1–25 秒で到着、件名 «Wxctf Ai email verification»、6 文字コード、有効期間 10 分 |
| 登録 | `POST /api/user/register` | body `{username,password,password2,email,verification_code,aff_code}`。**同じ cookie jar**、**新鮮な**コード、パスワード 8 文字以上 |
| セッション | `POST /api/user/login` | コンソール呼び出し用の `data.access_token` (Bearer JWT) |
| 資金 | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — 無料枠、キーを証明する前に請求される |
| キー | `POST /api/token/` → `POST /api/token/<id>/key` | グループ **`Default`** (`default` は非推奨 → `403 Group default has been deprecated`)。リストはキーをマスクして表示し、`/key` が平文を返す |
| 証明 | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`。キーは補完が 200 を返したときにのみ書き込まれる |

## ⚙️ CLI オプション

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| フラグ | デフォルト | 意味 |
| --- | --- | --- |
| `--count N` | `1` | 作成するアカウント数 |
| `--pool FILE` | 存在すれば `proxies.txt` | 出口、1 行に 1 つ (`http://`、`socks5://`、`socks4://`) |
| `--proxy URL` | — | 単一の出口を固定 |
| `--exits K` | `6` | アカウントごとに試す出口候補 |
| `--model ID` | 最も安いテキストモデル | ライブゲートに使用するモデル |
| `--verify-only` | オフ | `keys.txt` を再証明 |
| `--screen` | オフ | 出口リストをパネルに対して審査 |
| `--connect-9router` | オフ | この CLI の隣で 9Router コネクタを実行 |

## 🛰️ プロキシの背後で

**任意。** プールがなければツールは `DIRECT (no proxy)` と表示し、このマシンから直接パネルを呼び出します。プールがあれば、すべてのパネルリクエストはリストの出口に固定され、死んだ出口はそれ自身の試行分のコストしかかかりません。

メールボックスは意図的に**直接**読み取られます: パネルのメールゲートは API ホストとは別であり、TLS 傍受する出口経由で Emailnator をプロキシすると `unable to get local issuer certificate` を返します (これはかつて配信されたコードを「メールが届かなかった」ように見せました)。リーダーは IPv4 を強制します。このビルドは最初に IPv6 に解決し、IPv6 ルートが存在しないためです。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | ライブ証明済みのキーが 1 行に 1 つ — git 無視 |
| `accounts.json` | アカウントごとに 1 つの JSON レコード (メールボックス、出口、クォータ、チェックイン付与額、グループ、キー、証明) — git 無視 |
| `proxies.txt` | あなたの出口 — git 無視 |
| `out/` | 実行ログとアカウントごとのレコード — git 無視 |

## 🔌 9Router に接続

[9Router](https://9router.com) はセルフホスト型の OpenAI 互換ルーターです:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix wxctf`、`--base-url https://ai.wxctf.com/v1`、`--model deepseek-v4-flash`。モデルは `wxctf/<model id>` として提供され、ルーターはノードのすべてのキーに負荷分散します。

## 🔑 1 つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダー | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 プロバイダーが提供するもの | キーごとに 1 アカウント — デイリーチェックインがすべてのキーに資金を供給 (~$0.012–0.087/日、カードなし、captcha なし) |
| 🔑 1 つのキーで得られるもの | 自身のアカウントにスコープされた 1 つの API キー: 下記エンドポイントの 17 のモデル id を、あなたのプランの制限で。クォータは**アカウント**に存在し、まさにそれがこのツールがキーごとに 1 アカウントを作成する理由です |
| 🌐 API 形式 | OpenAI 互換: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 あなたのデータ | 接触するのは ai.wxctf.com と、あなたが設定した受信箱バックエンドのみ。キーはあなたのマシンに留まります |

## 🐞 問題報告

Wxctf Ai がアドレスポリシー、登録形式、トークングループ、またはカタログを変更した場合、ツールはそのステップで停止し、その旨を伝えます。コマンドと正確な出力を添えて **[issue を開いてください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey contributors

</div>

---

## ⚠️ アカウントリスクと最小化
