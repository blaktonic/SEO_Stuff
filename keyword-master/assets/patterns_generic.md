# Keyword-Klassifizierungs-Muster — Generisch (alle Branchen)

## Hinweis

Dieses Template ist ein Ausgangspunkt. Für spezifische Branchen immer anpassen:
- Banking → `patterns_banking.md`
- E-Commerce → B2C dominiert, B2B = Händler/Großhandel
- SaaS → B2B dominiert, B2C = kleinere Pläne/Consumer-Produkte

## Universelle Brand-Erkennung

```python
# Brand-Terme aus Domainname ableiten:
# domain = "beispiel.at" → brand_terms = ['beispiel']
# Manuelle Ergänzungen: Abkürzungen, alte Firmennamen, Tochtermarken

BRAND_OWN = ['<domainname>', '<kurzname>']
BRAND_COMPETITOR = ['<wettbewerber1>', '<wettbewerber2>']
```

## B2B URL-Muster (generisch)

```python
B2B_URL = [
    '/business', '/b2b', '/unternehmen', '/firmenkunden', '/gewerbe',
    '/partner', '/reseller', '/grosshandel', '/wholesale',
    '/corporate', '/enterprise', '/api', '/developer',
]
```

## B2C URL-Muster (generisch)

```python
B2C_URL = [
    '/privatkunden', '/shop', '/produkte', '/leistungen',
    '/preise', '/pricing', '/kostenlos', '/gratis',
    '/ratgeber', '/blog', '/faq',
]
```

## B2B Keyword-Muster (generisch)

```python
B2B_RE = [
    r'\bb2b\b',
    r'\bgeschäftskunden?\b', r'\bfirmenkunden?\b', r'\bunternehmen(s)?\b',
    r'\bgewerbe(lich)?\b', r'\bprofessionell\b',
    r'\bgroßhandel\b', r'\bwholesale\b', r'\breseller\b',
    r'\bpartner(programm)?\b',
    r'\bagentur(en)?\b', r'\bdienstleister\b',
    r'\bkmu\b', r'\bmittelstand\b',
    r'\bapi\b', r'\benterprise\b', r'\bcorporate\b',
    r'\bbulk\b', r'\bmengenrabatt\b', r'\blizenz(en)?\b',
    r'\boutsourcing\b', r'\bsla\b',
    # Finanzielle B2B-Begriffe
    r'\binvestitionskredit\b', r'\bbetriebsmittel\b', r'\bleasing\b',
    r'\bfactoring\b', r'\bkontokorrent\b',
]
```

## B2C Keyword-Muster (generisch)

```python
B2C_RE = [
    r'\bprivatp?ersonen?\b', r'\bkonsumenten?\b', r'\bkunden?\b',
    r'\bkaufen\b', r'\bbestellen\b', r'\bonline (kaufen|bestellen|shop)\b',
    r'\bpreisvergleich\b', r'\bvergleich\b', r'\btest\b', r'\bbewertung\b',
    r'\bbillig\b', r'\bgünstig\b', r'\bangebot\b',
    r'\bkostenlos\b', r'\bgratis\b', r'\bfree\b',
    r'\bprivat(kredit|konto|e)?\b',
    # Typische Consumer-Produkte
    r'\bkreditkarte\b', r'\bgirokonto\b', r'\bsparkonto\b',
    r'\bversicherung\b', r'\baltersvorsorge\b',
    r'\bimmobilien\b', r'\bhaus (kaufen|mieten|bauen)\b',
    r'\bauto (kaufen|leasing|kredit)\b',
    r'\bonlineshopping\b', r'\blieferung\b', r'\brücksendung\b',
]
```

## Potenzial-Kategorien (anpassbar)

```python
def pot_label(volume, competition_decimal):
    comp = competition_decimal * 100
    # Schwellwerte je nach Branche anpassen:
    if volume >= 5000 and comp < 60:  return 'Sehr hoch'
    if volume >= 1000 and comp < 65:  return 'Hoch'
    if volume >= 300  and comp < 70:  return 'Mittel'
    return 'Niedrig'
```

## Anpassungstipps

1. **URL-Muster** sind die zuverlässigste Klassifizierungsmethode — immer zuerst analysieren
2. **Regex \b** = Wortgrenze. Verhindert Fehlklassifizierung (z.B. "unternehmen" in "kleinunternehmen")
3. **B2B vor B2C** prüfen: B2B-Terme sind spezifischer und seltener
4. **Spotcheck** nach Generierung: 5–10 Keywords manuell prüfen ob Klassifizierung stimmt
5. **Non-Brand** ist der Fallback — lieber etwas unklar lassen als falsch klassifizieren
