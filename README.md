# Portfolio — Gary (Chaoxian) Xiao

Data scientist and AI engineer with a Ph.D. in computational chemistry. Most of my recent work is about one question: **how do you know an AI system actually works?** Each project below has a baseline, a held-out or pre-registered evaluation, and a section on what didn't work.

I build with AI coding agents (Claude Code, Codex, Cursor). I design the systems and experiments, set the evaluation rules, and review the results.

## Projects

### Precision Memory — long-term memory for AI coding agents
*AI engineering · retrieval · evaluation* · [case study](projects/precision-memory.md) · [code](https://github.com/gary1381/agent-memory-pm1)

Coding agents forget earlier sessions. Popular memory systems call an LLM on every write. Precision Memory ingests with no LLM calls and retrieves with BM25, embeddings, session summaries and a cross-encoder reranker, served to Claude Code, Codex and Cursor over MCP.

| LoCoMo, 1,540 questions, 4K-token budget | Answer accuracy | LLM calls to ingest |
|---|---|---|
| Mem0 2.2.1 | 90.6% | 6,453 |
| **Precision Memory (LLM-free)** | **89.4%** | **0** |
| Precision Memory + fact index (eval path) | 92.2% | ~1/23 of Mem0 |
| Hindsight 1.0.0 | 85.7% | 742 |
| Graphiti 0.31.2 | 71.6% | 18,835 |

Same answer model for every system, blind LLM judging, paired bootstrap 95% intervals. On LongMemEval-S retrieval, all evidence is in the top 5 for 92.3% of questions, against 84.7% for a BM25 baseline. **Limits:** it trails Mem0 by about 11 points on BEAM-1M long-conversation summaries, and median production retrieval takes about 3.7 s, mostly reranking.

### OpenPDF — a local PDF editor for people and a PDF toolkit for AI agents
*AI engineering · document AI · product* · [case study](projects/openpdf.md) · [code](https://github.com/gary1381/local-pdf-toolkit) · [installers v0.2.0](https://github.com/gary1381/openpdf-releases)

A desktop app and browser editor that runs entirely on your own computer, plus REST and MCP tools so AI agents can read, OCR, fill, sign and redact PDFs. On olmOCR-Bench (1,403 PDFs) its Markdown for agents scores 71.3 on the non-math categories, level with Docling (71.6) at 2.4× the speed and ahead of PyMuPDF4LLM (50.2). In a 13-document redaction test, nothing leaked from OpenPDF's output, while PyMuPDF and Stirling-PDF left secrets in 4–5 documents.

### DS Harness — making AI agents' data analysis verifiable
*Data science · experiment design* · [case study](projects/ds-harness.md) · [code](https://github.com/gary1381/ds-agent-harness)

A thin layer between an AI agent and the data (context, verification, delivery gates, an append-only registry), plus an evaluation loop that tests whether it really reduces answers that *look* right but are wrong. About 4,100 real-agent runs (Claude Code, Codex) across seven pre-registered experiments, with blind questions and blind two-model labels.

- Synthetic data: silent errors 3.9% with the gate vs 20.2% without.
- Real retail data (UCI): 0.0% vs 7.9%, but the gate held back 26.5% of correct answers.
- Reported as not met or inconclusive where that is what the data said (experiments E4, E3b, E3c), including cases where the gate's own feedback caused errors.

### MemPinyin — neural reranking for a Chinese input method
*Machine learning · benchmarking* · [case study](projects/mempinyin.md) · [code](https://github.com/gary1381/mempinyin)

Benchmarked four open-source pinyin engines, then reranked libime's N-best candidates with an int8 BERT model that uses the preceding text.

| 360 new test sentences | Top-1 accuracy | p (vs baseline) |
|---|---|---|
| libime (current Fcitx5) | 72.8% | — |
| + BERT rerank, top 3 | 80.0% | 1.3e-5 |
| + BERT rerank, all ~16 candidates | 83.9% | 4.6e-8 |

### Earlier (2024)
- [Text summarizer](https://github.com/gary1381/Text_summarizer) (fine-tuned Pegasus) · [Streamlit chatbot](https://github.com/gary1381/chatbot_streamlit)

## Background

- Senior Data Scientist, Bluestem Brands (Fingerhut), 2023–2025: XGBoost credit, payment and customer-behavior models; stress testing and swap analysis; model-monitoring dashboards.
- Co-founder / scientist at two small analytics companies: churn and sales forecasting.
- Postdoc and Assistant Scientist, Iowa State University: computational chemistry and ML for materials. Papers in JACS and ACS Catalysis ([Scholar](https://scholar.google.com/citations?user=85iI4ZQAAAAJ)).
- Ph.D. Physical Chemistry / Computer Modeling, Peking University.

[LinkedIn](https://www.linkedin.com/in/garycxiao/)
