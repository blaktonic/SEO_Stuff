# Keyword-Klassifizierungs-Muster — Banking / Finanzdienstleister (DACH)

## Brand-Erkennung (Beispiele)
```python
# Eigene Brand-Terme (anpassen!):
BRAND_OWN = ['oberbank', 'ober bank']

# Wettbewerber Brand-Terme (für Gap-Filterung):
BRAND_COMPETITOR = ['bank austria', 'bankaustria', 'unicredit', 'creditanstalt', 'ba24']
# Weitere Beispiele:
# Erste Bank: ['erste bank', 'erstebank', 's-bank', 'george']
# Raiffeisen:  ['raiffeisen', 'raika', 'raiffeisenbank']
# BAWAG:       ['bawag', 'bawag psk', 'easy bank', 'easybank']
# Volksbank:   ['volksbank', 'elba']
```

## B2B URL-Muster
```python
B2B_URL = [
    '/firmenkunden', '/business', '/unternehmen', '/gewerbe', '/firmen',
    '/corporate', '/factoring', '/unternehmensfinanzierung',
    '/investitionskredit', '/kontokorrent', '/exportfinanzierung',
    '/betriebsmittel', '/leasing',      # generische /leasing-Seite = B2B
    '/mittelstand', '/freiberufler',
]
```

## B2C URL-Muster
```python
B2C_URL = [
    '/privatkunden', '/privatpersonen',
    '/wohnbaukredit', '/hypothekenkredit', '/konsumkredit', '/wohnkredit',
    '/girokonto', '/gehaltskonto', '/sparkonto', '/festgeld', '/tagesgeld',
    '/edelmetalle', '/depot', '/aktien', '/fonds', '/altersvorsorge',
    '/lebensversicherung', '/kreditkarte', '/kredit-finanzieren',
    '/sparen', '/geldanlage', '/immobilien', '/baufinanzierung', '/bausparen',
    '/anleihen', '/wertpapiere', '/versicherung',
]
```

## B2B Keyword-Muster (Regex)
```python
B2B_RE = [
    # Unternehmensfinanzierung
    r'\bunternehmen(s)?finanzierung\b',
    r'\bfirmen(kredit|konto|kunden|wagen|rechnung)\b',
    r'\bgewerbe(kredit|betrieb|lich|finanzierung)?\b',
    r'\bgew​erblich\b',
    r'\bmittelstand(s)?\b',
    r'\binvestitionskredit\b',
    r'\bbetriebsmittel(kredit)?\b',
    r'\bfactoring\b',
    r'\bexportfinanzierung\b',
    r'\baußenhandel\b',
    r'\bkontokorrent(kredit)?\b',
    r'\bnachfolgefinanzierung\b',
    r'\bunternehmensnachfolge\b',
    r'\bkmu\b',
    r'\bfreiberufler\b',
    r'\bselbst.?ständig\b',
    r'\bfirmengründung\b',
    r'\bunternehmensgründung\b',
    r'\bcorporate banking\b',
    r'\bbusiness banking\b',
    # Leasing (nur B2B-spezifisch; auto/kfz/privat → B2C)
    r'\bmobilien.{0,1}leasing\b',
    r'\bfinanzierungsleasing\b',
    r'\boperating.?leasing\b',
    r'\bleasing firmenwagen\b',
    r'\bfuhrpark.?leasing\b',
    # Konten/Kredit für Firmen
    r'\bunternehmenskonto\b',
    r'\bgesch[äa]ftskonto\b',
    r'\bfirmenkonto\b',
    r'\bkredit f[üu]r (unternehmen|firmen|betriebe)\b',
    r'\bfirmendarlehen\b',
    r'\bunternehmensdarlehen\b',
    r'\bgeschäftskunden\b',
    r'\bgründungsfinanzierung\b',
    r'\bmikrofinanzierung\b',
]
```

## B2C Keyword-Muster (Regex)
```python
B2C_RE = [
    # Konten
    r'\bgehaltskonto\b', r'\bgirokonto\b', r'\blohnkonto\b', r'\bbankkonto\b',
    r'\bkonto (eröffnen|vergleich|online)\b', r'\bbankkonto eröffnen\b',
    r'\bprivat(konto|e)\b',
    # Sparen & Anlage
    r'\bsparkonto\b', r'\bsparzinsen\b', r'\bsparbuch\b',
    r'\bfestgeld(konto|zinsen)?\b', r'\btagesgeld(konto)?\b',
    r'\bsparen\b', r'\bgeldanlage\b', r'\bgeld anlegen\b', r'\banlage\b',
    r'\banleihen\b', r'\bbausparen\b',
    # Wertpapiere
    r'\bdepot\b', r'\baktien?\b', r'\bfonds?\b', r'\bwertpapiere?\b',
    r'\bprivatkredit\b',
    # Kredit für Privatpersonen
    r'\bwohnkredit\b', r'\bwohnbaukredit\b', r'\bhypothekenkredit\b',
    r'\bhypothek\b', r'\bbaukredit\b', r'\bhauskredit\b',
    r'\bkonsumkredit\b', r'\bratenkredit\b', r'\bautokredit\b',
    r'\bsofortkredit\b', r'\bonline kredit\b', r'\bonlinekredit\b',
    r'\bkredit (vergleich|österreich|online|rechner|beantragen)\b',
    # Immobilien
    r'\bimmobilienfinanzierung\b', r'\bimmobilien\b', r'\bhausbau\b',
    r'\bbaufinanzierung\b', r'\bsanierung\b',
    # Vorsorge & Versicherung
    r'\baltersvorsorge\b', r'\bpensionsvorsorge\b', r'\bzukunftsvorsorge\b',
    r'\blebensversicherung\b', r'\bversicherung\b',
    # Karten & Zahlung
    r'\bkreditkarte\b', r'\bdebitkarte\b', r'\bbankomatkarte\b',
    r'\bgoogle pay\b', r'\bapple pay\b',
    # Edelmetalle & Währungen
    r'\bedelmetalle?\b', r'\bgoldpreis\b', r'\bgoldkurs\b',
    r'\bgold\b', r'\bsilber(preis|kurs)?\b', r'\bplatin\b',
    r'\bwährungsrechner\b', r'\bdevisenrechner\b',
    # Rechner (Consumer-Tools)
    r'\bkreditrechner\b', r'\bzinsrechner\b', r'\bhaushaltsrechner\b',
    # Online-Banking (Consumer)
    r'\bonline banking\b', r'\binternet banking\b', r'\bebanking\b',
    r'\bmobile banking\b', r'\bbanking app\b', r'\bnetbanking\b',
    r'\bebanking login\b', r'\blogin ebanking\b',
    # Konto-Aktivitäten
    r'\büberweisungen?\b', r'\bdauerauftrag\b', r'\bbankomat\b',
    r'\bdispokredit\b', r'\bdispo\b', r'\bkreditrahmen\b',
    r'\bzinsen?\b',
    # Leasing Consumer
    r'\b(auto|kfz|pkw|fahrzeug|privat).?leasing\b',
    r'\bleasing (auto|kfz|pkw|angebote|rechner|vergleich|privat)\b',
    r'\bleasing\b',     # Generisches "leasing" → Consumer-Kontext
    r'\bprivatkunden\b',
]
```

## Tipps zur Anpassung

- **Neue B2B-Begriffe** ergänzen wenn die Domain auf `/firmenkunden`, `/gewerbe` etc. rankt
- **Leasing**: `/kfz-leasing` ≠ `/leasing` als Substring! Genau prüfen was die URL enthält
- **Brand-Terme**: Immer domain-spezifisch anpassen — falsche Klassifizierung kostet Analysewert
- **Wettbewerber-Filter**: Großzügig — lieber zu viele als zu wenige Brand-Terme filtern
