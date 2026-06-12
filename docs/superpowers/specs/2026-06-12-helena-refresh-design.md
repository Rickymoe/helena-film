# Helena — Hollywood Editorial Refresh

**Date:** 2026-06-12  
**Scope:** `index.html` full rewrite. `contact.html` follows same design language as separate step.  
**Stack:** Ren HTML/CSS/JS — ingen eksterne biblioteker utover Google Fonts.

---

## Mål

Redesign Helena Møhnckes porteføljeside fra dagens mørke filmestisk til en glossy Hollywood editorial-stil. Siden skal gi inntrykk av en høybudsjetts prestige-filmproduksjon — inspirert av A24, Cannes-brosjyrer og filmagasiner som Sight & Sound.

---

## Farger og typografi

| Token | Verdi | Bruk |
|---|---|---|
| `--black` | `#060608` | Sidebagrunn |
| `--gold` | `#c9a84c` | Primær aktuell, eyebrows, chapter-tall |
| `--gold-light` | `#e8c97a` | Hover-state på gull |
| `--crimson` | `#b82035` | Sekundær aktuell: chapter-nummer, understrek, toppstripe |
| `--white` | `#ffffff` | Overskrifter |
| `--muted` | `rgba(255,255,255,0.55)` | Brødtekst |
| `--faint` | `rgba(255,255,255,0.08)` | Skillelinjer, kort-kanter |

**Fonter:** Cormorant Garamond (display, serif) + Space Grotesk (brødtekst) — uendret fra nå.

Crimson brukes sparsomt: chapter-nummer, aktiv understrek i nav, hover-linjer på filmkort, tynn 1px toppstripe i hero.

---

## Effekter (globale)

### Film grain + scanlines
- Fast `position:fixed` overlay-element over hele siden, `pointer-events:none`, `z-index:999`
- **Grain:** SVG inline-filter med `feTurbulence` — CSS-animasjon endrer `seed`-attributt hvert 0.5s via `@keyframes` + `filter`-bytte mellom 2–3 seeds for å simulere kornbevegelse
- **Scanlines:** `repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.025) 2px, rgba(0,0,0,0.025) 4px)` på samme overlay
- Overlay-opacity: `0.035` — merkbar på mørke flater, usynlig på lys tekst

### Scroll-reveal (clip-path)
- `IntersectionObserver` med `threshold: 0.15` setter `.visible`-klasse på target-elementer
- CSS-overgang på alle `.reveal`-elementer:  
  `clip-path: inset(0 0 100% 0)` → `inset(0 0 0% 0)`  
  `transition: clip-path 0.8s cubic-bezier(0.76, 0, 0.24, 1)`
- Gjelder: alle section-headings, chapter-numre, filmkort, bilder, CV-items
- `will-change: clip-path` for GPU-akselerasjon
- **Mobil:** Behold effekten, men reduser `threshold` til `0.05` så elementer trigger tidligere på liten skjerm

---

## Sidestruktur

### Nav (fast, uendret posisjon)
- Logo: `Helena` i Cormorant Garamond kursiv, `H` i gull
- Lenker: uppercase, letter-spacing — aktiv lenke understrekes med crimson
- Hamburger på mobil (≤768px): åpner fullskjerm-meny

### Hero
- Full-bleed bakgrunnsbilde (`helena_hero_v6.png`)
- **Ghost chapter-tall:** `01` i Cormorant Garamond, `font-size: clamp(18rem, 35vw, 28rem)`, `opacity: 0.04`, posisjonert høyre side bak innhold
- **Crimson toppstripe:** 1px linje øverst på siden (`position:absolute; top:0`)
- Innhold (venstre/bunn): eyebrow med crimson strek → H1 → hero-sub → scroll-indikator
- **Progressbar:** 3px linje i bunn, `width` styrt av JS scroll-posisjon; farge: `linear-gradient(to right, #b82035, #c9a84c)`
- **Mobil:** Tekststørrelse skaleres ned, padding reduseres til 1.5rem. Ghost-tall skjules (`display:none`) for å spare plass.

### Chapter 01 — Filmer
- Section-header: `01` i crimson (liten, letter-spacing) + `Filmografi` i Cormorant Garamond
- Ghost `01` i bakgrunn (opacity 0.025)
- **Grid desktop:** Asymmetrisk — ett stort kort (venstre, `2fr`) + kolonne med 3 små kort (`1fr`), `gap: 3px`
- Hvert filmkort: bilde, hover `transform: scale(1.04)`, overlay nedenfra med år/tittel/genre. Hover-state: tynn crimson linje dukker opp øverst på kortet
- **Mobil:** Grid kollapser til én kolonne, alle kort like høye (250px)

### Tidslinje (visuelt mellomspill — ingen chapter-nummer)
- Mørk strip, full bredde
- To-kolonne grid med tidslinje-punkter og gull-markører (uendret innhold, ny stil)
- **Mobil:** Én kolonne

### Chapter 02 — Produksjon
- Section-header samme mønster som 01
- 3-kolonne video-grid — ett ekte videoklipp + 2 placeholder-kort
- Placeholder-kort viser `Video på vei` i kursiv Cormorant
- **Mobil:** Én kolonne

### Chapter 03 — Om Helena
- Split 50/50: bilde venstre, tekst-panel høyre
- Sitat med 2px crimson venstrekant
- **Mobil:** Bilde øverst (40vh), tekst under

### Portrett-strip (visuelt mellomspill)
- 3 bilder side om side, grayscale-filter, hover fjerner filter
- **Mobil:** Én kolonne, hvert bilde 220px høyt

### Galleri (visuelt mellomspill)
- Asymmetrisk CSS grid: `repeat(12, 1fr)` × 2 rader, 5 lokale stillbilder
- **Mobil:** 2-kolonne grid, jevne høyder

### Chapter 04 — CV
- To-kolonne: Utdanning venstre, Erfaring høyre
- Crimson-understrek på `h3`-overskrifter
- Hvert CV-item skilles med tynn `--faint`-linje
- **Mobil:** Én kolonne

### Kontakt (ingen chapter-nummer)
- Full-width sentrert seksjon
- Ghost-tekst `HELENA` i bakgrunn (opacity 0.02)
- E-postlenke i Cormorant kursiv, gull, med understrek
- **Mobil:** Samme, bare padding justert

### Footer
- Én linje: logo til venstre, `© 2026` til høyre
- Crimson toppkant 1px

---

## Mobil (≤768px) — generelle regler

- `nav ul` skjult, hamburger vises
- Alle `padding: 7rem 4rem` → `padding: 4rem 1.5rem`
- Multi-kolonne grids → én kolonne
- Ghost chapter-tall skjules
- Scroll-reveal threshold: `0.05`
- Film grain og scanlines: beholdes (ytelsen er god nok)
- Progressbar: beholdes

---

## Filer som endres

| Fil | Endring |
|---|---|
| `index.html` | Full omskriving |
| `.gitignore` | Lagt til (`.superpowers/`) |

## Filer som ikke endres i denne omgang

| Fil | Status |
|---|---|
| `contact.html` | Designjustering — separat steg |
| `images/` | Uendret |
| `video/` | Uendret |
