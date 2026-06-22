# MAGG: Multi-Agent Governed Knowledge Graph Construction

MAGG is a multi-agent framework for building **governed** knowledge graphs from text. Instead of treating a KG as a flat store of extracted triples, MAGG admits every fact through an explicit ownership, evidence, and review pathway, then reuses that same domain structure to answer questions over the resulting graph.

The framework is organized into three layers:

- **Creation layer.** A sequential extraction pipeline (segmentation → schema discovery → entity extraction → relation extraction → evidence linking → verification) with a deliberation side-loop for low-confidence items.
- **Governance layer.** Candidate triples are routed to LLM-based **Domain Expert Agents**, which review evidence and local domain memory before approving, rejecting, or revising each triple. Every admission decision is recorded.
- **Application layer.** Queries are decomposed and routed to domain experts that answer from their owned subgraphs; a synthesis agent combines the answers into a single grounded response.

## Installation

```bash
git clone https://github.com/PranavBykampadi/MAGG.git
cd MAGG
python -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
```

## Configuration

Copy `.env.example` to `.env` and set your credentials:

```bash
cp .env.example .env
# edit .env and set OPENAI_API_KEY
```

By default MAGG calls the OpenAI API. To run against a local or remote Ollama server instead, set `LLM_BACKEND=ollama` and (optionally) `OLLAMA_BASE_URL` in `.env`.

## Quick start

The minimal end-to-end demo builds a governed KG from a short document and runs a question against it:

```python
from multi_agent_kg.core.deliberative_orchestrator import DeliberativeOrchestrator
from multi_agent_kg.core.governed_kg import GovernedKnowledgeGraph
from multi_agent_kg.core.qa_orchestrator import QAOrchestrator

text = """
SciBERT is a pretrained language model for scientific text. The authors
fine-tuned SciBERT on the SciERC dataset for relation extraction and
showed that it outperforms BERT on entity and relation F1.
"""

orchestrator = DeliberativeOrchestrator()
result = orchestrator.run(document_id="demo", text=text)

governed_kg = GovernedKnowledgeGraph.from_orchestrator_result(result)

qa = QAOrchestrator(governed_kg=governed_kg)
answer = qa.query("What was SciBERT fine-tuned on?")
print(answer["final_answer_short"])
```

A runnable version of the same demo lives in `examples/run_demo.py`.

## Repository layout

```
MAGG/
├── multi_agent_kg/              # Core framework
│   ├── core/                    # Governance, deliberation, QA, memory
│   ├── agents/                  # DocumentProcessor, DomainClassifier,
│   │                            # EntityExtractor, RelationExtractor,
│   │                            # EvidenceLinker, ExtractionValidator,
│   │                            # ExtractionVerificationAgent, KnowledgeOrganizer
│   ├── llm/                     # OpenAI / Ollama client wrapper
│   └── utils/                   # Debug logger, KG visualizer
│
├── examples/                    # Runnable scripts
│   ├── run_demo.py              # Smallest end-to-end demo
│   ├── run_pipeline.py          # Generic single-document pipeline runner
│   ├── extract_pdf.py           # Build a graph from a PDF
│   └── qa_server.py             # HTTP wrapper around QAOrchestrator
│
├── experiments/                 # Benchmark-specific code (data not included)
│   ├── scierc/                  # SciERC build + governance ablations
│   ├── musique/                 # MuSiQue KG build + multi-hop QA
│   ├── hotpotqa/                # HotpotQA KG build + QA
│   └── kgafe/                   # KGAFE atomic-fact evaluation framework
│
└── tests/                       # Framework unit tests
```

## Reproducing the experiments

The `experiments/` directory contains the build and evaluation scripts used in the paper. The benchmark datasets themselves (SciERC, MuSiQue, HotpotQA) are **not** shipped in this repository; download them from their official sources and point the scripts at the local copies. See `experiments/README.md` for the per-benchmark workflow.

