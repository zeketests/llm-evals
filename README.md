# LLM Evals with Promptfoo

Learning project to apply QA practices to Large Language Models (LLMs), using local models and an evaluation framework.

## What this project does

Runs automated evaluations against a local LLM (qwen2.5:7b via Ollama) to validate that its responses meet expected criteria. Two kinds of evals:

| File | What it tests | Assertion type |
|------|---------------|----------------|
| `promptfooconfig.yaml` | Factual answers (country capitals) | Deterministic: `icontains-any` |
| `judge.yaml` | Tone/persona of a bank support agent | LLM as a judge: `llm-rubric` |

## Stack

- [Promptfoo](https://www.promptfoo.dev/) — LLM evaluation framework
- [Ollama](https://ollama.com/) — runs models locally, no API cost
- qwen2.5:7b — model under test (and also the judge)

## Eval 1: Capitals (`promptfooconfig.yaml`)

Asks the model for the capital of Argentina, France, Japan and Korea. Each test uses `icontains-any` with a list of accepted values (e.g. `["Paris", "París"]`), so the assertion validates meaning instead of an exact string.

## Eval 2: LLM as a judge (`judge.yaml`)

Some qualities (tone, persona, helpfulness) can't be checked with string matching. Here a second LLM call grades the answer against a written criterion (rubric).

- **Agent under test**: a bank customer support agent answering questions like "How do I block my credit card?".
- **Judge**: the same qwen2.5:7b, configured in `defaultTest.options.provider` so it applies to every test.
- **Rubric**: "The answer speaks as the bank itself (for example 'our app' or 'call us'), not as an outsider telling the customer to contact their bank".

### Finding

The agent answered like an outsider: *"contact your bank"* instead of speaking as the bank (*"our app"*, *"call us"*). The first rubric didn't catch it, so a more specific criterion was written for that exact bug. A good rubric must **fail** when the bug is present — otherwise it isn't testing anything.

After that, the system prompt was made explicit ("You speak as the bank. Use 'our app', 'call us', 'we can help'") to fix the behavior, and the rubric acts as a regression check.

## Key QA concepts applied

- **Non-deterministic testing**: LLMs don't always answer with the same text, so assertions validate meaning instead of exact matches.
- **LLM as a judge**: use a model to grade subjective qualities via a rubric. The rubric must be specific enough to catch the real bug.
- **Validate the test, not just the model**: confirm a criterion fails on known-bad output before trusting it when it passes.
- **Judge limitations**: using the same small model as agent and judge is cheap but biased; a stronger judge model would be more reliable.
- **Cache awareness**: Promptfoo caches responses by default. Running with `--no-cache` forces fresh answers to detect inconsistent behavior.
- **Metrics beyond pass/fail**: latency and token usage are tracked, since they translate to performance and cost in real applications.

## How to run

Requirements: Node.js >= 22.22 and Ollama installed.

    ollama pull qwen2.5:7b

    # Eval 1: capitals
    npx promptfoo@latest eval

    # Eval 2: LLM as a judge
    npx promptfoo@latest eval -c judge.yaml

    # View results in the browser
    npx promptfoo@latest view

## Roadmap

- [x] Deterministic assertions (`icontains-any`)
- [x] LLM-as-a-judge assertions (`llm-rubric`) for tone/persona
- [ ] Python + pytest
- [ ] LangSmith evals
- [ ] CI integration with GitHub Actions
- [ ] Security testing (prompt injection)
