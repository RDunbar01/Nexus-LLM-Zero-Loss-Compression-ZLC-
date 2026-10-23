# Nexus ZLC

Experimental lossless GGUF compression, exact restoration and paired evaluation of supported compressed model representations. The published **`Nexus-ZLC.html`** application is unchanged by this documentation update.

[Application HTML](Nexus-ZLC.html) · [Recorded results and technical notes](docs/README_PREVIOUS.md)

## What it does

- Compress supported F16/F32 layouts into F16TRI1 and supported Q8_0 data into NHDMOD1 containers without adding another quantization step.
- Restore original GGUF bytes, including metadata and padding, with SHA-256 and exact-byte checks.
- Inspect container bounds, tensor layouts and per-region integrity information before decoding.
- Evaluate admitted SmolLM2-135M-Instruct F16/F16TRI1 and Q8_0/NHDMOD1 pairs with a custom WebGPU backend.
- Compare original and compressed logits and perplexity while reconstructing operands inside kernels rather than allocating a fully expanded weight matrix.

## Engineering contribution

Developed by **Rich Dunbar / Nexus Emerging Technology with AI-assisted coding**. The contribution is the integration of GGUF-oriented lossless containers, exact restoration, kernel-local operand reconstruction and matched before/after evaluation in one inspectable application.

Dictionary coding, lossless model compression and compressed-weight execution have established prior work. The [technical notes](docs/README_PREVIOUS.md#academic-context) credit CER/CSER, ZipNN, DFloat11 and ZipServ; this project does not claim to have invented those fields or to outperform those systems. Shared cross-model dictionaries remain a research proposal, not an implemented capability.

The monolithic HTML keeps the interface, codecs and browser evaluation code together to simplify local inspection and online trial packaging without a build step. No hosted trial has been verified for this repository. Model files remain separate.

## Recorded results

The preserved SmolLM2-135M-Instruct experiments report **27.76% complete-file reduction for F16** and **3.01% for Q8_0**. Two matched 128-target software-adapter pilots recorded identical original/compressed logits and perplexity within each representation.

These are short-prefix results for one checkpoint family, not full-corpus scores or physical-Radeon benchmarks. Smaller files do not establish equivalent reductions in total VRAM, faster generation or unchanged capability on every task. Exact byte restoration and successful inference are separate checks.

## Inspect locally

1. Download and open [`Nexus-ZLC.html`](Nexus-ZLC.html). GitHub's source viewer does not run it.
2. Select a supported original GGUF, compress it and verify Restore against the original. Keep the original reference.
3. For an admitted model pair, run the evaluator and retain hashes, target count, context and runtime details with the result.

The browser application needs **neither Python nor PyTorch** and uses no remote inference service. If WebGPU is unavailable from a local-file origin, optionally serve the directory with `python -m http.server 8000 --bind 127.0.0.1`, then open `http://127.0.0.1:8000/Nexus-ZLC.html`. Python only serves the file.

The codec accepts bounded little-endian GGUF v2/v3: original model at most **512 MiB**, tensor and metadata prefix at most **64 MiB** each, and manifest at most **16 MiB**. Inference is restricted to the admitted SmolLM2 pairs and validated filename-only aliases. Arbitrary GGUF, Mistral 14B and unsupported layouts are not qualified.

## Credits and licensing

Credit HuggingFaceTB for SmolLM2, bartowski for the evaluated GGUF artifacts, the WikiText authors and maintainers, ggml/llama.cpp contributors and the cited research teams. Model, dataset and third-party terms remain separate.

The [historical package record](docs/README_PREVIOUS.md#credits-and-licensing) describes its inspected app package as `UNLICENSED`, without a package LICENSE. The repository separately contains an existing Apache 2.0 [LICENSE](LICENSE). These are different source scopes; this README does not resolve that discrepancy, alter either record, revoke previously granted rights or grant new reuse permissions.
