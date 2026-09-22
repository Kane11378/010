# Candidate J local attempt — 2026-09-22

Scientific status: INVALID EXECUTION — NO SCIENTIFIC EVIDENCE.

## What succeeded

- WSL2 detected.
- RTX 5090 detected.
- PyTorch `2.13.0+cu130`, CUDA available.
- vLLM `0.29.0` installed.
- `SetFit/ag_news` loaded and the frozen 64-row sample / 256-target manifest were produced.
- Exact `Qwen/Qwen3-1.7B` snapshot revision was reused.

## Failure

The run stopped during vLLM engine initialization before the first target inference.

Root exception:

```text
RuntimeError: UVA is not available
```

The traceback originates from the V2 model runner creating `RequestState -> StagedWriteTensor -> UvaBuffer` under WSL2.

The progress record contains only the model-loading event. No context repeat, semantic label, stable flip, flip rate, or scientific gate statistic was produced.

## Scientific interpretation

This attempt is not a zero-flip result and must not be counted as `KILL`, `WEAK_NARROW_SIGNAL`, `SURVIVE_REALITY_GATE`, or `INCONCLUSIVE` under the scientific gate. It is an engineering/environment failure that occurred before the scientific experiment started.

## Repair boundary

A repair may change launcher/setup/preflight behavior required to make the frozen serving stack executable. It must not silently change:

- vLLM 0.29.0;
- V2 model runner to V1;
- batch-invariance mode;
- model/revision;
- dataset/sample;
- prompt bytes;
- predicates;
- decoding settings;
- contexts/order;
- repeats;
- parser;
- gate thresholds.

The currently preferred engineering hypothesis is to retain the V2 runner and opt in to WSL2 pinned memory through vLLM's documented `VLLM_WSL2_ENABLE_PIN_MEMORY=1`, followed by an author-local preflight that proves the UVA path is available before the formal run.
