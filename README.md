# 3D Length & Girth Comparator

Compare 3D models side‑by‑side with bottoms aligned. Rotate, zoom, switch units, save your size, compute likely max/min from a partner count, and export a **labeled PNG**. Runs 100% in the browser (no backend).

---

## Features

- **A/B/C comparison**
  - **A (you)** — can be saved to `localStorage` and locked.
  - **B** — manual values *or* auto **max @ c%** from partner count.
  - **C** — auto **min @ c%** from partner count.
- **Bottoms aligned** for accurate length comparison.
- **Units**: inches ↔︎ centimeters (inputs convert both ways).
- **Snapshot (PNG)** with a **legend showing length × girth (in & cm)**.
- **Grid toggle**, **Reset view**, smooth drag‑rotate and wheel/pinch zoom.
- Single HTML file using Three.js via CDN.

---

## Quick Start

### Open locally
1. Download/clone this repo.
2. Open `index.html` in your browser.
   - If the browser blocks local resources, run a tiny server:
     ```bash
     python3 -m http.server 8080
     # Visit http://localhost:8080/
     ```

### Free hosting with GitHub Pages
1. Repo **Settings → Pages**.
2. **Source**: *Deploy from a branch*.
3. **Branch**: `main` • **Folder**: `/ (root)` • **Save**.
4. Your site: `https://<your-username>.github.io/size-comparator-3d/`

> Netlify Drop / Vercel / Cloudflare Pages work too (no build step).

---

## Controls

- **Rotate**: click‑drag (or touch‑drag)  
- **Zoom**: mouse wheel / trackpad pinch / buttons  
- **Reset view**: *Reset View*  
- **Grid**: toggle *Show grid*  
- **Export**: *Snapshot (PNG)* saves an image with labels

---

## UI Guide

- **Units**: switch **in ↔︎ cm** (values convert live).
- **A (you)**: set **Length** & **Girth** → *Save as my size*.  
  Enable *Lock A* to freeze A while you play with B/C.
- **Manual B**: enter B’s **Length** & **Girth**.
- **Partners mode**: tick *Use number of partners…* to compute:
  - **B = c‑quantile of maximum**, **C = c‑quantile of minimum**
  - Choose *Both*, *Length only*, or *Girth only*
  - Set **Partners (n)** and **Confidence (%)**
- **Presets**: quick buttons for common comparisons.

---

## Stats Model (order statistics)

Assuming independent normal distributions:

- **Length (cm)**: μ = **13.12**, σ = **1.66**  
- **Girth (cm)**:  μ = **11.66**, σ = **1.10**

Let `n` be partners and `c` be confidence in (0,1).

- Quantile of the **maximum** (value `x` with P(max ≤ x) = `c`):
  ```
  q_max = μ + σ * Φ⁻¹( c^(1/n) )
  ```
- Quantile of the **minimum** (value `x` with P(min ≥ x) = `c`):
  ```
  q_min = μ + σ * Φ⁻¹( 1 - (1 - c)^(1/n) )
  ```

`Φ⁻¹` is the inverse standard normal CDF (Acklam approximation in code).  
**Note:** *Both* treats length & girth independently.

> These are illustrative population stats; real anatomy varies.

---

## Privacy

- Everything runs locally in your browser.
- No analytics; only the Three.js CDN is fetched.
- “Save as my size” uses `localStorage` keys: `myLenIn`, `myGirIn`.

---

## Tech

- **Three.js** (from CDN)
- Single‑file app: `index.html`
- Canvas export for labeled PNG

---

## Troubleshooting

- **Blank page** → Open DevTools **Console**. If the CDN is blocked, vendor Three.js locally:  
  download `three.min.js` to repo root and change:
  ```html
  <script src="./three.min.js"></script>
  ```
- **Pages 404** → Check Settings → Pages uses `main` + `/(root)` and file is `index.html` at repo root. Wait ~1 minute.
- **Blurry PNG** → Make browser window larger (export uses current canvas size).

---

## License

MIT
