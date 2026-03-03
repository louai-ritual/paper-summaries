# MultiAgentBench: Evaluating the Collaboration and Competition of LLM Agents

**Authors:** Kunlun Zhu, Hongyi Du, Zhaochen Hong, Xiaocheng Yang, Shuyi Guo, Zhe Wang, Zhenhailong Wang, Cheng Qian, Xiangru Tang, Heng Ji, Jiaxuan You (University of Illinois Urbana-Champaign)  
**Link:** https://arxiv.org/html/2503.01935

---

## Brief Overview

**MultiAgentBench** is a comprehensive benchmark for evaluating LLM-based multi-agent systems in six diverse interactive scenarios covering both cooperative (mutual-goal) and competitive (conflicting-goal) settings. The benchmark is paired with **MARBLE** (Multi-agent cooRdination Backbone with LLM Engine), a flexible framework supporting four coordination topologies (star, tree, chain, graph-mesh) and four planning strategies (vanilla, Chain-of-Thought, group discussion, cognitive self-evolving planning). Beyond task success, MultiAgentBench introduces novel **milestone-based KPIs** and **coordination scores** (communication + planning) to measure the quality of agent interactions.

---

## Methodology

**MARBLE Framework:**  
- *Agent Graph Module*: Represents agents and their relationships as a typed graph G=(A, E) with edges denoting collaboration, supervision, or negotiation.
- *Cognitive Module*: Maintains each agent's internal state (persona, inter-agent relationships, reasoning strategy). Supports Chain-of-Thought and ReAct-style reasoning, mirroring human theory-of-mind.
- *Coordination Engine*: Orchestrates execution flow, distinguishing between *planners* (strategy and allocation) and *actors* (execution and environment interaction).

**Coordination Protocols:**
- *Star*: Centralized single planner assigns tasks to all actors.
- *Tree*: Hierarchical planning with top-level and subordinate planners.
- *Graph-Mesh*: Decentralized, fully interconnected agents communicating directly.
- *Chain*: Sequential handoff of decisions between agents.

**Planning Strategies:**
- *Vanilla*: Zero-shot instruction.
- *Chain-of-Thought (CoT)*: Step-by-step reasoning with agent profiles and subtask summaries.
- *Group Discussion*: Multiple agents share insights collaboratively.
- *Cognitive Self-Evolving Planning*: Reflexion-style mechanism that compares expected vs. actual outcomes to iteratively refine planning.

**Benchmark Scenarios (6 total):**
- *Cooperative*: Research co-authoring (ResearchTown-style), Minecraft building, database error analysis, coding collaboration.
- *Competitive*: Werewolf (social deduction with deception), Bargaining (negotiation over shared resources).

**Evaluation Metrics:**
- *Milestone-based KPI*: Fraction of milestones each agent contributes to, averaged across all agents.
- *Task Score (TS)*: Final output quality (LLM rubric for open-ended tasks; rule-based for games).
- *Coordination Score (CS)*: Average of Communication Score and Planning Score (each on a 5-point scale, rated by an LLM judge).

---

## Results

- **gpt-4o-mini** achieves the highest average Task Score across most scenarios (e.g., Research TS = 84.13%), confirming that underlying model capability is the primary driver of task performance.
- **Coordination Score** is a useful but incomplete predictor of task success: Meta-Llama-3.1-70B scores high CS (75.00) in Minecraft but nearly zero TS (0.21), revealing a gap between coordination quality and task execution ability.
- **Graph-mesh coordination** outperforms all other protocols in the research scenario, achieving the best task performance and planning efficiency with lower token usage than tree-based coordination.
- **Cognitive Self-Evolving Planning** yields the best Coordination Score and achieves a task score comparable to CoT, improving milestone achievement rates by **3%** over baselines.
- **Group Discussion** surprisingly underperforms, suggesting that overly large planning groups create communication overhead that hinders effectiveness.
- **Emergent behaviors** observed: strategic information withholding (Seer in Werewolf), trust-polarized collaboration, and role-driven strategy iteration — analogous to human social dynamics.
- More agents (up to 7) decrease individual KPI but improve coordination scores from 1→3 agents; beyond 3, coordination challenges increase.

---

## Strengths

- **Comprehensive scope**: covers both cooperative and competitive dynamics, making it the first benchmark to systematically evaluate both paradigms simultaneously.
- **Novel evaluation metrics**: milestone-based KPIs and coordination scores go beyond binary task success, capturing the quality of multi-agent processes.
- **Flexible framework**: MARBLE's modular design supports easy swapping of models, protocols, and scenarios.
- **Emergent behavior analysis**: the paper identifies and characterizes novel social behaviors arising from multi-agent interactions, providing insights toward AGI-level collaboration.
- **Multiple coordination protocols**: systematic comparison of star, tree, graph, and chain topologies provides actionable design guidance.

---

## Weaknesses

- **Limited model coverage**: experiments use only five models (three Llama variants, GPT-3.5, GPT-4o-mini); stronger models (e.g., GPT-4, Claude, DeepSeek) are excluded.
- **Scenario diversity gap**: six scenarios may not capture the full breadth of real-world multi-agent challenges (open-world environments, complex social cognition, task-oriented dialogues).
- **Coordination score reliability**: the LLM-based coordination judge may itself be subject to biases or inconsistencies, and human validation of CS is limited.
- **Fixed max iterations**: setting a fixed iteration cap (5 for research, 20 for Minecraft) may prematurely terminate tasks or introduce artifacts.
- **Competition mechanisms**: the competitive scenarios (Werewolf, Bargaining) do not capture multi-party negotiations or repeated strategic play over evolving environments.
- **Ill-defined tasks excluded**: all scenarios have well-defined objectives; open-ended, ambiguous real-world tasks are not addressed.

---

## Directions for Improvement

- **Broader model evaluation**: include larger closed-source models (GPT-4, Claude Sonnet, DeepSeek) and fine-tuned multi-agent specialists.
- **Open-ended scenarios**: design tasks without clear success criteria to test agent adaptability in exploratory contexts.
- **Memory mechanism ablations**: investigate long-term, short-term, and shared memory mechanisms more thoroughly.
- **Dynamic agent roles**: allow agents to dynamically negotiate and reassign roles during task execution, reflecting real organizational dynamics.
- **Richer competition**: design adversarial settings with multi-party negotiations, repeated games, and stochastic elements.
- **Scalability studies**: systematically study the effect of very large agent populations (10–100 agents) on coordination and performance.
- **Real-world integration**: connect benchmark findings to deployed multi-agent systems (e.g., AutoGen, LangGraph) with real API interactions.
