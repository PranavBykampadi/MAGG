# Experiments

Benchmark-specific build and evaluation scripts for the experiments reported in the paper. The framework code itself lives in `multi_agent_kg/`. Datasets are not redistributed here — download them from their official sources before running these scripts.

## SciERC (`scierc/`)

[SciERC](http://nlp.cs.washington.edu/sciIE/) is the benchmark used for the extraction-quality and governance-ablation experiments.

- `build_governed_scierc.py` — build a governed KG from SciERC abstracts.
- `build_ungoverned_scierc.py` — build a flat-insertion KG for the baseline.
- `run_governed_vs_ungoverned.py` — run both pipelines side-by-side on the same documents.
- `analyze_scierc_build.py` — diagnostics over a build (per-doc relation funnel, hallucination rate, etc.).
- `score_accumulated_scierc_rich.py` — strict / mapped triple F1 against the SciERC gold annotations.
- `run_governance_replay_ablation.py` and `sample_governance_ablation_triples.py` — replay candidate triples through alternative admission policies (domain rules, global reviewer, etc.) for the governance ablation table.

## MuSiQue (`musique/`)

[MuSiQue](https://github.com/StonyBrookNLP/musique) is used for multi-hop QA over an open-world governed graph.

- `prepare_musique_pilot.py` — sample a 2-hop slice from the MuSiQue dev set.
- `build_musique_governed_kg.py` / `build_musique_governed_kg_combined.py` — build the governed graph from supporting passages.
- `run_musique_kg_qa.py` — domain-routed QA over the governed graph.
- `run_musique_official_graphrag.py` — Microsoft GraphRAG baseline.
- `run_musique_rag.py` — passage-level dense-retrieval baseline.

## HotpotQA (`hotpotqa/`)

Mirrors the MuSiQue workflow on [HotpotQA](https://hotpotqa.github.io/).

- `prepare_hotpotqa_pilot.py`, `build_hotpotqa_governed_kg.py`, `run_hotpotqa_kg_qa.py`, `run_hotpotqa_rag.py`, `run_hotpotqa_official_graphrag.py`, `summarize_hotpotqa_results.py`.

## KGAFE (`kgafe/`)

KGAFE is an atomic-fact evaluation framework for KG-grounded answers (entry point: `run_kgafe.py`). It is independent of any single QA benchmark.

## General evaluation utilities

`evaluate_kg.py`, `qa_support.py`, and `run_evaluation.py` (in `experiments/`) provide shared scoring code reused by the benchmark-specific scripts above.
