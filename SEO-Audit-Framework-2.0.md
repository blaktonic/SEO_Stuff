---
title: "NFX Operational SEO Framework"
version: "2.0"
type: "Agentic-Instruction-Set"
focus: "Technical Excellence, Accessibility & Content Hygiene"
author: "Noa (NFX Engine)"
last_updated: "2026-02-09"
---

# NFX Operational SEO Framework 2.0

## System-Logik für Agenten
Dieses Framework dient als strikte operative Anweisung für KI-Agenten.
* **Input-Pflicht:** Kein Audit ohne vollständigen Crawl (Modul 0).
* **Scoring:** 1 (Fail/Critical) bis 6 (Perfect/Clean).
* **Priorität:** High (Sofort fixen), Medium (Optimieren), Low (Monitoring).

---

## Modul 0: Inventory & Discovery (Die Basis)
*Bevor geprüft wird, muss das gesamte URL-Set erfasst werden. Keine Blindflüge.*

### 0.1 – URL Harvesting & Scope
* **Crawl-Simulation:** Starte von der Homepage (Root) und folge allen internen Links, um das reale Webseiten-Netzwerk zu erfassen.
* **Sitemap-Parsing:** Extrahiere parallel alle URLs aus der/den `sitemap.xml`.
* **Scope-Definition:** Erstelle eine Master-Liste aller einzigartigen HTML-Dokumente. Ignoriere Parameter-URLs (sofern canonicalisiert) und System-Pfade.

### 0.2 – Gap Analysis
* **Orphan Pages:** Identifiziere URLs, die in der Sitemap stehen, aber beim Crawl nicht gefunden wurden (verwaist).
* **Hidden Pages:** Identifiziere URLs, die verlinkt sind, aber in der Sitemap fehlen.

---

## Modul 1: Technical Foundation (Der Maschinenraum)
*Prüfung der Infrastruktur, Erreichbarkeit und Crawlability.*

### 1.1 – Indexing Health (High Impact)
* **Status Codes:** Identifiziere alle 4xx (Client Error) und 5xx (Server Error). Ziel: 0 tote Links.
* **Robots.txt & Meta-Robots:** Validierung auf blockierte Ressourcen und Prüfung auf ungewollte `noindex`/`nofollow` Tags.
* **XML-Sitemap Validierung:** Sind die in Modul 0 gefundenen URLs auch wirklich indexierbar (Status 200, kein noindex)?

### 1.2 – Architecture & Canonicals (High Impact)
* **Canonical Tags:** Ist der Tag gesetzt? Zeigt er auf sich selbst oder extern? Identifiziere Canonical-Loops oder Ketten.
* **Interne Verlinkung:** Analyse der Klicktiefe. Haben wichtige Seiten weniger als 3 eingehende interne Links?

### 1.3 – International & Schema (Medium Impact)
* **Hreflang:** Validierung der Rückverlinkung (Reciprocal Links) und der Sprachcodes.
* **JSON-LD / Schema:** Validierung gegen Schema.org. Sind Breadcrumbs und Seitentyp-Schemata fehlerfrei implementiert?

### 1.4 – Performance (Medium Impact)
* **Pagespeed:** Messung von TTFB (Time to First Byte).
* **Core Web Vitals:** Abgleich von LCP und CLS Werten (schnelle Seiten sind inklusive Seiten!).

---

## Modul 2: Content Hygiene & Inclusivity (Die Substanz)
*Prüfung der OnPage-Elemente, Semantik und Barrierefreiheit.*

### 2.1 – Meta-Assets (High Impact)
* **Title Tags:** Prüfung auf Vorhandensein, Duplikate und Länge.
* **Meta Descriptions:** Prüfung auf Vorhandensein und Duplikate.

### 2.2 – Structural Integrity (Medium Impact)
* **H1 Headlines:** Existiert exakt *eine* H1 pro URL? Ist sie domainweit einzigartig?
* **Headline-Hierarchie:** Ist die semantische Struktur logisch (H1 -> H2 -> H3) oder gebrochen?

### 2.3 – Content Quality & Accessibility (High Impact)
* **Duplicate Content:** Identifizierung von URLs mit >90% Textübereinstimmung (Near-Duplicate / Ressourcenverschwendung).
* **Thin Content:** URLs mit < 300 Wörtern Main Content flaggen (ausgenommen Kontakt/Impressum).
* **Image Accessibility:** Prüfung aller `<img>` Tags auf `alt`-Attribute. Der Alt-Text muss beschreibend sein (Screenreader-Pflicht!).
* **Dateinamen:** Prüfung auf generische Bildnamen (z.B. `IMG_001.jpg` statt beschreibendem Text).

---

## Modul 3: Authority & Rankings (Das Netzwerk)
*Prüfung externer Signale und Keyword-Mappings.*

### 3.1 – Backlink Health (High Impact)
* **Broken Backlinks:** Finde externe Links, die auf 404-Seiten der eigenen Domain zeigen (verschwendetes Potenzial).
* **Backlink-Profil:** (Erfordert API) Domain Authority und verweisende Domains.

### 3.2 – Keyword Rankings (Monitoring)
* **Keyword-Mapping:** Aktuelle Keyword Rankings mit den zugehörigen URLs mappen (via GSC).

---

## Modul 4: Relaunch Clean-Up (Der Besen)
*Spezial-Modul: Nur bei Migrationen oder Aufräumarbeiten ausführen.*

### 4.1 – Legacy Handling
* **Identifizierung:** Alte URLs gegen aktuellen Status Code prüfen.
* **Index-Leichen:** Prüfen, ob alte 404-URLs noch im Google Index hängen.
* **Value Check:** Haben diese alten URLs noch Backlinks oder generieren Traffic?
* **Redirect-Mapping:** Generiere 301-Redirect-Vorschläge auf die inhaltlich passendste neue URL.
