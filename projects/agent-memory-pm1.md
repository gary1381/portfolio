# Agent Memory (pm1) — long-term memory for AI coding agents

*AI engineering · information retrieval · evaluation* · Oct–Nov 2026 · Python · [code](https://github.com/gary1381/agent-memory-pm1)

## Problem

Coding agents (Claude Code, Codex, Cursor) start every session without memory of earlier ones. The popular memory systems, such as Mem0, call an LLM on every write to extract facts, so ingesting a history is slow and costs tokens. I wanted to know how close a system can get **without any LLM calls at write time**, and to measure it fairly against the existing systems.

## Approach

- **Ingest:** store raw session records and session summaries; build a BM25 index and embeddings. No LLM calls.
- **Retrieve:** keyword and vector candidates, a cross-encoder reranker (Qwen3) over the top candidates, then pack the best records into a fixed token budget, grouped by session with one date header per session.
- **Serve:** an MCP server, so any agent that speaks MCP can search its own past work.
- **Optional:** a fact index written by an LLM once per session (an evaluation variant, not in production).

## Evaluation method

- Public benchmarks: LoCoMo (1,540 questions), LongMemEval-S, BEAM, REALTALK.
- The same answer model and prompt for every system; blind LLM judging.
- Paired bootstrap with 10,000 resamples; a difference counts only if the 95% interval excludes zero.
- Pre-registered decision rules for new features; features that helped on only one dataset were not shipped.

## Results

**LoCoMo, answer accuracy at a 4K-token budget**

| System | Accuracy | LLM calls to ingest |
|---|---|---|
| pm1 + LLM fact index (evaluation variant) | 92.2% | about 1/23 of Mem0's |
| Mem0 2.2.1 | 90.6% | 6,453 |
| **pm1, LLM-free (production)** | **89.4%** | **0** |
| Hindsight 1.0.0 | 85.7% | 742 |
| Graphiti 0.31.2 | 71.6% | 18,835 |

Production is about 1 point below Mem0. That gap comes from two separate reports, so it is a comparison, not a significance test.

**Retrieval quality.** On LongMemEval-S (470 questions), all evidence is in the top 5 for 92.3% of questions, against 84.7% for an agentmemory BM25 baseline (+7.7 points, 95% CI [+5.1, +10.4]). A CPU-only configuration without the reranker scores 92.6% on a 94-question sample, where full production retrieval scores 93.6%.

## What didn't work

- **Long-conversation summaries:** on BEAM-1M it trails Mem0 by about 11 points with the production answer prompt.
- **Latency:** median production retrieval is about 3.7 s, mostly spent in the reranker.
- **Graph recall** (two hops over entities) did not raise LoCoMo accuracy (91.8% vs 92.2% at 4K for the fact-index variant), so it stays off.
- **Error analysis at 4K:** most remaining wrong answers already had the evidence in context. Further retrieval work has a ceiling there; the answer step is the bottleneck.

## What I'd do next

Distill the reranker for lower latency, and test long-conversation summarization separately from retrieval.

---
*Built with AI coding agents; I designed the system and experiments, set the evaluation rules and reviewed every result.*
