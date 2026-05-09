# VennCompare

> Set comparison, statistics, and visualization — fully in your browser.

A free, open-source, browser-only tool for comparing 2–7 sets of identifiers (genes, samples, customers, documents, ML predictions — anything). No data ever leaves your machine. Runs on Quarto + Shinylive (R / WebAssembly).

**Live:** [somatogenomics.github.io/venn-compare](https://somatogenomics.github.io/venn-compare/)

---

## What's new in v2

v1 was a clean Venn-and-bar viewer for up to 7 sets. v2 turns it into a full set-comparison workbench that beats every other browser-based tool we tested (Venny, InteractiVenn, jvenn, DeepVenn) on capability:

| Capability                     | v1   | v2   |
| ------------------------------ | :--: | :--: |
| Up to 7 sets                   | ✓    | ✓    |
| Venn (2–4 sets)                | ✓    | ✓    |
| **Venn (5 sets, ellipse)**     | —    | ✓    |
| Bar chart of intersections     | ✓    | ✓    |
| **UpSet plot (any size)**      | —    | ✓    |
| **Pairwise similarity heatmap**| —    | ✓    |
| **Statistical p-values**       | —    | ✓    |
| **Background universe**        | —    | ✓    |
| **File upload (CSV/TSV/GMT)**  | —    | ✓    |
| **Long-format upload**         | —    | ✓    |
| **Custom set algebra**         | —    | ✓    |
| **Element search**             | —    | ✓    |
| **Reproducible R code export** | —    | ✓    |
| **PNG / SVG / PDF download**   | partial | ✓ |
| **Session save / load (JSON)** | —    | ✓    |
| **Methods text + citations**   | —    | ✓    |
| Custom typography & theme      | —    | ✓    |

### The killer features

1. **Statistical significance.** Every pairwise overlap gets a one-sided Fisher's exact p-value. Every multi-way intersection gets an exact hypergeometric p-value (Wang, Zhao & Sherman, *Sci Rep* 2015). All adjusted with Benjamini–Hochberg. You finally know whether your overlap is "real" or just expected by chance — something Venny / InteractiVenn / jvenn cannot tell you.

2. **Background universe.** Specify the size of your sample frame (e.g. ~20,000 expressed genes for a transcriptomics study). v2 warns you when you're using the union as the universe (rarely what you want).

3. **UpSet plots.** Custom base-R UpSet plot that scales to any number of sets — the only sensible visualization once you have 6+ sets.

4. **Reproducibility tab.** Auto-generated R code (functions defined inline, no VennCompare dependency) that recreates every analysis. Copy-paste into any R session. Methods paragraph included for manuscripts.

5. **Custom set algebra.** Type `(GroupA & GroupB) - GroupC` to get exactly the intersection you want. Operators: `& | ! -` plus parentheses, with Unicode aliases (`∩ ∪ ¬ \`) and word forms (AND/OR/NOT) accepted.

6. **File upload.** CSV/TSV in either **wide** format (one column per set) or **long** format (id + group columns). GMT and GMX from MSigDB-style sources. Drag and drop.

7. **Element search.** "Which of my 6 sets contain `TP53`?" — answered in one keystroke.

8. **Session save/load.** Round-trip your full analysis as a JSON file. Share with collaborators.

9. **Curated examples.** Four built-in example datasets covering agnostic survey-style data, cancer pathway genes, DEG timecourse analysis, and GO-style pathway overlap.

10. **Polish.** Custom typography (Fraunces + Manrope + JetBrains Mono via Google Fonts), editorial color palette (ink / copper / paper), 7-color set palette, badge counts per set, intersection chips, pill-tags for stats.

---

## Architecture

- **One file, no build step.** Everything lives in `index.qmd`. The whole app (UI + server + helpers + plotting + parsers + statistics) is a single `{shinylive-r}` block.
- **Browser-only.** No backend. No telemetry. No CDN beyond Google Fonts.
- **Statistical engine implemented from scratch in base R** — no dependence on `eulerr`, `UpSetR`, `SuperExactTest`, `VennDiagram`, etc. (Avoids package availability issues in WebR.) Reproducibility tab points users to the proper R packages for publication output.
- **Plot output:** all four plot modes are custom base-R graphics. Exports to PNG (1600 × 1100, 180 dpi), SVG, and PDF.
- **Standard WebR packages only:** `shiny`, `bslib`, `jsonlite`. All in the default Shinylive WebR distribution.

---

## Deployment (unchanged from v1)

The repo's existing GitHub Actions workflow at `.github/workflows/publish.yml` builds and deploys to GitHub Pages on every push to `main`. It already installs `any::shiny`, `any::bslib`, `any::shinylive` — sufficient for v2 (the v2 R code uses `jsonlite` at runtime, which is in the standard WebR distribution and does not need to be added to the build-time installs).

To upgrade an existing deployment:

```bash
# In your venn-compare repo:
cp /path/to/v2/index.qmd .
git add index.qmd
git commit -m "v2.0.0 — statistics, UpSet, file upload, custom queries, reproducibility"
git push
```

GitHub Pages rebuilds automatically. Watch the Actions tab.

### Local preview

```bash
quarto preview index.qmd
```

(Requires Quarto ≥ 1.4 with the `shinylive` extension. The first preview pre-fetches WebR + R packages — that takes ~30 seconds; subsequent loads are instant.)

---

## Statistical methods

All p-values use exact tests (no asymptotic approximations).

- **Pairwise overlap:** one-sided Fisher's exact test on the 2 × 2 contingency table `(both, A only, B only, neither)`. The "neither" cell uses the user-specified universe size.
- **Multi-way intersection:** exact hypergeometric formula
  $$P(|A_1 \cap \dots \cap A_k| \geq x) = \sum_{j=x}^{\min |A_i|} \binom{N}{j} \prod_{i=1}^{k} \frac{\binom{N-j}{|A_i|-j}}{\binom{N}{|A_i|}}$$
  computed in log-space with the log-sum-exp trick. (Wang, Zhao & Sherman, *Sci Rep* 2015; doi:10.1038/srep16923.)
- **Multiple testing:** Benjamini–Hochberg FDR across the relevant family (all pairwise tests, or all degree-≥2 multi-way tests).
- **Expected intersection size:** $\mathbb{E}[|A_1 \cap \dots \cap A_k|] = N \cdot \prod (|A_i| / N)$.
- **Fold enrichment:** observed / expected.

Assumptions surfaced in the *About* tab. The "Methods text" generated under *Reproduce* paste-fits a manuscript paragraph.

---

## Cite

> Kobiowu S. *VennCompare* v2 (2026). [somatogenomics.github.io/venn-compare](https://somatogenomics.github.io/venn-compare/)

If you used the multi-set p-values, please also cite:

> Wang M, Zhao Y, Sherman BT. Efficient test and visualization of multi-set intersections. *Scientific Reports* 2015; 5: 16923. doi:[10.1038/srep16923](https://doi.org/10.1038/srep16923)

If you used UpSet plots:

> Lex A, Gehlenborg N, Strobelt H, Vuillemot R, Pfister H. UpSet: Visualization of intersecting sets. *IEEE TVCG* 2014; 20(12): 1983–1992. doi:[10.1109/TVCG.2014.2346248](https://doi.org/10.1109/TVCG.2014.2346248)

---

## Roadmap (v2.1)

- [ ] Dark-mode toggle (with palette that holds up in both modes)
- [ ] Multi-language UI (i18n)
- [ ] MSigDB / Reactome / GO browse integration
- [ ] URL-state sharing (currently replaced by JSON session save/load due to iframe sandbox limitations)
- [ ] Optional area-proportional (Euler) layouts for n=2,3
- [ ] Permutation tests for users uncomfortable with exact-test assumptions
- [ ] Bring-your-own-CDN for offline-first deployments

---

## License

MIT.
