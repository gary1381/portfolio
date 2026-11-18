# rankai — a bilingual job and collaboration search engine, tested on new queries every round

*Search and ranking · data pipelines · evaluation* · Oct–Nov 2026 · Python · [code](https://github.com/gary1381/rankai)

## Problem

Someone looking for "a remote part-time RAG engineer role, at least $60/h", or for a full-time frontend job in Hangzhou, has to search many boards: company career pages, remote-job boards, government job services, forums and GitHub bounties. Many postings are duplicates or closed, and Chinese queries match almost nothing in a mostly English corpus. I wanted to know which parts of a modern ranking stack actually help, measured on queries the system had never seen.

## Approach

- **Ingest:** 15 public sources via official APIs, RSS feeds and public ATS endpoints (Greenhouse, Ashby, Lever, SmartRecruiters, remote-job boards, Hacker News, Taiwan's open-data job service and others). Each source's terms were reviewed; one without documented terms is opt-in only.
- **Clean:** duplicate clustering, closed-posting checks, rule-based job type, remote flag and place.
- **Rank:** BM25 + e5 retrieval fused with RRF, an LLM query rewrite (Chinese ↔ English, constraints), a Qwen3 cross-encoder reranker, and a learned gradient-boosting ranker.
- **Serve:** an MCP server for AI agents. It returns a calibrated `verdict`, so an agent says so when no good match exists.

On 2026-11-17 the corpus held 82,169 postings in 7,298 duplicate clusters.

## Evaluation method

- Four rounds of new test queries (48 each): v0 written by the developer agent, v1–v3 by other models that never saw the corpus or the ranker.
- Two LLM labelers from different vendors grade every pooled result 0–3. A model that wrote a query set never labels it.
- nDCG@10, paired randomization tests, Holm correction; the analysis plan was fixed before each round's queries existed.

## Results

**Confirmation round v3 (48 new queries):**

| Arm | nDCG@10 |
|---|---|
| F: hybrid retrieval, reranker, LLM rewrite, hand-set penalties and priors | 0.654 |
| **J: same candidates, ordered by the reranker score alone** | **0.785** (Holm-adjusted p = 0.0009 vs F) |
| L (default): learned ranker | 0.782 (tie with J, p = 0.90) |

- **The LLM query rewrite was the biggest single gain** (v0: +0.250 nDCG@10; Chinese queries 0.37 → 0.80), and it replicated on new queries in v1.
- **Hand-set weights hurt.** Every hand-set term was neutral or harmful once the reranker was in place; all together −0.060 (exploratory).
- **Two pre-registered tests failed and are reported:** learned weights did not beat F in v2, and an extra city-level source added no coverage in v3.

## What I learned

The real bottleneck was coverage, not ranking. In v3 only 15 of 48 queries had any good posting in the pool, and every ranking arm found the same 15; all were remote roles. City-specific jobs, freelance gigs, bounties and co-founder searches went 0 for 30. Better ranking cannot fix a missing source, so the MCP server now tells agents when nothing good exists instead of returning loosely related results.

**Limits:** all relevance labels come from LLMs (weighted kappa 0.61–0.73, no human audit yet), the test queries are synthetic, and 48 queries per round can only detect large differences.

---
*Built with AI coding agents; I designed the system and experiments, set the evaluation rules and reviewed every result.*
