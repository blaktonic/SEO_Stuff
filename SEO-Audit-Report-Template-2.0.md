# NFX Operational Audit Report

**Domain:** {{domain_url}}
**Date:** {{date}}
**Agent:** NFX Engine (v2.0)
**Scope:** Full Discovery, Technical & Content Audit

---

## 1. Executive Summary
*Faktenbasierte Übersicht der Systemgesundheit.*

| Audit-Bereich | Status | Critical Issues |
| :--- | :--- | :--- |
| **0. Discovery** | {{status_discovery}} | {{issues_discovery_count}} |
| **1. Technical Health** | {{status_tech}} | {{issues_tech_count}} |
| **2. Content & Accessibility**| {{status_content}} | {{issues_content_count}} |
| **3. Authority & Links** | {{status_auth}} | {{issues_auth_count}} |

**🔥 Top 3 Prioritäten (Sofort fixen):**
1. {{prio_1}}
2. {{prio_2}}
3. {{prio_3}}

---

## 2. Inventory & Discovery (Modul 0)

* **Gecrawlte HTML-URLs:** {{crawled_count}}
* **URLs in Sitemap.xml:** {{sitemap_count}}
* **Orphan Pages (Sitemap, aber nicht verlinkt):** {{orphan_count}}
* **Hidden Pages (Verlinkt, aber nicht in Sitemap):** {{hidden_count}}

---

## 3. Technical Deep Dive (Modul 1)

### Status Codes & Indexierbarkeit
* **200 OK:** {{count_200}}
* **3xx Redirects:** {{count_3xx}}
* **4xx Client Errors:** {{count_4xx}} *(Detail-Liste im Anhang)*
* **5xx Server Errors:** {{count_5xx}}
* **Blockiert durch Robots.txt / Meta-Robots:** {{blocked_count}}

### Canonicals & Architektur
* **Fehlende Canonicals:** {{missing_canonical_count}}
* **Canonical Konflikte / Loops:** {{conflict_canonical_count}}
* **Hreflang Fehler:** {{hreflang_errors}}

---

## 4. Content Hygiene & Accessibility (Modul 2)

### Metadata & Struktur
| Element | Missing | Duplicate | Issue |
| :--- | :---: | :---: | :--- |
| **Page Titles** | {{title_missing}} | {{title_dup}} | {{title_len_issue}} |
| **Meta Descriptions** | {{desc_missing}} | {{desc_dup}} | {{desc_len_issue}} |
| **H1 Headlines** | {{h1_missing}} | {{h1_dup}} | {{h1_hierarchy_errors}} |

### Quality & Inclusivity Check
* **Thin Content URLs (<300 Wörter):** {{thin_content_count}}
* **Duplicate Content Cluster:** {{dup_content_clusters}}
* **Bilder ohne beschreibenden Alt-Tag:** {{img_no_alt_count}} von {{img_total_count}} 🚩 *(Barrierefreiheit kritisch!)*
* **Generische Bild-Dateinamen:** {{filename_issue_count}}

---

## 5. Backlink & Ranking Status (Modul 3)

* **Broken Backlinks (404s mit externen Links):** {{broken_backlink_count}}
* **Top 3 Ranking Keywords (GSC):**
    1. {{kw_1}} (Pos: {{pos_1}} | URL: {{url_1}})
    2. {{kw_2}} (Pos: {{pos_2}} | URL: {{url_2}})
    3. {{kw_3}} (Pos: {{pos_3}} | URL: {{url_3}})

---

## 6. Relaunch Clean-Up Check (Modul 4)
*Wurde Relaunch-Modul getriggert? [ {{relaunch_status}} ]*

* **Alte URLs noch indexiert:** {{old_index_count}}
* **Alte URLs (404) mit Backlinks:** {{old_valuable_404}}
* *Mapping-Tabelle für Redirects wurde als CSV generiert.*

---

## Anhang: Developer & Content Action Plan

**[Tech-Tasks]**
- [ ] Fix 4xx Errors
- [ ] Orphan Pages intern verlinken oder aus Sitemap werfen
- [ ] Canonical-Logik bereinigen

**[Content-Tasks]**
- [ ] Beschreibende Alt-Tags für {{img_no_alt_count}} Bilder schreiben
- [ ] Fehlende Meta-Descriptions für {{desc_missing}} URLs nachtragen
- [ ] Thin Content Pages evaluieren (Ausbauen oder noindex)
