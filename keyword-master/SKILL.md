---
name: keyword-master
description: >
  Erstellt ein umfangreiches Keyword-Analyse-Excel aus Ranking-Exporten (SEMrush,
  Ahrefs, SEO Extension) und Google Ads Daten. Klassifiziert Keywords als
  Brand / Non-Brand / Produktnah B2C / Produktnah B2B, gleicht SEA-Keywords ab,
  identifiziert Keyword-Gaps zu Wettbewerbern und hebt Top-Ranking-Chancen hervor.
  Auslöser: "keyword analyse", "keyword excel", "keyword übersicht", "ranking
  analyse", "keyword gap", "SEA SEO vergleich", "keyword master".
---

# Keyword Master — Umfassende Keyword-Analyse Excel

## Was dieser Skill tut

Nimmt Ranking-Exporte und bezahlte Keyword-Listen und produziert eine strukturierte
Excel-Datei mit 5 Tabs:

| Tab | Inhalt |
|-----|--------|
| **Tab 1** | Alle eigenen Keywords: Kategorie, Position, KW-Difficulty, SEA-Match, Traffic, Intent |
| **Tab 2** | Keyword Gaps: Wettbewerber rankt, eigene Domain nicht — mit Potenzial-Score |
| **Tab 3** | Top Potenziale: Gefiltert auf hohe Chance (Volumen hoch, Schwierigkeit niedrig) |
| **Tab 4** | SEA Keywords Referenz: Alle gebuchten Paid-Keywords |
| **Tab 5** | Legende & Erklärungen |

---

## Prozess

### Schritt 1 — Input-Dateien erkennen

```bash
# Im aktuellen Verzeichnis nach Keyword-Exporten suchen
find . -maxdepth 2 \( -name "*.csv" -o -name "*.xlsx" -o -name "*.xltx" \) | sort
```

Erkenne folgende Datei-Typen:

**Eigene Rankings (SEMrush-Format):**
- Spalten: `Keyword, Position, Previous position, Search Volume, Keyword Difficulty, CPC, URL, Traffic, ...`
- Trennzeichen: Komma
- Typischer Dateiname: `domain.at-organic.Positions-*.csv`

**Eigene Rankings (SEO-Extension-Format):**
- Spalten: `Keyword;Position;" ";Klicks;Suchvolumen;Wettbewerber;Intent;CPC;URL`
- Trennzeichen: Semikolon
- Typischer Dateiname: `domain_seoext_download.csv`

**Wettbewerber-Rankings (SEO Extension, Semikolon-getrennt):**
- Gleiche Struktur wie oben, aber andere Domain im Dateinamen

**SEA Keywords (Google Ads XLTX-Export):**
- ZIP-komprimierte XML-Struktur
- Sheets: eine oder mehrere Kampagnen/Anzeigengruppen
- Spalten: `Keyword-Status, Keyword, Keyword-Option, Anzeigengruppe, Klicks, Impressionen, CTR, Durchschn. CPC, Kosten`
- Match-Typen: `"phrase"`, `[exact]`, `broad`

**Frage den Nutzer** wenn unklar, welche Datei welche Rolle hat:
- Welche CSV = eigene Domain?
- Welche CSV = Wettbewerber?
- Welche Datei = SEA/Paid-Keywords (optional)?
- Was sind die Brand-Begriffe? (z.B. "oberbank", "ober bank")

### Schritt 2 — Schlüssel-Parameter klären

Frage den Nutzer (oder leite aus Dateinamen/Domain ab):

```
1. EIGENE DOMAIN: oberbank.at
2. BRAND-BEGRIFFE: ['oberbank', 'ober bank']
3. WETTBEWERBER-DOMAIN: bankaustria.at
4. WETTBEWERBER-BRAND-BEGRIFFE: ['bank austria', 'bankaustria', 'unicredit', 'creditanstalt']
5. BRANCHE: banking  (→ lädt Muster aus assets/patterns_banking.md)
6. OUTPUT-DATEINAME: <Domain>_Keyword_Analyse_<Datum>.xlsx
```

Verfügbare Branchen-Muster in `assets/`:
- `patterns_banking.md` — Banken & Finanzdienstleister (Österreich/DACH)
- `patterns_generic.md` — Universell einsetzbar

### Schritt 3 — Python-Skript generieren und ausführen

Lade die passenden Branchen-Muster aus `assets/` und generiere das Python-Skript.
Lese dazu zuerst die relevante `patterns_*.md` Datei vollständig ein.

Das Skript (`build_keyword_excel.py`) folgt dieser Struktur:

```python
#!/usr/bin/env python3
# Imports: csv, re, zipfile, xml.etree.ElementTree, openpyxl

# 1. SEA-Keywords extrahieren (aus XLTX via ZIP/XML)
# 2. Klassifizierungsfunktionen (Brand, B2B_RE, B2C_RE, URL-Muster)
# 3. Eigene Rankings einlesen + deduplizieren (bester Rang je Keyword)
# 4. Wettbewerber-Rankings einlesen
# 5. Keywords aufbereiten: Klassifizierung + SEA-Match + Trend berechnen
# 6. Gap-Analyse: Wettbewerber Non-Brand-Keywords ohne eigenes Ranking
# 7. Potenzial-Score: vol × (1 − schwierigkeit)
# 8. Excel mit 5 Tabs + Conditional Formatting + AutoFilter + Freeze Panes
```

Führe das generierte Skript sofort aus:
```bash
python3 build_keyword_excel.py
```

### Schritt 4 — Qualitätssicherung

Prüfe nach dem Ausführen:
- Sind alle 5 Tabs vorhanden?
- Stimmen die Keyword-Zahlen (Gesamt, Brand, B2C, B2B, Non-Brand)?
- Spotcheck: 5 zufällige Keywords und ihre Klassifizierung plausibel?
- Keyword-Gaps > 0?
- Top Potenziale > 0?

Gib dem Nutzer eine Zusammenfassung:
```
✓ Excel erstellt: <Dateiname>
  Tab 1 — Eigene Keywords  : X.XXX Keywords (Brand: X | B2C: X | B2B: X | Non-Brand: X)
  Tab 2 — Keyword Gaps     : X.XXX Keywords
  Tab 3 — Top Potenziale   : X Keywords (hohes Volumen + niedrige Schwierigkeit)
  Tab 4 — SEA Keywords     : X Keywords
```

---

## Klassifizierungs-Logik (Reihenfolge beachten!)

```python
def classify(keyword, url='', brand_terms=BRAND, b2b_re=B2B_RE, b2c_re=B2C_RE, b2b_url=B2B_URL, b2c_url=B2C_URL):
    kl = keyword.lower().strip()
    ul = (url or '').lower()
    
    # 1. Brand (höchste Priorität)
    if any(term in kl for term in brand_terms):
        return 'Brand'
    
    # 2. URL-basierte Klassifizierung (zuverlässiger als Keyword-Muster)
    if any(p in ul for p in b2b_url):
        return 'Produktnah B2B'
    if any(p in ul for p in b2c_url):
        return 'Produktnah B2C'
    
    # 3. Keyword-Muster B2B (vor B2C, da spezifischer)
    if any(re.search(p, kl) for p in b2b_re):
        return 'Produktnah B2B'
    
    # 4. Keyword-Muster B2C
    if any(re.search(p, kl) for p in b2c_re):
        return 'Produktnah B2C'
    
    # 5. Fallback
    return 'Non-Brand'
```

## Potenzial-Score (Keyword Gaps)

```python
def pot_score(volume, competition_decimal):
    """competition_decimal: z.B. 0.65 für 65% Schwierigkeit"""
    return round(volume * (1.0 - min(competition_decimal, 0.99)), 0)

def pot_label(volume, competition_decimal):
    comp = competition_decimal * 100
    if volume >= 5000 and comp < 60:  return 'Sehr hoch'
    if volume >= 1000 and comp < 65:  return 'Hoch'
    if volume >= 300  and comp < 70:  return 'Mittel'
    return 'Niedrig'
```

## Deduplication-Strategie (eigene Keywords)

Gleiche Keywords können mit mehreren URLs/Positionen ranken (Organic, Local pack, AI overview…).
Dedupliziere auf Basis des Keyword-Texts:
- Organische Positionen haben Vorrang vor Local pack / AI overview / People also ask
- Bei gleicher Typ: niedrigste Positionsnummer gewinnt

```python
# Pseudo-code
if new_type == 'Organic' and current_type != 'Organic':
    replace()
elif same_type and new_position < current_position:
    replace()
```

---

## Excel-Formatierung (Standards)

```python
# Farben
COLORS = {
    'brand':    'D6EAF8',   # Blau   — Brand-Keywords
    'b2c':      'D5F5E3',   # Grün   — Produktnah B2C
    'b2b':      'FCF3CF',   # Gelb   — Produktnah B2B
    'nonbrand': 'F2F3F4',   # Grau   — Non-Brand
    'sea_yes':  'A9DFBF',   # Grün   — SEA gebucht
    'hdr_dark': '1B2631',   # Dunkelblau — Header-Hintergrund
}

# Je Tab:
# - Titel-Zeile (merged, 13pt, weiß auf dunkel)
# - Stats-Zeile (merged, 10pt, wichtigste Kennzahlen)
# - Header-Zeile (fett, weiß auf dunkel, AutoFilter)
# - Freeze Panes auf Daten-Zeile
# - Conditional Formatting auf KW-Difficulty: grün(0) → orange(50) → rot(100)
# - Conditional Formatting auf Potenzial-Score: rot(min) → orange(50%) → grün(max)
# - Spaltenbreiten angepasst (Keyword-Spalte ~35, URL-Spalte ~50)
```

---

## Spalten-Definitionen

### Tab 1 — Eigene Keywords
| Spalte | Inhalt | Format |
|--------|--------|--------|
| Keyword | Suchbegriff | Text |
| Kategorie | Brand / Produktnah B2C / Produktnah B2B / Non-Brand | Text (farbig) |
| Position | Aktueller Rang | Zahl |
| Trend | Pos.-Änderung (+X, -X, =) | Text |
| Vorherige Pos. | Rang im Vormonat | Zahl |
| Suchvolumen | Monatliche Suchanfragen | Zahl |
| KW-Difficulty | Schwierigkeit 0–100 (Farbgradient) | Zahl |
| CPC (€) | Cost per Click | Zahl |
| Traffic | Geschätzter organischer Traffic | Zahl |
| SEA gebucht | Ja / Nein (Grün wenn Ja) | Text |
| Search Intent | navigational / informational / transactional | Text |
| URL | Ranking-URL | Text |
| Pos. Typ | Organic / Local pack / AI overview… | Text |
| SERP Features | Featured Snippet, PAA, etc. | Text |

### Tab 2 & 3 — Keyword Gaps / Top Potenziale
| Spalte | Inhalt |
|--------|--------|
| Keyword | Suchbegriff |
| Kategorie (eigene Domain) | Klassifizierung für Kontext |
| Wettbewerber Position | Rang des Wettbewerbers |
| Suchvolumen | Monatliche Suchanfragen |
| Schwierigkeit (%) | Competition/KD-Wert |
| CPC (€) | Cost per Click |
| Intent | Suchintention |
| Potenzial-Score | vol × (1 − schwierigkeit) |
| Potenzial | Sehr hoch / Hoch / Mittel / Niedrig |
| Wettbewerber URL | Ranking-URL des Wettbewerbers |

---

## Häufige Probleme & Lösungen

**Problem: CSV hat nur Header, keine Daten**
→ Nutzer nach korrekter Datei fragen. Export-Tool nochmal ausführen.

**Problem: Encoding-Fehler beim CSV-Lesen**
→ `encoding='utf-8-sig'` verwenden (entfernt BOM-Zeichen)

**Problem: Zu wenige B2B-Keywords**
→ Prüfen ob B2B-URLs (`/firmenkunden`, `/business`, etc.) im eigenen Ranking vorhanden
→ B2B_RE-Muster ggf. um branchenspezifische Terme ergänzen

**Problem: SEA-XLTX hat falschen Sheet-Aufbau**
→ Shared Strings aus `xl/sharedStrings.xml` lesen
→ Sheet-Namen aus `xl/workbook.xml` auslesen (nicht auf sheet1/2/3 verlassen)

**Problem: Zu viele/wenige Keyword Gaps**
→ Wettbewerber-Brand-Terme prüfen (werden gefiltert)
→ Normalisierung: `keyword.lower().strip()` vor Set-Vergleich

---

## Abhängigkeiten

```bash
python3 -c "import openpyxl; import pandas" 2>/dev/null || pip3 install openpyxl pandas
```

Nur `openpyxl` ist zwingend nötig. `pandas` optional.
