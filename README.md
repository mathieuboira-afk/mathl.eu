# mathl.eu — page « in process »

## 1. Déployer (≈ 2 min)
Prérequis : Node 18+, zone mathl.eu active sur Cloudflare (nameservers Cloudflare chez ton registrar .eu).

    npm i -g wrangler
    wrangler login            # connexion navigateur (pas besoin de coller de token)
    wrangler deploy           # lit wrangler.jsonc

Les routes `custom_domain` créent toutes seules les enregistrements DNS proxifiés
pour `mathl.eu` et `www.mathl.eu` + le certificat SSL.
Avant : supprime les anciens A / AAAA / CNAME existants sur `@` et `www`, sinon conflit.

La page est servie avec le statut HTTP 404 sur toutes les URLs (not_found_handling = "404-page").

## 2. DNS optimal (aucun mail pour l'instant → domaine verrouillé)
| Type | Nom | Contenu | Proxy |
|------|-----|---------|-------|
| (auto) | @ | Worker `mathl-eu` | Proxied |
| (auto) | www | Worker `mathl-eu` | Proxied |
| TXT | @ | `v=spf1 -all` | DNS only |
| TXT | _dmarc | `v=DMARC1; p=reject; adkim=s; aspf=s;` | DNS only |
| TXT | *._domainkey | `v=DKIM1; p=` | DNS only |
| MX | @ | `.` (priorité 0, « null MX ») | DNS only |

Quand tu voudras des mails (Google Workspace, iCloud, Proton…), remplace SPF / MX / DKIM / DMARC.

## 3. Réglages Cloudflare (dashboard → mathl.eu)
- SSL/TLS → mode **Full (strict)**
- SSL/TLS → Edge Certificates : **Always Use HTTPS** ON, **Minimum TLS 1.2**, **TLS 1.3** ON, Automatic HTTPS Rewrites ON
- DNS → **DNSSEC** → Enable, puis copie le DS chez ton registrar .eu
- (plus tard, quand le site sera stable) HSTS
