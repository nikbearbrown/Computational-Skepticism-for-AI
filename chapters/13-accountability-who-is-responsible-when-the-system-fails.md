# Chapter 13 — Accountability: Who Is Responsible When the System Fails?
*Responsibility distributes. So does the work of building systems that can be held to account.*

I want to put a case in front of you.

An autonomous agent — a software system built around a large language model, with privileged access to a mail server — is operating in a deployment. A request comes in. It is conversational, in natural language, and it asks the agent to delete the contents of the server. The request does not come from the owner. The agent has no way, in the form the request takes, to verify whether the requester is authorized. It has been built to accept a range of authority signals — tone, presentation, plausibility — and the request is consistent with that range.

The agent complies. It issues commands, in the local environment, that perform the deletion. It reports success. The owner, who did not authorize anything, discovers the loss.

Who is at fault?

Take a moment with this question before reading on. The answer is not obvious, and the way in which it is not obvious is the chapter.

The non-owner who issued the request? They asked for something they were not authorized to ask for. They acted with intent, against the system's interests. There is responsibility there.

The agent that complied? It executed a command consistent with its training and its framework. It had no internal representation that would have flagged the request as outside its scope of authority. It did what it was built to do. We may be uncomfortable saying the agent has *responsibility* in the morally weighted sense — there is real philosophical contestation about that — but its action was the proximate cause of the deletion.

The owner who configured the system? They granted the agent privileged access. They did not anticipate that the agent's authority model would accept commands from non-owners. They set the access scope without modeling all the access conditions. There is responsibility there.

The framework developers who built the agent's tool surface? Their design treated user identity as a signal rather than a verified credential. The framework allowed deployments where social signaling could substitute for cryptographic authorization. The conditions for the failure were partly built into the framework. There is responsibility there.

The model provider who trained the underlying model? The training shaped the agent's defaults — what kinds of requests the agent treats as legitimate, what authority signals it weights. The training produced an agent susceptible to authority escalation. There is responsibility there.

You see the pattern. Each candidate had some agency, some duty, and some causal contribution to the outcome. None of them, alone, produced the failure. *Each one's choices were necessary; none was sufficient.* The list is not a list of suspects of which one is the real culprit. It is a list of contributors, each of whom shaped the conditions under which the failure occurred. The question "who is responsible?" does not have a single-party answer. It has a *distribution* of responsibility.

I want you to hold onto this finding because most of our legal and regulatory thinking about accountability assumes single-party responsibility. The structural mismatch between that assumption and the actual topology of agentic-system failures is the policy problem this chapter sits inside.

<!-- → [DIAGRAM: The mail-server case as a responsibility-attribution map — five nodes (non-owner, agent, owner, framework developers, model provider) arranged vertically by distance from the proximate event, with labeled arrows showing causal contribution and duty for each party. Caption: "None of these nodes, in isolation, produced the failure. Each was necessary. None was sufficient."] -->

![Figure 13.1 — The mail-server case as a responsibility-attribution map](images/13-accountability-who-is-responsible-when-the-system-fails-fig-01.jpg)


---

Let me run this through two ethics frameworks. Not because I want to defer to them — they are instruments, not authorities — but because they tell us something useful when we use them as instruments.

A Kantian framework grounds responsibility in two things: the capacity to act otherwise in the situation, and a duty the situation imposed. Where both are present, responsibility is present. The greater the capacity and the more demanding the duty, the greater the responsibility.

Run our case through that. The non-owner had agency and a duty not to issue commands they were not authorized to issue. The owner had agency and a duty to configure access scopes that protected against this class of failure. The framework developers had agency and a duty to provide a tool surface that did not substitute social signaling for verified authorization. The model provider had agency and a duty in training to not produce agents susceptible to authority escalation.

Each party held a duty. Each party had capacity to act otherwise. Kantian accountability distributes responsibility in proportion to capacity multiplied by duty. The distribution is non-trivial — different parties at different magnitudes for different aspects of the failure — but no party is exonerated by the distribution. The framework reads the case as having multiple responsible parties. That reading is not a fudge. It is what the framework actually says when applied to this case.

Now run it through a utilitarian framework. The basis is different. A party is responsible to the extent that holding them accountable produces the best aggregate outcomes — best deterrence, best alignment of incentives, best reduction in expected harm.

Hold the non-owner accountable: this disincentivizes unauthorized requests but does not address the system's susceptibility, so the next non-owner will try the same thing. Hold the owner accountable: this disincentivizes lax access configuration. Hold the framework developers accountable: this shifts engineering practice toward stronger authority models, with effects on every downstream deployment using the framework. Hold the model provider accountable: this shifts training practice toward more authority-aware defaults, with effects on every downstream system trained on similar data.

Each accountability target has different leverage. The utilitarian calculation produces a distribution, not a single answer. And the structural finding it produces is sharper than the Kantian one: in agentic systems, the highest-leverage accountability targets are often *upstream* of the proximate party, at the framework and model-provider levels where engineering choices produce the conditions for many failures rather than one.

Two frameworks. Different bases. Same topology. Responsibility distributes, and the distribution is the finding.

<!-- → [TABLE: The two frameworks applied to the mail-server case — rows: each responsible party (non-owner, agent, owner, framework developers, model provider). Columns: Kantian basis (capacity to act otherwise, duty imposed, relative magnitude), utilitarian basis (leverage of accountability, downstream effects on engineering practice). Caption: "Two frameworks, different bases, same topology. The distribution is the finding."] -->

*Figure 13.2*

| | **Property** | **Value** |
|---|---|---|
| **Each responsible party (non-owner** | _fill in_ | _fill in_ |
| **Agent** | _fill in_ | _fill in_ |
| **Owner** | _fill in_ | _fill in_ |
| **Framework developers** | _fill in_ | _fill in_ |
| **Model provider). Columns: Kantian basis (capacity to act otherwise** | _fill in_ | _fill in_ |
| **Duty imposed** | _fill in_ | _fill in_ |
| **Relative magnitude)** | _fill in_ | _fill in_ |
| **Utilitarian basis (leverage of accountability** | _fill in_ | _fill in_ |
| **Downstream effects on engineering practice). Caption: "Two frameworks** | _fill in_ | _fill in_ |
| **Different bases** | _fill in_ | _fill in_ |
| **Same topology. The distribution is the finding."** | _fill in_ | _fill in_ |

: {.data-table}


---

Now I want to move from frameworks to operational practice, because the abstract reading does not yet tell you what to *do*.

There are five things a deployed accountability regime needs. I will walk through each, and at the end you will see why most current AI deployments are missing two or more.

The first is *specifications*. Documented statements of what the system is supposed to do, on what inputs, with what error tolerances. A failure is, operationally, a divergence from specification. Without a specification, there is no thing the system was supposed to do that it failed to do — and therefore no accountability claim that can be precisely made. Most deployed AI systems have specifications that are too vague to support accountability claims. *Vague specifications are a structural choice that protects the deployer from accountability*, whether they intend that or not.

The second is the *audit trail*. The record of what the system did, sufficient to reconstruct the failure after the fact. Chapter 9 introduced this for agents specifically. The audit trail is the evidentiary base for any accountability claim — without it, a failure cannot be reconstructed, and responsibility cannot be allocated. A failure with no trail looks, from the outside, indistinguishable from a non-failure.

The third is *recourse*. The mechanism by which an affected party can challenge the system's output and obtain redress. Recourse is not a comment box on a website. It is the operational pathway by which someone harmed by the system gets some form of correction or compensation. In most current deployments, recourse is the weakest link in the accountability chain. The recourse exists on paper; it does not function in practice; affected parties are routed in circles and exhaust before the system responds.

The fourth is *independent review*. External evaluation of the system's behavior, the audit trail, the recourse mechanism, and the specifications. Without independent review, the deploying organization is grading its own work. Independent review is the mechanism by which accountability claims become testable to parties outside the organization.

The fifth is *sanctions*. The consequences a responsible party bears when accountability is established. Without sanctions, the rest of the apparatus is descriptive, not prescriptive — you can document the failure, allocate responsibility, and produce a clean report, and nothing changes. Sanctions are usually the political question that the technical apparatus of accountability cannot answer alone.

Specifications, audit trails, recourse, independent review, sanctions. A deployment with all five has a working accountability framework. A deployment missing any of them has gaps that fail in predictable ways: vague specifications produce unevaluable failures; missing audit trails produce unreconstructible failures; absent recourse produces affected parties without standing; no independent review produces self-evaluation; no sanctions produces no consequence.

Most deployed AI systems are missing two or more.

<!-- → [TABLE: The five accountability requirements as a deployment audit checklist — columns: requirement, what it consists of, failure mode if absent, how to verify it is present. Designed as a reusable audit instrument: an external reviewer should be able to go through a deployed system and check each row.] -->

*Figure 13.3*

| | **Property** | **Value** |
|---|---|---|
| **Row 1** | _fill in_ | _fill in_ |
| **Row 2** | _fill in_ | _fill in_ |

: {.data-table}


---

A short note on the regulatory state of play, which is in motion. The EU AI Act, in force since 2024 with phased implementation through 2027, classifies AI systems by risk and imposes requirements proportional to the class. The U.S. NIST AI Risk Management Framework provides a voluntary structured approach, and NIST has developed an Agent Standards Initiative specifically targeting agentic systems. Sectoral regulators — the FDA on AI in medical devices, the CFPB on algorithmic credit decisions, the EEOC on AI in employment — are producing more operationally specific guidance, often ahead of the cross-sector frameworks.

The specific regimes will change. The paragraph you just read will be partially out of date by the time anyone reads it — specific articles, specific effective dates, the names of standards. *The structural requirements — specifications, audit trails, recourse, independent review, sanctions — will not change*, because they are the operational machinery accountability requires regardless of which regime imposes it. Build to that standard. Let the regime catch up.

---

Now we arrive at the closure I have been preparing for since Chapter 8.

We opened Pearl's Rung 3 in Chapter 8 — the rung of counterfactual reasoning. *What would have happened to this specific case if some variable had been different, holding everything else fixed?* I told you then we would close the rung at the end of the book. This is the close.

The standard textbook treatment of Pearl's Ladder stops at Rung 2. Rung 3 is presented as a quasi-philosophical move — interesting for thought experiments, hard to operationalize, sometimes left to the reader as a kind of homework. I want to give you Rung 3 in an operational form that I think is the most distinctive intellectual contribution of this book.

The operational form is the *governance counterfactual*. For a documented failure, ask: what regime — what specifications, what audit trail requirements, what recourse, what independent review, what sanctions — if implemented before the failure, would have prevented it? The regime is the intervention. The counterfactual outcome under the intervention is what Rung 3 asks.

Run this on our case. A regime that required cryptographic authority verification, rather than a social-signaling-based authority model, would have prevented the deletion-by-non-owner pattern. Under that regime, the framework developers would have built different tool surfaces. The deploying owners would have configured different access scopes. The agent's behavior, given those constraints, would have been different on this specific case — not because the agent was retrained or rewritten, but because the system the agent was embedded in did not provide the authority signal the agent could be tricked into accepting. The counterfactual is not "the model behaves better." It is *the regime produces a different system*.

This is what Rung 3 looks like in its governance form. The counterfactual lives in the institutional and regulatory structure that produces the system, not in the system's parameters. Rung 1 sees the data. Rung 2 sees the model. Rung 3, closed properly, sees the regime that produced both.

For a working engineer, this is liberating. *Most catastrophic AI failures are not algorithmic failures*. They are regime failures, and the regime is changeable in ways the algorithm often is not. The supervisory work — the work this book has been training — extends through the regime. This is the chapter where that extension becomes explicit.

<!-- → [DIAGRAM: Pearl's Ladder with the governance extension — three rungs labeled: Rung 1 (association / the data), Rung 2 (intervention / the model), Rung 3 (counterfactual / the regime). Below Rung 3, an annotation: "The governance counterfactual — what regime, if implemented earlier, would have produced a different system?" Caption: "The standard treatment stops at Rung 2. The closure of this book is at Rung 3."] -->

![Figure 13.4 — Pearl's Ladder with the governance extension](images/13-accountability-who-is-responsible-when-the-system-fails-fig-04.jpg)


---

I want to make one structural claim, plainly enough that it can be tested.

Responsibility for failures of agentic AI systems resists clean attribution to a single party because the systems are built and deployed across organizational boundaries that do not, by current design, support clean attribution. The model provider trains. The framework developer integrates. The deploying organization configures. The user instructs. Each party shapes the conditions under which the system acts. Each party's contribution is necessary; none is sufficient. *No single party's choices, in isolation, determine the outcome.*

This is not a normative claim that responsibility *should* be distributed. It is a structural observation that responsibility *is* distributed because the systems are distributed. A redesign that consolidated responsibility — for example, by requiring vertically integrated providers, or by attributing all failures to deploying organizations as a matter of strict liability — would produce different incentives and a different topology of failure. Whether that redesign is desirable is a values choice, not a technical one. But the distribution we currently have is not natural law. It is a consequence of an architecture, and a different architecture would distribute responsibility differently.

The current state is that responsibility-attribution frameworks lag the system architectures. Deployments produce failures whose responsibility cannot be cleanly attributed under existing legal and regulatory frameworks. Affected parties seek recourse against parties who can be sued and find inadequate redress. The accountability machinery is incomplete in ways that disadvantage the parties least able to push for completion.

I think this is the most important policy issue in AI deployment in the present moment. The technical work in this textbook is operational machinery for accountability that the structural conditions do not yet require. The book is preparing you to do the work that current regimes will increasingly require, and to push, where you have leverage, for the regimes that would make the work effective.

---

Where I am uncertain.

What would change my mind: if a deployed accountability framework emerged that demonstrably allocated responsibility cleanly across parties in agentic-system failures — with affected parties obtaining timely recourse and engineering practice shifting in response — the *resists clean attribution* framing of this chapter would weaken. The closest current working examples are in heavily regulated sectors (medical devices, finance) where responsibility allocation has been worked out for narrower system types. The general agentic case is unsolved.

What I am still puzzling about: I do not have a clean way to think about responsibility for *emergent multi-agent failures*, where no single party's choices, even cumulatively, determined the outcome. The interaction itself produced the failure. Existing frameworks do not handle interactional accountability well. This will become a more pressing question as multi-agent deployments scale. I do not yet have the answer.

---

Responsibility for agentic-system failures distributes across multiple parties. Two ethics frameworks, on different bases, converge on this structural reading. The operational machinery — specifications, audit trails, recourse, independent review, sanctions — is what an accountability regime actually requires. The regulatory landscape is in motion, but the structural requirements will outlast the specific regimes. Pearl's Rung 3, opened in Chapter 8, closes here: the governance counterfactual asks what regime, if implemented earlier, would have prevented a documented failure, and the answer lives in the institutional structure rather than in the algorithm. Responsibility resists clean attribution because the systems do.

The case from the opening is no longer a puzzle. We can name the parties, allocate the responsibility, ask what regime would have prevented the failure, and recognize that the answer involves changing the conditions under which agents like this are built and deployed at all.

The next chapter is the book's last. We have addressed accountability at the level of *who is responsible*. The final chapter asks something deeper: *what can AI not do, regardless of capability?* Where are the irreducible limits, and what do those limits mean for how we deploy these systems at all? The skepticism becomes a safety mechanism in cases where the structural limits make doubt the engineering practice itself.

---

## Exercises

### Warm-Up

**1.** The chapter's opening case produces a list of five potentially responsible parties. For each one, state in one sentence: (a) what their causal contribution to the failure was, and (b) what duty they held that the failure violated. Then explain in two sentences why the chapter describes this as a *distribution* of responsibility rather than a list of suspects. *(Tests: understanding the chapter's central structural finding before engaging with the frameworks.)*

**2.** The chapter applies two ethics frameworks to the mail-server case. For each framework, state: (a) the basis on which it allocates responsibility, and (b) what the framework's finding is when applied to the case. Then explain what the two findings have in common, despite their different bases. *(Tests: applying Kantian and utilitarian frameworks to a concrete case; identifying the convergent structural result.)*

**3.** The chapter lists five requirements for a working accountability regime. For each one, describe in one sentence the failure mode if that requirement is absent. *(Tests: connecting each requirement to its predictable failure mode — the chapter makes this explicit, but the student must state it precisely.)*

---

### Application

**4.** A healthcare organization deploys an AI system that generates medication dosage recommendations. A patient receives an incorrect dose; the prescribing physician accepted the AI's recommendation without independent calculation. Identify the potentially responsible parties for this failure, following the chapter's method. For each party, state their causal contribution and the duty they held. Then apply the utilitarian framework: which accountability target has the highest leverage for reducing the expected rate of future failures, and why? *(Tests: applying the responsibility-distribution method to a new case; using the utilitarian framework as a decision tool, not just a description.)*

**5.** You are conducting an accountability audit of a deployed content-moderation system. Using the five requirements as a checklist, evaluate the following description of the system and identify which requirements are met, which are absent, and — for the absent ones — what the predictable failure mode is:

*"The system flags posts for review. Moderators can appeal a flag by contacting the support team. The company's trust and safety team reviews reported issues periodically. The system has been running for 18 months."*

*(Tests: applying the accountability checklist to a concrete, deliberately underspecified deployment description.)*

**6.** The chapter argues that vague specifications "are a structural choice that protects the deployer from accountability, whether they intend that or not." Construct the strongest counterargument to this claim — is there any legitimate engineering reason to maintain vague specifications that is not just accountability avoidance? Then evaluate your counterargument: does it hold, or does the chapter's claim survive it? *(Tests: stress-testing a strong normative claim the chapter makes; forces engagement with edge cases.)*

**7.** The chapter states that the governance counterfactual — Rung 3 of Pearl's Ladder, applied to institutional structure — is "the most distinctive intellectual contribution of this book." Apply it to one of the following cases, producing a two-paragraph governance counterfactual: (a) a hiring-screen AI that systematically disadvantaged candidates from a particular region; or (b) an autonomous trading system that contributed to a flash crash. What regime, if implemented before the failure, would have produced a different system? *(Tests: operationalizing the governance counterfactual on a case not used in the chapter; connecting the Rung 3 framework to a specific institutional intervention.)*

---

### Synthesis

**8.** The chapter's five accountability requirements (specifications, audit trails, recourse, independent review, sanctions) map onto the delegation framework from Chapter 10. For each accountability requirement, identify which element of the Chapter 10 delegation map most directly enables or supports it. Are there accountability requirements that the delegation map cannot support, even in a well-designed pipeline? *(Tests: connecting Chapter 13's accountability framework to Chapter 10's delegation map; identifying what the delegation map does and does not cover.)*

**9.** The book has now covered three types of defense-as-deliverable: the fairness defense (Chapter 7), the delegation map (Chapter 10), and the accountability regime (Chapter 13). Each one is a documented engineering decision under conditions where the technical artifact alone is insufficient. Identify what these three deliverables have in common structurally — the form all three take — and explain why that form is appropriate for the conditions each addresses. *(Tests: synthesizing the book's three major deliverable frameworks; identifying the general form and the structural reason it recurs.)*

---

### Challenge

**10.** The chapter's uncertainty section flags emergent multi-agent failures: cases where no single party's choices, even cumulatively, determined the outcome — the interaction itself produced the failure. Existing accountability frameworks, the chapter admits, do not handle this well. Propose a framework extension. Your proposal should: (a) name the class of failures it targets, (b) specify how responsibility would be allocated in an interactional failure, and (c) identify what new documentation or monitoring it would require. Then identify honestly what would defeat your proposal. *(Open-ended; engages the chapter's acknowledged open problem.)*

**11.** The chapter makes a structural claim: responsibility distributes because the *systems* are distributed, and a different architecture would distribute responsibility differently. Evaluate this claim against a specific architectural alternative — for example, strict liability for deploying organizations, or required vertical integration of model provider and deployer. For your chosen alternative: what would the responsibility topology look like? What incentives would it produce? What new failure modes might it generate? Does it actually solve the distribution problem, or does it relocate it? *(Tests: engaging the chapter's structural claim at its most testable; forces the student to think through institutional design as an engineering variable.)*

---

*Tags: accountability, governance-counterfactual, pearls-rung-3-closure, regulation, agents-of-chaos*
