---
name: seo-geo-analyse
description: >
  Erstellt eine vollständige SEO-GEO-Analyse Excel-Datei für eine Domain —
  10 Sheets: Entitäten-Check, Topical-Authority, Authority Map, Authority Proof,
  Content-Hub Plan, GEO/LLM Trigger, Authority Gap Score, GEO-Prompt-Research,
  AI-Overview-Optimierung, Basic-SEO-Themen. Input: Domain-Name +
  optional Screaming Frog Exporte (intern_alle.xls, hreflang_alle.xls).
  Auslöser: "seo geo analyse", "geo analyse", "seo analyse excel",
  "llm analyse", "authority analyse", "geo skill".
---

# SEO-GEO-Analyse — Vollständige Analyse-Excel

## Output

Eine Excel-Datei `<domain>_SEO-GEO-Analyse.xlsx` mit 10 Sheets:

| Sheet | Inhalt |
|---|---|
| **Entitäten-Check** | LLM-Verankerungsstatus der Marke + Handlungsempfehlungen |
| **Topical-Authority** | Themengebiete mit Expertise, Intent, Differenzierung |
| **Authority Map** | 5 Kern-Autoritätsfelder mit Konsequenzen und Maßnahmen |
| **Authority Proof** | Soft- vs Hard-Proof-Matrix + Asset-Priorisierung |
| **Content-Hub Plan** | Pillar- und Cluster-Seiten pro Hub mit URLs |
| **GEO / LLM Trigger** | LLM-Fragen, AIO-Snippets, Entity-Optimierung, H2/H3-Struktur |
| **Authority Gap Score** | Scoring über 5 Dimensionen, Lückenanalyse |
| **GEO-Prompt-Research** | 3-Phasen-Prompts + Top Content-Briefs |
| **AI-Overview-Optimierung** | Snippet-ready HTML + FAQ-Schema pro Seite |
| **Basic-SEO-Themen** | Technische Fixes aus Screaming Frog + Schema + Seitenstruktur |

---

## Prozess

### Schritt 1 — Input-Dateien erkennen

```bash
find . -maxdepth 2 \( -name "intern_alle.xls" -o -name "hreflang_alle.xls" \) | sort
```

Folgende Dateien werden erkannt und verarbeitet:
- `intern_alle.xls` — Screaming Frog "Alle internen URLs" Export
- `hreflang_alle.xls` — Screaming Frog "Hreflang" Export

Falls nicht vorhanden: Basic-SEO-Themen nur mit Live-Crawl befüllen.

**XLS einlesen:**
```python
import xlrd
wb = xlrd.open_workbook('intern_alle.xls')
ws = wb.sheet_by_index(0)
headers = [ws.cell_value(0, c) for c in range(ws.ncols)]
h = {v: i for i, v in enumerate(headers)}
```

**Aus intern_alle.xls extrahieren:**
- 404-Seiten: Status-Code == 404
- Canonicals die auf 404 zeigen: Canonical-Linkelement != Adresse UND Ziel-URL ist 404
- Seiten mit mehreren H1: H1-2 Spalte nicht leer
- Fehlende H1: H1-1 leer bei HTML-200-Seiten
- Duplicate Meta Descriptions: Counter über alle 200er HTML-Seiten
- noindex-Seiten: Indexierbarkeitsstatus enthält "noindex"
- 301-Redirects: Status-Code == 301
- Thin Content: Wortanzahl < 300 bei indexierbaren HTML-Seiten
- Crawltiefe-Verteilung: Crawltiefe-Spalte

**Aus hreflang_alle.xls extrahieren:**
- Prüfen ob HTML-Hreflang-1-Spalte Werte enthält
- Falls alle leer: hreflang komplett abwesend → Befund dokumentieren
- Falls vorhanden: Häufigkeit, Werte, Inkonsistenzen prüfen

### Schritt 2 — Live Website Research

Folgende URLs fetchen und analysieren:

```bash
# robots.txt
curl -s "https://<domain>/robots.txt" | head -30

# sitemap
curl -s "https://<domain>/sitemap.xml" | head -50

# Homepage HTML (für Schema, H1, hreflang, canonical)
curl -s "https://<domain>/" | python3 -c "
import sys; html = sys.stdin.read()
# Schema.org prüfen
import re
schemas = re.findall(r'\"@type\":\s*\"([^\"]+)\"', html)
print('Schema types:', schemas)
# hreflang
hreflangs = re.findall(r'hreflang=\"([^\"]+)\"', html)
print('Hreflang tags:', hreflangs)
# Canonical
canon = re.findall(r'rel=\"canonical\"[^>]+href=\"([^\"]+)\"', html)
print('Canonical:', canon)
# H1
h1 = re.findall(r'<h1[^>]*>(.*?)</h1>', html, re.DOTALL)
print('H1:', [re.sub('<[^>]+>', '', x).strip()[:80] for x in h1])
"
```

Außerdem 3–5 Kernseiten fetchen (Leistungsseiten / wichtigste URLs aus intern_alle.xls nach Inlinks sortiert) und auf Schema.org-Typen prüfen.

Entitäten-Selbstauskunft: Claude gibt an, ob die Marke im Trainings-Wissen bekannt ist, welche Assoziationen bestehen und wie stabil der semantische Vektor ist.

---

### Schritt 3 — Analyse-Daten vorbereiten (alle 10 Sheets)

Aus dem Research werden folgende Python-Daten-Strukturen befüllt, die dann das Excel-Skript schreibt. **Alle Inhalte auf Deutsch.**

---

#### ENTITY_CHECK — Entitäten-Check

```python
ENTITY = {
    "result": "🟢 Bekannt" | "🔴 Unbekannt" | "🟡 Schwach verankert",
    "result_detail": "Kurze Begründung (1–2 Sätze)",
    "domain": "example.at",
    "brand_name": "Markenname",
    "why_not_anchored": [
        "Erklärung 1",
        "Erklärung 2",
    ],
    "training_sources": [
        "öffentlich zugängliche Webtexte",
        "Medienberichte",
        "Branchenverzeichnisse",
        "Fachartikel",
        "Diskussionsforen",
        "strukturierte Datenquellen",
    ],
    "no_cluster_reasons": [
        "Keine stabilen Co-Occurrence-Muster mit Personen / Medienzitaten / Fachartikeln",
    ],
    "context_links": [
        "Begriff 1 für Kontextverknüpfung",
        "Begriff 2",
        "Begriff 3",
        "Begriff 4",
        "Begriff 5",
    ],
    "structured_mentions": [
        "Einträge in Branchenverzeichnissen",
        "Pressemitteilungen",
        "Fachartikel (Gastbeiträge)",
        "Erwähnungen in Podcasts",
        "Konferenz-Speaker-Profile",
        "Interviews mit Gründern",
        "LinkedIn-Artikel mit klarer Markennennung",
        "Wikipedia-relevante Erwähnungen",
    ],
    "semantic_positioning": [
        "Eindeutige Beschreibung (z.B. „...")",
        "Wiederkehrende Claims",
        "Konsistente Markenbeschreibung auf allen Plattformen",
        "Klare Zuordnung zu einem Themencluster",
    ],
    "knowledge_graph_anchors": [
        "Gründer mit öffentlichem Profil",
        "Konkrete Produkte / Leistungen",
        "Standort",
        "Partner",
        "Zertifizierungen",
        "Medienberichte",
    ],
}
```

---

#### TOPICAL_AUTHORITY — Topical-Authority

```python
TOPICS = [
    {
        "priority": 1,
        "topic": "Thema 1",
        "expertise": 5,          # 1–5
        "confidence": "0.9",     # 0.0–1.0 als String
        "intent": "Commercial",  # Commercial | Informational | Trust | Comparison
        "differentiation": "Abgrenzung zu Wettbewerbern",
        "role": "Core Authority", # Core Authority | Trust Builder | Differenzierung | Thought Leadership | GEO-Visibility
    },
    # weitere Themen...
]

TOPIC_PROOFS = [
    {
        "topic": "Thema 1",
        "proof_type": "Positionierung",   # Positionierung | Fachthema | Netzwerkstruktur | Case Study
        "proof": "Konkreter Beleg",
        "source": "URL oder Seitenname",
        "strength": 5,           # 1–5
        "missing": "Was fehlt noch?",
    },
    # weitere...
]

TOPIC_GEO = [
    {
        "topic": "Thema",
        "llm_question": "„Frage die LLMs stellen?"",
        "intent": "Commercial",
        "competition": "Mittel",  # Hoch | Mittel | Niedrig
        "mention_chance": "Hoch", # Hoch | Mittel | Niedrig
    },
]
```

---

#### AUTHORITY_MAP — Authority Map

```python
AUTHORITY_AREAS = [
    {
        "title": "1️⃣ Thema (Core Authority)",
        "what_it_is": "Erklärung was dieses Autoritätsfeld bedeutet.",
        "what_follows": "Was strategisch daraus folgt.",
        "action": [
            "→ Konkrete Maßnahme 1",
            "→ Konkrete Maßnahme 2",
            "→ Konkrete Maßnahme 3",
        ],
    },
    # 5 Bereiche insgesamt
]
```

---

#### AUTHORITY_PROOF — Authority Proof

```python
PROOF_ACTIONS = [
    {
        "action": "Case Studies entwickeln",
        "hard_proof_asset": "Case Studies",
        "authority_impact": "Sehr hoch",  # Sehr hoch | Hoch | Mittel
        "effort": "Mittel",               # Hoch | Mittel | Niedrig
        "recommendation": "Sofort starten", # Sofort starten | Phase 1 | Phase 2 | Phase 3 | Schnell umsetzen
    },
    # weitere...
]
```

---

#### CONTENT_HUBS — Content-Hub Plan

```python
HUBS = [
    {
        "hub_name": "Hub_1_Name",
        "hub_type": "Core Authority",  # Core Authority | Trust Authority | Differenzierung | Thought Leadership | GEO Strategy
        "pages": [
            {
                "topic": "Themengebiet",
                "url": "/url-slug/",
                "content_type": "Pillar",  # Pillar | Cluster
                "function": "Funktion dieser Seite im Hub",
                "priority": 1,
            },
            # 4–6 weitere Seiten pro Hub (1 Pillar + 4–5 Cluster)
        ],
    },
    # 4–5 Hubs insgesamt
]
```

---

#### GEO_TRIGGER — GEO / LLM Trigger

```python
GEO_TRIGGERS = [
    {
        "question": "Welche/Was/Wie...?",
        "intent": "Commercial",     # Commercial | Informational | Trust | Comparison
        "topic": "Themengebiet",
        "url": "/url-slug/",
        "content_type": "Pillar",   # Pillar | Cluster
        "competition": "Mittel",    # Hoch | Mittel | Niedrig
        "mention_chance": 3,        # 1–5
        "priority": 1,              # 1 | 2 | 3
        "action": "Konkrete Handlung",
    },
    # 12–18 Trigger insgesamt
]

AIO_SNIPPETS = [
    {
        "url": "/url-slug/",
        "llm_trigger": "Primärer Trigger",
        "secondary_triggers": "Trigger 2 | Trigger 3 | Trigger 4",
        "snippet": "40–60-Wörter-Snippet das direkt zitierbar ist. Klare Definition + Positionierung + ein konkretes Differenzierungsmerkmal.",
        "structure_triggers": "Vergleichstabelle | Abschnitt „Wann sinnvoll?" | 5-FAQ-Block",
        "internal_links": "/url-a/ | /url-b/ | /url-c/",
    },
    # Pro Pillar-URL
]

ENTITY_OPTIMIZATION = [
    {
        "url": "/url-slug/",
        "primary_entities": "Entity1 | Entity2 | Entity3",
        "secondary_entities": "Entity4 | Entity5 | Entity6",
        "opening": "Optimierter Eröffnungsabsatz (Definition im ersten Satz + klare Positionierung). Keine Floskeln.",
        "amplifiers": "Definition im ersten Absatz | klare Abgrenzung | Fachbegriffe",
        "avoid": "Superlative ohne Beleg | generische Qualitätsaussagen",
    },
    # Pro Pillar-URL
]

H_STRUCTURE = [
    {"url": "/url-slug/", "h2": "Erste H2", "h3": "Erste H3"},
    {"url": "/url-slug/", "h2": "Erste H2", "h3": "Zweite H3"},
    {"url": "/url-slug/", "h2": "Zweite H2", "h3": ""},
    # Vollständige H2/H3-Hierarchie pro Pillar-URL (8–12 Zeilen)
]
```

---

#### AUTHORITY_GAP — Authority Gap Score

```python
GAP_SCORES = [
    {
        "topic": "Thema",
        "content_strength": 4,    # 1–5
        "proof_strength": 2,      # 1–5
        "geo_relevance": 5,       # 1–5
        "competition": 4,         # 1–5
        "strategic_leverage": 5,  # 1–5
        # Gesamt-Score wird automatisch als Ø berechnet
        "action_needed": "Hoch",  # Sehr hoch | Hoch | Mittel | Niedrig
        "priority": 1,
    },
    # Alle Themen aus TOPICS
]

GAP_PRIORITIES = [
    "1️⃣ Handlungsempfehlung 1 (Grund)",
    "2️⃣ Handlungsempfehlung 2 (Grund)",
    "3️⃣ Handlungsempfehlung 3 (Grund)",
    "4️⃣ Handlungsempfehlung 4 (Grund)",
]
```

---

#### GEO_PROMPTS — GEO-Prompt-Research

```python
PHASE1 = [  # Must-Win: Kernthemen, höchste GEO-Priorität
    {
        "url": "/url-slug/",
        "prompt": "Vollständige LLM-Frage wie ein Nutzer sie stellt?",
        "prompt_short": "verkürzte suchähnliche Version",
        "tags": "SEO/GEO;Evaluation;Niveau",  # Format: Kanal;Intent;Driver
        "intent": "Evaluation",  # Purchase | Evaluation | Comparison | Exploration
    },
    # 10–14 Einträge
]

PHASE2 = [  # Coverage: Themenbreite, mittlere Priorität
    # gleiche Struktur, 10–12 Einträge
]

PHASE3 = [  # Edge Cases: Nischenthemen, Einwände, Sonderf. 
    # gleiche Struktur, 6–8 Einträge
]

CONTENT_BRIEFS = [  # Top 10–15 priorisierte Content-Briefs
    {
        "idea": "Kurze Ideen-Bezeichnung",
        "url": "/url-slug/",
        "h1": "Seitenüberschrift wie sie auf der Seite stehen soll",
        "prompt_short": "kurze Suchanfragen-Version",
        "intent": "Evaluation",
    },
    # 10–12 Einträge
]
```

---

#### AIO_PAGES — AI-Overview-Optimierung

```python
AIO_PAGES = [
    {
        "url": "https://www.domain.at/url/",
        "definition": "<p><strong>Begriff</strong> ist/bedeutet... [Snippet-ready, 1–2 Sätze, max 50 Wörter]</p>",
        "snippet_h2": "<h2>Was ist [Begriff]?</h2>",
        "snippet_p": "<p>Vollständige Featured-Snippet-Antwort. Selbsterklärend, ohne Kontext. 40–80 Wörter.</p>",
        "faq_schema": '''{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Frage 1?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Antwort 1."
      }
    },
    {
      "@type": "Question",
      "name": "Frage 2?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Antwort 2."
      }
    }
  ]
}''',
        "list_or_table": "",  # Optional: HTML-Tabelle oder UL für strukturierte Daten
    },
    # Für die 3–5 wichtigsten Seiten (Pillar-Pages)
]
```

---

#### BASIC_SEO — Basic-SEO-Themen

Wird aus Screaming Frog Daten + Live-Research befüllt.

```python
BASIC_SEO = {
    # Aus intern_alle.xls:
    "pages_404": [
        {"url": "https://...", "note": "Intern verlinkt"},
    ],
    "broken_canonicals": [
        {"url": "https://...", "canonical_target": "https://...", "issue": "Canonical zeigt auf 404"},
    ],
    "multiple_h1": [
        {"url": "https://...", "h1_1": "...", "h1_2": "..."},
    ],
    "missing_h1": ["https://..."],
    "duplicate_meta": [
        {"count": 33, "meta": "Duplikat Meta Description Text...", "note": "Betrifft X Seiten"},
    ],
    "noindex_pages": [
        {"url": "https://...", "reason": "noindex"},
    ],
    "redirects_301": [
        {"from": "http://...", "to": "https://...", "note": "HTTP→HTTPS korrekt"},
    ],
    "redirect_issues": [
        {"url": "http://...", "to": "https://.../index.html", "issue": "Redirect auf index.html statt /"},
    ],
    "thin_content": [],  # Leer wenn kein Thin Content gefunden

    # Aus hreflang_alle.xls:
    "hreflang_status": "absent" | "present" | "broken",
    "hreflang_fix": "Empfohlener hreflang-Code als String oder leer",

    # Aus Live-Research:
    "robots_txt": {
        "sitemap_referenced": True | False,
        "ai_crawlers": "allowed" | "blocked" | "not configured",
        "fix": "Korrektur-Code falls nötig",
    },
    "sitemap": {
        "exists": True | False,
        "url": "https://.../sitemap.xml",
        "note": "Anmerkung",
    },
    "schema_present": ["Organization", "WebSite"],  # Gefundene Schema-Typen
    "schema_missing": ["FAQPage", "BreadcrumbList", "Article"],
    "schema_recommendations": [
        "Sitewide: Organization, WebSite, BreadcrumbList (Templates bauen)",
        "Top 5 Seiten: FAQPage mit 5–8 sauberen Fragen",
        "Content-System: alle Guides/Artikel bekommen Article/BlogPosting (mit author + dates)",
        "Services: Leistungsseiten mit Service (nur 1–3 Kernleistungen)",
    ],

    # Strategisch (immer generieren):
    "filter_landing_pages": [
        "/kategorie/",
        "/kategorie/subcategory/",
    ],
    "example_page": {
        "url_slug": "/beispiel-landingpage/",
        "structure": """H1
Seitentitel der Landingpage

Direkt unter H1 – Definition (Snippet-Ready)
Ein [Begriff] ist... [2–3 Sätze, AIO-optimiert]

Sprungmarken (UX + AIO)
#abschnitt-1
#abschnitt-2
#faq
#kontakt

H2 Abschnitt 1 [#abschnitt-1]
H3 Unterabschnitt
H3 Unterabschnitt

H2 Abschnitt 2 [#abschnitt-2]
H3 Unterabschnitt

H2 FAQ [#faq]
Frage 1?
Antwort...
Frage 2?
Antwort...

H2 Kontakt [#kontakt]
CTA""",
    },
}
```

---

### Schritt 4 — Python Excel-Skript generieren und ausführen

Generiere das vollständige Skript `build_seo_geo_excel.py` im aktuellen Arbeitsverzeichnis. Das Skript:
1. Enthält alle Analyse-Daten als Python-Variablen (aus Schritt 3 befüllt)
2. Schreibt die Excel-Datei mit openpyxl
3. Wendet einheitliches Styling an

```bash
python3 -c "import openpyxl" 2>/dev/null || pip3 install openpyxl
python3 build_seo_geo_excel.py
```

---

## Excel-Skript Template

Das folgende Template wird mit den Analyse-Daten befüllt und ausgeführt. Alle `# FILL: ...` Kommentare werden durch echte Daten ersetzt.

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""SEO-GEO-Analyse Excel Generator"""

import openpyxl
from openpyxl.styles import (
    PatternFill, Font, Alignment, Border, Side
)
from openpyxl.utils import get_column_letter
from datetime import date

# ─── ANALYSE-DATEN ──────────────────────────────────────────────────────────

DOMAIN = "domain.at"  # FILL: Domain
BRAND  = "Markenname"  # FILL: Markenname

ENTITY = { }  # FILL: aus Schritt 3

TOPICS = [ ]   # FILL
TOPIC_PROOFS = [ ]  # FILL
TOPIC_GEO    = [ ]  # FILL

AUTHORITY_AREAS = [ ]  # FILL: 5 Einträge

PROOF_ACTIONS = [ ]  # FILL

HUBS = [ ]  # FILL

GEO_TRIGGERS     = [ ]  # FILL
AIO_SNIPPETS     = [ ]  # FILL
ENTITY_OPT       = [ ]  # FILL
H_STRUCTURE      = [ ]  # FILL

GAP_SCORES       = [ ]  # FILL
GAP_PRIORITIES   = [ ]  # FILL

PHASE1 = [ ]  # FILL
PHASE2 = [ ]  # FILL
PHASE3 = [ ]  # FILL
CONTENT_BRIEFS = [ ]  # FILL

AIO_PAGES = [ ]  # FILL

BASIC_SEO = { }  # FILL

# ─── STYLING ────────────────────────────────────────────────────────────────

HDR_DARK   = "1B3A4B"   # Dunkelblau-Teal — Haupt-Header
HDR_MID    = "2E6E8E"   # Mittleres Teal — Sub-Header
HDR_LIGHT  = "D6EEF8"   # Hellblau — Beschriftungszeilen
ACCENT_RED = "C0392B"   # Rot — Kritische Befunde
ACCENT_GRN = "1E8449"   # Grün — Positiv
ACCENT_YLW = "F39C12"   # Orange — Mittel
ROW_ALT    = "F4F8FB"   # Sehr helles Blau — Alternativzeilen
WHITE      = "FFFFFF"
FONT_MAIN  = "Calibri"

def hdr(text, size=11, bold=True, color=WHITE):
    return Font(name=FONT_MAIN, size=size, bold=bold, color=color)

def fill(hex_color):
    return PatternFill("solid", fgColor=hex_color)

def border_thin():
    s = Side(style="thin", color="CCCCCC")
    return Border(left=s, right=s, top=s, bottom=s)

def wrap():
    return Alignment(wrap_text=True, vertical="top")

def center():
    return Alignment(horizontal="center", vertical="center", wrap_text=True)

def set_col_width(ws, col_letter, width):
    ws.column_dimensions[col_letter].width = width

def write_section_header(ws, row, text, ncols=5, bg=HDR_DARK, fg=WHITE, size=12):
    """Schreibt einen gemergten, dunkel hinterlegten Abschnitts-Header."""
    ws.merge_cells(start_row=row, start_column=2,
                   end_row=row, end_column=ncols+1)
    c = ws.cell(row=row, column=2, value=text)
    c.font   = Font(name=FONT_MAIN, size=size, bold=True, color=fg)
    c.fill   = fill(bg)
    c.alignment = Alignment(horizontal="left", vertical="center", indent=1)
    ws.row_dimensions[row].height = 22

def write_table_header(ws, row, cols, bg=HDR_MID):
    """Schreibt eine Tabellen-Header-Zeile."""
    for i, col_text in enumerate(cols, 2):
        c = ws.cell(row=row, column=i, value=col_text)
        c.font      = hdr(col_text, size=10)
        c.fill      = fill(bg)
        c.alignment = center()
        c.border    = border_thin()
    ws.row_dimensions[row].height = 18

def write_data_row(ws, row, values, alt=False):
    """Schreibt eine Daten-Zeile mit optionalem Alternativ-Hintergrund."""
    bg = ROW_ALT if alt else WHITE
    for i, val in enumerate(values, 2):
        c = ws.cell(row=row, column=i, value=str(val) if val is not None else "")
        c.fill      = fill(bg)
        c.alignment = wrap()
        c.border    = border_thin()
        c.font      = Font(name=FONT_MAIN, size=10)
    ws.row_dimensions[row].height = 15

def write_info_block(ws, row, label, text, bg=HDR_LIGHT):
    """Label-Text Block für erklärende Sections."""
    c_lbl = ws.cell(row=row, column=2, value=label)
    c_lbl.font      = Font(name=FONT_MAIN, size=10, bold=True)
    c_lbl.fill      = fill(bg)
    c_lbl.alignment = wrap()
    c_lbl.border    = border_thin()
    c_txt = ws.cell(row=row, column=3, value=text)
    c_txt.font      = Font(name=FONT_MAIN, size=10)
    c_txt.fill      = fill(WHITE)
    c_txt.alignment = wrap()
    c_txt.border    = border_thin()

def setup_sheet(ws, title):
    """Standard-Setup für alle Sheets."""
    ws.sheet_view.showGridLines = False
    ws.column_dimensions["A"].width = 2  # Abstand links
    # Titelzeile
    ws.row_dimensions[1].height = 8
    ws.row_dimensions[2].height = 30
    ws.merge_cells("B2:J2")
    c = ws.cell(row=2, column=2, value=title)
    c.font      = Font(name=FONT_MAIN, size=14, bold=True, color=WHITE)
    c.fill      = fill(HDR_DARK)
    c.alignment = Alignment(horizontal="left", vertical="center", indent=1)
    ws.row_dimensions[3].height = 6  # Abstand nach Titel

# ─── SHEET 1: ENTITÄTEN-CHECK ───────────────────────────────────────────────

def build_entitaeten(wb):
    ws = wb.create_sheet("Entitäten-Check")
    setup_sheet(ws, f"Entitäten-Check — {BRAND}")
    set_col_width(ws, "B", 40)
    set_col_width(ws, "C", 40)
    set_col_width(ws, "D", 35)
    set_col_width(ws, "E", 35)

    r = 4
    # Ergebnis-Block
    ws.merge_cells(f"B{r}:E{r}")
    c = ws.cell(row=r, column=2, value=ENTITY.get("result", "") + "  " + ENTITY.get("result_detail", ""))
    result_color = (ACCENT_GRN if "Bekannt" in ENTITY.get("result","")
                    else ACCENT_RED if "Unbekannt" in ENTITY.get("result","")
                    else ACCENT_YLW)
    c.font = Font(name=FONT_MAIN, size=12, bold=True, color=WHITE)
    c.fill = fill(result_color)
    c.alignment = Alignment(horizontal="left", vertical="center", indent=1, wrap_text=True)
    ws.row_dimensions[r].height = 30
    r += 2

    # Warum nicht verankert
    write_section_header(ws, r, "Warum ist die Entität nicht verankert?", ncols=4)
    r += 1
    write_section_header(ws, r, "Wenn „" + ENTITY.get("result","") + "" ausgegeben wird, bedeutet das:", ncols=4, bg=HDR_MID, size=10)
    r += 1
    for reason in ENTITY.get("why_not_anchored", []):
        ws.merge_cells(f"B{r}:E{r}")
        c = ws.cell(row=r, column=2, value=reason)
        c.font = Font(name=FONT_MAIN, size=10, bold=True)
        c.fill = fill(HDR_LIGHT)
        c.alignment = wrap()
        c.border = border_thin()
        ws.row_dimensions[r].height = 14
        r += 1

    r += 1
    write_section_header(ws, r, "Kein klarer Entity-Cluster — keine konsistenten Co-Occurrence-Muster mit:", ncols=4, bg=HDR_MID, size=10)
    r += 1
    sources = ENTITY.get("training_sources", [])
    for i in range(0, len(sources), 2):
        left  = sources[i] if i < len(sources) else ""
        right = sources[i+1] if i+1 < len(sources) else ""
        ws.cell(row=r, column=2, value=left).font  = Font(name=FONT_MAIN, size=10)
        ws.cell(row=r, column=3, value=right).font = Font(name=FONT_MAIN, size=10)
        ws.cell(row=r, column=2).fill  = fill(WHITE if r % 2 == 0 else ROW_ALT)
        ws.cell(row=r, column=3).fill  = fill(WHITE if r % 2 == 0 else ROW_ALT)
        ws.cell(row=r, column=2).border = border_thin()
        ws.cell(row=r, column=3).border = border_thin()
        r += 1

    r += 1
    write_section_header(ws, r, "Was kann die Marke tun, um im Modellgedächtnis verankert zu werden?", ncols=4)
    r += 1

    col_titles = ["1️⃣ Wiederholte Kontextverknüpfung", "2️⃣ Strukturierte Erwähnungen",
                  "3️⃣ Semantische Positionierung", "4️⃣ Wissensgraph-Ankerpunkte"]
    col_data   = [
        ENTITY.get("context_links", []),
        ENTITY.get("structured_mentions", []),
        ENTITY.get("semantic_positioning", []),
        ENTITY.get("knowledge_graph_anchors", []),
    ]
    for i, title in enumerate(col_titles):
        c = ws.cell(row=r, column=2+i, value=title)
        c.font = hdr(title, size=10)
        c.fill = fill(HDR_MID)
        c.alignment = center()
        c.border = border_thin()
    ws.row_dimensions[r].height = 18
    r += 1

    max_items = max(len(d) for d in col_data) if col_data else 0
    for j in range(max_items):
        for i, col_list in enumerate(col_data):
            val = col_list[j] if j < len(col_list) else ""
            c = ws.cell(row=r, column=2+i, value=val)
            c.font   = Font(name=FONT_MAIN, size=10)
            c.fill   = fill(ROW_ALT if r % 2 == 0 else WHITE)
            c.alignment = wrap()
            c.border = border_thin()
        ws.row_dimensions[r].height = 14
        r += 1

# ─── SHEET 2: TOPICAL-AUTHORITY ─────────────────────────────────────────────

def build_topical(wb):
    ws = wb.create_sheet("Topical-Authority")
    setup_sheet(ws, "Authority Map-Analyse")
    set_col_width(ws, "B", 6)
    set_col_width(ws, "C", 30)
    set_col_width(ws, "D", 14)
    set_col_width(ws, "E", 10)
    set_col_width(ws, "F", 14)
    set_col_width(ws, "G", 30)
    set_col_width(ws, "H", 20)

    r = 4
    write_table_header(ws, r, ["Priorität","Themengebiet","Expertise-Level (1–5)",
                                 "Confidence (0–1)","Kern-Intent",
                                 "Abgrenzung zu Wettbewerbern","Strategische Rolle"])
    r += 1
    for i, t in enumerate(TOPICS):
        write_data_row(ws, r, [
            t["priority"], t["topic"], t["expertise"],
            t["confidence"], t["intent"], t["differentiation"], t["role"]
        ], alt=(i % 2 == 1))
        r += 1

    r += 1
    write_section_header(ws, r, "Belege für Autorität", ncols=7)
    r += 1
    write_table_header(ws, r, ["Themengebiet","Beleg-Typ","Konkreter Beleg","Quelle/URL","Stärke (1–5)","Fehlt noch?",""], ncols=7)
    r += 1
    for i, p in enumerate(TOPIC_PROOFS):
        write_data_row(ws, r, [p["topic"], p["proof_type"], p["proof"],
                                 p["source"], p["strength"], p["missing"], ""], alt=(i%2==1))
        r += 1

    r += 1
    write_section_header(ws, r, "GEO / LLM-Trigger-Keywords", ncols=7)
    r += 1
    write_table_header(ws, r, ["Themengebiet","LLM-Frage","Intent","Wettbewerb","Wahrscheinlichkeit Nennung","",""], ncols=7)
    r += 1
    for i, g in enumerate(TOPIC_GEO):
        write_data_row(ws, r, [g["topic"], g["llm_question"], g["intent"],
                                 g["competition"], g["mention_chance"], "", ""], alt=(i%2==1))
        r += 1

# ─── SHEET 3: AUTHORITY MAP ─────────────────────────────────────────────────

def build_authority_map(wb):
    ws = wb.create_sheet("Authority Map")
    setup_sheet(ws, "Authority Map")
    for i, letter in enumerate(["B","C","D","E","F"]):
        set_col_width(ws, letter, 32)

    r = 4
    # Spalten-Header (Authority Areas)
    for i, area in enumerate(AUTHORITY_AREAS):
        c = ws.cell(row=r, column=2+i, value=area["title"])
        c.font      = hdr(area["title"], size=10)
        c.fill      = fill(HDR_DARK)
        c.alignment = center()
        c.border    = border_thin()
    ws.row_dimensions[r].height = 30
    r += 1

    row_empty = r
    for col in range(2, 7):
        ws.cell(row=row_empty, column=col).fill = fill(WHITE)
    ws.row_dimensions[row_empty].height = 6
    r += 1

    sections = [("Was gemeint ist:", "what_it_is"),
                ("Was daraus folgt:", "what_follows")]
    for label, key in sections:
        for col in range(2, 7):
            c = ws.cell(row=r, column=col, value=label)
            c.font = hdr(label, size=9, color=WHITE)
            c.fill = fill(HDR_MID)
            c.alignment = Alignment(horizontal="left", vertical="center", indent=1)
            c.border = border_thin()
        ws.row_dimensions[r].height = 16
        r += 1
        for col_i, area in enumerate(AUTHORITY_AREAS):
            c = ws.cell(row=r, column=2+col_i, value=area.get(key, ""))
            c.font = Font(name=FONT_MAIN, size=10)
            c.fill = fill(ROW_ALT)
            c.alignment = wrap()
            c.border = border_thin()
        ws.row_dimensions[r].height = 60
        r += 1
        r += 1  # Abstand

    # Handlungsempfehlungen
    for col in range(2, 7):
        c = ws.cell(row=r, column=col, value="Handlungsempfehlung:")
        c.font = hdr("Handlungsempfehlung:", size=9, color=WHITE)
        c.fill = fill(HDR_DARK)
        c.alignment = Alignment(horizontal="left", vertical="center", indent=1)
        c.border = border_thin()
    ws.row_dimensions[r].height = 16
    r += 1

    max_actions = max(len(a.get("action", [])) for a in AUTHORITY_AREAS) if AUTHORITY_AREAS else 3
    for j in range(max_actions):
        for col_i, area in enumerate(AUTHORITY_AREAS):
            actions = area.get("action", [])
            val = actions[j] if j < len(actions) else ""
            c = ws.cell(row=r, column=2+col_i, value=val)
            c.font = Font(name=FONT_MAIN, size=10)
            c.fill = fill(WHITE if r % 2 == 0 else ROW_ALT)
            c.alignment = wrap()
            c.border = border_thin()
        ws.row_dimensions[r].height = 16
        r += 1

# ─── SHEET 4: AUTHORITY PROOF ───────────────────────────────────────────────

def build_authority_proof(wb):
    ws = wb.create_sheet("Authority Proof")
    setup_sheet(ws, "Authority Proof")
    set_col_width(ws, "B", 35)
    set_col_width(ws, "C", 25)
    set_col_width(ws, "D", 20)
    set_col_width(ws, "E", 15)
    set_col_width(ws, "F", 20)

    r = 4
    # Erklärung
    intro_lines = [
        ("Viele Websites behaupten Expertise.", "Soft Proof = Aussagen", "Hard Proof = Beweis", "", "Konkrete Ableitung"),
        ("Autorität entsteht erst durch Belege.", 'Beispiel: „Wir sind spezialisiert"', "Beispiel: Referenzkunden", "", "→ Hard Proof Assets aufbauen"),
    ]
    for line in intro_lines:
        for col_i, val in enumerate(line):
            c = ws.cell(row=r, column=2+col_i, value=val)
            c.font = Font(name=FONT_MAIN, size=10, bold=(col_i in [1,2,4]))
            c.fill = fill(HDR_LIGHT)
            c.alignment = wrap()
            c.border = border_thin()
        ws.row_dimensions[r].height = 16
        r += 1

    r += 1
    write_section_header(ws, r, "Handlungsempfehlung — Proof-Asset-Matrix", ncols=5)
    r += 1
    write_table_header(ws, r, ["Handlungsempfehlung","Hard-Proof-Asset","Autoritäts-Wirkung","Aufwand","Empfehlung"], ncols=5)
    r += 1
    for i, p in enumerate(PROOF_ACTIONS):
        alt = (i % 2 == 1)
        write_data_row(ws, r, [
            p["action"], p["hard_proof_asset"],
            p["authority_impact"], p["effort"], p["recommendation"]
        ], alt=alt)
        r += 1

# ─── SHEET 5: CONTENT-HUB PLAN ──────────────────────────────────────────────

def build_content_hub(wb):
    ws = wb.create_sheet("Content-Hub Plan")
    setup_sheet(ws, "Content Hub Plan")
    set_col_width(ws, "B", 20)
    set_col_width(ws, "C", 22)
    set_col_width(ws, "D", 35)
    set_col_width(ws, "E", 12)
    set_col_width(ws, "F", 30)
    set_col_width(ws, "G", 10)

    r = 4
    intro_lines = [
        "Ein Pillar = 1 zentrales Thema",
        "5–10 unterstützende Clusterartikel",
        "Google & LLMs erkennen so: → Thematische Tiefe + Strukturierte Expertise",
        "Ohne Cluster-Struktur keine Topical Authority.",
    ]
    for line in intro_lines:
        ws.merge_cells(f"B{r}:C{r}")
        c = ws.cell(row=r, column=2, value=line)
        c.font = Font(name=FONT_MAIN, size=10, italic=True)
        c.fill = fill(HDR_LIGHT)
        c.alignment = wrap()
        c.border = border_thin()
        ws.row_dimensions[r].height = 16
        r += 1

    r += 1
    for hub in HUBS:
        write_section_header(ws, r, hub["hub_name"], ncols=6, bg=HDR_DARK)
        r += 1
        write_table_header(ws, r, ["Hub-Typ","Themengebiet","URL","Content-Typ","Funktion im Hub","Priorität"], ncols=6)
        r += 1
        for i, page in enumerate(hub.get("pages", [])):
            write_data_row(ws, r, [
                hub["hub_type"], page["topic"], page["url"],
                page["content_type"], page["function"], page["priority"]
            ], alt=(i%2==1))
            r += 1
        r += 1

# ─── SHEET 6: GEO / LLM TRIGGER ─────────────────────────────────────────────

def build_geo_trigger(wb):
    ws = wb.create_sheet("GEO   LLM Trigger")
    setup_sheet(ws, "GEO / LLM Trigger")
    set_col_width(ws, "B", 32)
    set_col_width(ws, "C", 14)
    set_col_width(ws, "D", 22)
    set_col_width(ws, "E", 25)
    set_col_width(ws, "F", 12)
    set_col_width(ws, "G", 14)
    set_col_width(ws, "H", 14)
    set_col_width(ws, "I", 10)
    set_col_width(ws, "J", 30)

    r = 4
    # Erklärung
    write_section_header(ws, r, "Was gemeint ist", ncols=9, bg=HDR_MID, size=10)
    r += 1
    explanations = [
        "Das sind Fragen, die LLMs direkt beantworten.",
        'Wenn FAQ dort nicht strukturiert vorkommt → keine Nennung.',
        "Du musst Inhalte so schreiben, dass sie direkt zitierfähig sind.",
        "Klare, präzise Definitionen · Listenformate · Vergleichstabellen · FAQ-Block",
    ]
    for line in explanations:
        ws.merge_cells(f"B{r}:J{r}")
        c = ws.cell(row=r, column=2, value=line)
        c.font = Font(name=FONT_MAIN, size=10, italic=True)
        c.fill = fill(HDR_LIGHT)
        c.alignment = wrap()
        c.border = border_thin()
        ws.row_dimensions[r].height = 14
        r += 1

    r += 1
    write_section_header(ws, r, "GEO / LLM Trigger-Fragen", ncols=9)
    r += 1
    write_table_header(ws, r, ["Trigger-Frage","Intent","Themengebiet","URL-Zielseite",
                                  "Content-Typ","Wettbewerbsdruck","Nennungschance (1–5)",
                                  "Priorität","Konkrete Handlung"], ncols=9)
    r += 1
    for i, t in enumerate(GEO_TRIGGERS):
        write_data_row(ws, r, [
            t["question"], t["intent"], t["topic"], t["url"],
            t["content_type"], t["competition"], t["mention_chance"],
            t["priority"], t["action"]
        ], alt=(i%2==1))
        r += 1

    r += 1
    write_section_header(ws, r, "AIO-Snippet-Optimierung (pro Pillar-URL)", ncols=9)
    r += 1
    write_table_header(ws, r, ["URL-Slug","Primärer LLM-Trigger","Sekundäre Trigger",
                                  "AIO-Snippet (40–60 Wörter)","Struktur-Trigger",
                                  "Interne Links","","",""], ncols=9)
    r += 1
    for i, s in enumerate(AIO_SNIPPETS):
        write_data_row(ws, r, [
            s["url"], s["llm_trigger"], s["secondary_triggers"],
            s["snippet"], s["structure_triggers"], s["internal_links"],
            "","",""
        ], alt=(i%2==1))
        ws.row_dimensions[r].height = 50
        r += 1

    r += 1
    write_section_header(ws, r, "Entity-Optimierung (pro Pillar-URL)", ncols=9)
    r += 1
    write_table_header(ws, r, ["URL-Slug","Primäre Entities","Sekundäre Entities",
                                  "Optimierter Opening-Absatz","Semantische Verstärker",
                                  "Zu vermeiden","","",""], ncols=9)
    r += 1
    for i, e in enumerate(ENTITY_OPT):
        write_data_row(ws, r, [
            e["url"], e["primary_entities"], e["secondary_entities"],
            e["opening"], e["amplifiers"], e["avoid"],
            "","",""
        ], alt=(i%2==1))
        ws.row_dimensions[r].height = 45
        r += 1

    r += 1
    write_section_header(ws, r, "H2/H3-Struktur pro Pillar-URL", ncols=9)
    r += 1
    write_table_header(ws, r, ["URL-Slug","H2","H3","","","","","",""], ncols=9)
    r += 1
    for i, h in enumerate(H_STRUCTURE):
        write_data_row(ws, r, [
            h["url"], h["h2"], h["h3"],
            "","","","","",""
        ], alt=(i%2==1))
        r += 1

# ─── SHEET 7: AUTHORITY GAP SCORE ───────────────────────────────────────────

def build_gap_score(wb):
    ws = wb.create_sheet("Authority Gap Score")
    setup_sheet(ws, "Authority Gap Score")
    set_col_width(ws, "B", 30)
    set_col_width(ws, "C", 14)
    set_col_width(ws, "D", 14)
    set_col_width(ws, "E", 12)
    set_col_width(ws, "F", 14)
    set_col_width(ws, "G", 16)
    set_col_width(ws, "H", 13)
    set_col_width(ws, "I", 16)
    set_col_width(ws, "J", 10)

    r = 4
    write_section_header(ws, r, "Was gemeint ist: Bewertung, wo strategische Lücken sind.", ncols=9, bg=HDR_MID, size=10)
    r += 1
    explanation = "Niedriger Content-Wert + hohe GEO-Relevanz = → Sofort handeln."
    ws.merge_cells(f"B{r}:J{r}")
    c = ws.cell(row=r, column=2, value=explanation)
    c.font = Font(name=FONT_MAIN, size=10, italic=True)
    c.fill = fill(HDR_LIGHT)
    c.alignment = wrap()
    r += 1

    r += 1
    write_table_header(ws, r, ["Themengebiet","Content-Stärke (1–5)","Proof-Stärke (1–5)",
                                  "GEO-Relevanz (1–5)","Wettbewerbsdruck (1–5)",
                                  "Strategischer Hebel (1–5)","Gesamt-Score (Ø)",
                                  "Handlungsbedarf","Priorität"], ncols=9)
    r += 1
    for i, g in enumerate(GAP_SCORES):
        avg = round(sum([g["content_strength"], g["proof_strength"],
                         g["geo_relevance"], g["competition"],
                         g["strategic_leverage"]]) / 5, 1)
        action_color = (ACCENT_RED if g["action_needed"] in ["Sehr hoch","Hoch"]
                        else ACCENT_YLW if g["action_needed"] == "Mittel"
                        else ACCENT_GRN)
        write_data_row(ws, r, [
            g["topic"], g["content_strength"], g["proof_strength"],
            g["geo_relevance"], g["competition"], g["strategic_leverage"],
            avg, g["action_needed"], g["priority"]
        ], alt=(i%2==1))
        ws.cell(row=r, column=9).fill = fill(action_color)  # Handlungsbedarf farbig
        ws.cell(row=r, column=9).font = Font(name=FONT_MAIN, size=10, bold=True, color=WHITE)
        r += 1

    r += 1
    write_section_header(ws, r, "Erklärung der Bewertungslogik", ncols=9, bg=HDR_MID, size=10)
    r += 1
    logic = [
        ("Content-Stärke",  "Wie gut ist das Thema inhaltlich bereits ausgebaut?"),
        ("Proof-Stärke",    "Gibt es harte Belege (Case Studies, Reports, Referenzen)?"),
        ("GEO-Relevanz",    "Wie stark taucht dieses Thema in AI-Overviews / LLM-Fragen auf?"),
        ("Wettbewerbsdruck","Wie stark dominieren andere Anbieter das Thema?"),
        ("Strategischer Hebel","Wie wichtig ist das Thema für Positionierung & Differenzierung?"),
    ]
    write_table_header(ws, r, ["Dimension","Bedeutung","","","","","","",""], ncols=9)
    r += 1
    for i, (dim, meaning) in enumerate(logic):
        write_data_row(ws, r, [dim, meaning,"","","","","","",""], alt=(i%2==1))
        r += 1

    r += 1
    write_section_header(ws, r, "Handlungsempfehlung Priorisierung", ncols=9)
    r += 1
    for i, line in enumerate(GAP_PRIORITIES):
        ws.merge_cells(f"B{r}:J{r}")
        c = ws.cell(row=r, column=2, value=line)
        c.font = Font(name=FONT_MAIN, size=11, bold=True)
        c.fill = fill(ROW_ALT if i%2==1 else WHITE)
        c.alignment = Alignment(horizontal="left", vertical="center", indent=1, wrap_text=True)
        c.border = border_thin()
        ws.row_dimensions[r].height = 18
        r += 1

# ─── SHEET 8: GEO-PROMPT-RESEARCH ───────────────────────────────────────────

def build_geo_prompts(wb):
    ws = wb.create_sheet("GEO-Prompt-Research ")
    setup_sheet(ws, "GEO-Prompt-Research")
    set_col_width(ws, "B", 28)
    set_col_width(ws, "C", 55)
    set_col_width(ws, "D", 28)
    set_col_width(ws, "E", 25)
    set_col_width(ws, "F", 14)

    phases = [
        ("Phase 1: Must-Win", PHASE1),
        ("Phase 2: Coverage", PHASE2),
        ("Phase 3: Edge Cases", PHASE3),
    ]
    r = 4
    for phase_name, phase_data in phases:
        write_section_header(ws, r, phase_name, ncols=5)
        r += 1
        write_table_header(ws, r, ["URL-Slug","Prompt","Prompt (reduced)","Rankscale Tags","Intent"], ncols=5)
        r += 1
        for i, p in enumerate(phase_data):
            write_data_row(ws, r, [
                p["url"], p["prompt"], p["prompt_short"],
                p["tags"], p["intent"]
            ], alt=(i%2==1))
            ws.row_dimensions[r].height = 18
            r += 1
        r += 1

    write_section_header(ws, r, "Top 10–15 Content-Briefs", ncols=5)
    r += 1
    write_table_header(ws, r, ["Idee","URL-Slug","H1","Prompt (reduced)","Intent"], ncols=5)
    r += 1
    for i, b in enumerate(CONTENT_BRIEFS):
        write_data_row(ws, r, [
            b["idea"], b["url"], b["h1"],
            b["prompt_short"], b["intent"]
        ], alt=(i%2==1))
        ws.row_dimensions[r].height = 18
        r += 1

# ─── SHEET 9: AI-OVERVIEW-OPTIMIERUNG ───────────────────────────────────────

def build_aio(wb):
    ws = wb.create_sheet("AI-Overview-Optimierung")
    setup_sheet(ws, "AI-Overview-Optimierung")
    set_col_width(ws, "B", 40)
    set_col_width(ws, "C", 25)
    set_col_width(ws, "D", 70)

    r = 4
    for page in AIO_PAGES:
        write_section_header(ws, r, page["url"], ncols=3)
        r += 1
        rows = [
            ("1. Direkte Definition nach H1",        page["definition"]),
            ("2. Featured Snippet-ready Abschnitt",  page["snippet_h2"]),
            ("",                                      page["snippet_p"]),
            ("3. FAQ-Sektion mit Schema (Copy-Paste)",page["faq_schema"]),
        ]
        if page.get("list_or_table"):
            rows.append(("4. Strukturierte Listen-/Tabellen-Darstellung (AIO Trigger)",
                          page["list_or_table"]))
        for label, content in rows:
            c_lbl = ws.cell(row=r, column=2, value=label)
            c_lbl.font = Font(name=FONT_MAIN, size=10, bold=bool(label))
            c_lbl.fill = fill(HDR_LIGHT if label else WHITE)
            c_lbl.alignment = wrap()
            c_lbl.border = border_thin()

            c_content = ws.cell(row=r, column=4, value=content)
            c_content.font = Font(name=FONT_MAIN, size=9)
            c_content.fill = fill(WHITE)
            c_content.alignment = Alignment(wrap_text=True, vertical="top")
            c_content.border = border_thin()
            ws.row_dimensions[r].height = max(15, len(str(content)) // 6)
            r += 1
        r += 1

# ─── SHEET 10: BASIC-SEO-THEMEN ─────────────────────────────────────────────

def build_basic_seo(wb):
    ws = wb.create_sheet("Basic-SEO-Themen")
    setup_sheet(ws, "Basic-SEO-Themen")
    set_col_width(ws, "B", 45)
    set_col_width(ws, "C", 55)

    r = 4

    def write_fix_section(title, items, bg=HDR_DARK):
        nonlocal r
        write_section_header(ws, r, title, ncols=2, bg=bg)
        r += 1
        for item in items:
            if isinstance(item, dict):
                label = item.get("url") or item.get("from") or item.get("note") or ""
                detail = (item.get("issue") or item.get("canonical_target") or
                          item.get("to") or item.get("meta") or "")
                note = item.get("note") or item.get("reason") or ""
                text = label
                if detail: text += f"\n→ {detail}"
                if note:   text += f"\n({note})"
            else:
                text = str(item)
            c = ws.cell(row=r, column=2, value=text)
            c.font = Font(name=FONT_MAIN, size=10)
            c.fill = fill(ROW_ALT if r%2==0 else WHITE)
            c.alignment = wrap()
            c.border = border_thin()
            ws.row_dimensions[r].height = 14 * (text.count("\n") + 1)
            r += 1
        r += 1

    # Kritische Befunde (rot)
    if BASIC_SEO.get("broken_canonicals"):
        write_fix_section("🔴 Kritisch: Canonicals die auf 404-URLs zeigen",
                          BASIC_SEO["broken_canonicals"], bg=ACCENT_RED)
    if BASIC_SEO.get("pages_404"):
        write_fix_section("🔴 Kritisch: 404-Seiten (intern verlinkt)",
                          BASIC_SEO["pages_404"], bg=ACCENT_RED)

    # Mittel (orange)
    if BASIC_SEO.get("multiple_h1"):
        write_fix_section("🟠 Mehrere H1-Tags", BASIC_SEO["multiple_h1"], bg=HDR_MID)
    if BASIC_SEO.get("missing_h1"):
        write_fix_section("🟠 Fehlende H1", BASIC_SEO["missing_h1"], bg=HDR_MID)
    if BASIC_SEO.get("duplicate_meta"):
        write_fix_section("🟠 Duplicate Meta Descriptions", BASIC_SEO["duplicate_meta"], bg=HDR_MID)
    if BASIC_SEO.get("noindex_pages"):
        write_fix_section("🟡 noindex-Seiten", BASIC_SEO["noindex_pages"], bg=ACCENT_YLW)
    if BASIC_SEO.get("redirect_issues"):
        write_fix_section("🟡 Redirect-Probleme", BASIC_SEO["redirect_issues"], bg=ACCENT_YLW)

    # hreflang
    hl_status = BASIC_SEO.get("hreflang_status", "absent")
    if hl_status == "absent":
        write_section_header(ws, r, "hreflang: Nicht implementiert", ncols=2, bg=HDR_MID)
        r += 1
        lines = [
            "Die Website hat kein hreflang-Markup implementiert.",
            "Empfehlung: Mindestens de-AT und x-default setzen.",
        ]
        if BASIC_SEO.get("hreflang_fix"):
            lines.append(BASIC_SEO["hreflang_fix"])
        for line in lines:
            ws.merge_cells(f"B{r}:C{r}")
            c = ws.cell(row=r, column=2, value=line)
            c.font = Font(name=FONT_MAIN, size=10)
            c.fill = fill(ROW_ALT if r%2==0 else WHITE)
            c.alignment = wrap()
            c.border = border_thin()
            ws.row_dimensions[r].height = 14
            r += 1
        r += 1
    elif hl_status == "broken" and BASIC_SEO.get("hreflang_fix"):
        write_section_header(ws, r, "hreflang: Fehlerhafte Implementierung — Korrektur", ncols=2, bg=ACCENT_RED)
        r += 1
        c = ws.cell(row=r, column=2, value=BASIC_SEO["hreflang_fix"])
        c.font = Font(name=FONT_MAIN, size=9)
        c.fill = fill(WHITE)
        c.alignment = Alignment(wrap_text=True, vertical="top")
        c.border = border_thin()
        ws.row_dimensions[r].height = 80
        r += 2

    # robots.txt
    write_section_header(ws, r, "robots.txt & Sitemap", ncols=2, bg=HDR_MID)
    r += 1
    robots = BASIC_SEO.get("robots_txt", {})
    sitemap = BASIC_SEO.get("sitemap", {})
    robot_lines = []
    if not robots.get("sitemap_referenced"):
        robot_lines.append("⚠ Sitemap.xml ist in robots.txt nicht angegeben → Sitemap-Direktive ergänzen")
    if robots.get("ai_crawlers") == "blocked":
        robot_lines.append("⚠ KI-Crawler (GPTBot, ClaudeBot, PerplexityBot) sind blockiert → für GEO freigeben")
    elif robots.get("ai_crawlers") == "not configured":
        robot_lines.append("ℹ KI-Crawler nicht explizit konfiguriert → Freigabe für GPTBot, ClaudeBot, PerplexityBot empfohlen")
    if robots.get("fix"):
        robot_lines.append(robots["fix"])
    if not sitemap.get("exists"):
        robot_lines.append("⚠ Sitemap.xml nicht gefunden → Sitemap erstellen und in robots.txt eintragen")
    else:
        robot_lines.append(f"✓ Sitemap vorhanden: {sitemap.get('url','')}")
    for line in robot_lines:
        ws.merge_cells(f"B{r}:C{r}")
        c = ws.cell(row=r, column=2, value=line)
        c.font = Font(name=FONT_MAIN, size=10)
        c.fill = fill(ROW_ALT if r%2==0 else WHITE)
        c.alignment = wrap()
        c.border = border_thin()
        ws.row_dimensions[r].height = 14
        r += 1
    r += 1

    # Schema.org
    write_section_header(ws, r, "Schema.org: Empfehlungen", ncols=2)
    r += 1
    schema_found = BASIC_SEO.get("schema_present", [])
    if schema_found:
        c = ws.cell(row=r, column=2, value=f"✓ Gefunden: {', '.join(schema_found)}")
        c.font = Font(name=FONT_MAIN, size=10, color=ACCENT_GRN)
        c.fill = fill(WHITE)
        c.alignment = wrap()
        r += 1
    for rec in BASIC_SEO.get("schema_recommendations", []):
        ws.merge_cells(f"B{r}:C{r}")
        c = ws.cell(row=r, column=2, value=rec)
        c.font = Font(name=FONT_MAIN, size=10)
        c.fill = fill(ROW_ALT if r%2==0 else WHITE)
        c.alignment = wrap()
        c.border = border_thin()
        ws.row_dimensions[r].height = 16
        r += 1
    r += 1

    # Filter-Landingpages
    if BASIC_SEO.get("filter_landing_pages"):
        write_section_header(ws, r, "Indexierbare Filter-Landingpages (Empfehlung)", ncols=2)
        r += 1
        for url in BASIC_SEO["filter_landing_pages"]:
            c = ws.cell(row=r, column=2, value=url)
            c.font = Font(name=FONT_MAIN, size=10)
            c.fill = fill(ROW_ALT if r%2==0 else WHITE)
            c.alignment = wrap()
            c.border = border_thin()
            ws.row_dimensions[r].height = 14
            r += 1
        r += 1

    # Beispiel Landingpage-Struktur
    if BASIC_SEO.get("example_page"):
        write_section_header(ws, r, f"Beispiel Landingpage-Aufbau: {BASIC_SEO['example_page']['url_slug']}", ncols=2)
        r += 1
        c = ws.cell(row=r, column=2, value=BASIC_SEO["example_page"]["structure"])
        c.font = Font(name=FONT_MAIN, size=9)
        c.fill = fill(WHITE)
        c.alignment = Alignment(wrap_text=True, vertical="top")
        c.border = border_thin()
        ws.row_dimensions[r].height = 250
        r += 1

# ─── MAIN ────────────────────────────────────────────────────────────────────

def main():
    wb = openpyxl.Workbook()
    wb.remove(wb.active)  # Standard-Sheet entfernen

    build_entitaeten(wb)
    build_topical(wb)
    build_authority_map(wb)
    build_authority_proof(wb)
    build_content_hub(wb)
    build_geo_trigger(wb)
    build_gap_score(wb)
    build_geo_prompts(wb)
    build_aio(wb)
    build_basic_seo(wb)

    filename = f"{DOMAIN.replace('/', '_').replace(':', '')}_SEO-GEO-Analyse.xlsx"
    wb.save(filename)
    print(f"✓ Excel erstellt: {filename}")
    print(f"  Sheets: {len(wb.sheetnames)}")
    for sh in wb.sheetnames:
        print(f"  · {sh}")

if __name__ == "__main__":
    main()
```

---

## Qualitätsprüfung nach Excel-Erstellung

```
✓ Excel erstellt: <domain>_SEO-GEO-Analyse.xlsx
  · Entitäten-Check    — Entitätsstatus + 4-Spalten-Handlungsmatrix
  · Topical-Authority  — X Themengebiete, X Belege, X GEO-Trigger
  · Authority Map      — 5 Autoritätsfelder mit Empfehlungen
  · Authority Proof    — X Proof-Assets priorisiert
  · Content-Hub Plan   — X Hubs, X Seiten geplant
  · GEO / LLM Trigger  — X Trigger-Fragen, X AIO-Snippets, X Entity-Opt.
  · Authority Gap Score — X Themen gescored
  · GEO-Prompt-Research — Phase1: X | Phase2: X | Phase3: X | Briefs: X
  · AI-Overview-Opt.   — X Seiten optimiert
  · Basic-SEO-Themen   — X kritische Befunde, Schema + Struktur-Guide
```

Spotcheck nach Erstellung:
- Haben alle Sheets Inhalt?
- Sind die farbigen Header sichtbar?
- Sind die Canonical-auf-404-Fehler im Basic-SEO-Themen-Sheet?
- Haben die GEO-Trigger realistische, zitierbare Snippets?
