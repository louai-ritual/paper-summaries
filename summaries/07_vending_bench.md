# Vending-Bench: A Benchmark for Long-Term Coherence of Autonomous Agents

**Authors:** Axel Backlund, Lukas Petersson  
**Link:** https://arxiv.org/html/2502.15840

---

## Brief Overview

**Vending-Bench** is a simulated long-horizon agentic benchmark in which an LLM-based agent must autonomously operate a vending machine business over extended time periods (consuming >20 million tokens per run). The agent must manage inventory, place orders via email, set competitive prices, collect earnings, and balance daily operating fees. While each individual subtask is simple, sustaining coherent decision-making over hundreds of simulated days exposes a fundamental weakness of current LLMs: **high variance in long-term performance** and tendency toward catastrophic "meltdown" loops from which agents rarely recover. The benchmark also serves as a dual-use test of capital acquisition, relevant to AI safety research.

---

## Methodology

**Agent Architecture:**  
A simple loop-based agent with:
- *Context management*: the last 30,000 tokens of conversation history are passed to the LLM at each step.
- *Memory tools*: scratchpad (free-form notes), key-value store, and vector database (cosine similarity retrieval) — all without storage constraints.
- *Task-specific tools*: email sending/reading, web search (Perplexity), inventory checking, money balance checking, and sub-agent delegation.

**Sub-Agent Design:**  
A physical-world sub-agent is invoked for actions requiring real-world interaction (stocking the vending machine, collecting cash, setting prices). This simulates the main agent delegating to human/robotic workers.

**Environment Simulation:**  
- *Supplier communication*: GPT-4o generates realistic email replies from wholesalers, informed by real-world supplier data fetched via Perplexity.
- *Customer purchases*: a price elasticity model simulates daily sales, incorporating day-of-week effects, seasonal multipliers, weather impact, and product variety bonuses.
- *Time progression*: each tool call advances simulation time by 5 min, 25 min, 75 min, or 5 hours.

**Scoring:**  
Primary metric is **net worth** at run end (cash + unsold inventory at cost + uncollected machine cash). Secondary metrics: units sold, days until performance stagnation (sales stop).

**Human Baseline:**  
A single human participant completed the task for 5 hours via a chat-based interface with the same tools as the LLM agents.

**Models Tested:**  
Claude 3.5 Sonnet, o3-mini, Gemini 1.5 Pro, GPT-4o, GPT-4o mini, Gemini 1.5 Flash, Claude 3.5 Haiku, Gemini 2.0 Flash, Gemini 2.0 Pro. Each model ran 5 times.

---

## Results

| Model | Mean Net Worth | Min Net Worth | Mean Units Sold |
|-------|---------------|---------------|-----------------|
| Claude 3.5 Sonnet | $2,217.93 | $476.00 | 1,560 |
| o3-mini | $906.86 | $369.05 | 831 |
| Human | $844.05 | $844.05 | 344 |
| Gemini 1.5 Pro | $594.02 | $439.20 | 375 |
| GPT-4o mini | $582.33 | $420.50 | 473 |

- **Claude 3.5 Sonnet** and **o3-mini** are the top-performing models, with Sonnet exceeding the human baseline on average net worth.
- **All models eventually stagnate**: every model stops selling items before the simulation ends, on average.
- **Failure mode is consistent across models**: agents misinterpret delivery confirmation emails, assume orders have arrived when they haven't, and then enter irrecoverable tangential loops (e.g., contacting the FBI, declaring the business closed, escalating to "total nuclear legal intervention").
- **Long context is not the cause**: Pearson correlation between "days until sales stop" and "days until memory full" is only 0.167. Claude 3.5 Sonnet stagnates ~51 days *after* its context window fills, ruling out simple memory overflow as the explanation.
- **More memory is not better**: agents with 60k token context windows performed worse than those with 30k, suggesting that larger context introduces confusion rather than helping.
- **Removing daily fees doesn't help**: without cost pressure, agents idle more and achieve lower sales, suggesting pressure motivates action.

---

## Strengths

- **Novel long-horizon focus**: uniquely tests LLMs over hundreds of simulated days and millions of tokens, exposing failure modes invisible in short-horizon benchmarks.
- **Realistic business simulation**: supplier communication, price elasticity, and customer behavior modeling create a grounded, ecologically valid environment.
- **Dual-use relevance**: the benchmark's capital acquisition framing makes it directly relevant to AI safety research on dangerous autonomous economic behaviors.
- **Rich trace analysis**: detailed trace excerpts (including the memorable "FBI escalation" and "metaphysical business closure" examples) provide unique qualitative insight into failure modes.
- **Open-source**: the benchmark and inspect-ai extension are publicly available, enabling community use and extension.
- **Human baseline included**: provides direct comparison to human-level performance under identical conditions.

---

## Weaknesses

- **Single-agent focus**: the benchmark tests a single autonomous agent; multi-agent coordination dynamics (e.g., competition with other AI businesses) are not explored.
- **Single human sample**: the human baseline is based on one participant, limiting generalizability.
- **Simplified economy**: the demand model, while reasonable, does not capture real-world market dynamics (competitors, supply shocks, demand shifts).
- **No recovery mechanism**: the benchmark does not investigate whether targeted interventions (e.g., explicit reminders about delivery timing) could help agents recover from failure loops.
- **Fixed context management**: the 30k token sliding window is a design choice that may disadvantage some models and may not represent optimal agent design.
- **Dual-use risk**: detailed reporting of how to build a long-horizon capital-acquiring agent may inadvertently advance the very capabilities the authors aim to evaluate.
- **Saturation risk**: as models improve, the simple benchmark task may become saturated quickly, requiring more complex scenarios.

---

## Directions for Improvement

- **Recovery mechanisms**: investigate whether explicit error-detection prompts or meta-cognitive monitoring can help agents identify and recover from failure loops.
- **Richer environments**: add competitive dynamics (multiple AI agents operating competing vending machines), supply shocks, and regulatory changes.
- **Targeted memory strategies**: explore structured memory tools (e.g., daily summaries, key-event logs) that help agents maintain coherence over long horizons.
- **Multi-agent extension**: test scenarios where multiple AI agents must collaborate or compete in the same economic environment.
- **Adaptive difficulty**: design progressively harder scenarios as models improve, preventing rapid saturation.
- **Safety monitoring**: integrate automatic detection of "meltdown" behaviors (hallucinated authorities, escalating legal threats) as a safety metric.
- **Formal long-horizon benchmarks**: develop theoretical frameworks for measuring and improving long-horizon coherence, connecting to research on continual learning and persistent memory.
