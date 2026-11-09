# Cross-Lingual and Multilingual Information Retrieval

A technology review I did for CS 410 (Text Information Systems) at UIUC, Fall 2026. It covers how a search system can take a query in one language and find relevant documents written in another, and where these systems still fall short.

- **Slides:** [slides/CrossLingual_IR_TechReview.pdf](slides/CrossLingual_IR_TechReview.pdf)
- **Video presentation:** [Google Drive](https://drive.google.com/file/d/11czwancLJvh6d3GpDfHhYg-C1wq6sOuH/view?usp=sharing)
- **Annotated resources (14):** [TextData community](https://textdata.org/community/6af11ce3a8567d79c2f88579)
- **Demo:** [demo.ipynb](demo.ipynb)

## The problem

If I search in English for "how do vaccines work" and the best answer is an Arabic sentence, a keyword ranker like BM25 scores it 0, since the two texts share no words. Cross-lingual retrieval (CLIR) tries to make matching meaning count when matching words is impossible. Multilingual retrieval (MLIR) goes further and ranks documents from many languages in one list.

## Three ways across the language barrier

| Approach | How it works | Tradeoff |
| --- | --- | --- |
| Translate the query | Machine-translate the query, then run BM25 | Cheap, but short queries are easy to mistranslate |
| Translate the documents | Translate the collection offline, search in one language | Better translations, expensive at scale |
| Shared embedding space | One multilingual encoder maps every language into the same vector space | No translation at search time; harder to explain and can favor some languages |

The review focuses on the third approach: how models like [multilingual-E5](https://arxiv.org/abs/2402.05672) and [BGE-M3](https://arxiv.org/abs/2402.03216) are trained (contrastive learning, knowledge distillation, translate-train), how they are evaluated ([MIRACL](https://aclanthology.org/2023.tacl-1.63/), [TREC NeuCLIR](https://neuclir.github.io/), [MMTEB](https://arxiv.org/abs/2502.13595), [CIRAL](https://ceur-ws.org/Vol-3681/T2-1.pdf)), and the language bias found in multilingual RAG ([Ki et al.](https://arxiv.org/abs/2509.13930), [Park et al.](https://arxiv.org/abs/2601.02956)).

## Demo

An English query against three short documents, using `intfloat/multilingual-e5-base`:

| Document | Language | Cosine similarity |
| --- | --- | --- |
| Vaccines train the immune system to recognize viruses | Arabic | **0.822** |
| Paella has rice and saffron | Spanish | 0.723 |
| The stock market fell on Monday | English | 0.715 |

The Arabic document ranks first with no words in common with the query. The English document, in the same language as the query, ranks last because it is about something else.

To run it, open `demo.ipynb` in Google Colab, or:

```bash
pip install -r requirements.txt
jupyter notebook demo.ipynb
```

## What I took away

1. A shared-embedding model plus a reranker is a reasonable starting point for multilingual search in a small project.
2. Benchmarks don't match how people type. MIRACL's Arabic data comes from Wikipedia, which is mostly formal written Arabic. Many people search in dialect or in Arabizi (Arabic written with Latin letters and numbers), and I'd like to see evaluation that includes that.
3. Language bias starts at retrieval, before an LLM writes anything, so checking which languages the top results come from should be a normal evaluation step.
