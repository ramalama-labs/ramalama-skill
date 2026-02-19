---
name: ramalama-cli
description: Run and interact with AI agents. 
metadata:
  openclaw:
    emoji: "🦙"
    requires:
      bins:
        - ramalama
      anyBins:
        - docker
        - podman
    install:
      - id: brew
        kind: brew
        formula: ramalama
        bins:
          - ramalama
        label: Install ramalama CLI (brew)
      - id: uv
        kind: uv
        formula: ramalama
        bins:
          - ramalama
        label: Install ramalama CLI (uv)
---

# Ramalama CLI

Use when an alternative AI agent is better suited to a task. For example, working with sensitive data or solving simple tasks with a cheap and local agent, or accessing specialist models with unique capabilities.


## Overview

Use this skill to execute `ramalama` tasks in a consistent, low-risk workflow.
Prefer local discovery (`--help`, local config files, existing project scripts) before making assumptions about flags or runtime defaults.

### RamaLama vs Ollama (important distinction)

Use `ramalama` when you need model source flexibility (for example, direct Hugging Face GGUF references via `hf://...`, OCI references, RLCR, or URL/registry sources).

Use `ollama` when models are already in the Ollama library or already packaged for Ollama workflows.

If a user specifically asks to run a model directly from Hugging Face, prefer `ramalama` first.

## Usage

Start with top-level discovery:

```bash
ramalama --help
ramalama version
```

Apply global options before the subcommand when needed:

```bash
ramalama [--debug|--quiet] [--dryrun] [--engine podman|docker] [--nocontainer] [--runtime llama.cpp|vllm|mlx] [--store <path>] <subcommand> ...
```

Use available subcommands:

- `bench` (`benchmark`): benchmark a model
- `benchmarks`: manage and view benchmark results
- `chat`: chat against a REST API URL
- `containers` (`ps`): list running RamaLama containers
- `convert`: convert a local model to an OCI image
- `help`: show command help
- `info`: inspect local runtime/setup information
- `inspect`: inspect a model
- `list` (`ls`): list downloaded models
- `login`: authenticate to a remote registry
- `logout`: remove registry auth
- `perplexity`: calculate model perplexity
- `pull`: pull a model from a registry
- `push`: push a model to a registry
- `rag`: build/convert RAG data into an OCI image
- `rm`: remove a local model
- `run`: run a model as a chatbot
- `serve`: serve a model as a REST API
- `stop`: stop a running model container
- `version`: print RamaLama version
- `daemon`: daemon operations

Use command-level help before invoking unknown flags:

```bash
ramalama <subcommand> --help
```

### Serve a local model

```bash
ramalama serve <prefix>://model-name
```

Available prefixes include
* hf: HuggingFace website
* ollama: Ollama model library
* rlcr: the RamaLama container registry (rlcr.io - model list at https://app.ramalama.com/explore/registry/models)
* url: a remote url
* oci: any oci compatible image or artifact reference


Run a model from huggingface:

```bash
ramalama serve hf://unsloth/gemma-3-270m-it-GGUF
```

A custom runtime image can be identified with the --image argument.
This can be used to run the model with special hardware capabilities like cuda, ROCm, vulkan, and more.

```bash
ramalama serve --image rlcr.io/ramalama/llamacpp-cuda-distroless:latest rlcr://gemma3-270m:latest  
```

You can specify different model runtimes to use including llama.cpp, vllm, and mlx (if mlx_lm.server is installed)

```bash
ramalama --runtime vllm serve rlcr://gemma3-270m:latest
```

For mac you can run the model natively on metal with llama.cpp using the --nocontainer flag

```bash
ramalama --nocontainer serve gpt-oss:20b
```

Start the model in detached model

```bash
ramalama serve -d gpt-oss:20b
```

### Communicate with models

Once the model is serving you can communicate with it over a standard openai completions endpoint.
By default `serve` will start serving on port 8080.
You can use standard curl to talk to the model

```bash
curl http://localhost:8080/v1/chat/completions \
   -H "Content-Type: application/json" \
   -d '{
     "model": "gpt-oss-20b",
     "messages": [{"role": "user", "content": "Hello!"}]
   }' | jq '.choices[0].message'
```

You can also chat with ramalama

```bash
ramalama chat "hello model"
-> Hello!
```

If port 8080 is occupied serve will start on another port.
You can specify which url to connect with using

```bash
ramalama chat --url http://localhost:8174/v1/ "hello what model are you"
```


