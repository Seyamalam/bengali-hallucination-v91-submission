# Huntrix · Public v91 Phase 2 inference

This is the minimal public reproducibility package for Team Huntrix’s submission
to the Bengali LLM Hallucination Detection Challenge.

## Frozen configuration

| Field | Value |
|---|---|
| Kaggle notebook | [`seyamalam/bengali-hallucination-v91-compliance-inference`, version 3](https://www.kaggle.com/code/seyamalam/bengali-hallucination-v91-compliance-inference/versions/3) |
| Hardware | **Tesla T4 ×2** |
| Runtime reported in organizer form | **0.17 hours** |
| Internet | Disabled |
| Model | [`Qwen3.6-27B-Q4_K_P`, version 1](https://www.kaggle.com/models/snowrab/qwen3-6-27b-q4-k-p/pyTorch/default/1) |
| Public knowledge/runtime dataset | [`bengali-hallucination-phase2-runtime-v91`](https://www.kaggle.com/datasets/seyamalam/bengali-hallucination-phase2-runtime-v91) |
| Public CUDA runtime | [`llama.cpp CUDA b9637`](https://www.kaggle.com/datasets/seyamalam/bengali-hallucination-llama-cpp-cuda-b9637-public) |
| Competition input | `/kaggle/input/competitions/bengali-hallucination/test set.csv` |

The notebook is offline and produces `submission.csv` directly from the raw
competition input. It never loads a saved Phase 1 prediction vector and does
not depend on the Phase 1 row count.

Version 3 completed in `440.8734s` on T4×2 and produced a CSV byte-identical
to the scored v91 submission.

## Scored reference

- Kaggle competition submission: `54836339`
- Public macro-F1: **0.967**
- Phase 1 rows: `2,516`
- Label counts: `{0: 1181, 1: 1335}`
- Expected output SHA-256:
  `0fec5d610f4e7b94ab6c46e2094d58e940b0f36781e9fa98d5466d2135474c28`

## Files

- `inference.ipynb` — frozen raw-input inference notebook.
- `FREEZE_MANIFEST.json` — exact versions, hashes, hardware, runtime, and
  reproduction result.
- `CITATIONS.md` — model, runtime, benchmark, and evidence citations.
- `DATA_SOURCES.md` — external-data register with links and license-review
  status.
- `THIRD_PARTY_NOTICES.md` — upstream licenses, attribution, and unresolved
  redistribution status.
- `METHODOLOGY_DISCLOSURE.md` — rule audit and the public-source verification
  question sent to the organizers.
- `Huntrix_Technical_Report.pdf` — four-page technical methodology report.
- `LICENSE` — MIT license for original Huntrix code only.

No competition test data, generated predictions, private credentials, model
weights, or runtime corpora are stored in this GitHub repository.

The scored router includes short, citation-backed factual answer records
researched from public sources. Because Kaggle Foundational Rule 4(b) can be
read to restrict human prediction of test records, this exact method has been
disclosed to the organizers for clarification. No hidden label was available
or used; the question remains pending and is documented rather than concealed.

## Phase 2 replacement

The organizers replace only the file mounted at:

```python
TEST_PATH = Path(
    "/kaggle/input/competitions/bengali-hallucination/test set.csv"
)
```

No notebook variable needs to be edited for the held-out set. The notebook
infers `N` from that file, preserves its IDs in order, and writes exactly
`id,label`.

## Integrity

Do not update the notebook, model version, runtime datasets, or CUDA runtime
after the submission deadline. Verify all pinned hashes against
`FREEZE_MANIFEST.json`.
