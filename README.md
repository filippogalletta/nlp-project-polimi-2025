# PoliMillionaire - Autonomous NLP Agent

Autonomous Question-Answering agent developed for the **Natural Language Processing** course at **Politecnico di Milano**.

The agent is designed to play the *PoliMillionaire* live trivia game (15 progressive difficulty levels across varied categories), optimizing for answer accuracy while respecting a strict per-question latency limit (30 seconds).

---

## System Architecture

```
                       ┌─────────────────────────┐
                       │    Incoming Question    │
                       └────────────┬────────────┘
                                    │
                                    ▼
                          [ Competition Router ]
                                    │
         ┌──────────────────────────┼──────────────────────────┐
         │ (General Trivia)         │ (Maths)                  │ (News)
         ▼                          ▼                          ▼
  ┌──────────────┐          ┌──────────────┐          ┌────────────────┐
  │  Wikipedia   │          │ Theory vs.   │          │ DuckDuckGo     │
  │  RAG Engine  │          │ Practice CLF │          │ Web Retrieval  │
  └──────┬───────┘          └──────┬───────┘          └───────┬────────┘
         │                         │                          │
         │                         ├─► Theory ──► (Wiki RAG)  │
         │                         └─► Practice ─► Code Gen   │
         │                                                    │
         └──────────────────────────┬─────────────────────────┘
                                    ▼
                         ┌────────────────────┐
                         │ Qwen 2.5 7B        │
                         │ (4-bit quantized)  │
                         └─────────┬──────────┘
                                   │
                                   ▼
                         [ Extracted Answer ]
```

### Key Modules

1. **Base LLM & Prompt Optimization**
   * `Qwen/Qwen2.5-7B-Instruct` quantized in 4-bit (`bitsandbytes`) for efficient GPU memory usage and fast generation.
   * Benchmarked prompt formats, answer encodings (numeric vs. alphabetic), zero-shot, few-shot, and reasoning trade-offs against timeout limits.

2. **Wikipedia RAG Pipeline**
   * Multi-stage retrieval combining LLM-based query expansion, lexical search (**BM25**), and dense vector representations (**BAAI/bge-base-en-v1.5**).
   * Candidate merging via **Reciprocal Rank Fusion (RRF)** and precision re-ranking via **Cross-Encoder** (`BAAI/bge-reranker-v2-m3`).

3. **Specialized Mathematics Routing**
   * Supervised classifier (TF-IDF + clustering) distinguishing **Theory** from **Practice** math questions.
   * Theory questions route to conceptual Wikipedia retrieval; Practice questions generate executable Python code run in a sandboxed interpreter with fallback LLM verification.

4. **News & Current Events Handling**
   * Real-time search and article scraping (DuckDuckGo + BeautifulSoup) filtered by publication windows to answer recent events beyond LLM training cutoff.

5. **Speech-to-Text (ASR) Adaptation**
   * Benchmarked Whisper ASR models for audio-transcribed game modes.
   * Noise-tolerant prompts designed to preserve accuracy under transcription noise.

---

## Repository Contents

* [`final_nlp_notebook.ipynb`](final_nlp_notebook.ipynb): Comprehensive Jupyter Notebook containing:
  * Complete source code for all modules and pipelines.
  * Dataset analysis and split validation.
  * Comparative benchmarks (Accuracy, Latency, Failure Breakdown).
  * Pre-rendered evaluations, 36 publication-ready plots, and live-game analysis over 180+ sessions.

> **Note**: The notebook contains pre-computed logs and all visual artifacts pre-rendered. Heavy API calls and full-dataset benchmarks are commented out to prevent accidental re-execution.

---

## Authors

* **Filippo Galletta** — [GitHub](https://github.com/filippogalletta) • [LinkedIn](https://www.linkedin.com/in/filippogalletta/)
* **Cosimo Giovanni Negri** — [GitHub](https://github.com/cosimonegri) • [LinkedIn](https://www.linkedin.com/in/cosimogiovanninegri/)
* **Davide Paltrinieri** — [GitHub](https://github.com/PaltrinieriDavide/) • [LinkedIn](https://www.linkedin.com/in/davide-paltrinieri/)

