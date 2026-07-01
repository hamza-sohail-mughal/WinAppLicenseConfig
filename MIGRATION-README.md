# cloud-server-config.json — migration fields (commit karne se pehle bhar dein)

Ye file GitHub par se app fetch karta hai. Migration ke liye 4 fields hain.
**Approach: JWT token** — web API mein koi change nahi (endpoint pehle se `[Authorize]` JWT accept karta hai).

| Field | Kya dalna hai | Encrypted? |
|---|---|---|
| `cloudServerName` | **PURANA** (old) server ka naam — **jaise hai waise rehne dein** | Haan (pehle se) |
| `newCloudServerName` | **NAYA** (new) server ka naam, e.g. `194.233.71.8,1433` | **Haan — encrypt karke** |
| `newServerId` | Naye server ka registry Id (appsettings.json `ErpSettings:Servers` wala `Id`), e.g. `contabo` | Nahi (plain) |
| `migrationJwtToken` | Aapka admin **JWT token** (login se milta hai) | **Haan — encrypt karke** |

## `newCloudServerName` aur `migrationJwtToken` ko encrypt kaise karein

Wohi tareeqa jo server name ke liye use hota hai — aapki apni API se:

```
POST https://accountrixflow.runasp.net/api/passwordhelper/encrypt
Body (JSON):  { "PlainPassword": "194.233.71.8,1433" }
```

Response ka **`encryptedPassword`** field copy karke `newCloudServerName` mein paste karein.
Yehi step JWT token ke liye dohrayein (PlainPassword = poora JWT token) → result `migrationJwtToken` mein.

> Note: `/encrypt` default key/salt (`AppSecure2025` / `FlowERP@`) use karta hai — yehi client (`CloudServerConfig`)
> decrypt karta hai, is liye kuch aur parameter dene ki zaroorat nahi.

## ⚠️ JWT expiry — zaroori
`appsettings.json` mein `JwtSettings:ExpirationHours = 24` hai → token 24 ghante mein expire.
Migration ke dauran clients ko token valid chahiye. Do options:
- **(Behtar)** Migration se pehle `ExpirationHours` ko temporary `720` (30 din) kar dein, login karke long-lived
  token banayein, encrypt karke yahan daalein. Migration ke baad `24` par wapas kar dein.
- Ya har roz naya token bana kar GitHub JSON update karte rahein.

## Aakhri check (commit se pehle)
- [ ] `newCloudServerName` = encrypted new server name (REPLACE_... nahi bacha)
- [ ] `migrationJwtToken` = encrypted JWT token (REPLACE_... nahi bacha, aur expire na hua ho)
- [ ] `newServerId` = naye server ka sahi Id (appsettings se match kare)
- [ ] `cloudServerName` = abhi bhi PURANA server (taake jab tak migration na ho, purane clients chalte rahein)
