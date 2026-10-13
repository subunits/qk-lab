# QK Lab

An interactive, single-file demo of **quaternionic embeddings** and **metric-based attention**, running entirely in your browser. No build step, no dependencies, no server.

**Live demo:** `https://<your-username>.github.io/qk-lab/`

## What it does

Type two sentences. The app:

1. Turns each word into a quaternion embedding (C quaternion channels per word).
2. Runs one attention layer whose Q, K and V projections use the **Hamilton product**.
3. Scores attention with a metric you choose: **Euclidean**, **spherical** or **hyperbolic**.
4. Shows the attention weights as a heatmap for each sentence, the cosine similarity of the two sentences, a live **rotation-invariance test**, and a **parameter count** against a real-valued layer.

Click or tap any heatmap cell to read its exact value. With the matrix focused, the arrow keys move the selection.

## Important: what this is not

- **The weights are random and untrained.** Similarity scores show the geometry at work, not learned meaning. Words shared between the two sentences get identical embeddings, and that is what drives the similarity number.
- **"Kähler" here means a choice of attention metric.** The app does not implement quaternionic-Kähler geometry (Sp(n)·Sp(1) holonomy). It is a Kähler-*inspired* way of swapping the geometry used to score attention.
- It runs a single attention layer on up to 16 words per sentence. It is a teaching and exploration tool, not an NLP model.

## The maths

A quaternion is `q = a + bi + cj + dk` with `i² = j² = k² = ijk = −1`. The Hamilton product is non-commutative.

**Projections.** For C input channels, each output channel is `out_j = Σ_i x_i · W_ji`, with each weight multiplied on the **right**. Because of that choice, rotating every token on the left by a unit quaternion `r` rotates the outputs by `r` and leaves attention scores unchanged. The rotation test in the app checks this numerically (differences sit at floating-point noise, around 1e-14 to 1e-16).

**Scores** (per token pair, over C channels):

| Metric | Score |
| --- | --- |
| Euclidean | `⟨q, k⟩ / √(4C)` |
| Spherical | `−κ · Σ d² / √C`, where `d` is the angle between the unit-normalised quaternions |
| Hyperbolic | `−Σ d² / √C`, where `d` is the Poincaré-ball distance after mapping `u = q/‖q‖ · tanh(√κ‖q‖)` |

Scores go through a softmax per row, values are mixed with the weights, and the result is added back to the input (residual). Sentence embeddings are the mean over tokens, compared by cosine similarity.

**Parameters.** A quaternion linear layer on C channels has `4C²` weights. A real-valued layer of the same width (4C inputs and outputs) has `16C²`. The app shows this **4× reduction per linear layer**. It is not a claim about whole models.

## Controls

| Control | Effect |
| --- | --- |
| Metric | Euclidean, spherical or hyperbolic attention scoring |
| Curvature κ | Scales the geometry. Disabled for Euclidean, where it has no effect |
| Quaternion channels C | 4, 8 or 16 channels per word |
| Seed | Changes the random embeddings and weights |
| New rotation | Draws a new random rotation for the invariance test |

## Run it locally

Open `index.html` in any modern browser. That's it.

## Publish on GitHub Pages

1. Rename `qk-nlp-lab.html` to `index.html` and commit it with this README.
2. In the repo, open **Settings → Pages**.
3. Choose the `main` branch and the `/ (root)` folder, then save.
4. After a minute or two the page is live at `https://<your-username>.github.io/<repo-name>/`.

## Responsive behaviour

The layout adapts from small phones to wide desktops, in light and dark mode.

- Heatmaps scale to their container. As cells shrink, column headers turn vertical and long words are truncated with an ellipsis. Below roughly 30 px per cell the numbers are hidden, and tapping a cell still shows the value.
- Form fields use 16 px text so iOS Safari does not zoom the page on focus.
- Left, right, top and bottom safe-area insets are respected on notched devices.

## Testing

Checked in headless Chromium (Playwright) across seven viewport sizes from 320 px to 1920 px wide, in light and dark mode, plus emulated iPhone and iPad screens in portrait and landscape. The checks cover layout overflow, text contrast, touch-target size, every control, edge-case inputs (empty text, one word, 40 words, Unicode, HTML in input), keyboard navigation, and the rotation invariance for all three metrics.

**Not yet tested:** the real WebKit engine (Safari on iPhone and iPad) and physical devices. iOS behaviour has only been covered by emulation.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: HTML, CSS and JavaScript in one file |
| `README.md` | This file |

## License

Choose a license before publishing, for example MIT to match your earlier repositories.
