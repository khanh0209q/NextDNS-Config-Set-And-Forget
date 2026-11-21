<div align="center">

<h1>
  <img src="./assets/nextdns-logo.png" alt="NextDNS Logo" width="42" align="top" />
  NextDNS Set-and-Forget Config (Home & Office)
</h1>

<p>
  <img src="https://img.shields.io/badge/DNS-NextDNS-007BFF?style=for-the-badge" alt="NextDNS" />
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
> **Core Philosophy:** My goal isn't 100% aggressive blocking at any cost. Instead, I wanted a true **set-and-forget** setup that strips out ads, trackers, and malware while keeping false positives to a minimum—so banking apps, work tools, and everyday browsing keep working without constant allowlisting.

> [!IMPORTANT]
> Rows marked <code>🟡&nbsp;Split</code> need **separate NextDNS profiles**, because NextDNS settings apply per profile (e.g., **Routers & PCs** vs. **Laptops & Phones**, or **Home** vs. **Office**). See the [Deployment Note](#5-deployment-note).

---

<a name="1-security"></a>
<h2><img src="./assets/security.svg" alt="Security" width="30" height="30" align="top" /> 1. Security</h2>

<p align="justify">
  Rather than blindly flipping every switch on, I only enable features with a low risk of false positives. The <sup><code>NEW</code></sup> and <sup><code>EARLY&nbsp;ACCESS</code></sup> labels mirror the dashboard—these features may still change.
</p>

> [!NOTE]
> NextDNS retired the standalone **IDN Homographs**, **Typosquatting** and **Domain Generation Algorithms** toggles. These protections now live inside **AI-Driven Threat Detection**, together with combosquatting, brand impersonation, credential harvesting, malware delivery and decoy-website detection.

| Feature | State | Why I chose this |
| :--- | :---: | :--- |
| **Threat Intelligence Feeds** | <code>🟢&nbsp;ON</code> | Real-time, verified malware and phishing feeds with very few false positives. |
| **AI-Driven Threat Detection** | <code>⚪&nbsp;OFF</code> | Too trigger-happy for a set-and-forget profile—reports on `r/nextdns` and my own logs showed it falsely flagging legitimate CDN and cloud-hosted domains, streaming sites and game download sites, with no sensitivity slider.<br>⚠️ **Trade-off:** turning it off also disables the IDN homograph, typosquatting, combosquatting and DGA protections merged into it. **Threat Intelligence Feeds**, **Algorithmically Generated Domains** and the blocklists cover part of this gap. |
| **Google Safe Browsing** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, GSB wasn't designed as a DNS-level blocker and may flag legitimate CNAME domains as scams, and NextDNS can take months to remove such a false positive. Threat Intel Feeds + HaGeZi/OISD already cover this. |
| **Newly Registered Domains** | <code>⚪&nbsp;OFF</code> | Yokoffing explicitly advises disabling this for set-and-forget setups—it occasionally blocks brand-new legitimate websites, event pages, or startup domains. |
| **Newly Active Domains** <sup><code>NEW</code></sup> | <code>⚪&nbsp;OFF</code> | Per Yokoffing, this tends to flag low-traffic but completely harmless websites. |
| **Algorithmically Generated Domains** <sup><code>NEW</code></sup> | <code>🟢&nbsp;ON</code> | Blocks random-looking domains, word combinations and numbered sequences that attackers use to rotate their infrastructure. |
| **Free Hosting Domains** <sup><code>NEW</code></sup> | <code>⚪&nbsp;OFF</code> | Blocking the default `*.vercel.app`, `*.pages.dev`, or `*.netlify.app` subdomains breaks too many open-source tools, dev demos, and independent web apps (sites on a custom domain are not affected). |
| **Dynamic DNS Hostnames** | <code>🟢&nbsp;ON</code> | Blocks abused free DDNS subdomains used in phishing while leaving DDNS provider sites and update APIs accessible. *(Most modern smart cameras use Cloud P2P and aren't affected. If you self-host via No-IP/DuckDNS, add your own hostname to the **Allowlist**—allowing takes precedence over security features—or turn this **OFF**.)* |
| **Tunneling Endpoints** <sup><code>NEW</code></sup> | <code>⚪&nbsp;OFF</code> | Prevents breaking developer workflows and remote access tools like Cloudflare Tunnel or ngrok. |
| **Untrusted Certificates** <sup><code>NEW</code></sup> | <code>🟢&nbsp;ON</code> | Legitimate websites almost exclusively use trusted SSL/TLS certificates, while NextDNS notes that nearly 60% of commodity malware C2 servers rely on self-signed or untrusted certificates. *(Doesn't affect local IP access like `192.168.1.1` since DNS isn't involved.)* |
| **Residential Hosting** <sup><code>EARLY&nbsp;ACCESS</code></sup> | <code>⚪&nbsp;OFF</code> | Avoids breaking self-hosted services and hobbyist servers. |
| **Data Drop Services** <sup><code>EARLY&nbsp;ACCESS</code></sup> | <code>⚪&nbsp;OFF</code> | NextDNS notes this includes legitimate uses; enabling it breaks webhooks, Pastebin, and developer/forum workflows. |
| **DNS Payload Delivery** <sup><code>EARLY&nbsp;ACCESS</code></sup> | <code>⚪&nbsp;OFF</code> | Still in Early Access, and NextDNS doesn't document exactly how it limits the data DNS responses can carry. Kept off as a precaution, since this could in theory affect large `TXT` records (SPF/DKIM/domain verification) or `HTTPS` records published by CDNs and reverse proxies. |
| **Local Network Addresses** | <code>🟡&nbsp;Split</code> | • **Routers & PCs:** <code>🟢&nbsp;ON</code> to prevent DNS rebinding attacks on LAN devices.<br>• **Laptops & Phones:** <code>⚪&nbsp;OFF</code> so Wi-Fi captive portals at cafés and airports load properly. |
| **Cryptocurrency Mining** | <code>🟢&nbsp;ON</code> | Zero-maintenance background protection against browser cryptojacking. |
| **Decentralized Web Gateways** <sup><code>NEW</code></sup> | <code>🟢&nbsp;ON</code> | Blocks public IPFS gateways heavily abused to host "un-deletable" phishing pages, without affecting normal browsing. |
| **Parked Domains** | <code>🟢&nbsp;ON</code> | Blocks dead-end/expired domains filled with scam ads and malicious redirects. |
| **High-Risk TLDs** <sup><code>EARLY&nbsp;ACCESS</code></sup> | <code>⚪&nbsp;OFF</code> | Skipped for set-and-forget profiles to avoid false positives on legitimate niche domains. |
| **Top-Level Domains (TLDs)** | <code>⚪&nbsp;Empty</code> | Same reason—no manually blocked TLDs. |
| **Child Sexual Abuse Material (CSAM)** | <code>🟢&nbsp;ON</code> | Essential background safety layer with zero impact on normal browsing. |

---

<a name="2-privacy"></a>
<h2><img src="./assets/privacy.svg" alt="Privacy" width="30" height="30" align="top" /> 2. Privacy</h2>

### Blocklists

<p align="justify">
  I keep the blocklist count lean to avoid rule overlap and cumulative false positives:
</p>

| Blocklist | Status | Rationale |
| :--- | :---: | :--- |
| **OISD** + **HaGeZi - Multi NORMAL** | <code>🟢&nbsp;Active</code> | I originally ran `HaGeZi - Multi PRO`. While PRO is great, it proved a bit too aggressive in practice—occasionally breaking page layouts and triggering false positives on local banking portals. Stepping down to **Multi NORMAL** and pairing it with **OISD** (known for its strict "zero-breakage" policy) is the combination yokoffing recommends for set-and-forget profiles: solid ad and tracker blocking without breaking legitimate sites. |
| **ABPVN List** & **hostsVN** | <code>🟢&nbsp;Active</code> | HaGeZi uses both as input sources but keeps mainly widely used domains, so many Vietnamese ad networks, pop-ups and scam redirects still slip through (in November 2026, about 3,500 hostsVN domains were not in HaGeZi NORMAL or OISD). Essential if you browse from Vietnam.<br>*Outside Vietnam, replace these with the regional blocklists for your own country or language.* |
| **NextDNS Ads & Trackers Blocklist**<br>**AdGuard DNS Filter** | <code>🚫&nbsp;Omitted</code> | **Intentionally left out:** all of them overlap heavily with HaGeZi + OISD. In November 2026, roughly 65% of the NextDNS Ads & Trackers entries and 42% of the AdGuard DNS Filter entries were already covered. The remainder includes stale entries and aggressive first-party tracking subdomains, which add false-positive risk for a set-and-forget profile. Yokoffing also recommends removing the NextDNS default list. |

### Privacy Toggles

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Native Tracking** | <code>⚪&nbsp;Empty</code> | Redundant for this profile: `HaGeZi - Multi NORMAL` already blocks the baseline native trackers (the ones safe to block) for Windows, Apple, Samsung, Xiaomi, and others. Yokoffing advises enabling it only if you don't use the recommended blocklists. |
| **Block Rotating Ad Domains** <sup><code>NEW</code></sup> | <code>🟢&nbsp;ON</code> | Catches rotating pop-up and redirect ad infrastructure before blocklists have time to catch up. |
| **Block Disguised Third-Party Trackers** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, NextDNS already uncloaks CNAMEs by default, and this list blocks some referral domains that **Allow Tracking Links** doesn't unblock. The underlying list has also seen very few updates in recent years. |
| **Allow Tracking Links** | <code>🟢&nbsp;ON</code> | Keeps e-commerce links, search ads, and email marketing links working smoothly, while NextDNS automatically hides your IP address from the tracking services. |

---

<a name="3-parental-control-denylist--allowlist"></a>
<h2><img src="./assets/control.svg" alt="Parental Control" width="30" height="30" align="top" /> 3. Parental Control, Denylist & Allowlist</h2>

### Parental Control

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Websites, Apps & Games** | <code>⚪&nbsp;Empty</code> | No service-level restrictions needed for a home/office profile. |
| **Categories** | <code>🟡&nbsp;Split</code> | • **Home:** <code>Gambling</code> — blocks online betting and gambling sites (a major vector for local spam, scams, and malware) while leaving everything else untouched.<br>• **Office:** <code>Porn</code> • <code>Gambling</code> • <code>Dating</code> • <code>Piracy</code> — keeps the work network clean and compliant.<br>⚠️ Don't add the <code>Online Gaming</code> category on an office profile: in my testing it also blocks the App Store. |
| **Recreation Time** | <code>⚪&nbsp;Not&nbsp;set</code> | Only relevant when scheduling exceptions for blocked services. |
| **SafeSearch** | <code>⚪&nbsp;OFF</code> | Left disabled to avoid unnecessary search restrictions. It also blocks search engines that don't support SafeSearch. |
| **YouTube Restricted Mode** | <code>⚪&nbsp;OFF</code> | Left disabled to avoid unnecessary filtering. It also hides all YouTube comments. |
| **Block Bypass Methods** | <code>⚪&nbsp;OFF</code> | As Yokoffing notes, enabling this causes unintended behavior—it targets VPNs, proxies, Tor-related software and encrypted DNS providers, which legitimate apps also use. |

### Denylist & Allowlist

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Denylist** | <code>⚪&nbsp;Empty</code> | I rely entirely on the four blocklists in the **Privacy** tab to keep maintenance zero-touch.<br>To block specific domains without collateral breakage—such as `mask.icloud.com` and `mask-h2.icloud.com` to disable iCloud Private Relay on an office network, as [Apple recommends](https://developer.apple.com/support/prepare-your-network-for-icloud-private-relay/)—see [yokoffing's Denylist recommendations](https://github.com/yokoffing/NextDNS-Config#denylist-no_entry). |
| **Allowlist**<br>↳&nbsp;<code>nextdns.io</code> | <code>✅&nbsp;Allowed</code> | A critical failsafe so you never get locked out of your NextDNS dashboard if a third-party blocklist goes haywire.<br>Type `nextdns.io` without `*.`—the dashboard shows it as `*.nextdns.io` because subdomains are included automatically. |

---

<a name="4-settings--performance"></a>
<h2><img src="./assets/settings.svg" alt="Settings" width="30" height="30" align="top" /> 4. Settings & Performance</h2>

### Logs & Block Page

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Enable Logs** | <code>🟢&nbsp;ON</code> | `Log client IP addresses`: `ON` • `Log domains`: `ON` • `Retention`: `1 week`<br>Keeping domain and client logs for 7 days makes it effortless to spot and allowlist false positives across different home or office devices. |
| **Storage Location** | <img src="./assets/switzerland.svg" alt="CH" width="16" height="16" align="absmiddle" />&nbsp;<code>Switzerland</code> | Benefits from strict Swiss data privacy laws. |
| **Enable Block Page** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, enabling this may cause navigation issues unless NextDNS's Root CA is installed on every device, and it breaks PayPal 2FA, Microsoft Teams, iCloud Private Relay and possibly banking apps.<br>Banking apps that use SSL pinning are especially sensitive: with the block page off, blocked domains simply return `0.0.0.0` and fail fast instead of presenting a NextDNS certificate. |

### Performance & Miscellaneous

| Setting | State | Rationale |
| :--- | :---: | :--- |
| **Anonymized EDNS Client Subnet (ECS)** | <code>⚪&nbsp;OFF</code> | NextDNS runs resolvers inside Vietnam (Hanoi and Ho Chi Minh City), so CDNs that locate users by resolver IP already pick nearby servers without ECS. Disabling it also avoids the anonymized subnet occasionally being geolocated to the wrong country, and the cache fragmentation ECS causes.<br>*If NextDNS also runs resolvers in your country, disabling ECS is a sensible choice there too—check which server you use at `test.nextdns.io`.* |
| **Cache Boost** | <code>🟡&nbsp;Split</code> | Enforces a minimum TTL on DNS answers.<br>• **Routers & PCs:** <code>⚪&nbsp;OFF</code> — modern OSes and routers already handle DNS caching well, and forcing minimum TTLs can delay legitimate DNS updates.<br>• **Laptops & Phones:** optionally <code>🟢&nbsp;ON</code> to reduce background DNS wakeups and save battery, though <code>⚪&nbsp;OFF</code> is still recommended for accuracy. |
| **CNAME Flattening** | <code>⚪&nbsp;OFF</code> | Per Yokoffing, enabling this may break compatibility with Yahoo! Mail and cause issues with certain blocklists. |
| **Bypass Age Verification** <sup><code>BETA</code></sup> | <code>⚪&nbsp;OFF</code> | Kept disabled. |
| **Web3** <sup><code>BETA</code></sup> | <code>⚪&nbsp;OFF</code> | Kept disabled. |
| **Rewrites** | <code>⚪&nbsp;Empty</code> | *(Optional)* Because **Local Network Addresses** is **ON** for Routers & PCs, NextDNS blocks public DNS records resolving to private LAN IPs (`192.168.x.x` / `10.x.x.x`).<br>If you self-host internal services (like a NAS or Home Assistant) under a custom domain, map that domain to its local IP here—NextDNS supports local IPs as rewrite answers. *(If your router has its own DNS rebinding protection, add an exception there too.)* |

---

<a name="5-deployment-note"></a>
<h2><img src="./assets/deployment.svg" alt="Deployment" width="30" height="30" align="top" /> 5. Deployment Note</h2>

<p align="justify">
  To get the full benefit of this <strong>zero-breakage</strong> setup across your devices:
</p>

| Practice | Recommendation | Implementation Details |
| :--- | :---: | :--- |
| **Use Separate Profiles** | <code>🟢&nbsp;Recommended</code> | • **Routers & PCs:** Local Network Addresses `ON`, Cache Boost `OFF`.<br>• **Laptops & Phones:** Local Network Addresses `OFF`, Cache Boost optional.<br>• **Home vs. Office:** only the Parental Control categories differ.<br>Build the first profile, then use **Settings → Duplicate** and change only the <code>🟡&nbsp;Split</code> rows. |
| **Use Encrypted DNS (DoH / DoT)** | <code>🟢&nbsp;Recommended</code> | Configure NextDNS via native OS Encrypted DNS (iOS/macOS Configuration Profile, Android Private DNS, Windows 11 DoH), the official NextDNS app/CLI, or a modern router (OpenWrt, ASUS Merlin, MikroTik). Avoid plain unencrypted IPv4 DNS on ISP modems: it requires a linked IP, which breaks whenever your dynamic IP changes or sits behind CGNAT. |
| **NextDNS Root CA Certificate** | <code>🚫&nbsp;Do&nbsp;Not&nbsp;Install</code> | Because **Enable Block Page** is kept **OFF**, you never need to install or trust NextDNS's root certificate on your devices—keeping native HTTPS/SSL certificate validation completely intact. |

---

<a name="references"></a>
<h2><img src="./assets/references.svg" alt="References" width="30" height="30" align="top" /> References</h2>

| Resource | Role & Description |
| :--- | :--- |
| **[yokoffing/NextDNS-Config](https://github.com/yokoffing/NextDNS-Config)** | The gold-standard guide for low-breakage NextDNS setups. |
| **[HaGeZi's DNS Blocklists](https://github.com/hagezi/dns-blocklists) & [OISD](https://oisd.nl/)** | Well-maintained blocklists with fast false-positive resolution. |
| **[ABPVN](https://abpvn.com/) & [hostsVN](https://github.com/bigdargon/hostsVN)** | Essential community-maintained Vietnamese ad and scam filters. |

---

<p align="center"><sub><i>This project is not affiliated with, endorsed by, or sponsored by NextDNS. "NextDNS" and the NextDNS logo are trademarks of their respective owner.</i></sub></p>
