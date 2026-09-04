# Portfolio site design — Blessing Odeleye (BleOdel.github.io)

## 1. Purpose and positioning

A personal portfolio for Blessing Odeleye, PhD — Applied Security Researcher & XR
Systems Engineer — published on GitHub Pages at `bleodel.github.io`. Source brief:
`Blessing_Odeleye_GitHub_Pages_Creative_Brief.pdf` (supplied by the user).

Hero statement (site's master message):

> I build immersive systems, study how they fail, and engineer ways to make them safer.

The site must read as a research-led technical portfolio — closer to a systems lab
than a CV-as-webpage. It must avoid: a copy-pasted CV, a logo-driven developer
portfolio, hacker-terminal cliché, gaming/crypto/neon visual language, and an
academic profile that hides the engineering work.

Narrative spine (recurring motif across the site): **Research → Build → Attack →
Measure → Detect → Defend**.

## 2. Deployment

- New repo: `BleOdel/BleOdel.github.io` (public — required for free GitHub Pages on
  a user site), local project at `~/Developer/BleOdel.github.io`.
- No custom domain for v1 (github.io URL only).
- Plain static site — HTML + CSS + JS, no backend, no build step, no framework.
  With 7 pages total, nav/footer markup is duplicated per page rather than adding
  a templating layer.

## 3. File structure

```
index.html                 (single page: hero, evidence, narrative cycle,
                             featured work, research philosophy, about,
                             publications, contact — all anchor sections)
styles.css
script.js
assets/
  images/
    hero-headset.png       (finalized hero graphic — see §5)
  videos/
  icons/
projects/
  taxonomy.html            (VR Cybersecurity Taxonomy)
  vr-security.html         (Detecting Cybersecurity Threats in VR — PhD overview)
  intrusion-detection.html (Telemetry-Driven XR Intrusion Detection)
  human-impact.html        (Cyber-Physical Impact of VR Attacks)
  sun.html                 (SUN — Cyber Security for Human-Centred XR)
  gener8xr.html            (Gener8XR — Applied MR Platform Engineering)
```

## 4. Visual design system

Established and approved through iterative hero prototyping (see artifact history).
This is the system every page follows.

**Color tokens** (from the creative brief, verified against the finished hero):

| Token | Hex | Usage |
|---|---|---|
| `--bg` | `#050817` | Primary background |
| `--surface` | `#0a1024` | Cards, secondary surfaces |
| `--line` | `#1a2444` | Hairline borders/dividers |
| `--cyan` | `#57d8ff` | Primary technical accent, primary CTA |
| `--blue` | `#4a7dff` | Secondary accent |
| `--violet` | `#9d62ff` | Tertiary accent (sparing use) |
| `--text` | `#f7f9ff` | Primary text |
| `--muted` | `#9aa7c2` | Secondary text |
| `--muted-dim` | `#6b7794` | Tertiary/label text |

Single dark-committed theme (no light-mode variant) — this is the site's brand
identity, not a themable surface.

**Typography:** Space Grotesk (headings, labels, eyebrows, stat numbers,
navigation) paired with Inter (body copy). Both via Google Fonts. Uppercase
reserved for eyebrows, research-category labels, and status indicators.
`font-variant-numeric: tabular-nums` on the evidence-strip statistics.

**Homepage hero** (finalized): two-column layout — headline block on the left
(eyebrow → statement → supporting copy → CTAs → utility links), a `headline-row`
sub-layout placing the hero graphic tightly beside the headline text itself
(not spanning the full column height). Hero graphic is `assets/images/hero-headset.png`
— a glowing-rim VR headset silhouette with a security-shield/checkmark icon and a
telemetry pulse wired into it, background removed (color-keyed transparent) and
cropped to content. Below the hero: a five-tile evidence strip with real stats
(580K+ telemetry windows, 20+→5 engineered features, ≈99% F1, 0–2s detection
latency, 48 study participants).

**Interface motifs:** system/telemetry-flavored details used sparingly — small
status indicators, hairline dividers, metrics treated as first-class visual
elements (not footnotes) — without literal diagrams beyond the hero.

**Motion:** minimal — a single pulsing accent (already in the hero CSS),
soft hover states on cards/buttons, scroll reveals. No continuous scanning
animation, no aggressive glitch/flicker. Respect `prefers-reduced-motion`.

**Responsive rules:** desktop gets the richest layout; tablet reflows to
single-column vertical modules; mobile prioritizes title, research question,
one visual, one or two key metrics. Complex diagrams simplify to linear flows
on mobile rather than shrinking in place.

**Accessibility:** strong color contrast (verify text/background pairs against
WCAG AA), visible focus states (already present on nav links and buttons),
semantic headings, descriptive link text and alt text, captions/transcripts for
any future video.

## 5. Content sourcing (no fabrication)

Every factual claim on the site must trace to one of these primary sources —
already read in full this session:

- Four CVs (`Blessing_Odeleye_Applied_ML_Cybersecurity_CV.pdf`,
  `Blessing_Odeleye_XR.pdf`, `Blessing_Odeleye-Applied_security_researcher.pdf`,
  `Blessing_Odeleye_R_D.pdf`)
- SOUPS'21 workshop paper: *Detecting framerate-oriented cyber attacks on user
  experience in virtual reality* (GPU/network attack design, Isolation Forest IDS,
  per-attack-intensity latency/accuracy tables)
- Computers & Security journal paper: *Virtually secure: A taxonomic assessment
  of cybersecurity challenges in virtual reality environments* (Exploit/Breach/
  Impact/Intent taxonomy)
- PhD thesis *Addressing cyber security threats in virtual reality environments*
  (abstract, contents, contributions, chapter summaries — not full chapter text)

No paper figures/tables are reproduced verbatim (copyright) — diagrams are
redrawn as original SVG art in the site's own visual language. GPU-malware
techniques are described narratively, never published as runnable code. The
thesis dedication/acknowledgements are personal and excluded.

## 6. Page content map

`index.html` is one page, sectioned with anchor ids that match the nav
(`#work`, `#research`, `#about`, `#contact` — as already built in the hero),
in this order:

1. **Hero** (§4) — done.
2. **Evidence strip** (§4) — done.
3. **Narrative cycle** — the Research→Build→Attack→Measure→Detect→Defend
   motif as a horizontal strip (already sketched in the brief as five labeled
   steps: Research/Build/Attack/Measure/Detect, "Understand→Secure" as the
   through-line).
4. **`#work` — Featured work** — six cards (one per project below), each
   with a one-line problem statement and a link into its `projects/*.html`
   page.
5. **`#research` — Research philosophy & experience** — "Build. Measure.
   Understand. Defend." four-step philosophy; experience progression
   (Gener8 Spaces/Edge Hill 2024–present; Greenwich Research Fellow
   2023–2024; Greenwich PhD Researcher 2019–2025).
6. **`#about` — About & publications** — "Researcher. Engineer. Builder."
   positioning copy from the brief, education, selected publications
   (Computers & Security; VR4Sec/SOUPS'21), awards (ISEC Research Award
   £5,000; Re-Code Nigeria Hackathon runner-up).
7. **`#contact` — Contact** — "Let's build something interesting." + priority
   areas + GitHub/LinkedIn links. No contact form in v1 (no backend) — mailto
   link only.

### Project pages

Each follows the 10-part brief template (Problem, Research Question, System,
Threat, Instrumentation, Detection, Evaluation, Results, Human Impact,
Contribution), with sections omitted where not applicable to that project:

1. **`taxonomy.html`** — VR Cybersecurity Taxonomy. Source: Computers & Security
   paper. Redraw the Exploit(E)/Breach(B)/Impact(A)/Intent(I) taxonomy as an
   original SVG tree. Key presentation: Computers & Security, Q1 journal.

2. **`vr-security.html`** — Detecting Cybersecurity Threats in VR (PhD overview).
   Source: thesis abstract/contributions. The umbrella narrative: GPU-based
   attack, custom modular VR testbed, Isolation Forest IDS, taxonomy — the four
   contributions named in the thesis abstract.

3. **`intrusion-detection.html`** — Telemetry-Driven XR Intrusion Detection.
   Source: CVs + SOUPS'21 paper + thesis. 20+ engineered features (frame
   duration stats, framerate entropy, HRI, pose) → PCA → 5-feature
   representation, ≈580k time-series windows, Isolation Forest, ≈99% F1,
   0–2s latency across four GPU attack intensities (542KB/1.06MB/1.82MB/3.13MB
   — real per-intensity latency/accuracy table from SOUPS'21 Tables 1–2).
   Visual story: NORMAL → ANOMALOUS → ATTACK DETECTED (per brief).

4. **`human-impact.html`** — Cyber-Physical Impact of VR Attacks. Source:
   SOUPS'21 paper + thesis Ch.4–5. GPU-based attack (OpenGL/C++ malware) and
   network-based (ICMP flood) attack designs, 48-participant VRSQ study,
   in-headset warning-visualization design and user acceptance results.

5. **`sun.html`** — SUN: Cyber Security for Human-Centred XR. Source: CVs.
   Horizon Europe Research Fellow role (2023–2024), multi-national consortium
   for rehabilitation/worker-safety/accessibility XR, IDS extended to
   healthcare/manufacturing demonstrators, in-headset warning mechanisms,
   knowledge reuse from PhD tooling.

6. **`gener8xr.html`** — Gener8XR: Applied Mixed-Reality Platform Engineering.
   Source: CVs. Innovate UK KTP (2024–present, £205,332 funded value),
   Unity/OpenXR/Photon mixed-reality platform unifying CAVE + Quest 3, shared
   spatial anchoring with sub-15cm drift detection, HTTP/WebSocket bridge to
   Intuiface, radial/wrist-mounted/world-anchored UI, reusable XR dev
   blueprint, SRS/requirements engineering.

## 7. Media

No photos/video in v1 beyond `assets/images/hero-headset.png`. Each project
page's "visual story" gets an original SVG diagram (redrawn, not copied from
papers) in place of a screenshot. Video sections are marked with an HTML
comment (`<!-- MEDIA: ... -->`) noting what belongs there, for the user to
fill in later per the brief's XR video strategy (10–30s muted looping clips
in project cards; longer demos linked externally).

## 8. Out of scope for v1

Custom domain, real photos/video, a blog or CMS, a contact form/backend,
non-English content, light-mode theme.
