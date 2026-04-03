# Silent Push API — Jupyter Notebooks

Interactive Python notebooks for exploring and integrating Silent Push threat intelligence APIs. Each notebook is self-contained and runnable in Jupyter or Google Colab. Use the V3 version for the latest APIs.

## Quick Start

1. Install dependencies:
   ```bash
   pip install requests pandas jupyter
   ```
2. Launch Jupyter and open `silentpush_api_overview_v3.ipynb`
3. Run the **Setup** cell (Section 0) and enter your API credentials when prompted — or set them as environment variables beforehand:
   ```bash
   export SP_API_KEY="your-api-key"
   export SP_ACCESS_KEY="your-access-key"
   ```
4. Run any section independently — each is self-contained after setup.

> Get your API key from the [Silent Push dashboard](https://app.silentpush.com).

---

## Notebook: `silentpush_api_overview_v3.ipynb`

The primary reference notebook. Covers all standard Silent Push APIs used for integrations.

### Contents

| Section | APIs Covered |
|---|---|
| **0 — Setup** | Authentication, credential validation, helper functions |
| **1 — ThreatCheck** | IOFA threat check, Traffic Origin threat check |
| **2 — Data Enrichment** | Enrich domain / IPv4 / IPv6, Bulk enrich (domains, IPv4, IPv6) |
| **3 — Domain Intelligence** | Domain search, WHOIS, SSL certificates, Bulk risk score |
| **4 — Passive DNS (PADNS)** | Forward lookup, Reverse lookup, Multi-condition lookup, Density, IP diversity, ASNs for domain |
| **5 — Reputation & Risk History** | IPv4 reputation history, Nameserver reputation history, Subnet reputation history |
| **6 — SPQL** | Silent Push Query Language search across scan datasources (webscan, torscan, etc.) |
| **7 — Live Scanning** | Live Scan (v2), Live Screenshot (v2) |
| **8 — Data Exports** | IOFA export, Organization export, Bulk data export, IP Context export |
| **9 — Customer Feed Management** | Create feed, Add indicators, Tag indicators |
| **10 — End-to-End Investigation** | Chained workflow: enrich → PADNS → co-hosted domains → bulk score → SPQL deep-dive |

### Key Endpoints Reference

| Section | Method | Endpoint |
|---|---|---|
| ThreatCheck | `GET` | `https://api.threatcheck.silentpush.com/v1/` |
| Enrich indicator | `GET` | `/api/v1/merge-api/explore/enrich/{type}/{indicator}` |
| Bulk enrich domains | `POST` | `/api/v1/merge-api/explore/bulk/summary/domain` |
| Bulk enrich IPv4/IPv6 | `POST` | `/api/v1/merge-api/explore/bulk/ip2asn/{ipv4\|ipv6}` |
| Domain search | `GET` | `/api/v1/merge-api/explore/domain/search` |
| WHOIS | `GET` | `/api/v1/merge-api/explore/domain/whois/{domain}` |
| Forward PADNS | `GET` | `/api/v1/merge-api/explore/padns/lookup/query/{type}/{domain}` |
| Reverse PADNS | `GET` | `/api/v1/merge-api/explore/padns/lookup/answer/{type}/{ip}` |
| SPQL search | `POST` | `/api/v1/merge-api/explore/scandata/search/raw` |
| Live Scan | `GET` | `/api/v2/live-scan/scan-on-demand/query/` |
| Live Screenshot | `GET` | `/api/v2/live-scan/screenshot-on-demand` |
| Customer feeds | `POST` | `/api/v1/feeds/` |

Base URL for most endpoints: `https://app.silentpush.com` and `https://api.silentpush.com`

---

## SPQL Datasources

SPQL queries run against the `/api/v1/merge-api/explore/scandata/search/raw` endpoint. By default a query searches webscan data, but you can scope it to a specific datasource by prefixing your query with `datasource=<name> AND ...`. Beware that different datasources have different availible fields.

### Available Datasources

| Datasource | Description |
|---|---|
| `webscan` | Standard web scan data — HTML, favicons, SSL certs, headers, redirect chains |
| `torscan` | Web scan data collected through the Tor network |
| `services` | Port/service scan data (open ports, banners, service fingerprints) |
| `opendirectory` | Detected open directory listings on web servers |
| `whois` | Whois records collected by Silent Push |

### Query Syntax Examples

```python
# Search a single datasource
query = "datasource=webscan AND favicon_murmur3 = 309020573"

# Search multiple datasources at once
query = "datasource=webscan,torscan AND domain = 'example.com'"

# No datasource prefix — searches across all datasources
query = "ip = '8.8.8.8'"

# Combine datasource scope with field filters
query = "datasource=services AND asn = 15169 AND domain LIKE '%.google.%'"

# Find self-signed certs on newly registered domains (webscan only)
query = "datasource=webscan AND domain_age < 30 AND cert_is_self_signed = true"

# Find all WHOIS records for a specific domain
query = datasource=whois AND domain = "silentpush.com""
```

Full field reference: [SPQL Docs](https://help.silentpush.com/docs/spql-api)

## Requirements

- Python 3.9+
- `requests`, `pandas`, `jupyter` (or `google-colab`)
- A valid Silent Push API key
- Some sections (ThreatCheck) also require a Silent Push **Access Key**

## Resources

- [Silent Push API Highlights](https://help.silentpush.com/docs/silent-push-api-highlights)
- [SPQL Reference](https://help.silentpush.com/docs/spql)
