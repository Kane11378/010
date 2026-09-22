# Candidate J reality-gate protocol

Status: topic incubation; not an adopted scientific core.

## Research object

Test whether an otherwise identical short semantic-operator call can change output solely because its physical serving context changes.

The intended causal path is:

`physical query / serving context -> batch composition, batch size, or request order -> model-serving numerics -> semantic operator label -> query result`

Ordinary run-to-run stochasticity, prompt fusion, prompt rewriting, model changes, decoding changes, and semantic-expression changes are outside this gate.

## Frozen inputs

- Model: `Qwen/Qwen3-1.7B`
- Model revision: `0fa42c2d7d84b04fafae145d7c0263b77225bbc8`
- Serving stack: vLLM `0.29.0`
- Dataset: `SetFit/ag_news`
- Resolved dataset SHA from the first local attempt: `ca5ba619eb034211db5f70932b6702efd21e7c73`
- Split: `test`
- Sample: 64 rows, sampling seed `20260922`
- Predicates: world, sports, business, sci_tech
- Target calls: 256
- Model dtype: bfloat16
- max_model_len: 2048
- gpu_memory_utilization: 0.72
- model seed: 42
- prefix caching: disabled
- temperature: 0
- max_tokens: 4
- repeats per context: 2

## Frozen serving contexts

1. singleton: batch size 1
2. batch4: batch size 4, shuffle seed 4004
3. batch16_a: batch size 16, shuffle seed 1601
4. batch16_a_reversed: same members as batch16_a, reversed request order
5. batch16_b: batch size 16, shuffle seed 1602

Prompt bytes, model/revision, decoding parameters, sampled rows, and logical predicates must remain identical across contexts.

## Validity controls

- Minimum parsed-output rate: 0.98
- Maximum within-context repeat mismatch rate: 0.001
- A stable context flip requires the same context to reproduce internally while another context yields the opposite YES/NO label.

## Frozen adjudication

- `KILL`: zero stable batch-context flips in an otherwise valid run.
- `SURVIVE_REALITY_GATE`: at least 3 stable flipped target prompts, target flip rate at least 1%, and flips span at least 2 predicates.
- `WEAK_NARROW_SIGNAL`: at least one stable flip but the survival threshold is not met.
- `INCONCLUSIVE`: parsing or repeat-control validity requirements fail.

Engineering or environment failure before valid target inference is not a scientific gate outcome.

## Execution-sensitive constraints

Because the research question concerns serving-context effects, changing vLLM version, changing between V1 and V2 model runners, enabling batch-invariant execution, changing attention/kernel backends, or otherwise changing the serving implementation is not a routine engineering repair. Such changes require Lead review before a scientific run.
