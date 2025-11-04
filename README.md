# 3D Length and Girth Comparator

A web app to compare 3D penis-like models side by side. Rotate, zoom, switch units, save your size, estimate likely biggest/smallest sizes from a partner count, and export a labeled PNG. Runs 100% in the browser.

https://bean141.github.io/size-comparator-3d/

---

## Features

- A/B/C comparison
  - A (you): can be saved to browser localStorage and locked
  - B: manual size or computed max at confidence c from partners n
  - C: computed min at confidence c from partners n
- Bottoms aligned for clean length comparisons
- Units toggle (inches <-> centimeters) with live conversion
- Presets for common comparisons
- Snapshot (PNG) with a legend that labels each model's length x girth in both in/cm
- Grid toggle, Reset View, smooth drag rotate and wheel/pinch zoom
- Single HTML file using Three.js via CDN (can be vendored locally)

---

## Statistics and Methods (what the app is doing)

This tool is an illustrative visualizer based on simple, transparent assumptions. It does not predict individuals.

### Population parameters (defaults)
We use global erect measurements reported by Veale et al. (2015, BJU International):
- Erect length (cm): mean mu_L = 13.12, SD sigma_L = 1.66
- Erect circumference / girth (cm): mean mu_G = 11.66, SD sigma_G = 1.10

These are close to the global means, although sampling, geography, and methods differ.

### Distributional assumption
Following Veale's nomogram approach, we model erect length and girth separately as approximately normal in the general population. The app treats length and girth as independent when "Both" is selected. This keeps the math and UI simple; it is a useful approximation near the center of the distribution.

### Number of partners

If someone has been with n partners, the biggest and smallest sizes they’ve encountered won’t sit at the population average; they’ll drift toward the tails just because you sampled multiple people. You want a principled, non-anecdotal way to turn “n partners” into plausible extremes to compare against.

Why use order statistics?

Order statistics are the math for “the biggest of n” and “the smallest of n.”
They let us answer questions like:

Max bound (B): “With c% confidence, the largest she’s seen is ≤ q_max.”

Min bound (C): “With c% confidence, the smallest she’s seen is ≤ q_min.” (an upper bound on the minimum—see alt below)

This converts “how many partners?” into realistic ceilings/floors instead of guesswork.

Given n partners and a confidence level c in (0,1), the app estimates a size q so that:
- Max case (B): P(max <= q_max) = c
- Min case (C): P(min >= q_min) = c

Assuming independent observations from N(mu, sigma^2), the closed-form solutions are:
```
q_max = mu + sigma * Phi^{-1}( c^(1/n) )
q_min = mu + sigma * Phi^{-1}( 1 - (1 - c)^(1/n) )
```
where Phi^{-1} is the inverse standard normal CDF.

Implementation detail: the code uses the Acklam approximation for the normal quantile (fast and accurate for UI use).

Mode handling in the UI:
- "Length only": apply the quantile to length; keep girth equal to A (you)
- "Girth only": apply the quantile to girth; keep length equal to A (you)
- "Both": apply the quantiles independently to length and girth (simplifying assumption)

### Caveats
- Independence is an approximation; a full joint model is out of scope here.
- Populations, regions, ages, and measurement protocols vary. Newer studies discuss regional differences and possible temporal changes; we keep a fixed baseline so comparisons are interpretable.
- For small n, order-statistic quantiles can be jumpy; confidence intervals around those quantiles are not shown.

---

## Customizing the model (edit index.html)

Open `index.html` and locate these constants (values are centimeters):
```js
// Population parameters (cm)
const MU_LEN = 13.12, SD_LEN = 1.66;  // erect length
const MU_GIR = 11.66, SD_GIR = 1.10;  // erect circumference (girth)
```
To use the country specific sample means as a rough baseline:
```js
const MU_LEN = 13.01;  // keep SD unless you have robust local SD
const SD_LEN = 1.66;
const MU_GIR = 11.46;
const SD_GIR = 1.10;
```
You can also add a UI toggle to switch baselines.

---

## Controls

- Rotate: drag (mouse/touch)
- Zoom: wheel / pinch / buttons
- Reset view: "Reset View"
- Grid: toggle "Show grid"
- Export: "Snapshot (PNG)" saves a labeled image

---

## Privacy

- 100% client-side.
- Only external fetch is the Three.js CDN.
- "Save as my size" uses localStorage keys: `myLenIn`, `myGirIn`.

---

## Tech

- Three.js (CDN)
- Single-file app: `index.html`
- Canvas export for labeled PNG

If your network blocks CDNs, vendor Three locally by downloading `three.min.js` to the repo root and replacing the script tag:
```html
<script src="./three.min.js"></script>
```

## References

Veale D. et al. (2015). "Am I normal? A systematic review and construction of nomograms for flaccid and erect penis length and circumference...". BJU International.
PubMed: https://pubmed.ncbi.nlm.nih.gov/25487360/
PDF: https://drjromero-otero.com/wp-content/uploads/2018/08/Veale_et_al-2015-BJU_International.pdf

Belladelli F. et al. (2023). "Worldwide Temporal Trends in Penile Length". World Journal of Men's Health.
PDF: https://wjmh.org/pdf/10.5534/wjmh.220203

Mostafaei H. et al. (2025). "A Systematic Review and Meta-Analysis of Penis Length and Circumference according to WHO Regions". Urology Research & Practice.
PMC: https://pmc.ncbi.nlm.nih.gov/articles/PMC11923605/

Acklam P. J. "An algorithm for computing the inverse normal cumulative distribution function".
Overview: https://stackedboxes.org/2017/05/01/acklams-normal-quantile-function/

---

## License

MIT
