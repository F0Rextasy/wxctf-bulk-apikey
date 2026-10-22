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

**Wxctf Ai hesaplarını API anahtarlarıyla toplu oluşturun — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-17%20served-412991?style=for-the-badge)](https://ai.wxctf.com/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-17-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

Wxctf Ai (ai.wxctf.com), kaydı **açık** tutan bir New API çatalıdır — captcha yok, OAuth yok, yalnızca e-posta kodu — ve kendi masrafını karşılar: her hesap bakiyeye gerçek kota ekleyen bir **günlük giriş** talep edebilir, böylece her anahtar kart olmadan fonlanmış olarak doğar.

Onu sıra dışı yapan iki şey var. Birincisi, kaydın tamamı **yalnızca API üzerinden** yapılır: düz bir gmail adresi, bir doğrulama e-postası ve bir form POST'u, tarayıcı hiç yok (2026-10-21 ölçümü: beş hesap ~7 dakikada, ardından iki partide on tane daha). İkincisi, panel **adres biçimi konusunda katıdır**: `gmail.com`, `outlook.com` ve `qq.com` kabul eder, ancak yerel kısımda nokta veya `+` bulunan her şeyi "özel karakter" olarak reddeder, dolayısıyla plus/nokta takma adları burada işe yaramaz ve **düz** bir `@gmail.com` kutusu çalışan yoldur.

Diğer tuzaklar da aynı ölçüde belirgin ve hepsi araçta kodlanmıştır: kayıt çağrısı, kodu talep eden **aynı cookie jar** ile ve **taze** bir kodla (10 dakikalık TTL) yapılmalıdır, parola en az 8 karakter olmalıdır ve token grubu canlı `Default` olmalıdır — `default` ile oluşturulan bir token, aktarımda `403 Group default has been deprecated` yanıtı verir.

## 🎁 Ücretsiz katman — ne kadar alıyorsunuz

| Ne | Değer |
| --- | --- |
| Kayıt bonusu | **yok** (kayıttan hemen sonra `quota = 0`) |
| Ücretsiz katman | **günlük giriş** — `POST /api/user/checkin` `quota_awarded` döndürür |
| Ölçülen ödüller | **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** kota (10 hesap, 2026-10-21) |
| USD olarak | **hesap başına günde $0.012–$0.087** (500 000 kota = $1) — her talepte rastgele, **asla sona ermez** |
| Günlük stok | 10 hesap ⇒ **259 288 kota ≈ günde $0.5186**, hesap başına günde bir kez (`checkin_date` zorunlu kılınır) |
| Kart / telefon / OAuth | **hiçbiri** — yalnızca e-posta kodu, `turnstile_check: false` |
| Modeller | 17 canlı id (DeepSeek V4 Flash/Pro, GLM 5.x, Kimi K3, LongCat, Qwen 3.7/3.8) |
| Hız sınırı | yayımlanmamış; 30 çağrılık bir patlama normal yanıtlanır |

> 💡 Ücretsiz katman Wxctf Ai tarafından belirlenir ve değişebilir. Canlı sayıları [ai.wxctf.com](https://ai.wxctf.com) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
```bash
# keys.txt içindeki her anahtar: model listesi artı bir gerçek tamamlama
node src/cli.js --verify-only

# elle (anahtarlarda `sk-` öneki yoktur)
curl https://ai.wxctf.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_KEY" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash","messages":[{"role":"user","content":"gday"}],"max_tokens":64}'
```

Kapı `content || reasoning_content` sayar, böylece bütçesini düşünmeye harcayan bir akıl yürütme modeli bile canlı bir anahtarı kanıtlar.
```

## 🤖 Modeller — 17'sinin tamamı

Bu partinin bir anahtarı üzerinde `GET /v1/models` (2026-10-21) — **17 id**:

| Aile | Id'ler |
| --- | --- |
| DeepSeek V4 | `deepseek-v4-flash`, `deepseek-v4-flash-0731`, `deepseek-v4-pro`, `deepseek-v4-pro-0813`, `deepseek-v4.1-flash`, `deepseek-v4.1-flash-1017` |
| GLM 5.x | `glm-5.1`, `glm-5.2`, `glm-5.3`, `glm-5.3-flash`, `glm-5.3-flashx` |
| Diğerleri | `kimi-k3`, `LongCat-2.0`, `LongCat-2.5-Preview`, `qwen-3.7-max`, `qwen-3.7-plus`, `qwen-3.8-max` |

`/api/pricing` 18 satır listeler (bir id fiyatlandırılmış ama sunulmamıştır) ve **katı $0 satırı yoktur** — ücretsiz katman giriştir, ücretsiz model değil. En ucuz metin modeli `deepseek-v4-flash`'tir.

## 🧪 API nasıl kullanılır

Temel URL **`https://ai.wxctf.com/v1`** — OpenAI uyumlu; anahtarlar 48 karakterdir ve **`sk-` öneki yoktur**, `Authorization: Bearer` başlığına gider.

```python
from openai import OpenAI
c = OpenAI(base_url="https://ai.wxctf.com/v1", api_key="YOUR_KEY")
print(c.chat.completions.create(model="deepseek-v4-flash",
      messages=[{"role":"user","content":"gday"}]).choices[0].message.content)
```

Kota bir hesabın her anahtarı tarafından paylaşılır, dolayısıyla giriş o hesabın günlük bütçesini belirler: **günde anahtar başına bir hesap** doğal biçimdir.

## 🛠 Hızlı başlangıç

```bash
cd wxctf-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # isteğe bağlı — olmadan araç doğrudan çalışır
node src/cli.js --screen                     # çıkışları panele karşı denetle
node src/cli.js --count 5                    # beş fonlanmış anahtar (tarayıcı gerekmez)
node src/cli.js --verify-only                # her anahtarı yeniden kanıtla
node src/cli.js --connect-9router            # bunları yerel bir 9Router'a kaydet
```

Anahtar başına bir hesap: araç düz bir gmail posta kutusu oluşturur, kodu talep eder, onu bekler, aynı cookie jar ile kaydolur, oturum açar, **günlük girişi talep eder**, `Default` grubunda bir token üretir, anahtarı açığa çıkarır ve ancak ondan sonra gerçek bir tamamlamayla kanıtlar.

### 🖱️ Başlatıcı

Başlatıcı proxy listesini (isteğe bağlı) ve hesap sayısını sorar, ikisini de hatırlar ve CLI'yi çalıştırır — ayrıca `--verify-only`, `--screen` ve 9Router bağlayıcısını.

## ✅ Doğrulandı — her anahtar test edilir

Aşağıdaki her kontrol **2026-10-21** tarihinde bu paketin kendi CLI'sinden (`src/cli.js`), genel çıkışlar üzerinden (döngüde VPN yok) çalıştırıldı.

| Kontrol | Sonuç |
| --- | --- |
| gerçek bir akışta kayıt | **tarayıcı gerekmez** — panelde captcha yok (`turnstile_check: false`) |
| düz gmail yolu | Emailnator, **doğrudan** okunur; nokta/`+` panel tarafından "özel karakter" olarak reddedilir |
| kod e-postası | `noreply@<panel-mail>` adresinden «Wxctf Ai email verification», **1–25 sn**, 6 karakterli kod, **10 dakika** geçerli |
| kayıt | aynı cookie jar + taze kod + ≥8 karakterli parola ile **200 `{"success":true}`** |
| ücretsiz katman | `POST /api/user/checkin` → `quota_awarded` **6247 · 11829 · 15361 · 21586 · 26079 · 26112 · 31742 · 36089 · 40940 · 43303** ⇒ **hesap başına günde $0.012–$0.087** |
| günlük stok | 10 hesap ⇒ **259 288 kota ≈ günde $0.5186** (hesap başına günde bir kez) |
| token grubu | **`Default`** — `default` ile oluşturulan bir token `403 Group default has been deprecated` yanıtı verir |
| anahtar yolu | liste onu maskeli gösterir; `POST /api/token/<id>/key` 48 karakterli düz metni döndürür |
| anahtar başına `GET /v1/models` | **200**, 17 id |
| anahtar başına `POST /v1/chat/completions` | içerikle **200** — **10/10 anahtar CANLI** (`--verify-only`), bir anahtar çıkış tarafındaki bir hatadan sonra 2/3 çıkışta yeniden doğrulandı |
| uçtan uca hız | **fonlanmış anahtar başına ~50–90 sn** (posta → kayıt → giriş → anahtar → kanıt) |

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Wxctf Ai page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://ai.wxctf.com/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| posta kutusu | Emailnator düz https üzerinden | **düz** bir gmail (`plain` biçimi, noktalar çıkarılmış); panel noktaları ve `+` işaretini reddeder |
| kod | `GET /api/verification?email=` | `noreply@<panel-mail>` adresinden ~1–25 sn içinde gelir, konu «Wxctf Ai email verification», 6 karakterli kod, 10 dakika geçerli |
| kayıt | `POST /api/user/register` | gövde `{username,password,password2,email,verification_code,aff_code}`; **aynı cookie jar**, **taze** kod, parola ≥8 |
| oturum | `POST /api/user/login` | konsol çağrıları için `data.access_token` (Bearer JWT) |
| fonlar | `POST /api/user/checkin` | `{"data":{"checkin_date":"…","quota_awarded":N}}` — ücretsiz katman, anahtar kanıtlanmadan önce talep edilir |
| anahtar | `POST /api/token/` → `POST /api/token/<id>/key` | grup **`Default`** (`default` kullanımdan kaldırıldı → `403 Group default has been deprecated`); liste anahtarı maskeli gösterir ve `/key` düz metni döndürür |
| kanıt | `GET /v1/models` + `POST /v1/chat/completions` | `content || reasoning_content`; anahtar yalnızca tamamlama 200 yanıtladığında yazılır |

## ⚙️ CLI seçenekleri

```bash
node src/cli.js --count N
node src/cli.js --pool proxies.txt --exits 6
node src/cli.js --proxy socks5://host:port
node src/cli.js --model deepseek-v4-flash
node src/cli.js --verify-only
node src/cli.js --screen
node src/cli.js --connect-9router --base http://localhost:20128 --password 123456
```

| Bayrak | Varsayılan | Anlamı |
| --- | --- | --- |
| `--count N` | `1` | üretilecek hesap sayısı |
| `--pool FILE` | varsa `proxies.txt` | çıkışlar, satır başına bir tane (`http://`, `socks5://`, `socks4://`) |
| `--proxy URL` | — | tek bir çıkışı sabitle |
| `--exits K` | `6` | hesap başına denenen çıkış adayı |
| `--model ID` | en ucuz metin modeli | canlı kapı için kullanılan model |
| `--verify-only` | kapalı | `keys.txt` dosyasını yeniden kanıtla |
| `--screen` | kapalı | çıkış listesini panele karşı denetle |
| `--connect-9router` | kapalı | bu CLI'nin yanında 9Router bağlayıcısını çalıştır |

## 🛰️ Proxy arkasında

**İsteğe bağlı.** Havuz olmadan araç `DIRECT (no proxy)` yazdırır ve paneli doğrudan bu makineden çağırır; havuzla birlikte her panel isteği listeden bir çıkışa sabitlenir ve ölü bir çıkış yalnızca kendi denemesine mal olur.

Posta kutusu kasıtlı olarak **doğrudan** okunur: panelin posta kapısı API ana bilgisayarından ayrıdır ve Emailnator'ı TLS araya giren bir çıkış üzerinden proxy'lemek `unable to get local issuer certificate` yanıtı verir (bu bir kez teslim edilmiş bir kodun "posta hiç gelmedi" gibi görünmesine neden olmuştu). Okuyucu IPv4'ü zorlar çünkü bu derleme önce IPv6'ya çözümlenir ve IPv6 rotası yoktur.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | satır başına bir canlı kanıtlanmış anahtar — git tarafından yok sayılır |
| `accounts.json` | hesap başına bir JSON kaydı (posta kutusu, çıkış, kota, giriş ödülü, grup, anahtar, kanıt) — git tarafından yok sayılır |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `out/` | çalıştırma günlükleri ve hesap başına kayıtlar — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Modeller `wxctf/<model id>` olarak sunulur ve yönlendirici düğümün her anahtarı arasında yük dengelemesi yapar.

## 🔌 OmniRoute'a bağlan

[OmniRoute](https://omniroute.online) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix wxctf`, `--base-url https://ai.wxctf.com/v1`, `--model deepseek-v4-flash`. Modeller `wxctf/<model id>` olarak sunulur ve yönlendirici düğümün her anahtarı arasında yük dengelemesi yapar.
## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **Wxctf Ai** — [ai.wxctf.com](https://ai.wxctf.com) |
| 🎁 Sağlayıcının verdikleri | anahtar başına bir hesap — günlük giriş her anahtarı fonlar (~günde $0.012–0.087, kart yok, captcha yok) |
| 🔑 Bir anahtar size ne kazandırır | kendi hesabına kapsanmış bir API anahtarı: aşağıdaki uç noktada 17 model id'si, planınızın sınırlarında. Kota **hesapta** yaşar, bu aracın anahtar başına bir hesap oluşturmasının nedeni tam da budur |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://ai.wxctf.com/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca ai.wxctf.com ve yapılandırdığınız posta kutusu arka ucuna bağlanılır; anahtarlar makinenizde kalır |

## 🐞 Sorunlar

Wxctf Ai adres politikasını, kayıt biçimini, token grubunu veya kataloğu değiştirirse, araç o adımda durur ve bunu bildirir. Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 wxctf-bulk-apikey katkıda bulunanlar

</div>

---

## ⚠️ Hesap riski ve riski en aza indirme
