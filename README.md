<div align="center">

<h1>
  <img src="./assets/nextdns-logo.png" alt="NextDNS Logo" width="42" align="top" />
  NextDNS Set-and-Forget Config (Home & Office)
</h1>

<p>
  <img src="https://img.shields.io/badge/DNS-NextDNS-007BFF?style=for-the-badge&logoColor=white" alt="NextDNS" />
  <img src="https://img.shields.io/badge/Philosophy-Zero_Breakage-2EA44F?style=for-the-badge" alt="Zero Breakage" />
  <img src="https://img.shields.io/badge/Environment-Home_%26_Office-6F42C1?style=for-the-badge" alt="Home & Office" />
</p>

<p>
  <a href="#1-security">Security</a> •
  <a href="#2-privacy">Privacy</a> •
  <a href="#3-parental-control-denylist--allowlist">Parental Control, Denylist & Allowlist</a> •
  <a href="#4-settings--performance">Settings & Performance</a> •
  <a href="#5-deployment-note">Deployment Note</a> •
  <a href="#references">References</a>
</p>

</div>

---

<p align="justify">
  This is my daily-driver <strong>NextDNS</strong> configuration, refined through real-world trial and error and heavily inspired by <a href="https://github.com/yokoffing/NextDNS-Config">yokoffing/NextDNS-Config</a> and community discussions on <code>r/nextdns</code>.
</p>

> [!TIP]
> **Core Philosophy:** My goal isn't 100% aggressive blocking at any cost. Instead, I wanted a true **set-and-forget** setup that strips out ads, trackers, and malware while keeping false positives near zero—so banking apps, work tools, and everyday browsing never break.

---

<h2><img src="./assets/security.svg" alt="Security" width="30" height="30" align="top" /> 1. Security</h2>

<p align="justify">
  Rather than blindly flipping every switch on, I only enable features with a proven track record of low false positives.
</p>

> [!NOTE]
> NextDNS deprecated standalone **IDN Homographs** and **Typosquatting** toggles and merged them into **AI-Driven Threat Detection**.

| Feature | State | Why I chose this |
| :--- | :---: | :--- |
| **Threat Intelligence Feeds** | <code>🟢&nbsp;ON</code> | Real-time, verified malware and phishing feeds with virtually zero false positives. |
| **AI-Driven Threat Detection** | <code>⚪&nbsp;OFF</code> | Too trigger-happy in practice—reports on `r/nextdns` and my own logs showed it falsely flagging legitimate CDNs, cloud services, and streaming sites with no sensitivity slider. |
| **Google Safe Browsing** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, GSB wasn't built as a DNS-level blocker; it can falsely flag legitimate CNAMEs and take months to clear. Threat Intel Feeds + HaGeZi/OISD already cover this. |
| **Newly Registered Domains (NRDs)** | <code>⚪&nbsp;OFF</code> | Yokoffing explicitly advises disabling this for set-and-forget setups—it frequently blocks brand-new legitimate websites, event pages, or startup domains. |
| **Newly Active Domains** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, this tends to flag low-traffic but completely harmless websites. |
| **Algorithmically Generated Domains** | <code>🟢&nbsp;ON</code> | Quietly blocks randomized malware/scam domains and anti-adblock scripts with very low false positives. |
| **Free Hosting Domains** | <code>⚪&nbsp;OFF</code> | Blocking `*.vercel.app`, `*.pages.dev`, or `*.netlify.app` breaks too many open-source tools, dev demos, and independent web apps. |
| **Dynamic DNS Hostnames** | <code>🟢&nbsp;ON</code> | Blocks abused free DDNS subdomains used in phishing while leaving DDNS provider sites and update APIs accessible. *(Most modern smart cameras use Cloud P2P and aren't affected, though you should turn this **OFF** if you self-host via No-IP/DuckDNS).* |
| **Tunneling Endpoints** | <code>⚪&nbsp;OFF</code> | Prevents breaking developer workflows and remote access tools like Cloudflare Tunnel or ngrok. |
| **Residential Hosting** | <code>⚪&nbsp;OFF</code> | Avoids breaking self-hosted services and hobbyist servers. |
| **Untrusted Certificates** | <code>🟢&nbsp;ON</code> | Major legitimate websites use valid SSL/TLS certs, while many malware C2 servers rely on self-signed certs. *(Doesn't affect local IP access like `192.168.1.1` since DNS isn't involved).* |
| **Data Drop Services** | <code>⚪&nbsp;OFF</code> | NextDNS notes this includes legitimate uses; enabling it breaks webhooks, Pastebin, and developer/forum workflows. |
| **DNS Payload Delivery** | <code>⚪&nbsp;OFF</code> | Still in Early Access; restricting DNS payload sizes can interfere with large `TXT` records (SPF/DKIM email verification) and reverse proxies. |
| **Local Network Addresses** | <code>🟡&nbsp;Split</code> | **ON** for **Routers & PCs** to prevent DNS Rebinding attacks on LAN devices.<br>**OFF** for **Laptops & Phones** so Wi-Fi captive portals at cafés and airports load properly. |
| **Cryptocurrency Mining** | <code>🟢&nbsp;ON</code> | Zero-maintenance background protection against browser cryptojacking. |
| **Decentralized Web Gateways** | <code>🟢&nbsp;ON</code> | Blocks public IPFS/Web3 bridges heavily abused to host "un-deletable" phishing pages without affecting normal browsing. |
| **Parked Domains** | <code>🟢&nbsp;ON</code> | Blocks dead-end/expired domains filled with scam ads and malicious redirects. |
| **High-Risk TLDs / Custom TLDs** | <code>⚪&nbsp;OFF&nbsp;/&nbsp;Empty</code> | Skip this for set-and-forget profiles to avoid false positives on legitimate niche domains. |
| **Child Sexual Abuse Material (CSAM)** | <code>🟢&nbsp;ON</code> | Essential background safety layer with zero impact on normal browsing. |

---

<h2><img src="./assets/privacy.svg" alt="Privacy" width="30" height="30" align="top" /> 2. Privacy</h2>

### Blocklists

<p align="justify">
  I keep the blocklist count lean to avoid rule overlap and cumulative false positives:
</p>

| Blocklist | Status | Rationale |
| :--- | :---: | :--- |
| **HaGeZi - Multi NORMAL** + **OISD** | <code>🟢&nbsp;Active</code> | I originally ran `HaGeZi - Multi PRO`. While PRO is great, it proved a bit too aggressive in practice—occasionally breaking page layouts and triggering false positives on local banking portals. Stepping down to **Multi NORMAL** and pairing it with **OISD** (famous for its strict "zero-breakage" policy) hits the sweet spot: solid ad and tracker blocking without breaking legitimate sites. |
| **ABPVN List** & **hostsVN** | <code>🟢&nbsp;Active</code> | Global lists often miss regional ad networks, pop-ups, and scam redirects, so these two are essential if you are browsing from Vietnam. |
| **NextDNS Ads & Trackers Blocklist** & **AdGuard DNS Filter** | <code>🚫&nbsp;Omitted</code> | **What I intentionally left out:** Over 90% redundant since both HaGeZi and OISD already incorporate their sources. Stacking them only increases false-positive risk. |

### Privacy Toggles

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Native Tracking Protection** | <code>⚪&nbsp;Empty</code> | Redundant. `HaGeZi - Multi NORMAL` already covers OS-level telemetry for Windows, Apple, Samsung, Xiaomi, and others. |
| **Block Rotating Ad Domains** | <code>🟢&nbsp;ON</code> | Catches rotating pop-up and redirect ad infrastructure before blocklists have time to catch up. |
| **Block Disguised Third-Party Trackers** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, NextDNS already uncloaks CNAMEs by default, and this legacy list hasn't been updated in years—leaving it on breaks legitimate referral links. |
| **Allow Tracking Links** | <code>🟢&nbsp;ON</code> | Keeps e-commerce links, search ads, and email marketing links working smoothly while NextDNS proxies your IP to protect your privacy. |

---

<h2><img src="./assets/control.svg" alt="Parental Control" width="30" height="30" align="top" /> 3. Parental Control, Denylist & Allowlist</h2>

### Parental Control

| Setting / Selected Option | State | Rationale |
| :--- | :---: | :--- |
| **Categories**<br>↳ <code>Gambling</code> | <code>🟢&nbsp;ON</code> | Blocks online betting and gambling sites (a major vector for local spam, scams, and malware) while leaving everything else untouched. |
| **Block Bypass Methods** | <code>⚪&nbsp;OFF</code> | As Yokoffing notes, enabling this causes unintended behavior with legitimate encrypted apps and VPNs. |
| **Everything else**<br>*(SafeSearch, YouTube Restricted Mode, etc.)* | <code>⚪&nbsp;OFF</code> | Left disabled to avoid unnecessary filtering or search restrictions. |

### Denylist & Allowlist

| Setting / Selected Domain | State | Rationale |
| :--- | :---: | :--- |
| **Denylist** | <code>⚪&nbsp;Empty</code> | I rely entirely on the four blocklists in the **Privacy** tab to keep maintenance zero-touch.<br>If you want to manually block specific domains accurately without causing collateral breakage (such as adding `mask.icloud.com` and `mask-h2.icloud.com` to disable iCloud Private Relay on an office network), check out [yokoffing's Denylist recommendations](https://github.com/yokoffing/NextDNS-Config#denylist-). |
| **Allowlist**<br>↳ <code>*.nextdns.io</code> | <code>✅&nbsp;Allowed</code> | A critical failsafe so you never get locked out of your NextDNS dashboard if a third-party blocklist goes haywire. |

---

<h2><img src="./assets/settings.svg" alt="Settings" width="30" height="30" align="top" /> 4. Settings & Performance</h2>

### Logs & Block Page

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Enable Logs** | <code>🟢&nbsp;ON</code> | `Log client IP addresses`: `ON` • `Log domains`: `ON` • `Retention`: `1 week`<br>Keeping domain and client logs for 7 days makes it effortless to spot and whitelist false positives across different home or office devices. |
| **Storage Location** | <img src="./assets/switzerland.svg" alt="CH" width="19" height="16" align="absmiddle" /><code>Switzerland</code> | Benefits from strict Swiss data privacy laws. |
| **Enable Block Page** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, enabling this without manually installing NextDNS's Root CA on every device triggers SSL warnings and breaks banking apps, PayPal 2FA, Microsoft Teams, and iCloud Private Relay. |

### Performance & Miscellaneous

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Anonymized EDNS Client Subnet (ECS)** | <code>⚪&nbsp;OFF</code> | Disabling ECS avoids cache fragmentation and lets NextDNS's local Anycast nodes route traffic to the closest CDN servers *(noticeably more reliable on local ISPs like Viettel and VNPT)*. |
| **Cache Boost** | <code>🟡&nbsp;Split</code> | • **Routers & PCs:** <code>⚪&nbsp;OFF</code> (modern OSes and routers already handle DNS caching well, and forcing minimum TTLs can delay legitimate DNS updates).<br>• **Laptops & Phones:** You can optionally toggle it <code>🟢&nbsp;ON</code> to reduce background DNS wakeups and save battery, though keeping it <code>⚪&nbsp;OFF</code> is still recommended for accuracy. |
| **CNAME Flattening** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, enabling this can break compatibility with legacy email services (like Yahoo! Mail) and interfere with certain filter rules. |
| **Rewrites** | <code>⚪&nbsp;Empty</code> | *(Optional)* — Because **Local Network Addresses** is turned **ON** for Routers & PCs, NextDNS blocks public DNS records resolving to private LAN IPs (`192.168.x.x` / `10.x.x.x`).<br>If you self-host internal services (like a NAS or Home Assistant) using a custom domain name, map that domain to its local IP here so NextDNS resolves it cleanly. |
| **Bypass Age Verification (BETA)** | <code>⚪&nbsp;OFF</code> | Kept disabled. |
| **Web3 (BETA)** | <code>⚪&nbsp;OFF</code> | Kept disabled. |

---

<h2><img src="./assets/deployment.svg" alt="Deployment" width="30" height="30" align="top" /> 5. Deployment Note</h2>

<p align="justify">
  To get the full benefit of this <strong>zero-breakage</strong> setup across your devices:
</p>

| Practice | Recommendation | Implementation Details |
| :--- | :---: | :--- |
| **Use Encrypted DNS (DoH / DoT)** | <code>🟢&nbsp;Recommended</code> | Configure NextDNS via native OS Encrypted DNS (iOS/macOS Configuration Profile, Android Private DNS, Windows 11 DoH), the official NextDNS app/CLI, or a modern router (OpenWrt, ASUS Merlin, MikroTik). Avoid relying on plain unencrypted IPv4 DNS on ISP modems, which breaks whenever your dynamic IP changes or gets affected by CGNAT. |
| **NextDNS Root CA Certificate** | <code>🚫&nbsp;Do&nbsp;Not&nbsp;Install</code> | Because **Enable Block Page** is kept **OFF**, you never need to install or trust NextDNS's root certificate on your devices—keeping native HTTPS/SSL certificate validation completely intact. |

---

<h2><img src="./assets/references.svg" alt="References" width="30" height="30" align="top" /> References</h2>

| Resource | Role & Description |
| :--- | :--- |
| **[yokoffing/NextDNS-Config](https://github.com/yokoffing/NextDNS-Config)** | The gold-standard guide for low-breakage NextDNS setups. |
| **[HaGeZi's DNS Blocklists](https://github.com/hagezi/dns-blocklists) & [OISD](https://oisd.nl/)** | Well-maintained blocklists with fast false-positive resolution. |
| **[ABPVN](https://abpvn.com/) & [hostsVN](https://github.com/bigdargon/hostsVN)** | Essential community-maintained Vietnamese ad and scam filters. |
