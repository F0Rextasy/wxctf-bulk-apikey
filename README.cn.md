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

**批量创建带 API 密钥的 Wxctf Ai 账户 — 兼容 OpenAI 的推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-17-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>


---

## ✨ 为什么会有这个项目

Wxctf Ai（ai.wxctf.com）是 New API 的一个分支，它保持注册**开放**——没有验证码、没有 OAuth，仅需邮箱验证码——而且能自给自足：每个账户都可以领取**每日签到**，为余额带来真实额度，因此每个密钥一出生就有资金，无需银行卡。

有两点让它与众不同。第一，整个注册流程**仅通过 API**：一个普通 gmail 地址、一封验证邮件和一个表单 POST，完全不需要浏览器（2026-10-21 实测：约 7 分钟创建五个账户，随后分两批又创建了十个）。第二，面板对**地址格式非常严格**：它接受 `gmail.com`、`outlook.com` 和 `qq.com`，但会拒绝本地部分中带点或 `+` 的地址，视为“特殊字符”，因此加号/点别名在这里毫无用处，一个**普通**的 `@gmail.com` 邮箱才是可行的通道。

其他陷阱同样具体，且都已编码进工具中：注册调用必须携带与请求验证码时**相同的 cookie jar**，并使用**新鲜的**验证码（10 分钟有效期），密码必须至少 8 个字符，令牌分组必须是有效的 `Default`——用 `default` 创建的令牌在中继上会返回 `403 Group default has been deprecated`。

## 🎁 免费额度 — 你能获得多少

| 项目 | 数值 |
| --- | --- |
| 注册奖励 | **无**（注册后立即 `quota = 0`） |
| 免费额度 | **每日签到** — `POST /api/user/checkin` 返回 `quota_awarded` |
| 实测奖励 | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** 额度（10 个账户，2026-10-21） |
| 折合美元 | **每账户每天 $0.012–$0.087**（500 000 额度 = $1）——每次领取随机，**永不过期** |
| 每日存量 | 10 个账户 ⇒ **259 288 额度 ≈ 每天 $0.5186**，每账户每天一次（强制校验 `checkin_date`） |
| 银行卡 / 手机 / OAuth | **都不需要** — 仅邮箱验证码，`turnstile_check: false` |
| 模型 | 17 个在线 id（DeepSeek V4 Flash/Pro、GLM 5.x、Kimi K3、LongCat、Qwen 3.7/3.8） |
| 速率限制 | 未公布；30 次突发调用可正常响应 |

> 💡 免费额度由 Wxctf Ai 设定，可能变化。请在 [ai.wxctf.com](https://ai.wxctf.com) 查看实时数值——并让工具自身的关卡来判定：

```bash
```bash
# keys.txt 中的每个密钥：模型列表加一次真实补全
node src/cli.js --verify-only

# 手动操作（密钥不带 `sk-` 前缀）
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

关卡会统计 `content || reasoning_content`，因此一个把预算花在思考上的推理模型仍能证明密钥有效。
```

## 🤖 模型 — 全部 17 个

本批次某个密钥上的 `GET /v1/models`（2026-10-21）——**17 个 id**：

| 系列 | Id |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| 其他 | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` 列出 18 行（有一个 id 有定价但未提供）且**没有严格 $0 的行**——免费额度是签到，而不是免费模型。最便宜的文本模型是 `deepseek-v4-flash`。

## 🧪 如何使用 API

基础 URL **`https://ai.wxctf.com/v1`** — 兼容 OpenAI；密钥为 48 个字符，**不带 `sk-` 前缀**，放在 `Authorization: Bearer` 请求头中。

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

额度由同一账户的所有密钥共享，因此签到决定了该账户的每日预算：**每密钥每天一个账户**是自然的形态。

## 🛠 快速开始

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # 可选 — 没有它工具将直连运行
node src/cli.js --screen                     # 针对面板筛查出口
node src/cli.js --count 5                    # 五个已充值密钥（无需浏览器）
node src/cli.js --verify-only                # 重新验证每个密钥
node src/cli.js --connect-9router            # 将它们注册到本地 9Router
```

每密钥一个账户：工具创建一个普通 gmail 邮箱，请求验证码，等待它，用相同的 cookie jar 注册，登录，**领取每日签到**，在 `Default` 分组中铸造令牌，显示密钥，然后才用一次真实补全来验证它。

### 🖱️ 启动器

启动器会询问代理列表（可选）和账户数量，记住两者并运行 CLI——外加 `--verify-only`、`--screen` 和 9Router 连接器。

## ✅ 已验证可用 — 每个密钥都经过测试

以下每项检查均于 **2026-10-21** 在本包自带的 CLI（`src/cli.js`）上运行，通过公共出口（全程无 VPN）。

| 检查 | 结果 |
| --- | --- |
| 真实流程中的注册 | **无需浏览器** — 面板没有验证码（`turnstile_check: false`） |
| 普通 gmail 通道 | Emailnator，**直接**读取；点和 `+` 会被面板拒绝为“特殊字符” |
| 验证码邮件 | 来自 `noreply@<panel-mail>` 的 «Wxctf Ai email verification»，**1–25 秒**，6 位验证码，有效期 **10 分钟** |
| 注册 | **200 `{"success":true}`**，使用相同的 cookie jar + 新鲜验证码 + ≥8 字符密码 |
| 免费额度 | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **每账户每天 $0.012–$0.087** |
| 每日存量 | 10 个账户 ⇒ **259 288 额度 ≈ 每天 $0.5186**（每账户每天一次） |
| 令牌分组 | **`Default`** — 用 `default` 创建的令牌会返回 `403 Group default has been deprecated` |
| 密钥路由 | 列表显示为掩码；`POST /api/token/<id>/key` 返回 48 字符明文 |
| 每个密钥的 `GET /v1/models` | **200**，17 个 id |
| 每个密钥的 `POST /v1/chat/completions` | **200** 且有内容 — **10/10 密钥在线**（`--verify-only`），一个密钥在出口侧故障后在 2/3 出口上重新确认 |
| 端到端速度 | **每个已充值密钥约 50–90 秒**（邮件 → 注册 → 签到 → 密钥 → 验证） |


## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 说明 |
| --- | --- | --- |
| 邮箱 | 通过纯 https 使用 Emailnator | 一个**普通** gmail（`plain` 形式，去除点）；面板拒绝点和 `+` |
| 验证码 | `GET /api/verification?email=` | 约 1–25 秒内到达，来自 `noreply@<panel-mail>`，主题 «Wxctf Ai email verification»，6 位验证码，有效期 10 分钟 |
| 注册 | `POST /api/user/register` | 请求体 `{username,password,password2,email,verification_code,aff_code}`；**相同 cookie jar**，**新鲜**验证码，密码 ≥8 |
| 会话 | `POST /api/user/login` | `data.access_token`（Bearer JWT）用于控制台调用 |
| 资金 | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — 免费额度，在密钥验证前领取 |
| 密钥 | `POST /api/token/` → `POST /api/token/<id>/key` | 分组 **`Default`**（`default` 已弃用 → `403 Group default has been deprecated`）；列表显示掩码密钥，`/key` 返回明文 |
| 验证 | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`；仅当补全返回 200 时才写入密钥 |

## ⚙️ CLI 选项

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| 标志 | 默认值 | 含义 |
| --- | --- | --- |
| `--count N` | `1` | 要铸造的账户数 |
| `--pool FILE` | 若存在则为 `proxies.txt` | 出口，每行一个（`http://`、`socks5://`、`socks4://`） |
| `--proxy URL` | — | 固定单个出口 |
| `--exits K` | `6` | 每个账户尝试的出口候选数 |
| `--model ID` | 最便宜的文本模型 | 用于在线关卡验证的模型 |
| `--verify-only` | 关闭 | 重新验证 `keys.txt` |
| `--screen` | 关闭 | 针对面板筛查出口列表 |
| `--connect-9router` | 关闭 | 在此 CLI 旁运行 9Router 连接器 |

## 🛰️ 在代理之后

**可选。** 没有代理池时，工具会打印 `DIRECT (no proxy)` 并直接从本机调用面板；有代理池时，每个面板请求都固定到列表中的一个出口，一个失效的出口只会损失它自己的那次尝试。

邮箱是**有意直接**读取的：面板的邮件网关与 API 主机是分开的，通过 TLS 拦截出口代理 Emailnator 会返回 `unable to get local issuer certificate`（这曾让一封已送达的验证码看起来像“邮件从未到达”）。读取器强制使用 IPv4，因为此构建会优先解析到 IPv6，而这里没有 IPv6 路由。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 每行一个经在线验证的密钥 — 已被 git 忽略 |
| `accounts.json` | 每个账户一条 JSON 记录（邮箱、出口、额度、签到奖励、分组、密钥、验证）— 已被 git 忽略 |
| `proxies.txt` | 你的出口 — 已被 git 忽略 |
| `out/` | 运行日志和每账户记录 — 已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、兼容 OpenAI 的路由器：

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

默认值：`--prefix wxctf`、`--base-url https://ai.wxctf.com/v1`、`--model deepseek-v4-flash`。模型以 `wxctf/<model id>` 形式提供，路由器会在该节点的每个密钥之间进行负载均衡。

## 🔑 一个密钥能给你什么

| | |
| --- | --- |
| 🏢 提供商 | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 提供商提供什么 | 每密钥一个账户 — 每日签到为每个密钥提供资金（约 $0.012–0.087/天，无银行卡，无验证码） |
| 🔑 一个密钥能给你什么 | 一个作用域为其自身账户的 API 密钥：下方端点上的 17 个模型 id，受你套餐的限制。额度存在于**账户**上，这正是本工具为每密钥创建一个账户的原因 |
| 🌐 API 形态 | 兼容 OpenAI：`POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 你的数据 | 仅联系 ai.wxctf.com 和你配置的收件箱后端；密钥保留在你的机器上 |

## 🐞 问题

如果 Wxctf Ai 更改其地址策略、注册形态、令牌分组或目录，工具会在该步骤停止并说明原因。请**[提交 issue](../../issues)**，附上命令和确切输出。

## 📄 许可证

MIT © 2026 — 见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey 贡献者

</div>

---

## ⚠️ 账号风险与降低方法
