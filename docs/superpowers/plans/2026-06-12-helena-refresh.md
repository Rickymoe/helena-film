# Helena Hollywood Editorial Refresh — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fullstendig omskriving av `index.html` til en glossy Hollywood editorial-stil med chapter-struktur, gold+crimson palett, film grain og scroll-reveal.

**Architecture:** Én enkelt `index.html` med inline CSS og JS — ingen build-steg, ingen eksterne biblioteker utover Google Fonts. Siden bygges seksjon for seksjon, én commit per task. `contact.html` er utenfor scope.

**Tech Stack:** HTML5, CSS (custom properties, clip-path, CSS Grid, scroll-driven), vanilla JS (IntersectionObserver, scroll-event), Google Fonts (Cormorant Garamond + Space Grotesk).

**Spec:** `docs/superpowers/specs/2026-06-12-helena-refresh-design.md`

---

## Filstruktur

| Fil | Handling |
|---|---|
| `index.html` | Full omskriving — alle endringer skjer her |
| `.gitignore` | Allerede lagt til (`.superpowers/`) |

Alle andre filer (`images/`, `video/`, `contact.html`) er uendret.

---

## Task 1: Skeleton + CSS-fundament

**Filer:**
- Skriv om: `index.html` (erstatter eksisterende)

- [ ] **Steg 1: Skriv ny index.html med head, variabler og tom body**

```html
<!DOCTYPE html>
<html lang="no">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Helena Møhncke — Filmregissør</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Space+Grotesk:wght@300;400;500&display=swap" rel="stylesheet">
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
:root {
  --black:   #060608;
  --off-black: #111;
  --dark:    #1a1a1a;
  --gold:    #c9a84c;
  --gold-light: #e8c97a;
  --crimson: #b82035;
  --white:   #ffffff;
  --muted:   rgba(255,255,255,0.55);
  --faint:   rgba(255,255,255,0.08);
}
html { scroll-behavior: smooth; }
body {
  font-family: 'Space Grotesk', sans-serif;
  background: var(--black);
  color: var(--white);
  overflow-x: hidden;
}
</style>
</head>
<body>
<!-- GRAIN -->
<!-- NAV -->
<!-- MOBILE MENU -->
<!-- HERO -->
<!-- CHAPTER 01: FILMER -->
<!-- TIDSLINJE INTERLUDE -->
<!-- CHAPTER 02: PRODUKSJON -->
<!-- CHAPTER 03: OM HELENA -->
<!-- PORTRETT-STRIP INTERLUDE -->
<!-- GALLERI INTERLUDE -->
<!-- CHAPTER 04: CV -->
<!-- KONTAKT -->
<!-- FOOTER -->
<script>
/* JS her */
</script>
</body>
</html>
```

- [ ] **Steg 2: Verifiser i nettleser**

Åpne `index.html` i Firefox/Chrome.  
Forventet: Helt svart/mørk side, ingen feil i konsollen.

- [ ] **Steg 3: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: ny index.html skeleton med CSS-variabler"
```

---

## Task 2: Film grain + scanline overlay

**Filer:**
- Modifiser: `index.html` — legg til grain-div og CSS

- [ ] **Steg 1: Legg til grain-div etter `<body>`**

Erstatt kommentaren `<!-- GRAIN -->` med:

```html
<div class="grain" aria-hidden="true"></div>
```

- [ ] **Steg 2: Legg til grain-CSS i `<style>`-blokken**

```css
/* ── GRAIN + SCANLINES ─────────────────────────────── */
.grain {
  position: fixed;
  inset: 0;
  z-index: 999;
  pointer-events: none;
  opacity: 0.04;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  animation: grain-shift 0.5s steps(1) infinite;
}
.grain::after {
  content: '';
  position: fixed;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent 0px,
    transparent 2px,
    rgba(0,0,0,0.025) 2px,
    rgba(0,0,0,0.025) 4px
  );
}
@keyframes grain-shift {
  0%   { transform: translate(0,    0);    }
  10%  { transform: translate(-2%,  -3%);  }
  20%  { transform: translate(3%,   2%);   }
  30%  { transform: translate(-1%,  4%);   }
  40%  { transform: translate(2%,  -1%);   }
  50%  { transform: translate(-3%,  1%);   }
  60%  { transform: translate(1%,  -4%);   }
  70%  { transform: translate(-2%,  3%);   }
  80%  { transform: translate(4%,  -2%);   }
  90%  { transform: translate(-1%,  2%);   }
  100% { transform: translate(0,    0);    }
}
```

- [ ] **Steg 3: Verifiser i nettleser**

Last inn siden på nytt. Inspiser siden mot lys bakgrunn (midlertidig: `body { background: #333; }`).  
Forventet: Subtil kornstruktur beveger seg. Fjern testbakgrunnen etterpå.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: legg til film grain og scanline overlay"
```

---

## Task 3: Scroll-reveal system

**Filer:**
- Modifiser: `index.html` — legg til `.reveal`-CSS og IntersectionObserver JS

- [ ] **Steg 1: Legg til scroll-reveal CSS i `<style>`**

```css
/* ── SCROLL-REVEAL ─────────────────────────────────── */
.reveal {
  clip-path: inset(0 0 100% 0);
  transition: clip-path 0.8s cubic-bezier(0.76, 0, 0.24, 1);
  will-change: clip-path;
}
.reveal.visible {
  clip-path: inset(0 0 0% 0);
}
```

- [ ] **Steg 2: Legg til IntersectionObserver i `<script>`-blokken**

```js
// Scroll-reveal
const revealThreshold = window.innerWidth <= 768 ? 0.05 : 0.12;
const revealObserver = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) {
      e.target.classList.add('visible');
      revealObserver.unobserve(e.target);
    }
  });
}, { threshold: revealThreshold });
document.querySelectorAll('.reveal').forEach(el => revealObserver.observe(el));
```

- [ ] **Steg 3: Verifiser**

Legg midlertidig til en testdiv i body:
```html
<div class="reveal" style="background:red;height:100px;margin:200px auto;width:200px;">TEST</div>
```
Last inn siden. Scroll ned. Forventet: rød boks glir inn nedenfra. Fjern testdiven.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: legg til scroll-reveal system (clip-path + IntersectionObserver)"
```

---

## Task 4: Nav

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- NAV -->` og `<!-- MOBILE MENU -->`

- [ ] **Steg 1: Legg til nav-CSS i `<style>`**

```css
/* ── NAV ───────────────────────────────────────────── */
nav {
  position: fixed; top: 0; left: 0; right: 0;
  padding: 1.5rem 4rem;
  display: flex; align-items: center; justify-content: space-between;
  z-index: 100;
  background: linear-gradient(to bottom, rgba(6,6,8,0.96), transparent);
}
.nav-logo {
  font-family: 'Cormorant Garamond', serif;
  font-size: 1.4rem; font-style: italic; letter-spacing: 2px;
  color: var(--white); text-decoration: none;
}
.nav-logo span { color: var(--gold); }
nav ul { list-style: none; display: flex; gap: 2.5rem; }
nav ul li a {
  text-decoration: none; color: rgba(255,255,255,0.55);
  font-size: 0.75rem; letter-spacing: 2px; text-transform: uppercase;
  transition: color 0.2s; position: relative; padding-bottom: 3px;
}
nav ul li a::after {
  content: ''; position: absolute; bottom: 0; left: 0; right: 0;
  height: 1px; background: var(--crimson);
  transform: scaleX(0); transition: transform 0.2s;
}
nav ul li a:hover { color: var(--white); }
nav ul li a:hover::after { transform: scaleX(1); }

.hamburger {
  display: none; flex-direction: column; gap: 5px;
  cursor: pointer; background: none; border: none; padding: 4px;
}
.hamburger span {
  display: block; width: 24px; height: 1.5px; background: var(--white);
  transition: transform 0.3s, opacity 0.3s;
}
.hamburger.open span:nth-child(1) { transform: translateY(6.5px) rotate(45deg); }
.hamburger.open span:nth-child(2) { opacity: 0; }
.hamburger.open span:nth-child(3) { transform: translateY(-6.5px) rotate(-45deg); }

.mobile-menu {
  display: none; position: fixed; inset: 0; background: rgba(6,6,8,0.97);
  z-index: 99; flex-direction: column; align-items: center; justify-content: center; gap: 2.5rem;
}
.mobile-menu.open { display: flex; }
.mobile-menu a {
  font-family: 'Cormorant Garamond', serif; font-size: 2.5rem;
  font-weight: 300; font-style: italic; color: rgba(255,255,255,0.7);
  text-decoration: none; letter-spacing: 2px; transition: color 0.2s;
}
.mobile-menu a:hover { color: var(--gold); }
```

- [ ] **Steg 2: Legg til nav-HTML**

Erstatt `<!-- NAV -->` og `<!-- MOBILE MENU -->` med:

```html
<nav>
  <a href="#" class="nav-logo"><span>H</span>elena</a>
  <ul>
    <li><a href="#films">Filmer</a></li>
    <li><a href="#produksjon">Produksjon</a></li>
    <li><a href="#om">Om</a></li>
    <li><a href="#cv">CV</a></li>
    <li><a href="contact.html">Kontakt</a></li>
  </ul>
  <button class="hamburger" id="hamburger" aria-label="Meny">
    <span></span><span></span><span></span>
  </button>
</nav>

<div class="mobile-menu" id="mobileMenu">
  <a href="#films"     onclick="closeMenu()">Filmer</a>
  <a href="#produksjon" onclick="closeMenu()">Produksjon</a>
  <a href="#om"        onclick="closeMenu()">Om</a>
  <a href="#cv"        onclick="closeMenu()">CV</a>
  <a href="contact.html">Kontakt</a>
</div>
```

- [ ] **Steg 3: Legg til hamburger JS i `<script>`**

```js
// Hamburger
const hamburger = document.getElementById('hamburger');
const mobileMenu = document.getElementById('mobileMenu');
hamburger.addEventListener('click', () => {
  hamburger.classList.toggle('open');
  mobileMenu.classList.toggle('open');
});
function closeMenu() {
  hamburger.classList.remove('open');
  mobileMenu.classList.remove('open');
}
```

- [ ] **Steg 4: Verifiser**

Desktop: Nav vises øverst, lenker har crimson understrek-animasjon på hover.  
Mobil (DevTools → 375px): Hamburger vises, meny åpner/lukker korrekt.

- [ ] **Steg 5: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: nav med crimson hover-understrek og mobilmeny"
```

---

## Task 5: Hero

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- HERO -->`

- [ ] **Steg 1: Legg til hero-CSS i `<style>`**

```css
/* ── HERO ──────────────────────────────────────────── */
.hero {
  min-height: 100vh; position: relative;
  display: flex; align-items: flex-end;
  padding: 0 4rem 7rem; overflow: hidden;
}
.hero-bg {
  position: absolute; inset: 0;
  background: url('images/helena_hero_v6.png') center/cover no-repeat;
}
.hero-overlay {
  position: absolute; inset: 0;
  background: linear-gradient(to top, rgba(6,6,8,1) 0%, rgba(6,6,8,0.55) 50%, rgba(6,6,8,0.2) 100%);
}
.hero-stripe {
  position: absolute; top: 0; left: 0; right: 0; height: 1px;
  background: var(--crimson); opacity: 0.7;
}
.hero-ghost {
  position: absolute; right: -0.05em; bottom: -0.15em;
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(16rem, 32vw, 26rem);
  font-weight: 300; font-style: italic;
  color: rgba(255,255,255,0.035);
  line-height: 1; pointer-events: none; user-select: none;
}
.hero-content {
  position: relative; z-index: 1; max-width: 680px;
  animation: fadeUp 1.2s ease both;
}
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(40px); }
  to   { opacity: 1; transform: translateY(0); }
}
.hero-chapter {
  display: flex; align-items: center; gap: 10px; margin-bottom: 1.4rem;
}
.hero-chapter-line {
  width: 24px; height: 1px; background: var(--crimson);
}
.hero-chapter-label {
  font-size: 0.7rem; letter-spacing: 4px; text-transform: uppercase;
  color: var(--crimson);
}
.hero h1 {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(4rem, 9vw, 7.5rem);
  line-height: 0.92; font-weight: 300; margin-bottom: 1.6rem;
}
.hero h1 em { font-style: italic; color: var(--gold); }
.hero-sub {
  font-size: 1rem; color: var(--muted); line-height: 1.75;
  max-width: 460px; font-weight: 300; margin-bottom: 2.5rem;
}
.hero-scroll {
  display: flex; align-items: center; gap: 1rem;
  color: rgba(255,255,255,0.35); font-size: 0.7rem;
  letter-spacing: 3px; text-transform: uppercase;
}
.scroll-line { width: 50px; height: 1px; background: var(--gold); opacity: 0.5; }

/* Progressbar */
.progress-bar {
  position: fixed; bottom: 0; left: 0; right: 0; height: 3px;
  z-index: 200; pointer-events: none;
  background: rgba(255,255,255,0.05);
}
.progress-bar-fill {
  height: 100%; width: 0%;
  background: linear-gradient(to right, var(--crimson), var(--gold));
  transition: width 0.1s linear;
}
```

- [ ] **Steg 2: Legg til hero-HTML**

Erstatt `<!-- HERO -->` med:

```html
<div class="progress-bar" aria-hidden="true">
  <div class="progress-bar-fill" id="progressFill"></div>
</div>

<section class="hero">
  <div class="hero-bg"></div>
  <div class="hero-overlay"></div>
  <div class="hero-stripe"></div>
  <div class="hero-ghost" aria-hidden="true">01</div>
  <div class="hero-content">
    <div class="hero-chapter">
      <div class="hero-chapter-line"></div>
      <span class="hero-chapter-label">Filmregissør · Norge</span>
    </div>
    <h1>Fortellinger<br>som <em>brenner</em><br>seg fast</h1>
    <p class="hero-sub">Helena skaper film som berører, utfordrer og setter spor — fra kortfilm til dokumentar, alltid med et ekte menneskelig blikk.</p>
    <div class="hero-scroll">
      <div class="scroll-line"></div>
      Scroll for å utforske
    </div>
  </div>
</section>
```

- [ ] **Steg 3: Legg til progressbar JS i `<script>`**

```js
// Progressbar
const progressFill = document.getElementById('progressFill');
window.addEventListener('scroll', () => {
  const scrolled = window.scrollY;
  const total = document.documentElement.scrollHeight - window.innerHeight;
  progressFill.style.width = (scrolled / total * 100) + '%';
}, { passive: true });
```

- [ ] **Steg 4: Verifiser**

Åpne siden. Forventet:
- Hero fyller hele skjermen med bilde
- Crimson 1px linje øverst
- Ghost `01` bak innhold (svakt synlig)
- Scrolling: gradient crimson→gull progressbar vokser langs bunnen

- [ ] **Steg 5: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: hero med chapter-ghost, crimson stripe og scroll-progressbar"
```

---

## Task 6: Chapter 01 — Filmer

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- CHAPTER 01: FILMER -->`

- [ ] **Steg 1: Legg til chapter-fellesklasser og films-CSS**

```css
/* ── CHAPTER FELLES ─────────────────────────────────── */
.chapter { padding: 7rem 4rem; position: relative; overflow: hidden; }
.chapter-ghost {
  position: absolute; right: -0.05em; top: -0.1em;
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(12rem, 22vw, 20rem); font-weight: 300; font-style: italic;
  color: rgba(255,255,255,0.025); line-height: 1;
  pointer-events: none; user-select: none;
}
.chapter-header { margin-bottom: 4rem; }
.chapter-number {
  display: inline-block; font-size: 0.68rem; letter-spacing: 5px;
  color: var(--crimson); text-transform: uppercase; margin-bottom: 0.5rem;
}
.section-eyebrow {
  font-size: 0.7rem; letter-spacing: 4px; text-transform: uppercase;
  color: var(--gold); margin-bottom: 0.8rem;
}
.section-title {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(2.5rem, 4vw, 4rem); font-weight: 300; line-height: 1.1;
  margin-bottom: 0.8rem;
}
.section-sub {
  color: var(--muted); font-size: 0.92rem; line-height: 1.8;
  font-weight: 300; max-width: 460px;
}

/* ── CHAPTER 01: FILMER ─────────────────────────────── */
.films-grid { display: grid; grid-template-columns: 2fr 1fr; gap: 3px; }
.films-right { display: grid; grid-template-rows: 1fr 1fr 1fr; gap: 3px; }
.film-card { position: relative; overflow: hidden; cursor: pointer; }
.film-card.large { height: 580px; }
.film-card.small { height: 190px; }
.film-card img {
  width: 100%; height: 100%; object-fit: cover; display: block;
  transition: transform 0.6s ease;
}
.film-card:hover img { transform: scale(1.05); }
.film-card-overlay {
  position: absolute; inset: 0;
  background: linear-gradient(to top, rgba(6,6,8,0.92) 0%, transparent 55%);
  display: flex; flex-direction: column; justify-content: flex-end;
  padding: 1.4rem;
  transition: background 0.3s;
}
.film-card::before {
  content: ''; position: absolute; top: 0; left: 0; right: 0;
  height: 2px; background: var(--crimson);
  transform: scaleX(0); transform-origin: left;
  transition: transform 0.35s ease; z-index: 2;
}
.film-card:hover::before { transform: scaleX(1); }
.film-year { font-size: 0.68rem; letter-spacing: 3px; color: var(--crimson); text-transform: uppercase; margin-bottom: 0.3rem; }
.film-title { font-family: 'Cormorant Garamond', serif; font-size: 1.5rem; font-weight: 400; margin-bottom: 0.25rem; }
.film-card.small .film-title { font-size: 1.15rem; }
.film-genre { font-size: 0.72rem; color: rgba(255,255,255,0.45); }
```

- [ ] **Steg 2: Legg til HTML for Chapter 01**

Erstatt `<!-- CHAPTER 01: FILMER -->` med:

```html
<section class="chapter" id="films" style="background: var(--black);">
  <div class="chapter-ghost" aria-hidden="true">01</div>
  <div class="chapter-header reveal">
    <div class="chapter-number">01 — Filmografi</div>
    <p class="section-eyebrow">Utvalgte prosjekter</p>
    <h2 class="section-title">Fra idé til lerret</h2>
    <p class="section-sub">Et knippe av Helenas arbeider — fra studentprosjekter til profesjonelle produksjoner.</p>
  </div>
  <div class="films-grid">
    <div class="film-card large reveal">
      <img src="https://images.unsplash.com/photo-1440404653325-ab127d49abc1?w=900&q=80" alt="Mellom Fjordene" loading="lazy" />
      <div class="film-card-overlay">
        <div class="film-year">2024</div>
        <div class="film-title">Mellom Fjordene</div>
        <div class="film-genre">Kortfilm · 18 min</div>
      </div>
    </div>
    <div class="films-right">
      <div class="film-card small reveal">
        <img src="https://images.unsplash.com/photo-1478720568477-152d9b164e26?w=600&q=80" alt="Stilleben" loading="lazy" />
        <div class="film-card-overlay">
          <div class="film-year">2023</div>
          <div class="film-title">Stilleben</div>
          <div class="film-genre">Dokumentar · 34 min</div>
        </div>
      </div>
      <div class="film-card small reveal">
        <img src="https://images.unsplash.com/photo-1500210600724-a6acd3d06b3f?w=600&q=80" alt="Nattskiftet" loading="lazy" />
        <div class="film-card-overlay">
          <div class="film-year">2022</div>
          <div class="film-title">Nattskiftet</div>
          <div class="film-genre">Kortfilm · 12 min</div>
        </div>
      </div>
      <div class="film-card small reveal">
        <img src="https://images.unsplash.com/photo-1536440136628-849c177e76a1?w=600&q=80" alt="Grenser" loading="lazy" />
        <div class="film-card-overlay">
          <div class="film-year">2021</div>
          <div class="film-title">Grenser</div>
          <div class="film-genre">Eksperimentell · 8 min</div>
        </div>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Steg 3: Re-kjør IntersectionObserver etter DOM er klar**

Sørg for at `document.querySelectorAll('.reveal').forEach(...)` er etter at alle HTML-seksjoner er på plass, eller legg det i en `DOMContentLoaded`-lytter. Sjekk at dette er nederst i `<script>` (etter at alle `.reveal`-elementer er definert i HTML).

- [ ] **Steg 4: Verifiser**

Scroll ned forbi hero. Forventet:
- Chapter-header glir inn nedenfra (clip-path reveal)
- Asymmetrisk grid: ett stort bilde til venstre, tre små til høyre
- Hover: bilde zoomer, crimson linje tørkes inn fra venstre på toppen av kortet

- [ ] **Steg 5: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: chapter 01 filmografi med asymmetrisk grid og crimson hover"
```

---

## Task 7: Tidslinje (visuelt mellomspill)

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- TIDSLINJE INTERLUDE -->`

- [ ] **Steg 1: Legg til tidslinje-CSS**

```css
/* ── TIDSLINJE INTERLUDE ────────────────────────────── */
.timeline { padding: 6rem 4rem; background: var(--dark); }
.timeline-grid {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 0; margin-top: 3.5rem;
}
.timeline-item {
  padding: 2.2rem 2.8rem; border-left: 1px solid rgba(201,168,76,0.18);
  position: relative; transition: background 0.3s;
}
.timeline-item:hover { background: rgba(201,168,76,0.03); }
.timeline-item::before {
  content: ''; position: absolute; left: -5px; top: 2.6rem;
  width: 9px; height: 9px; border-radius: 50%;
  background: var(--gold);
}
.timeline-year {
  font-size: 0.68rem; letter-spacing: 3px; color: var(--crimson);
  text-transform: uppercase; margin-bottom: 0.4rem;
}
.timeline-title {
  font-family: 'Cormorant Garamond', serif;
  font-size: 1.35rem; font-weight: 400; margin-bottom: 0.4rem;
}
.timeline-desc { font-size: 0.83rem; color: var(--muted); line-height: 1.65; font-weight: 300; }
```

- [ ] **Steg 2: Legg til HTML**

Erstatt `<!-- TIDSLINJE INTERLUDE -->` med:

```html
<section class="timeline" id="timeline">
  <p class="section-eyebrow reveal">Reisen</p>
  <h2 class="section-title reveal">Fra drøm til regissørstolen</h2>
  <div class="timeline-grid">
    <div class="timeline-item reveal">
      <div class="timeline-year">2018</div>
      <div class="timeline-title">Første kortfilm</div>
      <p class="timeline-desc">Med et lånt kamera og venner som skuespillere laget Helena sin første kortfilm i videregående.</p>
    </div>
    <div class="timeline-item reveal">
      <div class="timeline-year">2020</div>
      <div class="timeline-title">Filmskolen</div>
      <p class="timeline-desc">Tatt opp ved filmlinjen — begynte å utvikle sin egen stemme og estetikk.</p>
    </div>
    <div class="timeline-item reveal">
      <div class="timeline-year">2022</div>
      <div class="timeline-title">Første festivalpremiere</div>
      <p class="timeline-desc">«Nattskiftet» valgt ut til Nordic Film Festival og fikk strålende kritikker.</p>
    </div>
    <div class="timeline-item reveal">
      <div class="timeline-year">2024</div>
      <div class="timeline-title">Ferdig utdannet</div>
      <p class="timeline-desc">Avslutter utdannelsen med et sterkt avgangsprosjekt — klar for sitt første profesjonelle prosjekt.</p>
    </div>
  </div>
</section>
```

- [ ] **Steg 3: Verifiser**

Scroll forbi filmografi. Forventet: mørk strip med gull-markerte tidslinjepunkter, crimson årstall, hvert element glir inn ved scroll.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: tidslinje-interlude med crimson årstall"
```

---

## Task 8: Chapter 02 — Produksjon

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- CHAPTER 02: PRODUKSJON -->`

- [ ] **Steg 1: Legg til produksjon-CSS**

```css
/* ── CHAPTER 02: PRODUKSJON ─────────────────────────── */
.produksjon { background: var(--off-black); }
.video-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 3px; margin-top: 4rem; }
.video-card {
  position: relative; overflow: hidden; cursor: pointer;
  aspect-ratio: 16/9; background: var(--dark);
}
.video-card video {
  width: 100%; height: 100%; object-fit: cover; display: block;
  transition: transform 0.5s ease;
}
.video-card:hover video { transform: scale(1.04); }
.video-card-overlay {
  position: absolute; inset: 0;
  background: linear-gradient(to top, rgba(6,6,8,0.88) 0%, transparent 55%);
  display: flex; flex-direction: column; justify-content: flex-end;
  padding: 1.1rem 1.3rem;
}
.video-card--placeholder {
  display: flex; align-items: center; justify-content: center;
  border: 1px solid rgba(201,168,76,0.12);
}
.video-placeholder-text {
  font-family: 'Cormorant Garamond', serif; font-style: italic;
  font-size: 1.3rem; font-weight: 300; color: rgba(201,168,76,0.35); letter-spacing: 2px;
}
.video-card-year { font-size: 0.68rem; letter-spacing: 3px; color: var(--crimson); text-transform: uppercase; margin-bottom: 0.25rem; }
.video-card-title { font-family: 'Cormorant Garamond', serif; font-size: 1.5rem; font-weight: 400; margin-bottom: 0.2rem; }
.video-card-genre { font-size: 0.72rem; color: rgba(255,255,255,0.45); }
```

- [ ] **Steg 2: Legg til HTML**

Erstatt `<!-- CHAPTER 02: PRODUKSJON -->` med:

```html
<section class="chapter produksjon" id="produksjon">
  <div class="chapter-ghost" aria-hidden="true">02</div>
  <div class="chapter-header reveal">
    <div class="chapter-number">02 — Produksjon</div>
    <p class="section-eyebrow">Bak kameraet</p>
    <h2 class="section-title">Et innblikk i prosessen</h2>
  </div>
  <div class="video-grid">
    <div class="video-card reveal" onclick="this.querySelector('video').paused ? this.querySelector('video').play() : this.querySelector('video').pause()">
      <video preload="metadata" playsinline poster="video/Opptak1_poster.jpg" loop>
        <source src="video/Opptak1.mp4" type="video/mp4">
      </video>
      <div class="video-card-overlay">
        <div class="video-card-year">2025</div>
        <div class="video-card-title">Klipp 15052026.15</div>
        <div class="video-card-genre">Kortfilm · 8 sek</div>
      </div>
    </div>
    <div class="video-card video-card--placeholder reveal">
      <div class="video-placeholder-text">Video på vei!</div>
    </div>
    <div class="video-card video-card--placeholder reveal">
      <div class="video-placeholder-text">Video på vei!</div>
    </div>
  </div>
</section>
```

- [ ] **Steg 3: Verifiser**

Scroll til produksjon. Forventet: 3-kolonne grid, video avspilles ved klikk, ghost `02` i bakgrunn.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: chapter 02 produksjon med video-grid"
```

---

## Task 9: Chapter 03 — Om Helena + Portrett-strip

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- CHAPTER 03: OM HELENA -->` og `<!-- PORTRETT-STRIP INTERLUDE -->`

- [ ] **Steg 1: Legg til om-CSS**

```css
/* ── CHAPTER 03: OM HELENA ──────────────────────────── */
.about { display: grid; grid-template-columns: 1fr 1fr; min-height: 80vh; }
.about-image { position: relative; overflow: hidden; min-height: 560px; }
.about-image img { width: 100%; height: 100%; object-fit: cover; display: block; }
.about-image::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(to right, transparent 55%, var(--off-black));
}
.about-content {
  background: var(--off-black); padding: 6rem 5rem;
  display: flex; flex-direction: column; justify-content: center;
  position: relative;
}
.about-content::before {
  content: '03'; position: absolute; right: 2rem; top: 2rem;
  font-family: 'Cormorant Garamond', serif;
  font-size: 8rem; font-weight: 300; font-style: italic;
  color: rgba(255,255,255,0.025); line-height: 1;
}
.about-quote {
  font-family: 'Cormorant Garamond', serif; font-size: 1.65rem;
  font-style: italic; font-weight: 300; color: var(--gold-light);
  line-height: 1.45; margin-bottom: 2rem;
  border-left: 2px solid var(--crimson); padding-left: 1.4rem;
}
.about-text {
  color: var(--muted); font-size: 0.93rem; line-height: 1.85;
  font-weight: 300; margin-bottom: 1.4rem;
}

/* ── PORTRETT-STRIP ─────────────────────────────────── */
.portrait-strip { display: grid; grid-template-columns: repeat(3, 1fr); gap: 3px; height: 380px; }
.portrait-strip img {
  width: 100%; height: 100%; object-fit: cover; display: block;
  filter: grayscale(25%); transition: filter 0.4s;
}
.portrait-strip img:hover { filter: grayscale(0%); }
```

- [ ] **Steg 2: Legg til HTML**

Erstatt `<!-- CHAPTER 03: OM HELENA -->` og `<!-- PORTRETT-STRIP INTERLUDE -->` med:

```html
<section id="om">
  <div class="about">
    <div class="about-image reveal">
      <img src="https://images.unsplash.com/photo-1599508704512-2f19efd1e35f?w=800&q=80" alt="Helena bak kamera" loading="lazy" />
    </div>
    <div class="about-content">
      <div class="chapter-number reveal">03 — Om Helena</div>
      <h2 class="section-title reveal">En lidenskap<br>som startet tidlig</h2>
      <p class="about-quote reveal">"Film er den eneste kunsten som kan stoppe tiden."</p>
      <p class="about-text reveal">Helena vokste opp med kamera i hånden og historier i hodet. Det som begynte som korte videoer på mobil ble til en fullverdig utdannelse i filmregi.</p>
      <p class="about-text reveal">Med et skarpt blikk for det menneskelige og en unik evne til å skape ro i bildet, lager Helena filmer som setter seg fast lenge etter rulleteksten.</p>
    </div>
  </div>
  <div class="portrait-strip">
    <img src="https://images.unsplash.com/photo-1578662996442-48f60103fc96?w=700&q=80" alt="På settet" loading="lazy" />
    <img src="https://images.unsplash.com/photo-1492691527719-9d1e07e534b4?w=700&q=80" alt="Kamera" loading="lazy" />
    <img src="https://images.unsplash.com/photo-1524712245354-2c4e5e7121c0?w=700&q=80" alt="Klipping" loading="lazy" />
  </div>
</section>
```

- [ ] **Steg 3: Verifiser**

Scroll til Om-seksjonen. Forventet: split-layout bilde/tekst, crimson venstrekant på sitat, ghost `03` øverst til høyre. Portrett-strip under: grayscale → farge på hover.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: chapter 03 om helena med crimson sitat-kant og portrett-strip"
```

---

## Task 10: Galleri + Chapter 04 — CV

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- GALLERI INTERLUDE -->` og `<!-- CHAPTER 04: CV -->`

- [ ] **Steg 1: Legg til galleri- og CV-CSS**

```css
/* ── GALLERI INTERLUDE ──────────────────────────────── */
.gallery { padding: 7rem 4rem; background: var(--black); }
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-template-rows: 290px 290px;
  gap: 3px; margin-top: 4rem;
}
.g1 { grid-column: span 5; grid-row: span 2; }
.g2 { grid-column: span 7; }
.g3 { grid-column: span 3; }
.g4 { grid-column: span 2; }
.g5 { grid-column: span 2; }
.gallery-item { overflow: hidden; }
.gallery-item img {
  width: 100%; height: 100%; object-fit: cover; display: block;
  filter: grayscale(20%); transition: transform 0.5s, filter 0.4s;
}
.gallery-item:hover img { transform: scale(1.06); filter: grayscale(0%); }

/* ── CHAPTER 04: CV ─────────────────────────────────── */
.cv-section { padding: 7rem 4rem; background: var(--off-black); }
.cv-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 4rem; margin-top: 4rem; }
.cv-block h3 {
  font-family: 'Cormorant Garamond', serif; font-size: 1.5rem;
  font-weight: 300; color: var(--gold); margin-bottom: 2rem;
  padding-bottom: 0.6rem; border-bottom: 1px solid var(--crimson);
}
.cv-item { padding: 1.4rem 0; border-bottom: 1px solid var(--faint); }
.cv-item:last-child { border-bottom: none; }
.cv-meta { font-size: 0.68rem; letter-spacing: 2px; color: var(--crimson); text-transform: uppercase; margin-bottom: 0.25rem; }
.cv-title { font-size: 0.97rem; font-weight: 400; margin-bottom: 0.2rem; }
.cv-detail { font-size: 0.83rem; color: var(--muted); font-weight: 300; }
```

- [ ] **Steg 2: Legg til HTML**

Erstatt `<!-- GALLERI INTERLUDE -->` og `<!-- CHAPTER 04: CV -->` med:

```html
<section class="gallery" id="gallery">
  <p class="section-eyebrow reveal">Fra produksjon</p>
  <h2 class="section-title reveal">Bak kameraet</h2>
  <p class="section-sub reveal">Glimt fra settet — de øyeblikkene som sjelden havner på lerretet.</p>
  <div class="gallery-grid">
    <div class="gallery-item g1 reveal"><img src="images/StillBilder/IMG_7758.jpg" alt="Bak kameraet" loading="lazy" /></div>
    <div class="gallery-item g2 reveal"><img src="images/StillBilder/IMG_7760.jpg" alt="Bak kameraet" loading="lazy" /></div>
    <div class="gallery-item g3 reveal"><img src="images/StillBilder/IMG_7765.jpg" alt="Bak kameraet" loading="lazy" /></div>
    <div class="gallery-item g4 reveal"><img src="images/StillBilder/IMG_7767.jpg" alt="Bak kameraet" loading="lazy" /></div>
    <div class="gallery-item g5 reveal"><img src="images/StillBilder/IMG_7770.jpg" alt="Bak kameraet" loading="lazy" /></div>
  </div>
</section>

<section class="cv-section" id="cv">
  <div style="position:relative;overflow:hidden;">
    <div class="chapter-ghost" aria-hidden="true">04</div>
    <div class="chapter-header reveal">
      <div class="chapter-number">04 — CV</div>
      <p class="section-eyebrow">Curriculum Vitae</p>
      <h2 class="section-title">Erfaring og utdanning</h2>
    </div>
  </div>
  <div class="cv-grid">
    <div class="cv-block">
      <h3 class="reveal">Utdanning</h3>
      <div class="cv-item reveal">
        <div class="cv-meta">2020 — 2024</div>
        <div class="cv-title">Bachelor i filmregi</div>
        <div class="cv-detail">Norsk filmskole, Lillehammer</div>
      </div>
      <div class="cv-item reveal">
        <div class="cv-meta">2017 — 2020</div>
        <div class="cv-title">Medier og kommunikasjon</div>
        <div class="cv-detail">Videregående skole</div>
      </div>
      <div class="cv-item reveal">
        <div class="cv-meta">2023</div>
        <div class="cv-title">Workshopkurs i dokumentar</div>
        <div class="cv-detail">DOX:LAB, København</div>
      </div>
    </div>
    <div class="cv-block">
      <h3 class="reveal">Erfaring</h3>
      <div class="cv-item reveal">
        <div class="cv-meta">2023 — 2024</div>
        <div class="cv-title">Regi &amp; manus — «Mellom Fjordene»</div>
        <div class="cv-detail">Eget produksjonsprosjekt</div>
      </div>
      <div class="cv-item reveal">
        <div class="cv-meta">2022</div>
        <div class="cv-title">Regissørassistent</div>
        <div class="cv-detail">NRK Drama, Oslo</div>
      </div>
      <div class="cv-item reveal">
        <div class="cv-meta">2021</div>
        <div class="cv-title">Frilansfotograf &amp; klipper</div>
        <div class="cv-detail">Diverse reklameproduksjoner</div>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Steg 3: Verifiser**

Galleri: 5-bilde asymmetrisk grid med grayscale→farge hover.  
CV: To kolonner med gull overskrifter, crimson understrek på h3 og crimson meta-årstall.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: galleri-interlude og chapter 04 CV med crimson detaljer"
```

---

## Task 11: Kontakt + Footer

**Filer:**
- Modifiser: `index.html` — erstatt `<!-- KONTAKT -->` og `<!-- FOOTER -->`

- [ ] **Steg 1: Legg til kontakt- og footer-CSS**

```css
/* ── KONTAKT ────────────────────────────────────────── */
.contact { padding: 9rem 4rem; background: var(--black); text-align: center; position: relative; overflow: hidden; }
.contact::before {
  content: 'HELENA'; position: absolute; top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  font-family: 'Cormorant Garamond', serif; font-size: 18vw;
  font-weight: 300; color: rgba(255,255,255,0.018);
  white-space: nowrap; pointer-events: none;
}
.contact .section-title { margin: 0 auto 0.8rem; }
.contact .section-sub { margin: 0 auto 3rem; text-align: center; }
.contact-email {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(1.8rem, 4vw, 3rem); font-style: italic;
  color: var(--gold); text-decoration: none;
  border-bottom: 1px solid rgba(201,168,76,0.3);
  padding-bottom: 0.3rem; transition: color 0.2s;
  display: inline-block; margin-bottom: 3rem;
}
.contact-email:hover { color: var(--gold-light); }
.social-links { display: flex; gap: 2.5rem; justify-content: center; }
.social-link {
  font-size: 0.7rem; letter-spacing: 3px; text-transform: uppercase;
  color: rgba(255,255,255,0.35); text-decoration: none; transition: color 0.2s;
}
.social-link:hover { color: var(--crimson); }

/* ── FOOTER ─────────────────────────────────────────── */
footer {
  padding: 2rem 4rem;
  border-top: 1px solid var(--crimson);
  display: flex; justify-content: space-between; align-items: center;
  font-size: 0.76rem; color: rgba(255,255,255,0.22);
}
.footer-logo {
  font-family: 'Cormorant Garamond', serif; font-style: italic;
  color: rgba(255,255,255,0.38); font-size: 0.95rem;
}
```

- [ ] **Steg 2: Legg til HTML**

Erstatt `<!-- KONTAKT -->` og `<!-- FOOTER -->` med:

```html
<section class="contact">
  <p class="section-eyebrow reveal">Kontakt</p>
  <h2 class="section-title reveal">La oss lage noe sammen</h2>
  <p class="section-sub reveal" style="max-width:400px;">For samarbeid, spørsmål om prosjekter eller bare en god prat om film.</p>
  <a href="mailto:hmohncke@gmail.com" class="contact-email reveal">hmohncke@gmail.com</a>
  <div class="social-links reveal">
    <a href="#" class="social-link">Instagram</a>
    <a href="#" class="social-link">Vimeo</a>
    <a href="#" class="social-link">LinkedIn</a>
  </div>
</section>

<footer>
  <div class="footer-logo">Helena Møhncke — Filmregissør</div>
  <div>© 2026 · Alle rettigheter forbeholdt</div>
</footer>
```

- [ ] **Steg 3: Verifiser**

Bunn av siden: ghost-tekst `HELENA` bak kontaktinfo. Footer med crimson toppkant. Social-lenker bytter til crimson på hover.

- [ ] **Steg 4: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: kontakt-seksjon og footer med crimson detaljer"
```

---

## Task 12: Mobil responsivitet

**Filer:**
- Modifiser: `index.html` — legg til `@media`-blokk i `<style>`

- [ ] **Steg 1: Legg til alle media queries etter eksisterende CSS**

```css
/* ── MOBIL (≤768px) ─────────────────────────────────── */
@media (max-width: 768px) {
  /* Nav */
  nav { padding: 1rem 1.5rem; }
  nav ul { display: none; }
  .hamburger { display: flex; }

  /* Hero */
  .hero { padding: 5rem 1.5rem 4rem; }
  .hero h1 { font-size: 3rem; }
  .hero-ghost { display: none; }

  /* Chapter generelt */
  .chapter { padding: 4rem 1.5rem; }
  .chapter-ghost { display: none; }

  /* Filmer */
  .films-grid { grid-template-columns: 1fr; }
  .films-right { grid-template-columns: 1fr; grid-template-rows: unset; }
  .film-card.large { height: 280px; }
  .film-card.small { height: 200px; }

  /* Tidslinje */
  .timeline { padding: 4rem 1.5rem; }
  .timeline-grid { grid-template-columns: 1fr; }

  /* Produksjon */
  .video-grid { grid-template-columns: 1fr; }

  /* Om Helena */
  .about { grid-template-columns: 1fr; }
  .about-image { min-height: 300px; }
  .about-image::after { background: linear-gradient(to top, var(--off-black) 10%, transparent); }
  .about-content { padding: 3rem 1.5rem; }
  .about-content::before { display: none; }

  /* Portrett-strip */
  .portrait-strip { grid-template-columns: 1fr; height: auto; }
  .portrait-strip img { height: 220px; }

  /* Galleri */
  .gallery { padding: 4rem 1.5rem; }
  .gallery-grid {
    grid-template-columns: 1fr 1fr;
    grid-template-rows: auto;
  }
  .g1 { grid-column: span 2; grid-row: span 1; height: 220px; }
  .g2, .g3, .g4, .g5 { grid-column: span 1; height: 150px; }

  /* CV */
  .cv-section { padding: 4rem 1.5rem; }
  .cv-grid { grid-template-columns: 1fr; gap: 2.5rem; }

  /* Kontakt */
  .contact { padding: 5rem 1.5rem; }

  /* Footer */
  footer { padding: 2rem 1.5rem; flex-direction: column; gap: 1rem; text-align: center; }
}
```

- [ ] **Steg 2: Verifiser på mobil**

Åpne DevTools → Responsive Mode → velg iPhone SE (375×667) og iPhone 14 Pro (393×852).  
Sjekk fra topp til bunn:
- Nav: hamburger vises, meny fungerer
- Hero: tekst lesbar, ghost-tall skjult
- Filmer: én kolonne, kort fulle bredde
- Alle chapter-ghost-tall skjult
- Tidslinje: én kolonne
- Produksjon: én kolonne
- Om Helena: bilde øverst, tekst under
- Galleri: 2-kolonne grid
- CV: én kolonne
- Footer: sentrert, stablet

- [ ] **Steg 3: Commit**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena add index.html
git -C /home/ricky/Dokumenter/Koding/Helena commit -m "feat: full mobilresponsivitet for alle seksjoner"
```

---

## Task 13: Final gjennomgang + push

**Filer:**
- Leser: `index.html` (ingen endringer — bare verifikasjon)

- [ ] **Steg 1: Desktop-gjennomgang**

Åpne `index.html` i nettleser (fullskjerm, 1440px+). Scroll sakte fra topp til bunn og sjekk:
- [ ] Film grain synlig på mørke flater
- [ ] Progressbar vokser korrekt (gradient crimson → gull)
- [ ] Alle `.reveal`-elementer animerer inn ved scroll
- [ ] Crimson-detaljer konsistente: hero-stripe, chapter-numre, film-hover, sitat-kant, CV-h3, footer-kant, social-hover
- [ ] Ghost chapter-tall synlige men subtile
- [ ] Video avspilles ved klikk i produksjon-seksjonen

- [ ] **Steg 2: Mobil-gjennomgang**

DevTools → iPhone 14 Pro (393px). Scroll fra topp til bunn:
- [ ] Ingen horisontal overflow
- [ ] Hamburger-meny åpner og lukker
- [ ] Alle bilder fyller kolonne korrekt
- [ ] Tekst lesbar (ingen avskjæring)

- [ ] **Steg 3: Push til GitHub**

```bash
git -C /home/ricky/Dokumenter/Koding/Helena push
```

---

## Kjente avgrensninger

- `contact.html` er ikke oppdatert i denne planen — tas som eget steg etterpå
- Unsplash-bilder er fortsatt i bruk i filmografi og om-seksjon — erstattes når Helena leverer egne bilder
- Horisontal filmografi-scroll (effekt C fra brainstorming) er ikke inkludert — kan legges til som en fremtidig forbedring
