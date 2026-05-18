## Self-RAG Pipeline (LangGraph + Ollama)

A step-by-step Self-Reflective RAG system built with LangGraph, running fully locally via Ollama — no OpenAI API required.

### Models Used
- **LLM:** `qwen3.5:latest` (via Ollama)
- **Embeddings:** `embeddinggemma:300m` (via Ollama)
- **Vector Store:** FAISS

### Pipeline Steps

| Step | What it adds |
|------|-------------|
| **Step 1** | Base RAG — decide retrieval → retrieve → generate direct or from docs |
| **Step 2** | Relevance filtering — filters retrieved docs before generation |
| **Step 3** | RAG generation — generates answer strictly from relevant context |
| **Step 4** | IsSUP check — verifies if the answer is supported by the context (`fully_supported`, `partially_supported`, `no_support`) |
| **Step 5** | Revise loop — if not fully supported, rewrites the answer using only direct quotes from context, loops back to IsSUP |
| **Step 6** | IsUSE check — verifies if the final answer actually addresses the question (`useful` / `not_useful`) |
| **Step 7** | Query rewriting — if answer is not useful, rewrites the retrieval query and retries the full pipeline |

### How it works

```
Question
  └─► Decide Retrieval
        ├─► [No] Generate Direct ──► END
        └─► [Yes] Retrieve
                └─► Relevance Filter
                      ├─► [No relevant docs] No Answer Found ──► END
                      └─► [Relevant docs] Generate from Context
                                └─► IsSUP Verify
                                      ├─► [Not supported] Revise Answer ──► IsSUP (loop)
                                      └─► [Fully supported] IsUSE Check
                                                ├─► [Useful] END
                                                ├─► [Not useful, retries left] Rewrite Query ──► Retrieve (loop)
                                                └─► [Not useful, max retries] No Answer Found ──► END
```

### Documents
The pipeline is built around three internal company PDFs:
- `Company_Policies.pdf`
- `Company_Profile.pdf`
- `Product_and_Pricing.pdf`

### Tech Stack
- [LangGraph](https://github.com/langchain-ai/langgraph)
- [LangChain](https://github.com/langchain-ai/langchain)
- [Ollama](https://ollama.com)
- [FAISS](https://github.com/facebookresearch/faiss)
