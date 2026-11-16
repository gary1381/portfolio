# MemPinyin — neural reranking for a Chinese input method

*Machine learning · benchmarking* · Oct–Nov 2026 · Python, C++ · [code](https://github.com/gary1381/mempinyin)

## Problem

Chinese pinyin input methods guess which characters you mean from the letters you type. Open-source engines such as libime (used by Fcitx5 on Linux) choose the top candidate from a statistical language model and do not look at the sentence you are writing. I wanted to know how much a small neural model that reads the preceding text could improve the first choice, and at what latency.

## Approach

1. Benchmarked four open-source pinyin engines (libime, librime, libpinyin, libgooglepinyin) on the same sentences.
2. Took libime's N-best candidates and reranked them with an int8-quantized `bert-base-chinese` that scores each candidate together with the preceding context.
3. Tested on 360 new sentences that were not used during development, with paired significance tests against the baseline.

## Results

| 360 test sentences | Top-1 accuracy | p vs baseline |
|---|---|---|
| libime (current Fcitx5) | 72.8% (262) | — |
| + BERT rerank of the top 3 candidates | 80.0% (288) | 1.3e-5 |
| + BERT rerank of all ~16 candidates | 83.9% (302) | 4.6e-8 |

## Limits

- The reranker runs as an offline script; it is not yet integrated into the input method.
- Latency is the main obstacle to integration. Reranking the top 3 takes 260 ms at the median and 440 ms at p95. Reranking all ~16 candidates takes 3.2 s at the median.
- The test sentences come from PerLTQA (CC BY-NC 4.0), so the repository includes a rebuild script and per-item correctness flags, not the sentences themselves. `bench/reproduce_tables.py` recomputes every headline number from the stored outputs.

---
*Built with AI coding agents; I designed the experiments and reviewed every result.*
