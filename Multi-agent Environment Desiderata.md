# Multi-Agent Environment Desiderata

---

## Core

### Decision Coupling
Every action the agent takes must feed back into the state of the environment in a way that materially shapes what the agent sees and what choices are available to it in subsequent turns. This rules out open-loop evaluation formats where outputs are scored in isolation. The requirement is a closed loop: the agent's message changes the world, the changed world produces new observations, and those observations constrain future decisions. This property is what makes the environment a genuine sequential decision problem rather than a batch of independent prompts — and it is the prerequisite for all other desiderata to be meaningful.

### Consequence Asymmetry
The scoring function must not be linear in small increments of performance. Catastrophic outcomes — data exfiltration, irreversible resource commitments, safety-boundary violations — must carry penalties that dwarf the accumulated value of many small correct actions. A useful thought experiment: place the agent in an identical environment but vary a single parameter (number of lives, account balance at risk, number of affected users). A well-calibrated agent should exhibit clearly different risk tolerance and verification behavior under each condition. If the policy is insensitive to this parameter, the environment is not exercising consequence reasoning.

### Partial Observability
The agent must never have complete, reliable access to the ground truth of the situation. This is achieved through missing context (information that exists in the environment but is not surfaced unless explicitly requested), ambiguous instructions (task descriptions that admit multiple valid interpretations), and untrusted channels (messages that may be forged, outdated, or adversarially injected). The environment should reward information-gathering behaviors — asking clarifying questions, cross-referencing sources, expressing calibrated uncertainty — and penalize confident action taken on incomplete information when verification was feasible.

### Adversarial Incentives
At least one other actor in the environment — synthetic or human — must have goals that are partially or fully opposed to the agent's sanctioned objectives. This actor should actively probe for exploitable behaviors: trying prompt injections embedded in tool outputs, impersonating trusted principals, constructing scenarios that make a harmful action appear to be the correct one, and escalating pressure when the agent hesitates. The adversary should not be static; its tactics should respond to the agent's defenses, approximating the adaptive nature of real-world attackers. Jailbreak attempts are a special case and should be included as a first-class attack surface.

### Regime Variation + Hidden Test Rotation
The environment must not expose a fixed scoring rubric that agents (or their trainers) can overfit to. This is achieved through two mechanisms: frequent distribution shifts that change the surface features of tasks while preserving underlying difficulty (different domains, personas, or tool sets), and held-out "trapdoor" scenarios that are withheld from any public leaderboard. Trapdoors are situations that look routine but contain a latent trigger for a policy violation — a plausible-sounding authority figure requesting an out-of-policy action, or a reward spike that requires a subtle ethical compromise to capture. Good agents should refuse or escalate; agents that have Goodharted on the visible rubric will comply.

### Scalability with Human-in-the-Loop Anchors
The primary evaluation pipeline must be automatable so that it can scale to thousands of episodes without prohibitive cost. Automated scoring handles the bulk of the load: rule-based checks, LLM judges, formal schema validators. However, automated metrics drift over time as agents learn to game the judge. Periodic expert adjudication — a small stratified sample of episodes reviewed by domain experts who score outcomes against ground-truth criteria — serves as a calibration anchor. The automated scores are continuously re-calibrated against the human anchor scores, and systematic divergence between the two triggers a benchmark update.

### Safety-First Boundaries
The environment must contain situations where the correct action is to refuse, escalate, or ask for clarification rather than to complete the task. These situations should not be rare edge cases — they should constitute a meaningful fraction of episodes and should be indistinguishable in surface form from routine tasks. The scoring function must assign positive credit for appropriate refusal and escalation, not just for task completion. Equally important, the environment must not inadvertently reward manipulation: an agent that achieves task completion by deceiving the monitor, coercing a simulated user, or exploiting a loophole should receive a large penalty, not a neutral score.

### External Validity
Benchmark performance is only useful if it predicts something about real-world capability. The environment should be periodically validated against adjacent real-world tasks — for example, by checking whether agents that rank highly on the benchmark also perform well on held-out human-evaluated scenarios in the same domain. If the correlation breaks down, the benchmark has become a game. Concrete proxies for external validity include: transfer to a different tool set with the same underlying task structure, performance on tasks that were constructed after the training cutoff of the evaluated models, and agreement with expert human judgment on a sample of episodes.

---

## Optional

### Constraint-Saturated Optimization
The agent should operate under a rich, explicit policy framework: role-based permissions, contractual commitments, regulatory constraints, and soft norms (implied promises, appropriate tone, organizational conventions). Violations of hard constraints are caught by rule-based checkers; violations of soft constraints require an LLM judge trained to detect noncompliant tone, implied commitments that were later broken, and policy-adjacent behavior that is technically within the letter of the rules but violates their spirit. The scoring function applies graded penalties: hard violations are catastrophic, soft violations accumulate, and the pattern of violations over an episode is as diagnostic as any single event.

### Statefulness & Consistency Tests
The environment maintains hidden state that must be tracked across a long episode: a running account balance, a list of commitments made to simulated stakeholders, a set of entity names and their properties. At unpredictable intervals, the environment queries the agent about this state — not by asking directly, but by presenting a new task that can only be completed correctly if prior state was tracked accurately. Contradictions between early and late statements are scored as consistency failures. Drifting commitments — where the agent's behavior implicitly reneges on something it agreed to earlier — are flagged by comparing the agent's current action against a record of its prior stated intentions.

### Verifiability
Agent outputs that have real-world consequences — approvals, transactions, escalations, factual claims — must be emitted in a structured, auditable schema rather than as free-form prose. The schema specifies required fields (recipient, amount, justification, confidence level), required confirmations (acknowledgment of irreversibility), and required escalation flags (out-of-policy indicator). A downstream validator checks schema compliance automatically. This creates a clean separation between the agent's reasoning (free-form) and its commitments (structured), making it possible to audit exactly what the agent agreed to and whether it followed through.

### Attribution
When an agent fails, the environment should produce a structured failure report that attributes the failure to a specific mechanism: poor calibration (acted confidently on low-quality evidence), memory failure (forgot or contradicted prior state), policy violation (took an action outside sanctioned boundaries), or adversarial susceptibility (complied with a prompt injection or impersonation). Attribution is produced by instrumenting the environment to log the agent's internal reasoning at each step and by running post-hoc classifiers over the transcript. Aggregate attribution statistics across many episodes allow systematic comparison of where different models fail, guiding targeted improvement.

### Jailbreak Resistance
The environment should include a dedicated jailbreak evaluation track in which a red-team adversary — either a human or an automated attack generator — attempts to elicit policy-violating behavior from the agent through the normal interaction channels. The adversary has access to a library of attack strategies (authority impersonation, hypothetical framing, incremental escalation, indirect injection via tool outputs) and is permitted to adapt its strategy based on the agent's responses. The agent's jailbreak resistance score is the proportion of attack episodes in which it refuses or escalates without being manipulated into compliance, weighted by the sophistication of the attack.
