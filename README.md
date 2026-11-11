# mlr-ex08 — Leaf Classifier (live camera, in-browser ONNX)

Classifies leaf photos into **apple / cherry / chestnut / maple** directly in
the browser with `leaf_model.onnx` (MobileNetV3, 96×96 input) via
[onnxruntime-web](https://cdn.jsdelivr.net/npm/onnxruntime-web/) from a CDN.
Single file: `index.html`, plain JavaScript.

**Live URL:** <https://johana-huzevkova.github.io/mlr-ex08/> — the in-page QR
code and link are generated from that constant (`SITE_URL` near the top of the
script in `index.html`); update it if the URL changes.

## Features

- **Live rear-camera classification** (`getUserMedia`, `facingMode: environment`)
  with per-class probability bars, updated continuously.
- **Photo upload fallback** — identical preprocessing path.
- iOS Safari support: `playsinline muted` video, WASM execution provider with
  `numThreads = 1` (GitHub Pages has no cross-origin isolation, so
  SharedArrayBuffer is unavailable), HTTPS-only camera requirement.
- A QR code (generated locally in the browser with `qrcode-generator`, no
  external QR service) + clickable link to the published page at the bottom.

## Preprocessing (follows `model_info.json`)

1. Central square crop of the frame.
2. Resize to 96×96 (`input_size`).
3. RGB scaled to 0–1.
4. Per-channel standardization with `mean [0.485, 0.456, 0.406]`,
   `std [0.229, 0.224, 0.225]`.
5. Input tensor: `float32`, shape `1×3×96×96`, name `image`.
6. Model output is named `logits` → softmax is applied to get probabilities.

## Run locally

Any static file server works (the camera additionally needs HTTPS or localhost):

```
python -m http.server 8765
# open http://127.0.0.1:8765/index.html
```

## Verification

- Headless self-test hook: open `index.html#selftest=check_image.jpg` — it runs
  the normal pipeline on `check_image.jpg` and writes the exact probabilities
  into `<pre id="selftest-result">`. Verified (Edge headless + onnxruntime-web
  1.31.1): probabilities match `check_expected.json` within ~1e-7.
- Independent reference check with onnxruntime (CPU) reproduces
  `check_expected.json` within ~2e-7.
- QR round-trip (qrcode-generator encode → jsQR decode): decoded URL equals
  `SITE_URL` exactly.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole web app |
| `leaf_model.onnx` | MobileNetV3 leaf classifier (6.0 MB) |
| `model_info.json` | Classes, input size, mean/std |
| `check_image.jpg` / `check_expected.json` | Self-test image and expected probabilities |
