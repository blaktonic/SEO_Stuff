---
title: "NFX SEO-Audit Framework"
version: "1.0"
status: "stable"
last_updated: "2026-01-23"
author: "Noa (NFX SEO Engine)"
description: "Heuristisches Framework zur SEO-Analyse für Google Antigravity. Fokus auf AI-Visibility, EEAT und Technical Excellence."
framework_type: "operational"
---

# NFX SEO-Audit Framework 1.0

## 1. Scoring-Logik (Die Antigravity-Regeln)

Jede Heuristik wird auf einer Skala von 1–6 bewertet. Der Gesamtscore errechnet sich aus der Gewichtung der einzelnen Kategorien.

### Gewichtungsmatrix
| Stufe | Faktor | Beschreibung |
|---|---|---|
| **High Impact** | 3.0 | Kritisch für Indexierung, AI-Visibility und Top-Rankings. |
| **Medium Impact** | 2.0 | Wichtig für Wettbewerbsfähigkeit und User-Vertrauen. |
| **Low Impact** | 1.0 | Optimierung von Details und "Long-Tail"-Performance. |

---

## 2. Die 4 SEO-Überbereiche (Analog zu UX)

### Bereich A: Be Found & Understood (Technical & Crawling)
**Fokus:** Kann die KI die Seite lesen und effizient verarbeiten?
* **H1 – Index-Ready (High):** SSR-Implementierung, IndexNow-Anbindung, saubere Sitemap.
* **H2 – Semantic Schema (High):** Tiefe der JSON-LD Auszeichnung (Entities, FAQ, Reviews).
* **H3 – Digital Sustainability (Low):** Page Payload und Server-Effizienz (CO2-Footprint).

### Bereich B: Be Fast & Reliable (Performance)
**Fokus:** Die "Device Experience" der Suchmaschine.
* **H4 – Interaction to Next Paint / INP (High):** Reaktionszeit der UI (Standard 2026).
* **H4 – Stability & LCP (Medium):** Visuelle Stabilität und Ladezeit des Main Contents.

### Bereich C: Be Useful & Unique (Content & AI-Readiness)
**Fokus:** Information Gain und Mehrwert gegenüber dem KI-Durchschnitt.
* **H5 – Information Gain Score (High):** Bietet der Content exklusive Daten oder Insights?
* **H6 – Semantic Depth (Medium):** Abdeckung des Themen-Clusters statt Keyword-Spamming.
* **H7 – Inclusivity & Tone (Low):** Barrierefreie Sprache und markengerechter Vibe.

### Bereich D: Be Trusted (EEAT & Authority)
**Fokus:** Warum sollte man dieser Quelle vertrauen?
* **H8 – Entity Authority (High):** Sichtbarkeit der Autoren als Experten in der Nische.
* **H9 – Brand Sentiment (Medium):** Wie wird die Marke im Netz erwähnt (Positive/Negative Mentions)?

---

## 3. Anweisungen für den Agenten (Execution Logic)

1. **Daten-Extraktion:** Analysiere den Quellcode auf SSR, Schema.org und Web Vitals.
2. **Semantischer Check:** Vergleiche den Content mit den Top 3 Google-Ergebnissen (Information Gain Analyse).
3. **Scoring:** Vergebe 1–6 Punkte pro Heuristik basierend auf dem Erfüllungsgrad.
4. **Aggregation:** Berechne den Bereichs-Score durch Multiplikation mit den Impact-Faktoren.
