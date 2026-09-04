# Portfolio Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the full GitHub Pages portfolio for Blessing Odeleye — one
homepage (`index.html`) with anchor sections, plus six standalone project
pages — in the visual system already validated through hero prototyping.

**Architecture:** Static HTML + CSS + JS, no build step, no framework.
`styles.css` and `script.js` are shared across every page; nav/footer markup
is duplicated per page (7 pages total — not worth a templating layer). Each
project page follows the same structural template with different content.

**Tech Stack:** Plain HTML5, CSS (custom properties, Grid/Flexbox), vanilla
JS (nav toggle + `IntersectionObserver` scroll reveal). Google Fonts (Space
Grotesk, Inter). No dependencies, no package manager.

**Spec:** `docs/superpowers/specs/2026-09-04-portfolio-design.md`

## Global Constraints

- Color tokens (exact hex, from the spec): `--bg:#050817` `--surface:#0a1024`
  `--line:#1a2444` `--cyan:#57d8ff` `--blue:#4a7dff` `--violet:#9d62ff`
  `--text:#f7f9ff` `--muted:#9aa7c2` `--muted-dim:#6b7794`.
- Single dark-committed theme — no light-mode variant, no `prefers-color-scheme`
  handling needed.
- Typography: Space Grotesk (headings/labels/nav/stats) + Inter (body), both
  via Google Fonts `<link>` tags — never inlined, never a different host.
- No paper figures/tables reproduced verbatim; diagrams are original SVG.
  GPU-malware techniques described narratively, never as runnable code.
- Every factual claim traces to: the four CVs, the SOUPS'21 paper, the
  Computers & Security paper, or the PhD thesis abstract/contents/
  contributions (all already read this session — see spec §5). Do not
  invent statistics, dates, or results.
- Respect `prefers-reduced-motion`: wrap all animation in
  `@media (prefers-reduced-motion: no-preference)`.
- No contact form, no backend, no custom domain (spec §8, out of scope).

---

### Task 1: Shared foundation — `styles.css`, `script.js`, page shell

**Files:**
- Create: `styles.css`
- Create: `script.js`
- Create: `index.html` (nav + empty `<main>` + footer only — content sections
  added in later tasks)

**Interfaces:**
- Produces: CSS custom properties listed in Global Constraints (available to
  every later task and every project page). Class names `nav.topbar`,
  `.mark`, `.navlinks`, `.page`, `.btn`/`.btn-primary`/`.btn-secondary`,
  `.eyebrow`, footer classes defined below — later tasks and project pages
  reuse these exactly, never redefine them.
- Produces: `script.js` exports no module (plain script), but defines a
  `data-nav-toggle` / `data-nav-menu` attribute contract for the mobile menu
  button, and a `.reveal` class contract for scroll-reveal (element starts
  visible via plain CSS, JS only adds a `.revealed` class — see Step 4).

- [ ] **Step 1: Create `styles.css` with tokens, reset, nav, and buttons**

Port verbatim from the approved hero prototype (these rules are already
validated — do not redesign them): the `:root` token block, the `*`/`html,body`/
`body` reset, `.page`, `nav.topbar` and its children (`.mark`, `.mark .dot`,
`.navlinks`, `.navlinks a`, `.navlinks .status`), and `.btn`/`.btn-primary`/
`.btn-secondary` (including hover/focus-visible states). Exact source:

```css
:root{
  --bg:#050817;
  --surface:#0a1024;
  --surface-2:#0d1530;
  --line:#1a2444;
  --cyan:#57d8ff;
  --blue:#4a7dff;
  --violet:#9d62ff;
  --text:#f7f9ff;
  --muted:#9aa7c2;
  --muted-dim:#6b7794;
}

*{box-sizing:border-box;}
html,body{margin:0;padding:0;}
body{
  background:var(--bg);
  color:var(--text);
  font-family:'Inter',system-ui,-apple-system,sans-serif;
  -webkit-font-smoothing:antialiased;
}

.page{
  max-width:1280px;
  margin:0 auto;
  padding:0 clamp(20px,4vw,56px);
}

/* ---------- nav ---------- */
nav.topbar{
  display:flex;
  align-items:center;
  justify-content:space-between;
  padding:26px clamp(20px,4vw,56px);
  border-bottom:1px solid var(--line);
  max-width:1280px;
  margin:0 auto;
}
.mark{
  display:flex;
  align-items:center;
  gap:10px;
  font-family:'Space Grotesk',sans-serif;
  font-weight:600;
  font-size:15px;
  letter-spacing:.02em;
  color:inherit;
  text-decoration:none;
}
.mark .dot{
  width:9px;height:9px;border-radius:2px;
  background:var(--cyan);
  transform:rotate(45deg);
  flex:none;
}
.navlinks{
  display:flex;
  align-items:center;
  gap:clamp(18px,3vw,36px);
  font-size:13.5px;
  color:var(--muted);
}
.navlinks a{color:inherit;text-decoration:none;}
.navlinks a:hover, .navlinks a:focus-visible{color:var(--text);}
.navlinks a:focus-visible{outline:1px solid var(--cyan);outline-offset:4px;}
.navlinks .status{
  display:flex;align-items:center;gap:7px;
  color:var(--muted-dim);
  font-family:'Space Grotesk',sans-serif;
  letter-spacing:.08em;
  font-size:11px;
  text-transform:uppercase;
}
.navlinks .status i{
  width:6px;height:6px;border-radius:50%;
  background:var(--cyan);
  box-shadow:0 0 0 3px rgba(87,216,255,.15);
}
.nav-toggle{
  display:none;
  background:none;
  border:1px solid var(--line);
  border-radius:6px;
  width:38px;height:38px;
  color:var(--text);
  cursor:pointer;
}
.nav-toggle:focus-visible{outline:2px solid var(--cyan);outline-offset:2px;}
@media (max-width:760px){
  .navlinks{
    display:none;
    position:absolute;
    top:100%; left:0; right:0;
    flex-direction:column;
    align-items:flex-start;
    gap:18px;
    background:var(--surface);
    border-bottom:1px solid var(--line);
    padding:20px clamp(20px,4vw,56px) 26px;
  }
  .navlinks.open{display:flex;}
  .nav-toggle{display:block;}
  nav.topbar{position:relative;}
}

/* ---------- buttons ---------- */
.ctas{display:flex; gap:14px; flex-wrap:wrap;}
.btn{
  font-family:'Space Grotesk',sans-serif;
  font-size:14px;
  font-weight:600;
  letter-spacing:.01em;
  padding:13px 22px;
  border-radius:6px;
  text-decoration:none;
  display:inline-flex;
  align-items:center;
  gap:8px;
  transition:transform .15s ease, background .15s ease, border-color .15s ease;
}
.btn:focus-visible{outline:2px solid var(--cyan);outline-offset:2px;}
.btn-primary{background:var(--cyan); color:#04121c;}
.btn-primary:hover{background:#7ee3ff; transform:translateY(-1px);}
.btn-secondary{background:transparent; color:var(--text); border:1px solid var(--line);}
.btn-secondary:hover{border-color:var(--muted); transform:translateY(-1px);}
.btn svg{width:14px;height:14px;flex:none;}

/* ---------- footer ---------- */
footer.site-footer{
  border-top:1px solid var(--line);
  padding:32px clamp(20px,4vw,56px);
  display:flex;
  flex-wrap:wrap;
  gap:16px;
  align-items:center;
  justify-content:space-between;
  max-width:1280px;
  margin:0 auto;
  font-size:13px;
  color:var(--muted-dim);
}
footer.site-footer a{color:var(--muted); text-decoration:none;}
footer.site-footer a:hover, footer.site-footer a:focus-visible{color:var(--text);}
.footer-links{display:flex; gap:20px;}

/* ---------- section scaffolding ---------- */
section{padding:clamp(56px,8vw,96px) 0;}
section + section{border-top:1px solid var(--line);}
.eyebrow{
  font-family:'Space Grotesk',sans-serif;
  font-size:13.5px;
  font-weight:500;
  letter-spacing:.06em;
  color:var(--cyan);
  text-transform:uppercase;
  margin:0 0 14px;
}
h2.section-title{
  font-family:'Space Grotesk',sans-serif;
  font-weight:600;
  font-size:clamp(28px,3.2vw,40px);
  line-height:1.2;
  letter-spacing:-0.01em;
  margin:0 0 20px;
  max-width:20ch;
}
p.section-lede{
  font-size:16px;
  line-height:1.7;
  color:var(--muted);
  max-width:60ch;
  margin:0 0 40px;
}

/* ---------- reveal-on-scroll ---------- */
@media (prefers-reduced-motion:no-preference){
  .reveal{opacity:0; transform:translateY(16px); transition:opacity .5s ease, transform .5s ease;}
  .reveal.revealed{opacity:1; transform:none;}
}
```

Append this to `styles.css`. Do not add hero/evidence/mark rules yet — Task 2
adds those.

- [ ] **Step 2: Create `script.js` with nav toggle and scroll reveal**

```javascript
document.addEventListener('DOMContentLoaded', () => {
  const toggle = document.querySelector('[data-nav-toggle]');
  const menu = document.querySelector('[data-nav-menu]');
  if (toggle && menu) {
    toggle.addEventListener('click', () => {
      const isOpen = menu.classList.toggle('open');
      toggle.setAttribute('aria-expanded', String(isOpen));
    });
    menu.querySelectorAll('a').forEach((link) => {
      link.addEventListener('click', () => {
        menu.classList.remove('open');
        toggle.setAttribute('aria-expanded', 'false');
      });
    });
  }

  const revealTargets = document.querySelectorAll('.reveal');
  if ('IntersectionObserver' in window && revealTargets.length) {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('revealed');
          observer.unobserve(entry.target);
        }
      });
    }, { threshold: 0.15 });
    revealTargets.forEach((el) => observer.observe(el));
  } else {
    revealTargets.forEach((el) => el.classList.add('revealed'));
  }
});
```

- [ ] **Step 3: Create `index.html` with nav, empty main, and footer**

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Blessing Odeleye, PhD — Applied Security Researcher & XR Systems Engineer</title>
<meta name="description" content="I build immersive systems, study how they fail, and engineer ways to make them safer.">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="styles.css">
</head>
<body>

<nav class="topbar">
  <a class="mark" href="#top"><span class="dot"></span>BLESSING ODELEYE</a>
  <button class="nav-toggle" data-nav-toggle aria-expanded="false" aria-controls="primary-nav" aria-label="Toggle navigation">☰</button>
  <div class="navlinks" id="primary-nav" data-nav-menu>
    <a href="#work">Work</a>
    <a href="#research">Research</a>
    <a href="#about">About</a>
    <a href="#contact">Contact</a>
    <span class="status"><i></i>OPEN TO ROLES</span>
  </div>
</nav>

<main id="top">
</main>

<footer class="site-footer">
  <span>&copy; 2026 Blessing Odeleye</span>
  <div class="footer-links">
    <a href="https://github.com/BleOdel" target="_blank" rel="noopener">GitHub</a>
    <a href="https://www.linkedin.com/in/blessingodeleye" target="_blank" rel="noopener">LinkedIn</a>
    <a href="mailto:blessingodeleye@gmail.com">Email</a>
  </div>
</footer>

<script src="script.js"></script>
</body>
</html>
```

- [ ] **Step 4: Verify in browser**

Open `index.html` directly in a browser (or via a static server). Confirm:
nav renders with the correct 4 links + status pill, resizing below 760px
hides the links and shows a toggle button that opens/closes the menu on
click, and the footer shows GitHub/LinkedIn/Email links. No console errors.

- [ ] **Step 5: Commit**

```bash
cd ~/Dev-Projects/BleOdel.github.io
git add index.html styles.css script.js
git commit -m "Add shared site shell: nav, footer, tokens, scroll reveal"
```

---

### Task 2: Homepage hero + evidence strip

**Files:**
- Create: `assets/images/hero-headset.png` (already exists — verify present,
  do not regenerate)
- Modify: `styles.css` (append hero + evidence rules)
- Modify: `index.html` (add hero + evidence markup inside `<main>`)

**Interfaces:**
- Consumes: `.btn`/`.btn-primary`/`.btn-secondary`, `.eyebrow` base styles
  from Task 1 (hero overrides `.eyebrow` color locally via a more specific
  selector — see CSS below).
- Produces: `.hero`, `.hero-copy`, `.headline-row`, `h1.statement`,
  `.hero-mark`, `.evidence` — reused nowhere else, but establishes the
  pattern project-page heroes follow in Task 5.

- [ ] **Step 1: Verify the hero image asset exists**

```bash
ls -la ~/Dev-Projects/BleOdel.github.io/assets/images/hero-headset.png
```

Expected: file exists, ~525KB. (It was copied from the approved hero
prototype in an earlier session — do not regenerate or re-crop it.)

- [ ] **Step 2: Append hero + evidence CSS to `styles.css`**

Port verbatim from the approved hero prototype (this exact CSS was visually
validated across multiple review rounds — do not modify values):

```css
/* ---------- hero ---------- */
.hero{
  padding:clamp(40px,7vw,88px) 0 clamp(40px,6vw,72px);
  position:relative;
}
.hero .eyebrow{
  font-size:13.5px;
  font-weight:500;
  letter-spacing:.06em;
  color:var(--muted);
  text-transform:none;
  margin:0 0 22px;
  display:flex;
  align-items:baseline;
  gap:10px;
  flex-wrap:wrap;
}
.hero .eyebrow .name{color:var(--text);font-weight:600;}
.hero .eyebrow .sep{color:var(--muted-dim);}

.headline-row{
  display:flex;
  align-items:flex-end;
  justify-content:space-between;
  gap:clamp(2px,0.6vw,8px);
  margin-bottom:26px;
}
@media (max-width:900px){
  .headline-row{flex-wrap:wrap-reverse;}
  .hero-mark{margin:0 0 28px; max-width:540px; width:100%;}
}

h1.statement{
  font-family:'Space Grotesk',sans-serif;
  font-weight:600;
  font-size:clamp(32px,3.6vw,50px);
  line-height:1.14;
  letter-spacing:-0.01em;
  margin:0;
  max-width:13ch;
  text-wrap:balance;
  flex:1 1 auto;
  min-width:0;
}
h1.statement em{font-style:normal; color:var(--cyan);}

p.lede{
  font-size:16.5px;
  line-height:1.65;
  color:var(--muted);
  max-width:52ch;
  margin:0 0 36px;
}

.hero .ctas{margin-bottom:38px;}

.utility{display:flex; gap:22px; font-size:13px; color:var(--muted-dim);}
.utility a{color:var(--muted); text-decoration:none; border-bottom:1px solid transparent;}
.utility a:hover, .utility a:focus-visible{color:var(--text); border-bottom-color:var(--cyan);}

.hero-mark{width:clamp(360px,45vw,630px); flex:none;}
.hero-mark img{width:100%;height:auto;display:block;}

@media (prefers-reduced-motion:no-preference){
  .pulse-ring{animation:pulsering 3s ease-out infinite;}
  .pulse-dot{animation:pulsedot 3s ease-in-out infinite;}
  @keyframes pulsering{0%{r:3; opacity:.6;} 100%{r:11; opacity:0;}}
  @keyframes pulsedot{0%,100%{opacity:.7;} 50%{opacity:1;}}
}

/* ---------- evidence strip ---------- */
.evidence{
  border-top:1px solid var(--line);
  display:grid;
  grid-template-columns:repeat(5,1fr);
}
@media (max-width:760px){
  .evidence{grid-template-columns:repeat(2,1fr);}
  .evidence li:nth-child(5){grid-column:1 / -1;}
}
.evidence li{list-style:none; padding:28px clamp(14px,2vw,26px) 32px; border-right:1px solid var(--line);}
.evidence li:last-child{border-right:none;}
@media (max-width:760px){
  .evidence li:nth-child(2n){border-right:none;}
  .evidence li{border-bottom:1px solid var(--line);}
}
.evidence .num{
  font-family:'Space Grotesk',sans-serif;
  font-weight:700;
  font-size:clamp(22px,2.6vw,30px);
  font-variant-numeric:tabular-nums;
  color:var(--text);
  display:block;
  margin-bottom:8px;
}
.evidence .num .accent{color:var(--cyan);}
.evidence .label{font-size:12.5px; color:var(--muted); line-height:1.4;}
```

Note: `section + section{border-top:1px solid var(--line);}` from Task 1
would double up with `.evidence{border-top:...}` — the hero and evidence
strip live inside one `<section id="top-hero">` wrapper (not two adjacent
`<section>` elements), so that rule doesn't apply between them. See markup
below.

- [ ] **Step 3: Add hero + evidence markup inside `<main>` in `index.html`**

Replace the empty `<main id="top"></main>` with:

```html
<main id="top">
<section class="hero-section">
  <div class="page">
    <div class="hero">
      <div class="hero-copy">
        <p class="eyebrow"><span class="name">BLESSING ODELEYE, PhD</span><span class="sep">&middot;</span>Applied Security Researcher &amp; XR Systems Engineer</p>
        <div class="headline-row">
          <h1 class="statement">I build immersive systems, study how they <em>fail</em>, and engineer ways to make them safer.</h1>
          <div class="hero-mark" aria-hidden="true">
            <img src="assets/images/hero-headset.png" alt="" width="1040" height="642">
          </div>
        </div>
        <p class="lede">My work combines cybersecurity research, XR engineering, telemetry-driven detection and human-centred experimentation to understand and defend emerging immersive systems.</p>
        <div class="ctas">
          <a class="btn btn-primary" href="#work">Explore My Work
            <svg viewBox="0 0 16 16" fill="none" aria-hidden="true"><path d="M3 8h10M9 4l4 4-4 4" stroke="#04121c" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/></svg>
          </a>
          <a class="btn btn-secondary" href="#research">View Research</a>
        </div>
        <div class="utility">
          <a href="https://github.com/BleOdel" target="_blank" rel="noopener">GitHub</a>
          <a href="https://www.linkedin.com/in/blessingodeleye" target="_blank" rel="noopener">LinkedIn</a>
          <a href="#about">Publications</a>
        </div>
      </div>
    </div>
  </div>
  <ul class="evidence page">
    <li><span class="num"><span class="accent">580K+</span></span><span class="label">VR telemetry windows analysed</span></li>
    <li><span class="num">20+&nbsp;&rarr;&nbsp;5</span><span class="label">Engineered features via PCA</span></li>
    <li><span class="num">&asymp;99%</span><span class="label">F1 score, Isolation Forest IDS</span></li>
    <li><span class="num">0&ndash;2 sec</span><span class="label">Detection latency</span></li>
    <li><span class="num">48</span><span class="label">Study participants (VRSQ)</span></li>
  </ul>
</section>
</main>
```

Note the `img` has real `width`/`height` attributes (1040×642, the actual
asset dimensions) to prevent layout shift — confirm these match the file
(`sips -g pixelWidth -g pixelHeight assets/images/hero-headset.png` if unsure).

- [ ] **Step 4: Verify in browser**

Confirm: hero renders identically to the approved artifact (headline, image
positioned tight beside it, CTAs, utility links), the evidence strip shows
5 stats in one row on desktop and 2 columns (last one spanning full width)
below 760px, and there's no visible layout jump on load.

- [ ] **Step 5: Commit**

```bash
git add index.html styles.css
git commit -m "Add homepage hero and evidence strip"
```

---

### Task 3: Narrative cycle + Featured work sections

**Files:**
- Modify: `styles.css` (append `.cycle` and `.work-grid`/`.work-card` rules)
- Modify: `index.html` (add two `<section>` elements after the hero)

**Interfaces:**
- Consumes: `section`, `h2.section-title`, `p.section-lede`, `.eyebrow`
  (uppercase variant) from Task 1.
- Produces: `#work` anchor id (linked from nav and from the hero's
  "Explore My Work" CTA) — the target section for Task 1's nav link.

- [ ] **Step 1: Append narrative-cycle and work-card CSS**

```css
/* ---------- narrative cycle ---------- */
.cycle{
  display:grid;
  grid-template-columns:repeat(5,1fr);
  gap:1px;
  background:var(--line);
  border:1px solid var(--line);
  border-radius:8px;
  overflow:hidden;
}
@media (max-width:700px){ .cycle{grid-template-columns:repeat(2,1fr);} }
.cycle-step{background:var(--surface); padding:22px 18px; text-align:center;}
.cycle-step .step-name{
  font-family:'Space Grotesk',sans-serif;
  font-weight:700;
  font-size:15px;
  letter-spacing:.04em;
  color:var(--cyan);
  text-transform:uppercase;
  margin:0 0 6px;
}
.cycle-step .step-sub{font-size:12.5px; color:var(--muted);}

/* ---------- featured work ---------- */
.work-grid{
  display:grid;
  grid-template-columns:repeat(3,1fr);
  gap:20px;
}
@media (max-width:900px){ .work-grid{grid-template-columns:repeat(2,1fr);} }
@media (max-width:620px){ .work-grid{grid-template-columns:1fr;} }
.work-card{
  display:block;
  background:var(--surface);
  border:1px solid var(--line);
  border-radius:10px;
  padding:24px;
  text-decoration:none;
  color:inherit;
  transition:transform .15s ease, border-color .15s ease;
}
.work-card:hover, .work-card:focus-visible{
  border-color:var(--cyan);
  transform:translateY(-3px);
}
.work-card:focus-visible{outline:2px solid var(--cyan); outline-offset:2px;}
.work-card .kicker{
  font-family:'Space Grotesk',sans-serif;
  font-size:11px;
  font-weight:600;
  letter-spacing:.08em;
  text-transform:uppercase;
  color:var(--muted-dim);
  margin:0 0 10px;
}
.work-card h3{
  font-family:'Space Grotesk',sans-serif;
  font-size:18px;
  font-weight:600;
  margin:0 0 10px;
  line-height:1.3;
}
.work-card p{font-size:14px; line-height:1.6; color:var(--muted); margin:0;}
```

- [ ] **Step 2: Add the narrative-cycle section markup**

Insert directly after the `</section>` that closes `hero-section`:

```html
<section id="cycle">
  <div class="page">
    <p class="eyebrow">How the work moves</p>
    <h2 class="section-title">A repeatable research-and-engineering cycle</h2>
    <p class="section-lede">Every project on this site moves through the same five stages: understand the problem, build the environment to study it, reproduce the failure under controlled conditions, capture the evidence, and turn that evidence into a defence.</p>
    <div class="cycle reveal">
      <div class="cycle-step"><div class="step-name">Research</div><div class="step-sub">Understand</div></div>
      <div class="cycle-step"><div class="step-name">Build</div><div class="step-sub">Create</div></div>
      <div class="cycle-step"><div class="step-name">Break</div><div class="step-sub">Test</div></div>
      <div class="cycle-step"><div class="step-name">Measure</div><div class="step-sub">Observe</div></div>
      <div class="cycle-step"><div class="step-name">Secure</div><div class="step-sub">Defend</div></div>
    </div>
  </div>
</section>
```

- [ ] **Step 3: Add the featured-work section markup**

```html
<section id="work">
  <div class="page">
    <p class="eyebrow">Featured work</p>
    <h2 class="section-title">Six projects, one throughline</h2>
    <p class="section-lede">Research depth, practical engineering, and security thinking — from a doctoral taxonomy of VR threats to a production mixed-reality platform.</p>
    <div class="work-grid">
      <a class="work-card reveal" href="projects/taxonomy.html">
        <p class="kicker">Research &middot; Computers &amp; Security</p>
        <h3>VR Cybersecurity Taxonomy</h3>
        <p>How should researchers systematically reason about security and privacy threats in immersive environments?</p>
      </a>
      <a class="work-card reveal" href="projects/vr-security.html">
        <p class="kicker">PhD research</p>
        <h3>Detecting Cybersecurity Threats in VR</h3>
        <p>Can a cyberattack against VR be detected from the behaviour of the system itself?</p>
      </a>
      <a class="work-card reveal" href="projects/intrusion-detection.html">
        <p class="kicker">Machine learning &middot; IDS</p>
        <h3>Telemetry-Driven XR Intrusion Detection</h3>
        <p>Can runtime telemetry reveal attacks that conventional monitoring may miss?</p>
      </a>
      <a class="work-card reveal" href="projects/human-impact.html">
        <p class="kicker">Human factors &middot; SOUPS'21</p>
        <h3>Cyber-Physical Impact of VR Attacks</h3>
        <p>What happens when a cyberattack affects both a machine and the person inside the experience?</p>
      </a>
      <a class="work-card reveal" href="projects/sun.html">
        <p class="kicker">Horizon Europe &middot; SUN</p>
        <h3>Cyber Security for Human-Centred XR</h3>
        <p>How can immersive security research support healthcare, accessibility and industrial scenarios?</p>
      </a>
      <a class="work-card reveal" href="projects/gener8xr.html">
        <p class="kicker">Innovate UK KTP &middot; Gener8XR</p>
        <h3>Applied Mixed-Reality Platform Engineering</h3>
        <p>How do you turn experimental immersive technology into a reusable, maintainable platform?</p>
      </a>
    </div>
  </div>
</section>
```

(Links to `projects/*.html` will 404 until Tasks 6–11 create those files —
expected at this point in the build.)

- [ ] **Step 4: Verify in browser**

Confirm: the 5-step cycle strip renders as one row on desktop and 2 columns
on narrow screens; the 6 work cards render 3-up on desktop, 2-up on tablet,
1-up on mobile, each with a hover lift and visible focus ring when tabbed to.

- [ ] **Step 5: Commit**

```bash
git add index.html styles.css
git commit -m "Add narrative cycle and featured work sections"
```

---

### Task 4: Research philosophy & experience section

**Files:**
- Modify: `styles.css` (append `.philosophy-grid`, `.timeline` rules)
- Modify: `index.html` (add `<section id="research">`)

**Interfaces:**
- Consumes: `section`, `h2.section-title`, `.eyebrow` from Task 1.
- Produces: `#research` anchor id (linked from nav and hero's "View Research"
  CTA).

Content sourced from the brief's "Research Philosophy & Experience" page and
the CVs' "Professional Experience" sections (spec §5/§6).

- [ ] **Step 1: Append CSS**

```css
.philosophy-grid{
  display:grid;
  grid-template-columns:repeat(4,1fr);
  gap:20px;
  margin-bottom:56px;
}
@media (max-width:900px){ .philosophy-grid{grid-template-columns:repeat(2,1fr);} }
@media (max-width:560px){ .philosophy-grid{grid-template-columns:1fr;} }
.philosophy-item .num{
  font-family:'Space Grotesk',sans-serif;
  font-weight:700;
  font-size:13px;
  color:var(--cyan);
  margin:0 0 8px;
}
.philosophy-item h3{font-family:'Space Grotesk',sans-serif; font-size:16px; margin:0 0 8px;}
.philosophy-item p{font-size:14px; color:var(--muted); line-height:1.6; margin:0;}

.timeline{border-left:1px solid var(--line); padding-left:24px; display:flex; flex-direction:column; gap:28px;}
.timeline-item .when{
  font-family:'Space Grotesk',sans-serif;
  font-size:12.5px;
  letter-spacing:.04em;
  color:var(--muted-dim);
  text-transform:uppercase;
  margin:0 0 6px;
}
.timeline-item h3{font-family:'Space Grotesk',sans-serif; font-size:17px; margin:0 0 6px;}
.timeline-item p{font-size:14px; color:var(--muted); line-height:1.6; margin:0;}
```

- [ ] **Step 2: Add markup**

```html
<section id="research">
  <div class="page">
    <p class="eyebrow">Research philosophy</p>
    <h2 class="section-title">Build. Measure. Understand. Defend.</h2>
    <p class="section-lede">Four steps, applied to every system I study — from a doctoral VR testbed to a production mixed-reality platform.</p>
    <div class="philosophy-grid">
      <div class="philosophy-item reveal">
        <p class="num">01</p>
        <h3>Understand the system</h3>
        <p>Identify assumptions, dependencies and attack surfaces.</p>
      </div>
      <div class="philosophy-item reveal">
        <p class="num">02</p>
        <h3>Reproduce the threat</h3>
        <p>Build controlled experimental environments for meaningful failure modes.</p>
      </div>
      <div class="philosophy-item reveal">
        <p class="num">03</p>
        <h3>Instrument the environment</h3>
        <p>Collect system and user signals that reveal behavioural change.</p>
      </div>
      <div class="philosophy-item reveal">
        <p class="num">04</p>
        <h3>Engineer the defence</h3>
        <p>Translate evidence into detection mechanisms, warning systems or architectural improvements.</p>
      </div>
    </div>

    <p class="eyebrow">Experience</p>
    <div class="timeline">
      <div class="timeline-item reveal">
        <p class="when">2024 &ndash; Present</p>
        <h3>XR Solutions Developer / KTP Associate &middot; Gener8 Spaces Ltd &amp; Edge Hill University</h3>
        <p>Lead researcher-engineer on a 32-month Innovate UK Knowledge Transfer Partnership, delivering the Gener8XR mixed-reality platform.</p>
      </div>
      <div class="timeline-item reveal">
        <p class="when">2023 &ndash; 2024</p>
        <h3>Research Fellow, Cyber Security of XR &middot; University of Greenwich</h3>
        <p>Horizon Europe SUN project — real-time ML-based threat detection for XR, deployed across healthcare and manufacturing demonstrators.</p>
      </div>
      <div class="timeline-item reveal">
        <p class="when">2019 &ndash; 2025</p>
        <h3>PhD Researcher, VR/XR Security &amp; Intrusion Detection &middot; University of Greenwich</h3>
        <p>Doctoral research combining controlled attack simulation, telemetry engineering and unsupervised anomaly detection.</p>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Step 3: Verify in browser**

Confirm: 4-column philosophy grid collapses to 2 then 1 column at the
breakpoints; timeline renders as a left-bordered vertical list in reverse-
chronological order (2024 → 2019).

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "Add research philosophy and experience timeline section"
```

---

### Task 5: About & publications, and Contact sections

**Files:**
- Modify: `styles.css` (append `.about-grid`, `.pub-list`, `.contact-block`
  rules)
- Modify: `index.html` (add `<section id="about">` and `<section id="contact">`)

**Interfaces:**
- Consumes: `section`, `h2.section-title`, `.eyebrow`, `.btn-primary` from
  Task 1.
- Produces: `#about` and `#contact` anchor ids (both linked from nav; `#about`
  also linked from the hero's "Publications" utility link).

Content sourced from the brief's "About heading"/"Selected publications"/
"Contact heading" copy and the CVs' Education/Awards sections (spec §5/§6).

- [ ] **Step 1: Append CSS**

```css
.about-grid{display:grid; grid-template-columns:1.3fr 1fr; gap:48px;}
@media (max-width:800px){ .about-grid{grid-template-columns:1fr;} }
.about-copy p{font-size:15.5px; line-height:1.75; color:var(--muted); margin:0 0 18px;}
.about-side h3{
  font-family:'Space Grotesk',sans-serif;
  font-size:13px;
  letter-spacing:.06em;
  text-transform:uppercase;
  color:var(--muted-dim);
  margin:0 0 14px;
}
.pub-list{list-style:none; margin:0 0 32px; padding:0; display:flex; flex-direction:column; gap:14px;}
.pub-list li{font-size:14px; line-height:1.6; color:var(--muted); border-left:2px solid var(--line); padding-left:14px;}
.pub-list li span{display:block; color:var(--text); margin-bottom:2px;}
.award-list{list-style:none; margin:0; padding:0; display:flex; flex-direction:column; gap:14px;}
.award-list li{font-size:14px; line-height:1.6; color:var(--muted);}
.award-list li span{display:block; color:var(--text); font-weight:600;}

.contact-block{
  background:var(--surface);
  border:1px solid var(--line);
  border-radius:12px;
  padding:clamp(32px,5vw,56px);
  text-align:center;
}
.contact-block h2{margin-top:0;}
.contact-priorities{
  margin:28px 0 0;
  font-size:13.5px;
  color:var(--muted-dim);
  letter-spacing:.02em;
}
```

- [ ] **Step 2: Add the About section markup**

```html
<section id="about">
  <div class="page">
    <p class="eyebrow">About</p>
    <h2 class="section-title">Researcher. Engineer. Builder.</h2>
    <div class="about-grid">
      <div class="about-copy reveal">
        <p>I work at the intersection of cybersecurity research and immersive systems engineering. My background spans computer forensics, cybersecurity, XR development, experimental research, machine learning and applied platform engineering.</p>
        <p>My PhD focused on understanding cybersecurity threats in virtual reality environments, but my broader interest is in complex systems where software behaviour, human experience and security intersect.</p>
        <p>I enjoy moving between research questions and working systems — building the environments required to investigate a problem, collecting evidence about how those systems behave, and translating those findings into practical engineering solutions.</p>
        <p><strong>Education:</strong> PhD, Computing and Information Systems, University of Greenwich (2019&ndash;2025) &middot; MSc, Computer Forensics and Cyber Security (Distinction), University of Greenwich (2017&ndash;2018) &middot; BSc, Computer Science, Ekiti State University, Nigeria (2016).</p>
      </div>
      <div class="about-side reveal">
        <h3>Selected publications</h3>
        <ul class="pub-list">
          <li><span>Virtually Secure: A taxonomic assessment of cybersecurity challenges in virtual reality environments</span>Computers &amp; Security (Q1 journal)</li>
          <li><span>Detecting framerate-oriented cyber attacks on user experience in virtual reality</span>VR4Sec Workshop, USENIX SOUPS 2021</li>
        </ul>
        <h3>Awards</h3>
        <ul class="award-list">
          <li><span>ISEC Research Group Research Award &mdash; &pound;5,000</span>University of Greenwich, in support of doctoral research into XR security and immersive systems</li>
          <li><span>2nd Runner-Up, Re-Code Nigeria Hackathon</span>Access Bank &mdash; national software-innovation competition</li>
        </ul>
      </div>
    </div>
  </div>
</section>
```

- [ ] **Step 3: Add the Contact section markup**

```html
<section id="contact">
  <div class="page">
    <div class="contact-block reveal">
      <p class="eyebrow" style="justify-content:center; display:flex;">Contact</p>
      <h2 class="section-title" style="max-width:none;">Let's build something interesting.</h2>
      <div class="ctas" style="justify-content:center;">
        <a class="btn btn-primary" href="mailto:blessingodeleye@gmail.com">Email me</a>
        <a class="btn btn-secondary" href="https://www.linkedin.com/in/blessingodeleye" target="_blank" rel="noopener">LinkedIn</a>
        <a class="btn btn-secondary" href="https://github.com/BleOdel" target="_blank" rel="noopener">GitHub</a>
      </div>
      <p class="contact-priorities">Priority areas: Applied Security &middot; XR Security &middot; Research Engineering &middot; Secure Systems &middot; Immersive Technologies &middot; Cyber-Physical Security</p>
    </div>
  </div>
</section>
```

- [ ] **Step 4: Verify in browser**

Confirm: About renders as a 2-column layout (copy left, publications/awards
right) collapsing to 1 column under 800px; Contact renders as a centered
card with 3 CTA buttons that wrap on narrow screens.

- [ ] **Step 5: Commit**

```bash
git add index.html styles.css
git commit -m "Add about, publications, and contact sections"
```

---

### Task 6: Project page template + Task 6a: `taxonomy.html`

**Files:**
- Modify: `styles.css` (append `.project-hero`, `.project-section`,
  `.story-strip`, `.svg-diagram` rules — shared by all six project pages)
- Create: `projects/taxonomy.html`

**Interfaces:**
- Consumes: nav/footer markup pattern from Task 1 (relative paths change:
  `href="../index.html#..."` and `href="styles.css"` becomes
  `href="../styles.css"` etc., since project pages live one directory down).
- Produces: the `.project-hero`/`.project-section` pattern that Tasks 7–11
  reuse verbatim for their own content.

- [ ] **Step 1: Append shared project-page CSS to `styles.css`**

```css
/* ---------- project pages ---------- */
.project-hero{padding:clamp(40px,7vw,72px) 0 48px;}
.project-hero .eyebrow{color:var(--cyan); text-transform:uppercase; font-size:12.5px; letter-spacing:.08em;}
.project-hero h1{
  font-family:'Space Grotesk',sans-serif;
  font-weight:600;
  font-size:clamp(30px,4vw,46px);
  line-height:1.15;
  letter-spacing:-0.01em;
  margin:14px 0 20px;
  max-width:20ch;
}
.project-hero .question{
  font-size:18px;
  line-height:1.6;
  color:var(--cyan);
  font-style:normal;
  max-width:56ch;
  margin:0 0 20px;
  font-family:'Space Grotesk',sans-serif;
  font-weight:500;
}
.project-hero .summary{font-size:15.5px; line-height:1.75; color:var(--muted); max-width:64ch; margin:0;}
.project-meta{display:flex; flex-wrap:wrap; gap:10px 24px; margin-top:24px; font-size:13px; color:var(--muted-dim);}
.project-meta strong{color:var(--muted); font-weight:600;}

.project-section{padding:44px 0; border-top:1px solid var(--line);}
.project-section h2{
  font-family:'Space Grotesk',sans-serif;
  font-weight:600;
  font-size:clamp(20px,2.4vw,26px);
  margin:0 0 16px;
}
.project-section p{font-size:15px; line-height:1.75; color:var(--muted); max-width:68ch; margin:0 0 16px;}
.project-section p:last-child{margin-bottom:0;}

.story-strip{
  display:flex;
  align-items:center;
  flex-wrap:wrap;
  gap:10px;
  background:var(--surface);
  border:1px solid var(--line);
  border-radius:8px;
  padding:16px 20px;
  margin:0 0 24px;
}
.story-strip span{
  font-family:'Space Grotesk',sans-serif;
  font-size:12px;
  font-weight:600;
  letter-spacing:.04em;
  text-transform:uppercase;
  color:var(--muted);
}
.story-strip span.arrow{color:var(--muted-dim); font-weight:400;}

.svg-diagram{
  background:var(--surface);
  border:1px solid var(--line);
  border-radius:10px;
  padding:20px;
  margin:0 0 24px;
}
.svg-diagram svg{width:100%; height:auto; display:block;}

.back-link{display:inline-flex; align-items:center; gap:6px; font-size:13.5px; color:var(--muted); text-decoration:none; margin-bottom:20px;}
.back-link:hover, .back-link:focus-visible{color:var(--text);}
```

- [ ] **Step 2: Create `projects/taxonomy.html`**

Content sourced from the Computers & Security paper (spec §5/§6, item 1).
The taxonomy diagram is redrawn as original SVG (four branches: Exploit,
Breach, Impact, Intent) — not a copy of the paper's Figure 2.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>VR Cybersecurity Taxonomy — Blessing Odeleye</title>
<meta name="description" content="A systematic taxonomy of cybersecurity challenges in virtual reality environments, published in Computers & Security.">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="../styles.css">
</head>
<body>

<nav class="topbar">
  <a class="mark" href="../index.html"><span class="dot"></span>BLESSING ODELEYE</a>
  <button class="nav-toggle" data-nav-toggle aria-expanded="false" aria-controls="primary-nav" aria-label="Toggle navigation">☰</button>
  <div class="navlinks" id="primary-nav" data-nav-menu>
    <a href="../index.html#work">Work</a>
    <a href="../index.html#research">Research</a>
    <a href="../index.html#about">About</a>
    <a href="../index.html#contact">Contact</a>
  </div>
</nav>

<main>
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">Research &middot; Computers &amp; Security (Q1 journal)</p>
    <h1>VR Cybersecurity Taxonomy</h1>
    <p class="question">How should researchers systematically reason about security and privacy threats in immersive environments?</p>
    <p class="summary">The first systematic classification of cybersecurity challenges in Virtual Reality Environments (VREs), mapping what aspect of the system may be exploited, what security property may be breached, what impact a breach has on the VR experience, and what damage the attacker intends to cause.</p>
    <div class="project-meta">
      <span><strong>Published:</strong> Odeleye, Loukas, Heartfield, Sakellari, Panaousis, Spyridonis &mdash; Computers &amp; Security, 2023</span>
      <span><strong>Role:</strong> Lead author, PhD research</span>
    </div>
  </div>
</section>

<!-- MEDIA: no footage needed here — the brief presents this taxonomy as an interactive/animated research artefact rather than video (creative brief §06); the SVG diagram below fills that role for v1 -->

<section class="project-section">
  <div class="page">
    <h2>The problem</h2>
    <p>Although virtual reality is not a new technology, its adoption across sectors beyond entertainment has outpaced the security research community's attention to the new cyber threats it introduces. VR's variety of system components presents an extensive attack surface, while its emphasis on immersion and presence means an attack can target the user directly &mdash; yet a head-mounted display prevents the user from observing that attack's impact on their immediate physical environment.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>The taxonomy</h2>
    <p>The taxonomy answers four questions about any VR cyber threat. <strong>Exploit</strong> &mdash; what aspect of the system may be exploited: its system parameters (network, display, audio, sensors) or the user's human sensory stimulus. <strong>Breach</strong> &mdash; what security property is broken: confidentiality, integrity or availability. <strong>Impact</strong> &mdash; what effect the breach has on the VR experience: interaction, immersion or presence. <strong>Intent</strong> &mdash; whether the intended damage is physical or non-physical.</p>
    <div class="svg-diagram reveal">
      <svg viewBox="0 0 800 320" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Taxonomy tree with four branches: Exploit, Breach, Impact, and Intent, each radiating from a central VR Security Challenges node.">
        <g fill="none" stroke="#1a2444" stroke-width="1.5">
          <line x1="400" y1="160" x2="140" y2="60"/>
          <line x1="400" y1="160" x2="400" y2="40"/>
          <line x1="400" y1="160" x2="660" y2="60"/>
          <line x1="400" y1="160" x2="400" y2="280"/>
        </g>
        <rect x="330" y="140" width="140" height="40" rx="8" fill="#0a1024" stroke="#57d8ff" stroke-width="1.5"/>
        <text x="400" y="165" text-anchor="middle" fill="#f7f9ff" font-family="Space Grotesk" font-size="13" font-weight="600">VR Security</text>

        <rect x="60" y="24" width="160" height="56" rx="8" fill="#0a1024" stroke="#57d8ff" stroke-width="1.5"/>
        <text x="140" y="47" text-anchor="middle" fill="#57d8ff" font-family="Space Grotesk" font-size="13" font-weight="700">EXPLOIT</text>
        <text x="140" y="65" text-anchor="middle" fill="#9aa7c2" font-family="Inter" font-size="10.5">System parameters / human senses</text>

        <rect x="320" y="4" width="160" height="56" rx="8" fill="#0a1024" stroke="#4a7dff" stroke-width="1.5"/>
        <text x="400" y="27" text-anchor="middle" fill="#4a7dff" font-family="Space Grotesk" font-size="13" font-weight="700">BREACH</text>
        <text x="400" y="45" text-anchor="middle" fill="#9aa7c2" font-family="Inter" font-size="10.5">Confidentiality / integrity / availability</text>

        <rect x="580" y="24" width="160" height="56" rx="8" fill="#0a1024" stroke="#9d62ff" stroke-width="1.5"/>
        <text x="660" y="47" text-anchor="middle" fill="#9d62ff" font-family="Space Grotesk" font-size="13" font-weight="700">IMPACT</text>
        <text x="660" y="65" text-anchor="middle" fill="#9aa7c2" font-family="Inter" font-size="10.5">Interaction / immersion / presence</text>

        <rect x="320" y="264" width="160" height="56" rx="8" fill="#0a1024" stroke="#57d8ff" stroke-width="1.5"/>
        <text x="400" y="287" text-anchor="middle" fill="#57d8ff" font-family="Space Grotesk" font-size="13" font-weight="700">INTENT</text>
        <text x="400" y="305" text-anchor="middle" fill="#9aa7c2" font-family="Inter" font-size="10.5">Physical / non-physical damage</text>
      </svg>
    </div>
    <p>Applying this taxonomy to existing published attacks and defences showed that current VR security research is heavily weighted toward authentication (70% of surveyed defences) and confidentiality breaches, leaving intrusion detection and availability-focused defences comparatively unexplored &mdash; the gap this doctoral research addresses directly (see <a href="intrusion-detection.html" style="color:var(--cyan)">Telemetry-Driven XR Intrusion Detection</a>).</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Why it matters</h2>
    <p>A shared taxonomy gives researchers from different backgrounds &mdash; systems engineers, HCI researchers, security analysts &mdash; a common vocabulary for identifying where their work can contribute to protecting immersive environments, from understanding an attack's impact to designing new defences.</p>
  </div>
</section>
</main>

<footer class="site-footer">
  <span>&copy; 2026 Blessing Odeleye</span>
  <div class="footer-links">
    <a href="https://github.com/BleOdel" target="_blank" rel="noopener">GitHub</a>
    <a href="https://www.linkedin.com/in/blessingodeleye" target="_blank" rel="noopener">LinkedIn</a>
    <a href="mailto:blessingodeleye@gmail.com">Email</a>
  </div>
</footer>

<script src="../script.js"></script>
</body>
</html>
```

- [ ] **Step 3: Verify in browser**

Confirm: page loads with correct relative paths (nav links jump to homepage
sections, back-link returns to `#work`, stylesheet/script load without 404s
in the console), the SVG diagram renders legibly with all four branch labels
visible and readable against the dark background.

- [ ] **Step 4: Commit**

```bash
git add projects/taxonomy.html styles.css
git commit -m "Add project-page template and VR Cybersecurity Taxonomy page"
```

---

### Task 7: `vr-security.html` (PhD overview)

**Files:**
- Create: `projects/vr-security.html`

**Interfaces:**
- Consumes: `.project-hero`/`.project-section`/`.story-strip` from Task 6.

Content sourced from the thesis abstract and contributions list (spec §5/§6,
item 2) — the four contributions named in the abstract: a new cyber attack
for VR, the first practical VR IDS, a modular VR testbed, and a VR security
taxonomy.

- [ ] **Step 1: Create the file**

Follow the exact document structure from `projects/taxonomy.html` (same
`<head>`, nav, footer, script tag). Replace the hero and body sections with:

```html
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">PhD research &middot; University of Greenwich, 2019&ndash;2025</p>
    <h1>Detecting Cybersecurity Threats in Virtual Reality</h1>
    <p class="question">Can a cyberattack against VR be detected from the behaviour of the system itself?</p>
    <p class="summary">Doctoral research combining a custom-built VR testbed, a novel GPU-based cyber attack, and an unsupervised machine-learning intrusion detection system &mdash; the first practical step toward reactive VR security defences, where prevention alone is insufficient.</p>
    <div class="project-meta">
      <span><strong>Thesis:</strong> Addressing Cyber Security Threats in Virtual Reality Environments (2024)</span>
      <span><strong>Supervisors:</strong> Prof. Georgios Loukas, Dr Georgia Sakellari, Dr Ryan Heartfield</span>
    </div>
  </div>
</section>

<!-- MEDIA: 10-30s muted loop of the VR experimental environment and framerate-attack behaviour, per XR Video Strategy (creative brief §07) -->

<section class="project-section">
  <div class="page">
    <h2>The problem</h2>
    <p>VR security research had focused almost entirely on prevention &mdash; risk assessment and authentication &mdash; with no consideration for what happens when prevention fails or a threat is entirely new. Head-mounted displays compound the problem: they isolate users from real-world cues (network state, CPU usage, unfamiliar processes) that would normally hint at a compromise.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Four contributions</h2>
    <div class="story-strip reveal">
      <span>New attack</span><span class="arrow">&rarr;</span>
      <span>Modular testbed</span><span class="arrow">&rarr;</span>
      <span>Practical IDS</span><span class="arrow">&rarr;</span>
      <span>Security taxonomy</span>
    </div>
    <p>A GPU-based attack that induces VR sickness on demand by manipulating frame rate; a custom-built, modular VR testbed designed specifically to carry out that attack and observe user reactions to real-time warnings; an Isolation Forest-based intrusion detection system &mdash; the first practical IDS for VR &mdash; that gives early warning before an attack significantly impacts the user; and a systematic taxonomy of VR cybersecurity challenges (see <a href="taxonomy.html" style="color:var(--cyan)">VR Cybersecurity Taxonomy</a>) that helped identify where this reactive-defence gap sat in the field.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Where it led</h2>
    <p>The testbed, telemetry pipeline and detection approach developed here became the technical foundation for two follow-on projects: the Horizon Europe <a href="sun.html" style="color:var(--cyan)">SUN</a> consortium, which extended the IDS into healthcare and manufacturing demonstrators, and the deeper feature-engineering and evaluation work covered in <a href="intrusion-detection.html" style="color:var(--cyan)">Telemetry-Driven XR Intrusion Detection</a> and <a href="human-impact.html" style="color:var(--cyan)">Cyber-Physical Impact of VR Attacks</a>.</p>
  </div>
</section>
```

- [ ] **Step 2: Verify in browser** — confirm all four internal links
(`taxonomy.html`, `sun.html`, `intrusion-detection.html`, `human-impact.html`)
are visually styled as links (cyan) even though some targets don't exist
until later tasks; no console errors from the stylesheet/script/nav paths.

- [ ] **Step 3: Commit**

```bash
git add projects/vr-security.html
git commit -m "Add PhD overview project page"
```

---

### Task 8: `intrusion-detection.html`

**Files:**
- Create: `projects/intrusion-detection.html`

**Interfaces:**
- Consumes: `.project-hero`/`.project-section`/`.story-strip`/`.svg-diagram`
  from Task 6.

Content sourced from the CVs, thesis, and SOUPS'21 paper Tables 1–2 (spec
§5/§6, item 3) — the real per-attack-intensity latency/accuracy figures.
Visual story per the brief: NORMAL → ANOMALOUS → ATTACK DETECTED.

- [ ] **Step 1: Create the file**

Same document shell as Task 6/7. Body content:

```html
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">Machine learning &middot; Intrusion detection</p>
    <h1>Telemetry-Driven XR Intrusion Detection</h1>
    <p class="question">Can runtime telemetry reveal attacks that conventional monitoring may miss?</p>
    <p class="summary">A telemetry-based intrusion detection system using more than 20 engineered features, PCA dimensionality reduction, and unsupervised anomaly detection across roughly 580,000 time-series windows of VR sensor data.</p>
    <div class="project-meta">
      <span><strong>Model:</strong> Isolation Forest (benchmarked against One-Class SVM, LOF, Elliptic Envelope)</span>
      <span><strong>Result:</strong> &asymp;99% F1, 0&ndash;2s detection latency</span>
    </div>
  </div>
</section>

<!-- MEDIA: 10-30s muted loop of the telemetry/IDS detection sequence, per XR Video Strategy (creative brief §07) -->

<section class="project-section">
  <div class="page">
    <h2>The pipeline</h2>
    <p>A telemetry feature pipeline extracts more than 20 real-time signals from the VR session &mdash; frame duration statistics, framerate entropy, Head Rotation Interval (HRI) metrics, and quaternion/Euler pose data &mdash; across roughly 580,000 time-series windows. Principal Component Analysis reduces this to a compact 5-feature representation optimised for low-latency inference, which an Isolation Forest model classifies as normal or anomalous using a two-second sliding window.</p>
    <div class="story-strip reveal">
      <span>Normal</span><span class="arrow">&rarr;</span>
      <span style="color:var(--violet)">Anomalous</span><span class="arrow">&rarr;</span>
      <span style="color:var(--cyan)">Attack detected</span>
    </div>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Detection latency by attack intensity</h2>
    <p>Evaluated against four GPU-based framerate attacks of increasing intensity (larger malicious image payloads loaded into the GPU), requiring a rule of two consecutive anomalous windows before raising a warning &mdash; a design choice that eliminated false positives at the cost of roughly one extra second of delay.</p>
    <div class="svg-diagram reveal">
      <svg viewBox="0 0 700 220" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Bar chart of detection latency in seconds across four GPU attack intensities: 542 kilobytes at 9 seconds, 1.06 megabytes at 3 seconds, 1.82 megabytes at 2 seconds, 3.13 megabytes at 4 seconds.">
        <g font-family="Inter" font-size="11" fill="#9aa7c2">
          <text x="90" y="205" text-anchor="middle">542 KB</text>
          <text x="270" y="205" text-anchor="middle">1.06 MB</text>
          <text x="450" y="205" text-anchor="middle">1.82 MB</text>
          <text x="630" y="205" text-anchor="middle">3.13 MB</text>
        </g>
        <g fill="#57d8ff">
          <rect x="65" y="20" width="50" height="160"/>
          <rect x="245" y="127" width="50" height="53"/>
          <rect x="425" y="145" width="50" height="35"/>
          <rect x="605" y="109" width="50" height="71"/>
        </g>
        <g font-family="Space Grotesk" font-size="14" font-weight="700" fill="#f7f9ff">
          <text x="90" y="14" text-anchor="middle">9s</text>
          <text x="270" y="121" text-anchor="middle">3s</text>
          <text x="450" y="139" text-anchor="middle">2s</text>
          <text x="630" y="103" text-anchor="middle">4s</text>
        </g>
      </svg>
    </div>
    <p>Detection accuracy stayed between 0.97 and 0.99 F1 across all four intensities, with the 1.82MB attack &mdash; the fastest to detect &mdash; forming the basis of the ≈99% F1 / 0&ndash;2s headline figure.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Why unsupervised</h2>
    <p>Isolation Forest requires no prior assumptions about the distribution of monitored parameters, which matters in a domain like VR security where there's limited existing knowledge of which telemetry signals actually indicate an attack. This let the model learn what "normal" looks like for a given VR system without labelled attack data.</p>
  </div>
</section>
```

- [ ] **Step 2: Verify in browser** — confirm the bar-chart SVG's four bars
render with visibly different heights matching the labeled seconds (9, 3, 2,
4), and the labels don't overlap the bars.

- [ ] **Step 3: Commit**

```bash
git add projects/intrusion-detection.html
git commit -m "Add telemetry-driven intrusion detection project page"
```

---

### Task 9: `human-impact.html`

**Files:**
- Create: `projects/human-impact.html`

**Interfaces:**
- Consumes: `.project-hero`/`.project-section`/`.story-strip` from Task 6.

Content sourced from the SOUPS'21 paper (attack design, Table 1/2) and the
thesis (48-participant VRSQ study, warning-acceptance results) — spec §5/§6,
item 4.

- [ ] **Step 1: Create the file**

```html
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">Human factors &middot; VR4Sec Workshop, USENIX SOUPS 2021</p>
    <h1>Cyber-Physical Impact of VR Attacks</h1>
    <p class="question">What happens when a cyberattack affects both a machine and the person inside the experience?</p>
    <p class="summary">A controlled 48-participant study linking framerate degradation from a GPU-based cyberattack to measured VR sickness and user comfort, and evaluating whether an in-headset security warning is accepted without breaking immersion.</p>
  </div>
</section>

<!-- MEDIA: 10-30s muted loop of the in-headset security warning mechanism, per XR Video Strategy (creative brief §07) -->

<section class="project-section">
  <div class="page">
    <h2>Two attacks, one goal</h2>
    <p>A GPU-based attack used a custom C++ malware (delivered as if via a post-exploitation tool such as Meterpreter) that overwhelmed the GPU with long-running OpenGL tasks, hidden from the user, at four configurable intensities. A network-based attack flooded the target with continuous ICMP echo requests &mdash; a simple denial-of-service pattern &mdash; to disrupt a collaborative VR session over Photon PUN2 networking. Both attacks share the same goal: degrade frame rate enough to cause visual discomfort, screen tearing, and eventually VR sickness.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>The study</h2>
    <p>48 participants experienced the induced framerate attack while their telemetry and Virtual Reality Sickness Questionnaire (VRSQ) responses were recorded, using Python and SPSS for both descriptive and inferential statistical analysis of symptom severity.</p>
    <div class="story-strip reveal">
      <span>Attack</span><span class="arrow">&rarr;</span>
      <span>XR environment</span><span class="arrow">&rarr;</span>
      <span>Telemetry</span><span class="arrow">&rarr;</span>
      <span style="color:var(--cyan)">User warning</span>
    </div>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>In-headset warning design</h2>
    <p>An unobtrusive in-headset threat-visualisation mechanism was designed and evaluated against a security-warning-design framework, aiming to communicate an active attack to the user without breaking immersion or presence. Participants rated the warning on clarity, timeliness, and whether they understood what action to take &mdash; feeding directly into the reactive-defence design used across the <a href="intrusion-detection.html" style="color:var(--cyan)">intrusion detection</a> and <a href="sun.html" style="color:var(--cyan)">SUN</a> work.</p>
  </div>
</section>
```

- [ ] **Step 2: Verify in browser** — confirm internal links to
`intrusion-detection.html` and `sun.html` render correctly.

- [ ] **Step 3: Commit**

```bash
git add projects/human-impact.html
git commit -m "Add cyber-physical impact project page"
```

---

### Task 10: `sun.html`

**Files:**
- Create: `projects/sun.html`

**Interfaces:**
- Consumes: `.project-hero`/`.project-section` from Task 6.

Content sourced from the CVs' "Research Fellow, Cyber Security of XR" role
descriptions (spec §5/§6, item 5) — Horizon Europe SUN project.

- [ ] **Step 1: Create the file**

```html
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">Horizon Europe &middot; University of Greenwich, 2023&ndash;2024</p>
    <h1>SUN: Cyber Security for Human-Centred XR</h1>
    <p class="question">How can immersive security research support healthcare, accessibility and industrial scenarios?</p>
    <p class="summary">SUN (Social &amp; human-centred XR) is a multi-national Horizon Europe consortium developing secure, trustworthy XR for rehabilitation, worker safety, and accessibility &mdash; including for people with severe mobility and communication impairments.</p>
  </div>
</section>

<!-- MEDIA: no specific footage listed in the brief for SUN — consider a demonstrator clip from a partner site if one becomes available for public release -->

<section class="project-section">
  <div class="page">
    <h2>From PhD research to reusable tooling</h2>
    <p>As Research Fellow, I extended the multi-user Unity/OpenXR security testbed from my PhD research to support cross-partner applications, fully instrumented for security monitoring and threat experimentation. I designed a real-time intrusion detection system monitoring orientation, gaze, and system/network telemetry to detect emerging, zero-day-style threats in XR use cases &mdash; reusing the core detection approach from <a href="intrusion-detection.html" style="color:var(--cyan)">Telemetry-Driven XR Intrusion Detection</a>.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>From research fellow to reusable tooling</h2>
    <p>Automated telemetry aggregation pipelines were built across multiple XR endpoints, with detection components integrated into high-stakes healthcare and manufacturing demonstrations run by project partners. Unobtrusive in-headset threat-visualisation mechanisms ensured critical safety alerts reached users without degrading operational performance or breaking immersion.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Continuity beyond the contract</h2>
    <p>After the formal Research Fellow contract ended, I continued supporting the SUN consortium by providing the core IDS architecture and facilitating technical reviews with partner teams &mdash; ensuring the security tooling and experimental frameworks from my PhD research remained a durable input to the project's continuation.</p>
  </div>
</section>
```

Note: the two `<h2>` headings ("From PhD research..." / "From research
fellow...") are intentionally distinct — do not deduplicate into one section;
they cover different content (detection design vs. deployment/demonstrators).

- [ ] **Step 2: Verify in browser** — confirm the internal link to
`intrusion-detection.html` renders correctly.

- [ ] **Step 3: Commit**

```bash
git add projects/sun.html
git commit -m "Add SUN project page"
```

---

### Task 11: `gener8xr.html`

**Files:**
- Create: `projects/gener8xr.html`

**Interfaces:**
- Consumes: `.project-hero`/`.project-section`/`.project-meta` from Task 6.

Content sourced from the CVs' "XR Solutions Developer (KTP Associate)" role
descriptions (spec §5/§6, item 6) — Innovate UK KTP, Gener8XR platform.

- [ ] **Step 1: Create the file**

```html
<section class="project-hero">
  <div class="page">
    <a class="back-link" href="../index.html#work">&larr; All work</a>
    <p class="eyebrow">Innovate UK KTP &middot; Gener8 Spaces Ltd &amp; Edge Hill University, 2024&ndash;Present</p>
    <h1>Gener8XR: Applied Mixed-Reality Platform Engineering</h1>
    <p class="question">How do you turn experimental immersive technology into a reusable, maintainable platform?</p>
    <p class="summary">A production mixed-reality platform unifying room-scale CAVE projection systems with Quest 3-based MR, built as the sole XR Solutions Developer on a 32-month Innovate UK Knowledge Transfer Partnership.</p>
    <div class="project-meta">
      <span><strong>Funded value:</strong> &pound;205,332</span>
      <span><strong>Stack:</strong> Unity &middot; C# &middot; OpenXR &middot; Photon &middot; Quest 3 &middot; Spatial anchors</span>
    </div>
  </div>
</section>

<!-- MEDIA: 10-30s muted loop of the Quest 3 mixed-reality environment, spatial anchoring/colocation, and radial/wrist UI, per XR Video Strategy (creative brief §07) -->

<section class="project-section">
  <div class="page">
    <h2>Spatial anchoring and persistence</h2>
    <p>A shared spatial anchoring and persistence system for colocated MR sessions achieves sub-15cm drift detection with automatic realignment, incorporating pose stabilisation and world-anchored scene restoration across users, sessions and devices &mdash; the core technical challenge in keeping multiple headsets agreeing on where virtual objects sit in physical space.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Bridging Unity and non-technical tools</h2>
    <p>A bidirectional interoperability layer connects Unity MR scenes to the Intuiface platform using a custom HTTP command API and WebSocket event broadcasting, enabling non-technical users to control live 3D scene content in real time &mdash; letting Gener8's own staff operate the platform without touching Unity directly.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Interaction design and reusable engineering</h2>
    <p>Core interactive interfaces &mdash; radial context menus, wrist-mounted controls, and world-anchored panels &mdash; were designed for spatial usability and clarity in shared MR environments, informed directly by the security-warning and cognitive-load insights from the <a href="human-impact.html" style="color:var(--cyan)">human-impact</a> study. A reusable XR development blueprint standardises C# patterns, prefabs and modular architecture, reducing future development cycles and establishing a product-delivery framework that outlives the KTP itself.</p>
  </div>
</section>

<section class="project-section">
  <div class="page">
    <h2>Requirements and knowledge transfer</h2>
    <p>Stakeholder discovery and user surveys were translated into a formal Software Requirements Specification (SRS) and Agile user stories, directly driving the UX design and development roadmap. Documentation and internal workshops upskilled non-technical staff on deploying, configuring and operating the platform in educational settings.</p>
  </div>
</section>
```

- [ ] **Step 2: Verify in browser** — confirm the internal link to
`human-impact.html` renders correctly.

- [ ] **Step 3: Commit**

```bash
git add projects/gener8xr.html
git commit -m "Add Gener8XR project page"
```

---

### Task 12: Full-site QA pass

**Files:**
- Modify: any file where a check below fails.

**Interfaces:** None — this task validates the interfaces produced by all
previous tasks are actually wired together correctly.

- [ ] **Step 1: Check every internal link resolves to a real file**

```bash
cd ~/Dev-Projects/BleOdel.github.io
grep -oE 'href="[^"#][^"]*\.html[^"]*"' index.html projects/*.html | sed 's/.*href="//;s/"$//' | sed 's/#.*//' | sort -u
```

For each path printed, confirm the target file exists relative to the file
it was found in (project pages use `../index.html` and sibling
`other-page.html` paths; the homepage uses `projects/other-page.html`
paths). Fix any that don't resolve.

- [ ] **Step 2: Check every internal anchor exists**

```bash
grep -oE 'href="[^"]*#[a-z-]+"' index.html projects/*.html
grep -oE 'id="[a-z-]+"' index.html
```

Confirm every `#fragment` referenced (`#top`, `#work`, `#research`, `#about`,
`#contact`) has a matching `id="..."` in `index.html`.

- [ ] **Step 3: Responsive check at three widths**

Using the browser's device toolbar (or `resize_window` if using the
Claude Browser tool), load `index.html` and each project page at 1440px,
900px, and 390px width. Confirm at each width: no horizontal scrollbar on
`<body>`, nav collapses to the toggle button below 760px and opens/closes
correctly, all grids (evidence strip, cycle, work cards, philosophy grid,
about grid) reflow per their documented breakpoints, and no text is clipped.

- [ ] **Step 4: Accessibility spot-check**

Confirm: every `<img>` has an `alt` attribute (empty `alt=""` is correct for
the purely decorative hero image and SVG diagrams that also carry
`role="img" aria-label="..."`), every interactive element (nav links, nav
toggle, buttons, work cards) shows a visible focus ring when tabbed to, and
heading levels don't skip (each page has exactly one `<h1>`, section titles
are `<h2>`).

- [ ] **Step 5: Commit any fixes**

```bash
git add -A
git commit -m "QA pass: verify links, anchors, responsive layout, accessibility"
```

If no fixes were needed, skip this commit (nothing to commit is fine — the
QA pass itself is the deliverable, not a forced commit).
