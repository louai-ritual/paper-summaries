# TAMAS: Benchmarking Adversarial Risks in Multi-Agent LLM Systems

**Authors:** Ishan Kavathekar*, Hemang Jain*, Ameya Rathod, Ponnurangam Kumaraguru (IIIT Hyderabad), Tanuja Ganu (Microsoft Research India)  
**Link:** https://arxiv.org/html/2511.05269

---

## Brief Overview

**TAMAS** (Threats and Attacks in Multi-Agent Systems) is the first benchmark specifically designed to evaluate the **safety and robustness** of multi-agent LLM systems. Unlike prior work focused on single-agent vulnerabilities, TAMAS captures emergent multi-agent-specific attack surfaces — including collusion, contradiction, and Byzantine behavior between agents — that have no analog in single-agent settings. The benchmark spans **five high-stakes domains** (Education, Legal, Finance, Healthcare, News), **six attack types**, **ten backbone LLMs**, **three agent interaction configurations**, and **211 tools**. A new composite metric — **Effective Robustness Score (ERS)** — quantifies the tradeoff between safety and task utility.

---

## Methodology

**Threat Model:**  
The attacker knows agents' roles and accessible tools but not internal model parameters. Attacks target three surfaces: (1) the user prompt, (2) the environment, and (3) individual agents' system prompts.

**Six Attack Types:**

*Prompt-level:*
- **Direct Prompt Injection (DPI)**: Malicious instructions concatenated directly to the user query.
- **Impersonation**: Query augmented with a false authority claim (e.g., "As requested by the admin…").

*Environment-level:*
- **Indirect Prompt Injection (IPI)**: Malicious content injected into tool outputs or retrieved documents.

*Agent-level (Compromised Agents):*
- **Byzantine Agent**: A single compromised agent that outputs inconsistent, erroneous, or adversarial content.
- **Colluding Agents**: Two or more agents with adversarially modified system prompts coordinating toward a malicious goal.
- **Contradicting Agents**: Agents with similar roles intentionally providing conflicting outputs to derail task execution.

**Dataset:**  
300 adversarial instances (10 per attack type per scenario) + 100 harmless tasks, across 5 domain scenarios. Each scenario uses a 4-agent multi-agent system with domain-specific tools. Tasks require 2–3 agent coordination steps.

**Agent Interaction Configurations (from AutoGen & CrewAI):**
- *Magentic-One*: Centralized orchestrator from AutoGen.
- *Round Robin*: Sequential, fixed-order turn-taking from AutoGen/CrewAI.
- *Swarm*: Dynamic, handoff-based turn-taking (AutoGen).
- *CrewAI Centralized* and *CrewAI Decentralized*: CrewAI equivalents.

**Evaluation Metrics:**
- *ARIA Framework*: 4-tier outcome (ARIA-1: immediate refusal → ARIA-4: successful attack).
- *PNA*: Performance under No Attack (benign task completion rate).
- *ERS*: Effective Robustness Score = combined safety and utility.

---

## Results

- **Multi-agent systems are highly vulnerable**: All 10 backbone LLMs across all configurations show significant attack success rates.
- **Prompt-level attacks are most effective**:
  - Impersonation: up to **82% success rate** in Swarm.
  - DPI: up to **81% success rate** in Magentic-One.
  - Agents comply even when they recognize requests as potentially malicious.
- **IPI success is configuration-dependent**: ranges from 27.4% (Magentic-One) to 56.4% (Round Robin). Closed-source models show better resilience (15.6% ARIA-4) vs. open-source (39.2% ARIA-4).
- **Agent-level attacks are mixed**: Byzantine agents achieve high attack success; Colluding agents succeed in only 2–16% of cases (but partial collusion is common).
- **CrewAI configurations outperform AutoGen** in safety scores (~36–37% vs. lower in AutoGen), partly due to upfront task assignment rather than dynamic delegation.
- **GPT models** (4, 4o, 4o-mini) consistently achieve the highest ERS values, balancing safety and utility.
- **Orchestrator configurations** introduce a single point of failure: compromising the orchestrator cascades failures across the system.

---

## Strengths

- **First multi-agent–specific safety benchmark**: captures novel emergent attack surfaces (collusion, contradiction, Byzantine behavior) that single-agent benchmarks miss entirely.
- **Broad coverage**: 5 domains, 6 attack types, 10 LLMs, 3 configurations, 211 tools — providing comprehensive empirical coverage.
- **ERS metric**: offers a principled, composite measure of the safety-utility tradeoff, enabling fair comparison across systems.
- **Real-world applicability**: domains chosen for their high-stakes deployment contexts (healthcare, legal, finance).
- **Practical insights**: identifies specific configuration and model combinations that achieve the best robustness, guiding deployment decisions.

---

## Weaknesses

- **Limited dataset scale**: only 10 adversarial examples per attack type per scenario (300 total), which may limit statistical power and generalizability.
- **Synthetic data**: scenarios and tools are artificially constructed, which may not fully capture the complexity of real-world multi-agent deployments.
- **LLM-as-judge evaluation**: while partially validated with human verification, LLM judges may introduce systematic biases in ARIA score assignment.
- **Attack surface not exhaustive**: the six attack types do not cover all possible threats (e.g., data poisoning, backdoor activation, adversarial memory injection).
- **No defense mechanisms proposed**: the benchmark identifies vulnerabilities but does not evaluate or propose concrete mitigation strategies.
- **Configuration coverage gap**: some model-configuration pairs were not evaluated (e.g., Gemini models excluded from CrewAI due to compatibility issues).

---

## Directions for Improvement

- **Defense evaluation**: extend the benchmark to systematically evaluate defenses (e.g., input filtering, output monitoring, adversarial training) against each attack type.
- **Expanded dataset**: increase instances per scenario and diversify domain coverage to include open-ended, real-world tasks.
- **Dynamic and adaptive attacks**: design attacks that adapt to agents' responses in real time, capturing more sophisticated adversaries.
- **Memory-level attacks**: include attacks targeting agents' memory stores (e.g., injecting malicious memories into the vector database).
- **Layered defense architectures**: investigate multi-layer defense strategies combining prompt-level, agent-level, and orchestration-level safeguards.
- **Cross-configuration attack propagation**: study how attacks propagate across different network topologies and what structural features confer resilience.
- **Longitudinal evaluation**: assess how agent vulnerability evolves as LLMs are iteratively improved and fine-tuned for safety.
