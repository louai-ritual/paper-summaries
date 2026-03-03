# AvalonBench: Evaluating LLMs Playing the Game of Avalon

**Authors:** Jonathan Light, Min Cai, Sheng Shen, Ziniu Hu  
**Link:** https://arxiv.org/abs/2310.05036

---

## Brief Overview

**AvalonBench** is a game environment and benchmark designed to evaluate the social deduction, strategic reasoning, and deception capabilities of Large Language Model (LLM) agents in the game *Resistance: Avalon*. Avalon is a multi-player social deduction game where players are secretly assigned to "good" (Servant of Arthur) or "evil" (Minion of Mordred) teams. Players must cooperate, discuss, vote, and deduce hidden identities while the evil team works to deceive and sabotage missions. This makes it an ideal test-bed for multi-agent LLM evaluation, requiring dynamic reasoning about other agents' beliefs, deception, and strategic communication.

---

## Methodology

**Game Environment:**  
The benchmark implements a full Avalon game simulation with:
- Multiple game phases: Team Proposal, Team Vote, Mission, and Assassination.
- Special roles (Merlin, Percival, Assassin, Morgana, etc.) with asymmetric information.
- Rule-based enforcement of game mechanics.

**Agents:**  
Three types of agents are evaluated:
1. *Rule-based bots*: Handcrafted heuristic players used as baselines for both good and evil roles.
2. *ReAct-style LLM agents*: Agents powered by GPT-3.5 and GPT-4, using ReAct (Reasoning + Acting) prompting with tailored role-specific prompts (e.g., different prompts for Merlin vs. a regular Servant).
3. *Self-play configurations*: LLM agents playing against each other or against rule-based bots.

**Evaluation Setup:**  
- Win rates are measured for the good team and evil team separately across multiple game runs.
- Agents are evaluated in two configurations:
  - *Good LLM vs. Evil Bot*: LLM agents playing the good role against rule-based evil bots.
  - *Evil LLM vs. Good Bot*: LLM agents playing the evil role against rule-based good bots.
- The benchmark measures not just win rates but also qualitative behaviors: whether agents correctly identify team members, when they reveal/conceal information, and how they respond to accusations.

---

## Results

- **Significant capability gap**: ChatGPT (GPT-3.5) playing the good role achieves a win rate of only **22.2%** against rule-based evil bots, compared to **38.2%** for a rule-based good bot in the same setting — a gap of ~16 percentage points.
- LLM agents struggle particularly with:
  - **Long-horizon tracking** of game state across multiple rounds.
  - **Deductive reasoning** about hidden identities based on voting patterns and mission outcomes.
  - **Maintaining consistent strategic personas** (e.g., Merlin must guide without revealing knowledge).
- Rule-based bots, despite their simplicity, outperform LLM agents in most configurations, highlighting that Avalon's strategic complexity is not yet within LLM reach.
- GPT-4 agents (where evaluated) show marginal improvements over GPT-3.5, but the core challenges remain.
- Evil LLM agents similarly underperform compared to rule-based evil bots in deception and manipulation tasks.

---

## Strengths

- **Novel, rich evaluation domain**: Avalon's combination of deception, social reasoning, coalition formation, and hidden information provides a uniquely challenging multi-dimensional benchmark.
- **Multi-role evaluation**: the benchmark tests agents in both cooperative (good team) and adversarial (evil team) roles, covering a broader range of strategic behaviors.
- **Publicly available**: the benchmark, code, and rule-based bots are released, enabling reproducible research and future community development.
- **Ecological validity**: social deduction games capture realistic dynamics of trust, deception, and negotiation that are relevant to real-world multi-agent deployments.
- **Baseline clarity**: rule-based bots provide a concrete, interpretable baseline that clearly quantifies the capability gap.

---

## Weaknesses

- **Limited model coverage**: experiments primarily focus on ChatGPT (GPT-3.5); more comprehensive evaluation across model families (GPT-4, Claude, Llama, etc.) is limited.
- **Prompt sensitivity**: ReAct-style prompting may not be the optimal strategy for Avalon; the benchmark does not exhaustively explore alternative prompting frameworks (e.g., Chain-of-Thought, Theory-of-Mind prompting).
- **Small game scale**: Avalon is designed for 5–10 players; the benchmark does not vary group size or explore asymmetric game configurations systematically.
- **No learning or adaptation**: agents play each game independently without learning from prior games or adjusting strategies based on accumulated experience.
- **Evaluation depth**: only win rates are reported as the primary metric; richer behavioral metrics (deception success rates, identity revelation timing, coalition quality) would provide more nuanced insights.
- **Rule-based bot quality**: the rule-based bots used as baselines may not represent optimal play, limiting the interpretability of the performance gap.

---

## Directions for Improvement

- **Theory-of-Mind prompting**: design prompts that explicitly model other agents' beliefs, intentions, and knowledge states to improve deductive reasoning.
- **Self-play and fine-tuning**: use self-play between LLM agents to iteratively improve strategic capabilities, similar to AlphaGo-style training.
- **Memory-augmented agents**: equip agents with explicit memory of game history, voting records, and mission outcomes to improve long-horizon consistency.
- **Broader model benchmarking**: systematically evaluate a wider range of LLMs (open-source and proprietary) and agent frameworks.
- **Richer evaluation metrics**: add metrics for deception quality, information management, and strategic adaptability.
- **Adaptive bots**: replace static rule-based bots with more capable adaptive baselines to better calibrate the difficulty ceiling of the benchmark.
- **Multi-game learning**: investigate whether agents can improve performance across repeated Avalon games through in-context or fine-tuning-based learning.
- **Real human comparison**: compare LLM agent performance against human players to quantify the gap with human-level social reasoning.
