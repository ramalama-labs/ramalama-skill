# Model Guide

This reference covers common open-weight models and practical selection tips for RamaLama workflows.

## Popular Models

### Chat/General Models

| Model | Params | Best For | Notes |
|-------|--------|----------|-------|
| Llama 3.1 Instruct | 8B / 70B / 405B | General assistant chat, multilingual use | Strong baseline family with long context variants; larger sizes improve reliability but need more hardware. |
| Qwen 2.5 Instruct | 0.5B–72B | Balanced chat + structured output | Good instruction following, JSON output, and long-context handling. |
| Gemma 2 Instruct | 2B / 9B / 27B | Efficient local assistants | Good quality-per-parameter for local inference and low-latency flows. |
| Mistral Nemo Instruct | ~12B | General-purpose chat and writing | Strong mid-size option; often good speed/quality tradeoff on consumer GPUs. |
| Granite 3.3 Instruct | 2B / 8B | Lightweight local chat and enterprise-ish tasks | Practical for CPU/Mac workflows; useful for quick local assistant tasks. |

### Coding Models

| Model | Params | Best For | Notes |
|-------|--------|----------|-------|
| Qwen2.5-Coder Instruct | 1.5B / 7B / 32B | Code generation, debugging, refactors | Strong code-focused open model family across multiple sizes. |
| DeepSeek-Coder-V2 | 16B / 236B (MoE family) | Complex coding + long-repo context | High capability on difficult coding tasks; heavier runtime requirements. |
| CodeLlama Instruct | 7B / 13B / 34B | Fast local coding assistant | Mature and widely available quantizations. |
| StarCoder2 | 3B / 7B / 15B | Repo-level completion + multilingual code | Strong open coding baseline with permissive ecosystem support. |
| Codestral (open-weight release variants) | ~22B class | Advanced coding and agentic code tasks | Higher quality but typically needs stronger hardware. |

### Embedding Models

| Model | Params | Dimensions | Notes |
|-------|--------|------------|-------|
| BAAI/bge-m3 | ~560M class (BERT-large scale) | 1024 | Strong multilingual retrieval; supports dense/sparse/multi-vector retrieval patterns. |
| nomic-embed-text-v1.5 | ~100M class | 768 (Matryoshka-resizable) | Very practical for RAG; supports task prefixes like `search_query:` and `search_document:`. |
| intfloat/e5-large-v2 | ~335M | 1024 | Strong text retrieval baseline; robust and widely adopted. |
| sentence-transformers/all-MiniLM-L6-v2 | ~22M | 384 | Very fast and lightweight embeddings for local/edge use. |
| jina-embeddings-v3 | ~500M class | 1024 (task-aware) | Good multilingual/document retrieval performance with long-context support. |

## Model Selection Guide

### By Task Type

- **Quick questions**: 1B–4B instruct models (e.g., Granite 2B, Gemma 2 2B)
- **General chat**: 7B–12B instruct models (Qwen 2.5 7B, Mistral Nemo)
- **Coding**: code-specialized 7B+ models (Qwen2.5-Coder 7B/32B, StarCoder2)
- **Complex reasoning**: larger 30B+ class models or MoE families
- **Creative/design**: 9B–27B instruct models with higher context and temperature tuning
- **Embeddings**: BGE-M3 / Nomic / E5 depending on language mix + latency needs

### Tool Use Support

Models with generally good tool/function calling behavior:

- Qwen 2.5 Instruct family
- Llama 3.1 Instruct family
- Mistral Instruct family
- Granite Instruct family

Tip: for any model, improve tool reliability with explicit JSON schemas, strict system instructions, and low temperature.

## OpenClaw Integration

Use this guide to pick a model before calling RamaLama commands in skills.

Recommended pattern:

1. Choose model class from the task section above.
2. Start with one-shot validation:
   ```bash
   ramalama run <model> "Reply with a 1-line readiness check."
   ```
3. For services, switch to:
   ```bash
   ramalama serve <model>
   ```
4. For RAG, package docs then query with `--rag`:
   ```bash
   ramalama rag ./docs my-rag
   ramalama run --rag my-rag <model> "Answer from docs only."
   ```

## Hardware Considerations

- **8GB VRAM**: target ~7B class for comfortable performance; 13B may require aggressive quantization/offload.
- **16GB VRAM**: 7B–14B runs well; some 20B+ models are possible with quantization/offload tradeoffs.
- **CPU offload**: llama.cpp can offload to CPU/RAM, but latency rises significantly as offload increases.
- **Macs**: try `--nocontainer` first to use native Metal paths where available.
- **Larger models**: expect slower startup and generation; reduce context (`-c`) first when memory pressure appears.
