# Chi Wang — Personal Academic Website

Single-file static site (`index.html`) deployable to GitHub Pages with no build step,
no framework, and no external libraries.

## Folder structure

```
/
├── index.html                          ← entire site
├── asset/                              ← (singular, matches your folder)
│   ├── photo.jpg                       ← headshot (hero)
│   ├── photo_presenting.jfif           ← accent photo (hero)
│   ├── cv.pdf                          ← embedded + downloadable
│   └── paper/
│       ├── Arbitrage_PMD2025.pdf
│       ├── essay1_draft.pdf            ← Virtual Fed (JMP)
│       ├── ETF_Volatility.pdf
│       └── Anomaly Returns at Night and Day.pdf
└── data/
    └── agentic_MPU.csv                 ← source data (embedded inline in HTML)
```

> **Note on filenames with spaces:** "Anomaly Returns at Night and Day.pdf" is
> referenced as `asset/paper/Anomaly%20Returns%20at%20Night%20and%20Day.pdf` in
> the HTML (URL-encoded). If you rename a file, update the corresponding `href`.

## Deploying to GitHub Pages

1. Push this folder to a GitHub repo (e.g. `chi-wang.github.io` or any repo with Pages enabled).
2. In **Settings → Pages**, set source to `main` branch, root `/`.
3. GitHub Pages serves `index.html` automatically.

No `_config.yml`, no Jekyll, no build step needed.

---

## How to add a new paper card

### Step 1 — Add the HTML card

Find the comment block `<!-- ══ PAPER 4 ══ -->` in `index.html` and insert a new
`<article>` block **after** it, inside `<div class="paper-cards">`.

Use this template (replace every `N` with the next integer, e.g. `5`):

```html
<!-- ══════════════════════════════════════════════════════
     PAPER N — Your Paper Title
     ══════════════════════════════════════════════════════ -->
<article class="paper-card" data-card="N">
  <div class="paper-card-header">
    <h3 class="paper-title">Your Paper Title</h3>
    <!-- Optional: add <span class="badge badge-jmp">Job Market Paper</span> -->
  </div>
  <p class="paper-status">Working Paper, 20XX · Venue if any</p>
  <p class="paper-summary">
    Two-sentence summary of the paper.
  </p>

  <!-- Figure — choose one of the patterns below and customise -->
  <div class="paper-figure">
    <div class="fig-subtitle">Schematic — qualitative direction only</div>
    <div id="figN">
      <!-- your figure markup here -->
    </div>
  </div>

  <div class="paper-links">
    <a href="assets/papers/your_paper.pdf" target="_blank" class="paper-link">PDF</a>
    <button class="abstract-btn" onclick="toggleAbstract('absN',this)">Abstract ▼</button>
  </div>
  <div class="abstract-panel" id="absN">
    Full abstract text.
  </div>
</article>
```

### Step 2 — Add the animation function

Inside the `<script>` block, find the `animateFig(n)` dispatcher and add a branch:

```js
function animateFig(n) {
  if (n === '1') animFig1();
  if (n === '2') animFig2();
  if (n === '3') animFig3();
  if (n === '4') animFig4();
  if (n === 'N') animFigN();   // ← add this line
}
```

Then define the function:

```js
function animFigN() {
  // Example: growing bars
  setH('bar-N-left',  90);
  setH('bar-N-right', 45);

  // Example: staggered callouts (see animFig3 for reference)
  // ['f-N-c1','f-N-c2'].forEach(function(id,i){
  //   setTimeout(function(){ document.getElementById(id).classList.add('in'); }, i*200);
  // });

  // Example: counter animation
  // counter('cnt-N', 0, 42.5, 1, '%', 1200);
}
```

### Available helper functions

| Function | Purpose |
|---|---|
| `setH(id, px)` | Animate a bar's height to `px` pixels (uses CSS transition) |
| `counter(id, from, to, decimals, suffix, durationMs)` | Count-up animation with ease-out cubic |
| `toggleAbstract(id, btn)` | Toggle abstract panel (already wired to the button) |

### Figure patterns already in the file

| Paper | Pattern | Key elements |
|---|---|---|
| 1 | Grouped bars | `.f1-bar`, `setH()`, `.f1-group-label` |
| 2 | Canvas line chart + counters | `drawMPU()`, `counter()` |
| 3 | Staggered slide-in callouts | `.f3-callout`, `.in` class |
| 4 | Two-phase bar swap | `setH()` with two `setTimeout` delays |

Copy the closest pattern's HTML + CSS class names, give every element a new unique `id`,
and write a corresponding `animFigN()`.

---

## Editing other sections

| Section | What to change |
|---|---|
| **Hero** | Name, role, bio `<p>`, contact `<div>`, photo `src` attributes |
| **Nav links** | `<li>` items inside `<ul class="nav-links">` |
| **Data & Resources** | `.data-card` block — title, description, download link |
| **Teaching** | `<li class="teaching-item">` entries |
| **CV** | `iframe src` and `<a href>` in the CV section |
| **Footer** | Copyright line at the bottom of `<body>` |
| **Colors** | CSS custom properties in `:root` at the top of `<style>` |

## Updating the MPU dataset

The MPU data is embedded directly in the JS as the `MPU` array (search for `var MPU = [`).
Each row is `["M/D/YYYY", qianwen_value, opus_value]`.

To update: append new rows in the same format, or replace the entire array.
The chart x-axis auto-scales to the first and last date in the array.
