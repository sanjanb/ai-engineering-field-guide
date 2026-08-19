# LLM Gateways
## Overview & Core Motivation

An **LLM Gateway** is a smart middleware layer that sits between your frontend/backend applications and multi-vendor LLM providers (OpenAI, Anthropic, Google Gemini, Groq, local Ollama, etc.).

---

> **Why are LLM Gateways crucial for production?**
> During the major OpenAI API outage on November 8, 2023, applications that directly hardcoded OpenAI APIs experienced complete downtime. An LLM Gateway prevents single points of failure by automatically redirecting traffic to healthy backup providers without altering application code.

#### Comparison: Without Gateway vs. With Gateway

| Requirement / Scenario | Without LLM Gateway | With LLM Gateway |
| --- | --- | --- |
| **API Integration** | Separate SDKs/APIs for every provider | **1 Unified API** call for 100+ providers |
| **Outage Handling** | App crashes or fails gracefully at best | **Automatic Fallbacks** to backup models |
| **Model Switching** | Requires code rewrites and redeploys | **Config-based changes** (Zero code change) |
| **Cost Tracking** | Dispersed across multiple dashboards | **Centralized tracking** per user, team, or route |
| **Duplicate Queries** | Re-queries LLM every time (high cost) | **Semantic / In-memory Caching** (zero cost re-query) |
| **Security & Privacy** | Custom logic needed per model API | **Centralized Guardrails** (PII redaction, injection filters) |

---

### The 8 Core Capabilities of LLM Gateways

1. **Unified API:** Call any model through a single standard format (e.g., LiteLLM `completion()` function).
2. **Automatic Fallbacks:** Switch to backup providers automatically if the primary API fails or returns error status codes.
3. **Smart Routing:** Direct prompt requests to designated models based on task type (e.g., Code $\rightarrow$ Claude 3.5 Sonnet, Summarization $\rightarrow$ GPT-4o-mini).
4. **Load Balancing:** Distribute traffic across multiple API keys or providers to stay below rate limits.
5. **Caching:** Store prompt/response pairs in local memory or Redis to cut latency and token costs by 40-60%.
6. **Observability & Cost Tracking:** Log inputs, outputs, token consumption, and exact dollar amounts per execution.
7. **Guardrails:** Intercept inputs to sanitize PII (emails, phone numbers, SSNs, Aadhaar, PAN) and block jailbreak/prompt injection attempts.
8. **Evaluations (Evals):** Hook in evaluation frameworks directly at the gateway layer.

---

### Code Walkthrough & Implementation Details

The video uses **LiteLLM** (an open-source LLM gateway library) alongside **LangChain** and **Python-dotenv**.

#### 1. Standardized Completion Calls

Instead of initializing vendor-specific SDKs, LiteLLM standardizes model execution through `completion()`:

```python
from litellm import completion

# OpenAI GPT-4o-mini
response = completion(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Explain RAG in one sentence."}]
)

# Groq Llama 3.3
response_groq = completion(
    model="groq/llama-3.3-70b-versatile",
    messages=[{"role": "user", "content": "Explain RAG in one sentence."}]
)
```

#### 2. Automatic Fallback Setup

If the primary model fails or is offline, execution seamlessly shifts down the fallback list:

```python
response = completion(
    model="gemini/gemini-1.5-flash", # Primary model
    fallbacks=["gpt-4o-mini", "groq/llama-3.3-70b-versatile"], # Fallback chain
    messages=[{"role": "user", "content": "Explain agentic AI."}]
)
```

#### 3. Cost Tracking & Local Caching

* **Cost calculation:** Call `completion_cost(completion_response=response)` to obtain exact dollar expense per request.
* **Local caching:** Enabling LiteLLM local in-memory cache drops response time on identical secondary prompts from ~1.45 seconds to ~0.0002 seconds (~700x speedup at 0 additional cost).

#### 4. Load Balancing Strategies

The LiteLLM `Router` manages distribution across multiple keys or models using distinct routing strategies:

* **`simple-shuffle`:** Randomly balances requests across configured API pools.
* **`least-busy`:** Routes incoming requests to whichever model deployment currently has the fewest active requests.
* **`latency-based-routing`:** Monitors recent response latencies and routes requests to the fastest provider.

#### 5. Integration with LangChain (`ChatLiteLLM`)

```python
from langchain_community.chat_models import ChatLiteLLM
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

primary = ChatLiteLLM(model="gpt-4o-mini")
fallback = ChatLiteLLM(model="groq/llama-3.3-70b-versatile")

# LangChain fallback chain syntax
robust_llm = primary.with_fallbacks([fallback])

chain = prompt | robust_llm | StrOutputParser()
```

#### 6. Task-Aware Smart Router Chatbot

The end-to-end architecture demonstrates how a router classifies user intent before executing the call:

1. **Classifier Step:** Lightweight model identifies if query is `code`, `summary`, or `general`.
2. **Dynamic Route:**
* `code` $\rightarrow$ Routes to GPT-4o
* `summary` $\rightarrow$ Routes to GPT-4o-mini
* `general` $\rightarrow$ Routes to Groq Llama 3.3

3. **Execution with Fallbacks & Telemetry:** Logs cost and execution latency.

#### 7. Input Guardrails: PII Redaction & Prompt Injection

Using LiteLLM input hooks (`input_callback`), sensitive data patterns are stripped prior to hitting provider servers:

* **PII Scrubbing:** Regular expressions scan for Emails, US SSNs, Indian Aadhaar numbers, PAN cards, and Phone numbers, replacing matches with `[REDACTED]`.
* **Prompt Injection Defense:** Regex filters evaluate prompts for common jailbreaks (e.g., *"Ignore previous instructions"*, *"DAN mode"*) and abort the call before forwarding.

---
