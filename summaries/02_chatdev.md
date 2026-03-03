# ChatDev: Communicative Agents for Software Development

**Authors:** Chen Qian, Wei Liu, Hongzhang Liu, Nuo Chen, et al. (Tsinghua University, University of Sydney, BUPT, Modelbest Inc.)  
**Link:** https://arxiv.org/html/2307.07924

---

## Brief Overview

**ChatDev** is a chat-powered, multi-agent software development framework in which specialized LLM-driven agents with distinct social roles (CEO, CTO, Programmer, Reviewer, Tester) collaborate to produce software end-to-end. The key insight is that language serves as a universal bridge: natural language is used for system design while programming language drives debugging. Two core innovations — the **Chat Chain** and **Communicative Dehallucination** — structure agent interactions to reduce coding hallucinations and produce complete, executable software from a single textual requirement.

---

## Methodology

**Chat Chain:**  
The software development lifecycle is divided into three sequential phases — Design, Coding, and Testing — each further broken into subtasks. In each subtask, two agents (an Instructor and an Assistant) engage in iterative multi-turn dialogue until consensus is reached. The solution from one subtask becomes the input to the next, enabling cross-phase continuity.

**Memory Management:**  
- *Short-term memory*: within-phase dialogue history for context-aware decision-making.  
- *Long-term memory*: only the extracted solution (not the full conversation) is passed between phases, reducing information overload.

**Communicative Dehallucination:**  
Rather than responding directly to vague instructions (which causes incomplete or inaccurate code), the assistant proactively asks clarifying questions before generating a formal response. This "role reversal" mechanism reduces occurrences of incomplete implementations, unexecutable code, and requirement inconsistencies.

**Inception Prompting:**  
At the start of each subtask, both the Instructor and Assistant are instantiated with symmetrical system prompts specifying roles, goals, tools, communication protocols, and termination conditions. This prevents pathological behaviors like role flipping, infinite loops, and fake replies.

**Dataset:**  
A new dataset (SRDD — Software Requirement Description Dataset) was created: 1,200 diverse software prompts across 5 categories (Education, Work, Life, Game, Creation) and 40 subcategories, generated and refined using LLMs with human post-processing.

**Evaluation Metrics:**  
- *Completeness*: % of software without placeholder code snippets.  
- *Executability*: % of software that compiles and runs successfully.  
- *Consistency*: cosine similarity between requirements and generated code embeddings.  
- *Quality*: product of the three above metrics.

---

## Results

- ChatDev outperforms **GPT-Engineer** (single-agent) and **MetaGPT** (multi-agent) on all metrics.
- Quality score: ChatDev **0.3953** vs. MetaGPT 0.1523 vs. GPT-Engineer 0.1419.
- Executability: ChatDev **88%** vs. MetaGPT 41.45% vs. GPT-Engineer 35.83%.
- Pairwise human evaluation: ChatDev wins **90.16%** of comparisons against GPT-Engineer and **88%** against MetaGPT.
- Ablation study confirmed that each phase (design, coding, testing), communicative dehallucination, and role assignment all independently contribute to quality.
- The most impactful single factor: removing **agent roles** from system prompts caused the largest quality drop.

---

## Strengths

- **Unified language paradigm**: integrating natural and programming language communication across all phases eliminates the fragmentation inherent in prior work.
- **Communicative dehallucination** is a practical, lightweight mechanism that significantly reduces code errors without requiring model retraining.
- **Transparent workflow**: the chain-structured approach allows inspection of intermediate outputs at every subtask, aiding debugging.
- **Strong empirical results**: statistically significant improvements across all metrics, validated by both GPT-4 and human evaluators.
- **Open-source**: code and dataset are publicly available, facilitating reproducibility and extension.

---

## Weaknesses

- **Computational cost**: multi-agent communication uses more tokens and time than single-agent approaches (~22,949 tokens and ~148s per task on average).
- **Limited software complexity**: the paper acknowledges that agents produce simple logic and are best suited for prototype-level software rather than real-world production systems.
- **Evaluation scope**: metrics (completeness, executability, consistency) do not capture robustness, safety, user-friendliness, or functionality depth.
- **Context window constraints**: despite the memory segmentation scheme, very long projects may still exceed LLM context limits.
- **Vague requirements cause failure**: without clear, detailed input prompts, agents struggle to grasp task intent, producing shallow implementations.

---

## Directions for Improvement

- **Richer evaluation**: develop benchmarks that assess functional correctness, robustness, security, and user experience beyond executability metrics.
- **Adaptive task complexity**: design mechanisms to handle real-world, complex software requirements beyond simple games and tools.
- **Efficiency optimization**: reduce token usage per task through smarter memory compression or selective agent invocation.
- **Multi-modal support**: incorporate visual tools (e.g., UI mockup agents) for front-end development tasks.
- **Continuous learning**: enable agents to learn from past development sessions, improving future performance (a direction noted in the paper's outlook).
- **Safety and bias**: investigate and mitigate risks of agents producing insecure or biased code at scale.
