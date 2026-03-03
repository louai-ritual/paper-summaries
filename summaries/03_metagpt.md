# MetaGPT: Meta Programming for a Multi-Agent Collaborative Framework

**Authors:** Sirui Hong, Mingchen Zhuge, Jiaqi Chen, Xiawu Zheng, Yuheng Cheng, Ceyao Zhang, et al. (DeepWisdom, KAUST, Xiamen University, CUHK-Shenzhen, etc.)  
**Link:** https://arxiv.org/html/2308.00352

---

## Brief Overview

**MetaGPT** is a meta-programming framework that encodes real-world **Standardized Operating Procedures (SOPs)** into an LLM-based multi-agent system for collaborative software development. Rather than relying on free-form natural language dialogue between agents, MetaGPT enforces structured communication interfaces (documents, diagrams) and an assembly-line workflow with specialized roles (Product Manager, Architect, Project Manager, Engineer, QA Engineer). An **executable feedback mechanism** enables agents to run and debug code at runtime, significantly improving code quality. MetaGPT achieves state-of-the-art performance on HumanEval (85.9% Pass@1) and MBPP (87.7% Pass@1).

---

## Methodology

**Role Specialization and SOP Workflow:**  
Five agents with distinct roles work sequentially:
1. *Product Manager* → produces a structured Product Requirements Document (PRD) with user stories and a competitive analysis.
2. *Architect* → translates PRD into system design: file lists, data structures, API interfaces, and sequence flow diagrams.
3. *Project Manager* → decomposes the design into a concrete task list assigned to engineers.
4. *Engineers* → implement classes and functions as specified.
5. *QA Engineer* → writes and runs unit tests.

**Structured Communication Interfaces:**  
Unlike ChatDev's dialogue-based approach, MetaGPT agents communicate via **structured outputs** (documents and diagrams), preventing information loss from natural language ambiguity ("Chinese whispers" effect).

**Publish-Subscribe Mechanism:**  
A shared global message pool stores all outputs. Agents subscribe to only the outputs relevant to their role (e.g., the Architect subscribes to PRDs, not QA outputs), preventing information overload.

**Executable Feedback:**  
After initial code generation, the Engineer runs the code, observes runtime errors, consults historical messages (PRD, system design), and iteratively debugs (up to 3 retries). This "code → execute → fix" loop catches runtime errors that static review misses.

**Evaluation:**  
Three datasets: HumanEval (164 tasks), MBPP (427 tasks), and SoftwareDev (70 diverse software engineering tasks). Metrics include Pass@k (for HumanEval/MBPP) and human-assessed executability, running cost, code statistics, productivity, and human revision cost (for SoftwareDev).

---

## Results

- **HumanEval**: 85.9% Pass@1 (new SOTA at time of publication); **MBPP**: 87.7% Pass@1.
- **SoftwareDev executability**: MetaGPT scores **3.75/4.0** vs. ChatDev's 2.25/4.0.
- **Human revision cost**: MetaGPT requires only **0.83** manual corrections per project vs. 2.5 for ChatDev.
- **Running time**: MetaGPT averages 541s/project vs. ChatDev's 762s.
- **Productivity**: MetaGPT uses **124.3 tokens/line** vs. ChatDev's 248.9 tokens/line.
- **100% task completion rate** on the SoftwareDev benchmark.
- Ablation study: adding each additional role (Product Manager, Architect, Project Manager) incrementally improves executability while slightly raising cost. Executable feedback alone yields +4.2%/+5.4% improvement on HumanEval/MBPP.

---

## Strengths

- **SOP-inspired design** brings human organizational wisdom into multi-agent collaboration, drastically reducing hallucination-induced errors.
- **Structured outputs** (documents, diagrams) are more reliable than free-form dialogue for conveying complex technical information between agents.
- **Executable feedback loop** is a practical, runtime self-correction mechanism that single-pass approaches lack.
- **Publish-subscribe communication** elegantly handles information routing in multi-agent settings without hardcoding communication topology.
- **Strong quantitative results** across multiple public benchmarks, with thorough ablation studies validating each design decision.
- **Open-source** with an active community (AgentStore ecosystem).

---

## Weaknesses

- **Higher token usage** than simpler approaches: MetaGPT uses ~31,255 tokens per project (with feedback), more than ChatDev's ~19,292.
- **Rigid sequential workflow**: the SOP pipeline does not adapt dynamically to task complexity; all projects go through all five roles regardless of scope.
- **Limited to Python-focused tasks**: current evaluations focus heavily on Python code; applicability to polyglot or front-end-heavy projects is unverified.
- **No inter-project learning**: each software project is executed independently; agents do not accumulate knowledge across projects (acknowledged in Appendix A).
- **Single-domain SOPs**: SOPs are hardcoded for software development; extending to other domains requires significant re-engineering.
- **Evaluation depth**: human revision cost is a proxy metric; true software quality (security, maintainability, user-friendliness) is not measured.

---

## Directions for Improvement

- **Cross-project learning**: implement memory mechanisms so agents improve from past projects (recursive self-improvement, as discussed in the appendix).
- **Dynamic SOPs**: allow agents to adapt their workflow and communication protocols to task complexity, rather than using a fixed pipeline.
- **Multi-modal agents**: add agents that handle UI design, front-end development, and visual specification (currently a stated limitation).
- **Multi-domain extension**: generalize SOP encoding to domains beyond software engineering (scientific research, legal analysis, etc.).
- **Economy of agents**: explore market-based incentive structures (EOM) to dynamically allocate tasks to the best-suited agents.
- **Security and ethics**: investigate vulnerabilities in agent-generated code and bias in automated development pipelines.
- **Open-source LLM backends**: improve performance parity when using open-source models (e.g., Deepseek Coder) compared to GPT-4, as current results show a significant gap.
