# LLM Evals with Promptfoo

Learning project to apply QA practices to Large Language Models (LLMs), using local models and an evaluation framework.

## What this project does

Runs automated evaluations against a local LLM (qwen2.5:7b via Ollama) to validate that its responses meet expected criteria.

## Stack

- [Promptfoo](https://www.promptfoo.dev/) — LLM evaluation framework
- [Ollama](https://ollama.com/) — runs models locally, no API cost
- qwen2.5:7b — model under test

## Key QA concepts applied

- **Non-deterministic testing**: LLMs don't always answer with the same text, so assertions validate meaning instead of exact matches (e.g. accepting both "París" and "Paris").
- **Cache awareness**: Promptfoo caches responses by default. Running with `--no-cache` forces fresh answers to detect inconsistent behavior.
- **Metrics beyond pass/fail**: latency and token usage are tracked, since they translate to performance and cost in real applications.

## How to run

Requirements: Node.js >= 22.22 and Ollama installed.

    ollama pull qwen2.5:7b
    npx promptfoo@latest eval
    npx promptfoo@latest view

## Roadmap

- [ ] LLM-as-a-judge assertions (tone, hallucinations)
- [ ] Security testing (prompt injection)
- [ ] CI integration with GitHub Actions
