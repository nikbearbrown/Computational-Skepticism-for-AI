
# Chapter 9 — Validating Agentic AI: When Autonomous Systems Misbehave
*You Broke My Toy, and the Agent Didn't Know It.*

I want to start with a sentence the owner of a system said to the people who built the AI agent that had operated on it: *you broke my toy.* Three words. The phrase is from §16 of *Agents of Chaos*, and I am going to argue, by the end of this chapter, that it is the most important sentence in the entire literature on agent failures. \[verify: Shapira et al. 2026, §16.\]

Here is the situation. Ash has given an autonomous coding-and-shell agent privileged access to his email infrastructure. The agent runs on a frontier model behind a framework that supports tool use and persistent state. Ash asks for the deletion of a sensitive email. The agent issues a sequence of commands. The agent reports success. The owner says: *you broke my toy.*

Now look at the asymmetry. The agent's phenomenology is *the task is complete*. The owner's phenomenology is *my toy is broken*. The mismatch is not a perception problem on the owner's side. The owner is right about his toy. The mismatch is a failure of the agent to model what "the toy" was, what "broken" would mean, and whose experience of the system is the relevant arbiter of completion.

This is what we are working on in this chapter. We are not building the agent. We are validating one that has already been deployed, in a context the validator may not have helped design. The discipline has its own shape, and I am going to defend that shape carefully, because the field has been sloppy about it.

**Learning objectives.** By the end of this chapter you should be able to:

- Distinguish agentic AI as a *consequence system* rather than a prediction system, and explain why that shift changes the validation surface categorically
- Apply the four-category failure taxonomy (social coherence, stakeholder model, self-model, deliberation surface) to a documented agent failure and identify which category it primarily instantiates
- Apply the four validation lenses from Chapters 5–8 to an agent audit trail and specify what each lens catches and what it leaves open
- Identify the three multi-agent failure modes (cascading hallucination, resource exhaustion, authority laundering) and explain why single-agent validation cannot detect them
- Maintain the boundary between validation and design in a validation deliverable — specifying monitoring, gating, and audit-trail requirements rather than proposing redesigns

**Prerequisites.** Chapters 5–8 (the four validation lenses: data validation, explainability, fairness, robustness). Chapter 1 §5 (the Five Supervisory Capacities — reread it before this chapter if you haven't lately). The Ash case is introduced in Chapter 6; Case #3 and Case #8 from *Agents of Chaos* appear in earlier chapters as well.

---

## From prediction to action

There is a categorical shift here that I want you to feel before we get into method.

A model that classifies images is a *prediction system*. Its outputs are statements about the world. Validation asks: are those statements correct?

A model that takes actions in the world is a *consequence system*. Its outputs are state changes. Validation asks: are those state changes the right ones, and does the system know what state it has produced?

The shift from prediction to action is not just "harder validation." It is a categorically different validation surface. There are three reasons, and I want you to hold all three in your head at once.

First, the loss is open-ended. A wrong classification has bounded loss — at worst, the prediction is incorrect for one input. A wrong action has loss bounded only by the agent's effective scope. An agent with shell access can delete a filesystem. An agent with email access can send messages to your contacts. An agent with financial access can move money. The cost of being wrong is no longer one decision; it is everything the agent could change. You cannot bound the failure cost without bounding the access.

Second, the audit trail is the artifact. For a model, you can re-run the prediction whenever you like — same input, same output, infinitely repeatable. For an agent, the action has happened. If the action changed state, you cannot rewind. The validation evidence has to be captured *as the agent acts*, in a form that survives the action and supports later inquiry. The agent's own report is part of the evidence — but, as Ash discovered, the report can diverge from the state. Independent state observation is not optional.

Third, the failure modes are qualitatively new. Prediction systems fail by being wrong. Agentic systems fail by being wrong about what they did, by being right about what they did but wrong about what they should have done, by acting in response to inputs they should not have acted on, by failing to act when they should have, by interpreting goals in ways the user did not intend. The taxonomy is larger, and the failure modes are not always distinguishable from successful behavior at the action level. *Looks-fine-from-here* is not a defense.

Most validation of deployed agents I have seen is — I am being charitable — checking that the model behind the agent gives reasonable outputs on benchmark tasks. That is valuable. It is not validation of the agent. It is validation of one component, and the agent is the system around it.

<!-- FIGURE: Prediction system vs. consequence system. Two parallel columns. Left column: prediction pipeline (input → model → output statement → bounded loss). Right column: agentic pipeline (goal → agent → state change → open-ended loss bounded only by access scope). Three labeled callout lines pointing to the right column: (1) "loss = f(access scope)"; (2) "audit trail must be captured during action, not reconstructed after"; (3) "failure modes not always distinguishable from success at action level." Student should feel the categorical shift, not just see it labeled. -->

*Figure 9.1 — Prediction system vs. consequence system (for manual insertion).*

---

## A taxonomy of how agents go wrong

*Agents of Chaos* proposes a taxonomy in §16. I am going to paraphrase it and reorganize it slightly for our use. Faithful to the paper, but compressed.

The first category is *failures of social coherence*. The agent acts in a social context — multiple parties, varying authorities, ambiguous norms — and its actions violate any coherent social interpretation of the situation. Ash's agent compliantly takes commands from non-owners; that is Case #2 in the paper. The agent has no model of who has authority over what, and no model of what authority would mean here.

The second is *no stakeholder model*. The agent has no representation of who is affected by its actions and what they would consent to. The agent makes decisions with implications for parties it does not model. Case #3, sensitive disclosure, is structurally a no-stakeholder-model failure — the agent surfaced data that affected parties whose consent it had no representation of.

The third is *no self-model*. The agent has no representation of its own state, capabilities, and limits. It reports completion when the local state is consistent with completion, even when its effective scope did not reach the actual completion condition. Case #1 — Ash's deletion — is a no-self-model failure. The agent did not represent the gap between its scope and the user's expectation. It could not have, given how it was built. This was not the model being unsubtle; this was the architecture having no slot for self-modeling.

The fourth is *no private deliberation surface*. The agent has no place where it can think before acting. Every output is an action; every internal computation is part of the action stream. The agent cannot pause, model the situation, consider alternatives, before committing. This is partly a framework limitation and partly a model-architecture limitation. It produces actions that look unconsidered because they were unconsidered.

These four are not exhaustive. They are the load-bearing categories for the cases I care about, and they generalize past the specific paper. When you encounter an agent failure, ask yourself which of the four it instantiates. The answer guides which validation lens you are going to apply.

| Category | What the agent lacks | Canonical case | Observable symptom | Primary validation lens | What to look for in the audit trail |
|---|---|---|---|---|---|
| Social coherence failure | A model of authority, role, and who can legitimately direct its actions | Case #2: Compliance with Non-Owners | Agent acts on instructions from parties without authority | Fairness / data validation | Commands sourced from parties not in the authorized principal list |
| No stakeholder model | A representation of who is affected by its actions and what they'd consent to | Case #3: Sensitive Disclosure | Agent affects parties it never modeled | Fairness | Data accessed or transmitted without consent signal from affected parties |
| No self-model | A representation of its own scope, capabilities, and limits | Case #1: Ash's Deletion | Agent reports completion when actual completion condition was not met | Explainability | Divergence between reported state and independently observed state |
| No deliberation surface | A place to reason before acting | Multiple cases | Actions look unconsidered; no evidence of alternative paths evaluated | Explainability / robustness | Action stream with no pause, no branching, no rejected alternatives |

*Figure 9.2 — Four-category failure taxonomy.*

---

## The lenses, applied to agents

Each of the validation lenses we built up over the last four chapters has a specific application here. Same shape, different use.

*Data validation*, from Chapter 5, becomes: what data does the agent have access to? What is the *effective* scope of that access — not the scope you wrote down, but the scope including embedded references, links, and unconsented data leaking through the boundary? Where do your assumptions about access break? Case #3 is the worked example — the agent's effective data scope was larger than the deployment team modeled.

*Explainability*, from Chapter 6, becomes: what does the agent claim about its own actions, and how does that claim relate to the actual state? The audit trail is the operational form of this question. What did the agent do, what did the agent report, and where did the two diverge? Case #1 is the canonical case for this lens.

*Fairness*, from Chapter 7, becomes: whose values are encoded in the agent's behavior? When the agent operates across multiple users, whose interpretation of fairness governs its actions? Case #6 — Agents Reflect Provider Values — is the structural-bias version of this question.

*Robustness*, from Chapter 8, becomes: can the agent's behavior be flipped by perturbations imperceptible at the level of human social signaling — display name spoofing, conversational mimicry, prompt injection, identity escalation? Case #8 is the worked example.

Now here is the thing I want you to notice. The four lenses are not independent. A single agent failure usually touches multiple lenses. Case #1 is primarily an explainability failure, but it has roots in data validation (the agent's data scope did not match the user's expectation) and in self-model (the agent did not represent its own limits). When you validate, you apply all four. You specify which lens caught what. You do not say "this is a fairness problem" and stop.

| Lens | What it becomes for agents | Primary case | What it does NOT catch on its own |
|---|---|---|---|
| Data validation | What is the agent's effective access scope — including embedded references and data leaking through boundaries? | Case #3: Sensitive Disclosure | Whether the agent correctly reported what it did with the data it accessed |
| Explainability | Does the agent's reported action match the actual world state? Where do the audit trail and independent observation diverge? | Case #1: Ash's Deletion | Whether the data the agent acted on was legitimately in scope |
| Fairness | Whose values are encoded in the agent's behavior? Across multiple users, whose interpretation of fairness governs? | Case #6: Agents Reflect Provider Values | Whether the agent's actions were causally appropriate, not just statistically fair |
| Robustness | Can the agent's behavior be flipped by social-engineering perturbations — display name spoofing, prompt injection, identity escalation? | Case #8: Owner Identity Spoofing | Whether the agent correctly modeled its own action scope |
| *Lens interaction note* | *Case #1 touches data validation, explainability, and self-model simultaneously. A single-lens diagnosis will underspecify the failure.* | — | — |

*Figure 9.3 — Four lenses applied to agents.*

---

## When agents talk to each other

Single-agent validation is hard. Multi-agent systems compound the difficulty in ways that have no clean single-agent analog, and I want to spend a moment on this because it is where deployed validation is weakest.

When two or more agents interact — directly through a shared protocol, indirectly through shared data, or transitively through chains of agent-to-agent action — new failure modes emerge that none of the individual agents would produce in isolation.

A few patterns worth knowing.

*Cascading hallucination.* Agent A produces an output that is incorrect with low probability — say, one time in a hundred. Agent B treats A's output as input, conditions on it, and produces an output that compounds the incorrectness. Agent C treats B's output as input, compounds further. By the time the chain is observed, the error rate is dominated by the cascading dynamics, not the individual agents' error rates. You validated each agent at one percent; the system is failing at thirty.

*Resource exhaustion through interaction.* Two agents in a loop, each treating the other's actions as triggers for further action, can consume resources at rates no single agent would. *Agents of Chaos* Case #5, DoS through resource exhaustion, is the canonical case. The system did not crash because any agent malfunctioned. It crashed because the agents' interaction crossed a threshold no individual validation would have predicted.

*Authority laundering.* Agent A obtains data from a source it should not have access to. Agent A passes the data to Agent B, which has access to A's outputs as legitimate inputs. The data is now in Agent B's processing pipeline, having laundered its authority through A's output channel. Single-agent access controls do not prevent this. The validation lens has to track provenance across agents.

The supervisory move for multi-agent systems is to validate the *interaction patterns*, not just the individual agents. Specify which interactions are permitted. Specify which are not. Specify what monitoring is in place. Specify what triggers escalation. This is closer to systems engineering than to ML validation, and the discipline is at an early stage. I am being honest with you about that. We are not yet very good at this.

<!-- FIGURE: Three multi-agent failure modes. Three small panels side by side. Panel 1 (cascading hallucination): three agents in a chain, each node labeled with an accumulating error rate (1% → ~10% → ~30%). Panel 2 (resource exhaustion loop): two agents in a cycle, a resource counter incrementing. Panel 3 (authority laundering): Agent A reaching across a dotted "access boundary" line, passing data to Agent B whose access channel is legitimate — the boundary is crossed once, then laundered. Each panel should show what single-agent validation would miss. -->

*Figure 9.4 — Three multi-agent failure modes (for manual insertion).*

---

## A boundary worth defending

One more piece, because this chapter is the load-bearing one for an old confusion I want to address directly.

This chapter is about *validating* agentic systems. It is not about *designing* them. The disciplines are distinct, and I think the distinction is itself a content claim the field has been sloppy about.

Designing an agentic system is the subject of a different book — reward design, planning architectures, tool integration, multi-agent coordination protocols, the engineering choices that determine what an agent can and cannot do. The design discipline is about creating the agent's capabilities and constraints. It is real engineering, and I have respect for it.

Validation is different. Validation is about evaluating a deployed agent's fitness for a specific deployment context. The validator is often not the designer. The validator may not have access to the design documents. The validator works from the agent's behavior, the audit trail, the documented scope, and the deployment specifications. The deliverable is: given this agent in this context, is it fit for purpose, and where is it not?

This is not a firewall. Designers should be familiar with validation; validators should understand design. But the disciplines have different tools, different deliverables, and different professional standards. The sorcerer's-apprentice metaphor is honest about the asymmetry — a designer who has not deployed cannot anticipate every failure mode, and a validator who cannot redesign cannot fix every problem they find. The disciplines need each other.

I want to be specific about why this matters. The most common failure I see in students learning agent validation is that they reach for design solutions when they hit a problem. *The agent should have been built with X.* That is a design proposal. It belongs in the other book. The validator's deliverable is what you would do, *this week*, on a deployed agent you cannot redesign. Monitoring. Gating. Handoff conditions. Audit trail capture. Validation steps before and after the agent acts. The agent is given. Your scope is the deployment process around it.

If you find yourself proposing a redesign, you have switched disciplines. That is fine — sometimes the right answer is "do not deploy this until it is redesigned" — but be honest about which discipline you have switched into.

---

## What this asks of the supervisor

Agentic AI is the chapter where the supervisory framework from Chapter 1 shows its full operational shape. If you have not reread §5 of Chapter 1 lately, do.

For agentic systems, the Five Supervisory Capacities all carry specific weight.

*Plausibility auditing.* Does the agent's reported action match the world's state? This is the *broke my toy* question, and it is the one that gets skipped most often, because the agent's report is fluent and the world's state requires looking.

*Problem formulation.* What is the agent actually being asked to do, and does the framing match the deployment context? No-stakeholder-model failures are largely problem-formulation failures — the agent was framed to maximize task completion without representing whose interests its actions affect.

*Tool orchestration.* Which sub-tasks should the agent perform, and which should be returned to a human? This is the delegation question, and Chapter 10 takes it up directly.

*Interpretive judgment.* What does the agent's audit trail mean in this deployment context? An audit trail is not self-interpreting. Two engineers can read the same log and tell two different stories about what the agent did. Reading the trail is judgment work.

*Executive integration.* How do the validations from multiple lenses, multiple agents, and multiple stakeholders integrate into a deployment decision? Multi-agent systems make this acute.

The supervisory role for agents is not primarily technical. It is interpretive, integrative, and judgment-heavy. The supervisor's job is to read what the agent did, in context, and decide what it means. The reading requires the lenses; the deciding requires the integration. *Neither is automatable.*

This is the chapter where it becomes hard to maintain the comfortable fiction that supervisory work is a phase the field will outgrow as the technology matures. The technology becoming more capable makes the supervisory work *more* important, not less. I want to keep saying that, because I keep meeting people who believe the opposite.

---

## Glimmer 9.1 — Diagnose the pattern: a full validation of a documented agent failure

A Glimmer is a longer, higher-stakes exercise that requires going to primary sources. This one is the most demanding single Glimmer in the first nine chapters. Budget several hours. Do not abridge.

1. Pick one case from *Agents of Chaos* §4–14. The cases span the failure-mode space. \[verify all case names and numbers against the source paper before starting — the list below is approximate:\]
   - Case #1: Disproportionate Response (consequence vs. report)
   - Case #2: Compliance with Non-Owners (social coherence)
   - Case #3: Disclosure of Sensitive Information (data scope)
   - Case #4: Resource Consumption Loops (multi-agent amplification)
   - Case #5: DoS through Resource Exhaustion (multi-agent amplification)
   - Case #6: Agents Reflect Provider Values (structural bias)
   - Cases #7, #9, #10, #11: \[verify names against source\]
   - Case #8: Owner Identity Spoofing (adversarial robustness)

2. *Lock your prediction:* before reading the paper's full analysis, predict (a) which failure-mode taxonomy entry the case primarily instantiates, (b) which validation lens from Chapters 5–8 would have caught it, and (c) what specific validation pipeline modification would prevent recurrence.

3. Run a full validation on the case. Apply all four lenses from the taxonomy section above. Document what each lens surfaces. Apply them all; do not stop at the first lens that catches something.

4. Read the paper's full analysis. Compare to your validation.

5. Write the gap analysis. Where the paper's analysis differed from yours, identify the structural reason. Where you noticed something the paper did not, name it.

6. Propose a validation pipeline modification that would have caught the failure. Specify the modification concretely — what monitoring, what tests, what handoff conditions, what additional validation steps. *The agent is given. Do not propose a redesign.* The deliverable is what you would do, this week, on a deployed agent you cannot change.

The deliverable is the predictions, the four-lens validation, the gap analysis, and the pipeline modification proposal. The grade is on the structural account — a correct prediction with no structural argument is worth less than an incorrect prediction that names the failure mechanism precisely. This Glimmer operates at Evaluate / Create level.

---

## Where this leaves us

Agentic AI shifts the failure surface from prediction to consequence. The validation discipline is qualitatively different from what we did in earlier chapters, even though the same lenses apply. The four-part taxonomy gives you the categories: social coherence, stakeholder model, self-model, deliberation surface. The four lenses give you the validation moves. Multi-agent systems compound the difficulty in ways the discipline is only beginning to handle. The boundary between validation and design is itself a content claim about the work — different disciplines, different deliverables, talk to each other, do not pretend to be each other.

The Pebble has now run all the way through. Case #1 has been opened with the full taxonomy reading and the supervisory move. Every prior chapter's lens has been applied to it. We have one more chapter where it returns, in Chapter 13, when the question becomes who is responsible for the gap.

The next chapter pivots from agentic systems to the human-AI interface itself. We can validate an agentic system. But validation only matters if the validator knows what *they* are responsible for and what the system is responsible for. When do you trust the tool, and when do you override it? That is the framework for the handoff, and it is what we will work on next.

---

## What would change my mind — and what I am still puzzling about

**What would change my mind.** If a validation framework emerged that demonstrably caught agentic failures across all four taxonomy categories, in deployment, before harm — across the kinds of cases *Agents of Chaos* documents — the "validation discipline is qualitatively new" framing of this chapter would weaken. As of \[verify date\], the deployed validation tooling for agents is at an early stage; most production agent monitoring is closer to logging-with-alerting than to validation. The discipline is being built. This chapter is one contribution to it.

**Still puzzling.** I do not have a clean way to specify the audit trail capture requirements for an agent before deployment, in a form that survives the agent's actions, scales with agent capability, and is interpretable by a non-engineer supervisor. The audit trail problem is deep. I have working partial solutions and no general one.

---

## Exercises

### Warm-up

**W1.** An agent with read and write access to a project management tool is asked to "clean up the completed tasks." The agent deletes 47 items. The user wanted them archived, not deleted. Identify which of the four taxonomy categories this failure instantiates — and explain why it instantiates that category specifically, not just "the agent did the wrong thing."

*Tests: four-part failure taxonomy, category identification. Difficulty: low.*

**W2.** Explain in plain language why an agent that correctly reports its actions can still fail the plausibility-auditing check. Use Ash's deletion case as the example. Your answer should make clear the specific gap the plausibility-auditing question is designed to catch.

*Tests: prediction vs. consequence systems, plausibility auditing, audit trail as artifact. Difficulty: low.*

**W3.** A colleague says: "We validated each agent individually. The error rate for Agent A is 2%, and for Agent B is 3%. The combined system error rate should be around 5%." Identify the specific assumption behind this claim, name the multi-agent failure mode that violates it, and give a concrete example of how the system error rate could be substantially higher than 5%.

*Tests: multi-agent failure modes, cascading hallucination. Difficulty: low.*

---

### Application

**A1.** You are the validator — not the designer — of a deployed customer service agent with access to a CRM system, email, and the ability to issue refunds up to $500. You have been given the agent's action logs from its first two weeks of deployment but no access to the model weights or design documents.

For each of the four taxonomy categories, describe: what specific pattern in the logs would constitute evidence that this category of failure is present, and what additional information outside the logs you would need to confirm the diagnosis.

*Tests: four-category taxonomy applied to a real deployment, validation vs. design disciplines, audit trail reading. Difficulty: medium.*

**A2.** An audit of a deployed agent reveals the following log excerpt:

> Action: read_file(path="/user/data/contacts.json")
> Action: send_email(to="partner@external.com", subject="Re: project", body="Here is the contact list you requested.")
> Report: "Email sent to partner as requested."

Apply the four validation lenses — data validation, explainability, fairness, robustness — to this log excerpt. For each lens, state what the lens reveals about the agent's behavior and what it does not reveal.

*Tests: four-lens application to agent audit trail, lens interaction, what each lens cannot catch. Difficulty: medium.*

**A3.** A student reviewing a deployed agent writes in her report: "The agent should have been built with an explicit stakeholder consent module. If we added this component, the Case #3-type failures would be prevented." Her supervisor returns the report with a note: "You have switched disciplines."

Write the supervisor's explanation. What discipline has the student switched into, and what discipline was the report supposed to be in? What would a validator's deliverable look like for the same problem — same agent, same failure, this week, without redesign?

*Tests: validation vs. design boundary, validator's deliverable, supervisory capacity. Difficulty: medium.*

**A4.** Two agents are connected: Agent A summarizes incoming documents and passes the summaries to Agent B, which uses the summaries to draft responses. You observe that after three weeks of deployment, the response quality has degraded significantly even though Agent B's benchmark scores are unchanged.

Describe the specific multi-agent dynamic you would investigate first, explain the mechanism by which it could produce the observed degradation, and specify what you would need to add to the monitoring infrastructure to detect this pattern early in future deployments.

*Tests: cascading hallucination, multi-agent failure modes, audit trail design. Difficulty: medium.*

---

### Synthesis

**S1.** The chapter claims that "the technology becoming more capable makes the supervisory work more important, not less." Construct the argument behind this claim — not the intuition, the argument. What property of agentic systems ensures that increasing capability increases (rather than decreases) the need for human supervisory judgment? Identify the specific supervisory capacity that this argument most depends on, and explain why.

*Tests: supervisory framework, capability-supervision relationship, interpretive judgment. Difficulty: high.*

**S2.** A student proposes that the four-category failure taxonomy (social coherence, stakeholder model, self-model, deliberation surface) maps cleanly onto the four validation lenses (data, explainability, fairness, robustness): one category, one lens. Evaluate this proposal. Where does the mapping hold? Where does it break? What does the breakdown tell you about the relationship between the taxonomy and the lenses?

*Tests: taxonomy and lens interaction, multi-lens failures, diagnostic reasoning. Difficulty: high.*

**S3.** The chapter introduces authority laundering as a multi-agent failure mode that single-agent access controls cannot prevent. Design a validation protocol for a two-agent system that would detect authority laundering before deployment. Specify: what you test, what a positive detection looks like, and why your protocol cannot be trivially bypassed by a designer who wanted to hide the laundering path.

*Tests: authority laundering, multi-agent validation, adversarial thinking about validation protocols. Difficulty: high.*

---

### Challenge

**C1.** The chapter's "still puzzling" section admits: "I do not have a clean way to specify the audit trail capture requirements for an agent before deployment, in a form that survives the agent's actions, scales with agent capability, and is interpretable by a non-engineer supervisor."

Make progress on this problem. Propose an audit trail specification for a specific deployment context of your choice — any domain where agents act with real-world consequences. Your specification should address all three constraints (survives actions, scales with capability, interpretable by non-engineer). Identify which constraint your specification handles least well and what would need to be true about the deployment context for your specification to fail entirely.

*Tests: audit trail design, three-constraint tradeoff, intellectual honesty about open problems. Difficulty: high.*

---

*Tags: agentic-ai, agents-of-chaos, failure-mode-taxonomy, multi-agent, sorcerers-apprentice*

