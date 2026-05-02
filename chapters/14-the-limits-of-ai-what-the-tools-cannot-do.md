
# Chapter 14 — The Limits of AI: What the Tools Cannot Do
*Three things capability scaling cannot fix, and what the supervisor does about them.*

## Learning objectives

By the end of this chapter, you will be able to:

- Name the three categorical limits of AI systems — meaning, intentionality, and the data-world gap — and explain why capability scaling does not close them
- State what Turing's argument actually claims and what it does not, and do the same for Searle's Chinese Room
- Identify which deployments require skepticism as a safety mechanism versus skepticism as methodology, and specify the criteria that distinguish them
- Design the five components of an engineering practice for high-stakes deployments where the categorical limits bite
- Explain why the supervisor's authority to refuse deployment is the most important structural authority in the system
- Apply the full validation apparatus from across this book to a specific deployment and stake a position you can defend

## Prerequisites

The entire book. This chapter is the culmination. Chapter 2's calibration baseline runs here for the third and final time. Chapters 5, 8, and throughout — the three categorical limits are the structural statements of what those chapters were operational versions of.

---

## Why this chapter

Every prior chapter has built technical apparatus for validating AI systems. This chapter names the limits beyond which that apparatus cannot reach, specifies the engineering practices those limits demand, and asks you to use everything the book has given you on a specific case and stake a position you can defend.

---

## The model that passed every test and failed in deployment

I want to tell you about a system that passed every test the engineers gave it.

It was a clinical decision-support system, deployed in a regional health network. On its validation set, it achieved ninety-four percent accuracy. It met every internal review threshold. It cleared regulatory submission. The fairness metrics were within tolerance. The robustness analysis showed acceptable behavior under perturbation. The communication apparatus was in place. The accountability framework was documented. By every measure the engineers had specified, the system was ready.

The system went live. Within six months, three patients were harmed by recommendations the system made and the clinicians accepted.

The post-mortem found something I want you to feel the strangeness of, because the strangeness is the lesson. The system was tested on the question it was built to answer — *does this presentation match a category in the training data?* The harms came from a different question — *what is going on with this specific patient?* The two questions are related, but they are not the same. The system's accuracy on the first question was real. Its inability to answer the second question was structural, in the sense that *the system was never built to answer it*. And the validation framework did not surface the gap, because the validation framework was scoped to the first question.

The system passed every test the engineers designed. The engineers designed the wrong tests. The wrongness was not negligence. It was something deeper — a *categorical limit on what the system could be tested for*, given what the system was. The validator's job, looking back, was to recognize the limit and decide whether the deployment should proceed in its absence. The validator had not been trained for this recognition.

That is the training this chapter is for.

---

## Three categorical limits

There are three structural limits that bound what current AI systems can do, regardless of capability scaling. Each demands a specific engineering practice from the supervisor.

<!-- FIGURE 14.1: Table — columns: Limit | What it means | Why capability scaling doesn't fix it | Operational consequence for the supervisor. Rows: Meaning, Intentionality, Data-world gap. Students should use this as a quick-reference summary connecting each limit to earlier chapters. Insert via Datawrapper or image after authoring content. -->

**Limit 1: Meaning.** The system processes symbols. The symbols have referents in the world. The system has no representation of the referents. It manipulates the symbols; the meaning of the symbols — what they refer to in the world the user inhabits — is supplied by the user, not the system.

This is contested. There is serious literature arguing that contemporary large multimodal models acquire something like meaning through embedding structure and grounding via diverse modalities. [Verify: Bender et al. 2021 stochastic-parrots framing; Chalmers 2023 on consciousness and large language models; Searle 1980, *Minds, Brains, and Programs*.] I do not consider that question settled. But the contestation does not need to be resolved for the operational consequence to bind. *The system's behavior is inconsistent with the user's expectation of meaning often enough that the supervisor has to perform meaning-attribution for the system, and the supervisor cannot offload that work to the system itself.*

When the system's picture of the world and the user's picture of the world align, the deployment looks fine. When they come apart — at the boundary of the data, at the edges of the training distribution, in cases the system has never seen — the user is still reading the output as a statement about the world, and the system is still producing symbols. The gap between those two activities is the failure mode.

The engineering practice: *the supervisor does the semantic work*. When the system produces an output, the supervisor maps the output's symbols to their referents in the deployment context. When the system answers a question, the supervisor checks whether the answer is responsive to the question the user asked, not just the question whose symbols the system processed.

**Limit 2: Intentionality.** Intentionality, in the philosopher's sense, is *aboutness* — a thought is about something in the world; an utterance is directed toward something. The system produces outputs. The outputs do not, in any obvious sense, carry stable directedness. The system does not have intentions in the directed-toward sense; it has parameters and inputs and outputs. Two deployments of the same system, in different contexts, produce outputs the user reads as being about different things. The system's "aboutness" tracks the user's reading — it does not track an independent stable directedness.

This too is contested. Some readings of agentic systems argue that goal-pursuit is functionally equivalent to intentionality. Whether functional equivalence captures what intentionality is supposed to capture is a deep question I am not going to settle here. The operational consequence binds regardless: *the system's outputs do not carry stable referents across deployments, and the supervisor must supply the directedness.*

The engineering practice: *the supervisor treats the system's outputs as evidence to be interpreted in context, not as statements with fixed referents*. The interpretation is the supervisor's job. The system supplies the symbols; the supervisor supplies the directedness.

**Limit 3: The data-world gap.** The system is trained on data. The data is a sample of the world, captured by particular instruments, under particular conditions, with particular exclusions. The system's competence is over the data, not the world. *No amount of data scaling closes the gap*, because the gap is structural — the data is always less than the world, and the parts of the world not in the data are not learnable from the data.

This is the limit I am most certain of, and it is not contested the way the first two are. It is sometimes obscured by the claim that "with enough data, the model can generalize" — which is true within a distribution and false at the boundary. The boundary is where the gap lives. The boundary is where AI systems most often fail.

You have met this limit before, in different clothes. It was Hume's induction problem in Chapter 2. It was the access-boundary failure in Chapter 5. It was distribution shift in Chapter 8. The categorical statement — the data is always less than the world — is what all those operational versions are operational versions of.

The engineering practice: *the supervisor specifies the deployment distribution, monitors for distribution shift, and is prepared to override or reject the system's outputs when the deployment is operating in a region the data does not cover.*

---

## Turing and Searle — what each argument settles, what each does not

Two famous arguments about AI limits. Each gets cited a lot and understood less than it should be.

**Turing's argument (1950).** [Verify: Turing 1950, *Computing Machinery and Intelligence*.] If a machine can convincingly imitate a human in conversation, by what principled basis would we deny it intelligence? The argument is operationally elegant: *behavior is the testable evidence; behavior consistent with intelligence warrants the attribution.* It settles a methodological question — don't require something more than behavioral evidence for intelligence in machines, because we do not require something more for other humans.

What Turing's argument does not settle: whether the entity satisfying the test has *meaning* (Limit 1), *intentionality* (Limit 2), or the kind of competence over the world (Limit 3) that the test implicitly assumes. The test is over behavior. The limits are about what stands behind behavior. Turing himself, I think, knew this. The test was a methodological proposal, not a metaphysical claim. People who cite Turing as having shown that behavioral imitation *is* intelligence are giving him credit for a stronger claim than he made.

**Searle's argument (1980).** A person who does not speak Chinese is in a room with a rulebook. Slips of paper come in through a slot with Chinese symbols. The person follows the rules and produces Chinese symbols on slips that go out. To a Chinese-speaking observer outside, the inputs and outputs are indistinguishable from those of a Chinese speaker. The person does not understand Chinese. *Therefore symbol manipulation is not understanding.*

The argument settles a conceptual question: behavior consistent with understanding does not entail understanding. What it does not settle is whether contemporary systems are doing only symbol manipulation — or whether embedding structures, attention patterns, and multimodal grounding constitute something more. Searle's argument is a strong constraint on shallow accounts of meaning. It is not a deep constraint on what current architectures might be. People who cite Searle as having shown that AI systems *cannot* understand are giving him credit for a stronger claim than he made.

<!-- FIGURE 14.2: Two-argument comparison table — columns: Argument | What it actually claims | What it does NOT claim | Common misreading. Rows: Turing (1950) / Imitation Game, Searle (1980) / Chinese Room. Students should cite either argument correctly and identify overclaims when they encounter them. Insert via Datawrapper or image after authoring content. -->

The two arguments together produce a useful operational stance: *behavior is testable evidence and should be taken seriously — and behavior is not the whole of what we mean by understanding, meaning, or intentionality.* Both moves at once. The validator who only tests behavior misses the limits. The validator who only invokes the limits skips the testing. The job is to do both.

---

## Skepticism as safety mechanism — when the limits bite

There are deployments in which the categorical limits matter directly, and deployments in which they do not. The supervisor's job is to know the difference.

A system that classifies images of products on a manufacturing line is operating in a world where the limits are largely irrelevant. The system processes pixels; the user interprets the classifications; the deployment context is well-specified; the data-world gap is small and monitorable. Skepticism is methodology, not a safety mechanism. The supervisor verifies, monitors, calibrates. The limits do not bite hard.

A system that produces clinical recommendations, autonomous-vehicle decisions, agentic actions in shared social spaces, judicial-risk assessments, or large language model outputs in unbounded conversational contexts — these are deployments where the limits bite. *The supervisor's skepticism is the safety mechanism* in those settings, because the system's apparent competence outruns its actual competence in ways the metrics cannot fully capture. You can have ninety-four percent accuracy and three harmed patients, because the ninety-four percent was over the question the data could answer, and the three patients were in a region the data could not.

The engineering practice for high-stakes deployments:

**Specify what the system can be tested for, and what it cannot.** The documentation includes the limits explicitly. A regulator or adoption committee reading the documentation can see what the validation does and does not warrant — not because the limits are hidden in fine print, but because they are part of the validation product.

**Maintain human oversight at the points where the limits bite.** For meaning: a human reviews the semantic interpretation. For intentionality: a human supplies the directedness. For the data-world gap: a human monitors the deployment distribution and is empowered to override.

**Build the infrastructure for the override.** An override that is documented but practically impossible — no time, no authority, no legibility — is not an override. It is a fiction in the documentation. The clinician has to have the time and standing to disagree with the system. The override has to be the practice, not the disclaimer.

**Make the limits visible to affected parties.** When a system's recommendation is the basis for a decision affecting a person, the person should be able to know, to a useful approximation, what the system can and cannot do. This is not "explain the model." It is "specify the limits the model has, in a form the affected person can read." This is operationally underdeveloped — I am leaving it as an open problem, not a solved one.

**Have a stop condition.** There are deployments where the limits, given the stakes, are a reason not to deploy at all. The supervisor's authority to refuse deployment is, structurally, the most important authority in the whole system. Most current deployments do not preserve it. The validator is hired to validate; the validation is expected to clear; the option of refusal is assumed away.

The skepticism this book has built — the supervisory capacities, the validation lenses, the delegation maps, the verb taxonomy, the governance counterfactual — culminates in an engineering practice that *includes the option to say no*. A practice without that option is not the practice this book is teaching. That is the most important thing I have to say in this whole book.

---

## Rapid prototyping for skepticism — testing assumptions before deployment

A practical move: how do you design an experiment that tests a model assumption before deployment, when the assumption is what you are trying to test?

The structure is the same one used throughout the book.

1. *Specify the assumption explicitly.* Often the assumption is implicit in the architecture or the deployment context. Make it explicit in writing.
2. *Identify what evidence would falsify the assumption.* Use Popper's move. The falsifying evidence may be unobservable in deployment but observable in a designed test.
3. *Design the test.* The test should be cheap to run, fast to interpret, and capable of producing evidence one way or the other.
4. *Pre-register predictions.* Before running the test, lock the prediction. The lock is what makes the test informative.
5. *Run the test.* Observe. Document the gap.
6. *Decide.* The test informs the deployment decision: proceed, modify, defer, refuse.

This is not novel methodology. It is the scientific method made operational for AI deployment decisions. The novelty is doing it at all. Most AI deployments skip the prototyping-to-test-assumptions step and rely on the validation set as a substitute. The validation set tests performance under conditions sampled from the same distribution as training. It does not test the assumption. The assumption requires its own test.

For your own deployments: identify, before you launch, the two or three load-bearing assumptions in the system. Build a small experiment for each. Run them. Make the deployment decision after, not before.

---

## Glimmer 14.1 — The Limits Exercise

The exercise:

1. Pick a specific AI deployment — from the news, from your own work, from a course case study. Document it: what does the system do, in what context, with what stated specifications.
2. *Lock your prediction:* identify which of the three categorical limits is most operationally relevant for this deployment. Predict where the limit will bite hardest and what failure mode the bite will produce.
3. Design an engineering practice — using the five components above — that addresses the limit. Specific enough that a deploying engineer could implement it.
4. Compare your designed practice to what is actually in place in the deployment (if knowable). Where are the gaps? Are the gaps explained by the deploying organization's documentation, or invisible?
5. State whether the deployment should proceed in its current form, be modified, be deferred, or be refused. Defend the position. Use the verb taxonomy from Chapter 12 — the verb of your judgment should match the evidence you have.
6. Identify what would change your mind.

The deliverable is the deployment documentation, the prediction, the designed practice, the gap analysis, and the position statement. The grade is on the position statement and the change-of-mind. *The exercise is the operational form of the entire book.* You have the validation apparatus. The Glimmer asks you to use it on a specific case and stake a position you can defend.

This is a high-stakes Glimmer for some students. The discomfort is the point. Engineers will have to make these calls in deployment for the rest of their careers, and the calls will not always be welcomed by the organizations that pay them. The Glimmer is rehearsal.

---

## The third calibration baseline — closing the arc

For the third and final time, you take the same form of forecasting questions. You provide 90% confidence intervals for each. You check what fraction of your intervals contain the truth.

The expected pattern across the three baselines:

- *First baseline (Chapter 2):* most students score in the 0.4–0.6 range. Systematically overconfident.
- *Second baseline (Chapter 8):* most students improve to the 0.6–0.75 range. Eight weeks of supervisory practice has begun to shift the stance toward uncertainty.
- *Third baseline (this chapter):* most students reach the 0.7–0.85 range. Calibration is improving but rarely reaches 0.9 — overconfidence is sticky.

If your trajectory does not match this pattern, the apparatus has not landed in one of two ways. If you are not improving, the apparatus has not shifted your operational stance, and structural reflection is overdue. If you are improving dramatically on the calibration baselines but not in your project work, the apparatus has been treated as a course mechanic rather than a working practice.

The third baseline is a diagnostic on the supervisor you are becoming, not a grade. The data is yours. Most engineers operate, throughout their careers, at calibrations between 0.5 and 0.7 on questions where they are stating 90% confidence. They do not know this because nobody runs the experiment on them. *You can be different.* The baseline shows whether you are.

---

## What would change my mind

If a development emerged that demonstrably closed one of the three categorical limits — meaning, intentionality, data-world gap — across diverse architectures and deployment contexts, the "skepticism as safety mechanism" framing would weaken to "skepticism as one approach among several." I do not see a candidate in current research. The work that addresses each limit (semantic grounding research, agentic alignment, distribution-shift detection) is making progress on operational instances. It is not closing the categorical limits. I am open to being shown otherwise.

The open problem I most want to flag: I do not know how, at scale, to make the limits visible to affected parties in a form they can act on. Disclosure-style approaches are partial; they often produce the appearance of transparency without the practical effect. The general problem of communicating AI limits to non-technical affected parties, at the moment of consequential decisions, is open.

---

## The closing argument

A last structural claim, made directly so that it can be tested.

This book has been about a specific class of work: supervising AI systems for fitness in deployment contexts. The work has technical and judgment components. The technical components — calibration, bias analysis, data validation, explainability evaluation, fairness defense, robustness testing, agentic-system validation, delegation mapping, communication of findings, accountability allocation — are the chapters you have read. The judgment components — when to defer, when to override, when to refuse — are the throughline.

The work is *engineering* in the sense that it requires technical competence, produces deliverables, and is testable against criteria. It is *supervisory* in the sense that it is irreducibly about the relationship between the AI system, the deployment context, and the affected parties — and that relationship is not in the system. The system does not know about the relationship. The relationship is the supervisor's job.

The work will become more important, not less, as AI systems become more capable. The capacity for confident wrongness scales with capability. The supervisory capacities scale with deliberate practice. The ratio of supervisor-engineering-graduates to deployed AI systems is, by my reading of the field, going the wrong direction. *The bottleneck is not models. The bottleneck is supervisors who can do this work.*

You have done a course's worth of the work. You are not yet expert. The course is the beginning of a practice, and the practice gets built over years. The instruments — the journal, the delegation maps, the calibration baselines, the verb taxonomy, the governance counterfactual — are tools you take with you. The judgment is what you build through using them.

I am not optimistic that the field will, on its current trajectory, develop enough supervisors fast enough. I am not pessimistic either. *The work is the variable.* Engineers who do the work will be needed. Engineers who do not will, increasingly, be a liability to the organizations that employ them. The choice between those two paths is in the practice you build after this book closes.

The system passed every test. The engineers designed the wrong tests. Three patients were harmed.

You can be the engineer who designs the right tests. You can be the validator who recognizes the limit and decides the deployment should not proceed in its absence. You can be the supervisor who has the authority to refuse, and who uses it, and who builds the infrastructure for that authority to be real.

Go do the work.

---

## Connections to the research project

There is no Chapter 15. The course's last week is the research-project presentations. Your research project (Version 4.0, the final version) is the operational form of the whole book:

- The validation pipeline (Chs. 5–9), with the delegation map (Ch. 10)
- The findings, communicated visually (Ch. 11) and in prose (Ch. 12), with calibrated verbs and named uncertainty
- The accountability framework (Ch. 13), with the regime under which the deployment would operate
- The categorical-limit analysis (this chapter), with your position on whether the deployment should proceed
- The Frictional record (Ch. 4), retained as supervisory log
- The AI Use Disclosure (Chs. 4 and 10), formatted as evidence of your supervisory work

The final paper is the layered writeup. The presentation is the Living Deck (Ch. 11) with the changelog visible. The defense is the verb taxonomy in real time.

---

## Exercises

### Warm-up

**1.** In one paragraph each, explain the three structural limits — meaning, intentionality, and the data-world gap — in plain language, without using the technical vocabulary introduced in this chapter. Then, for each limit, name the chapter earlier in the book where you first encountered the operational version of that limit. *(Tests: comprehension of the three limits and their connection to prior chapters)*

**2.** The chapter distinguishes between two readings of Turing's argument: the claim Turing actually made, and the stronger claim often attributed to him. State both in one sentence each, and explain in two sentences why the distinction matters for a validator deciding whether to deploy a system that has passed a behavioral test. *(Tests: Turing argument precision)*

**3.** A system classifies manufactured parts on an assembly line with 99.1% accuracy. A second system generates clinical recommendations for rare-disease diagnosis with 94% accuracy. Using the chapter's framework, explain why the supervisor's role is structurally different in the two deployments, even though the second system has lower accuracy. *(Tests: where-limits-bite reasoning)*

### Application

**4.** You are writing the limitations section of a validation report for a large language model deployed as a customer service agent for a financial services company. Using the three structural limits as your framework, write the section. For each limit: name it, describe how it manifests specifically in this deployment context, specify what human oversight is required as a result, and describe what the override infrastructure looks like. *(Tests: translating abstract limits into deployment-specific documentation)*

**5.** A colleague argues: "The meaning limit will disappear in a few years as multimodal models improve. We should design our validation frameworks now to be consistent with what the models will be capable of then, not what they are today." Write a two-paragraph response. Acknowledge what is right about the argument and explain what is wrong with it for the purpose of today's deployment decisions. *(Tests: engagement with the contested nature of the limits; operational consequence binding)*

**6.** The chapter says: "An override that is documented but practically impossible — no time, no authority, no legibility — is not an override. It is a fiction in the documentation." Design a test for whether a human override mechanism is real or fictitious. Your test should produce a result the deployment team can act on, and should be runnable before the system goes live, not after. *(Tests: override-infrastructure concept operationalized)*

**7.** The clinical decision-support system from the chapter's opening passed every test the engineers gave it. You are the validator who is called in before deployment. Using only the tools and frameworks from this book, describe the specific tests you would have run that the original engineers did not run — and for each test, name the chapter it comes from and explain why it would have surfaced the gap. *(Tests: full-book synthesis applied to the opening case)*

### Synthesis

**8.** The chapter claims: "The supervisor's authority to refuse deployment is, structurally, the most important authority in the whole system." An engineering manager responds: "That authority exists on paper but is not realistic in practice — the business case has already been made, the procurement is done, the announcement is scheduled." Write a structured response that (a) acknowledges the real institutional forces the manager is describing, (b) explains what the chapter's claim means in light of those forces, and (c) identifies one structural change to the validation process that would make refusal more institutionally available. *(Tests: stop-condition concept, institutional realism, chapter's culminating argument)*

**9.** The chapter distinguishes deployments where the limits "bite hard" from those where they do not. Develop a one-page classification rubric — not a list of examples, but a set of criteria — that a supervisor could use to determine, for any proposed deployment, how hard the three limits will bite. For each limit, specify the conditions under which it becomes a safety-mechanism-level concern rather than a methodology-level concern. *(Tests: generalizing the chapter's deployment-type distinction into a reusable tool)*

**10.** The book ends with the sentence: "Go do the work." Identify what, specifically, "the work" consists of — drawing from across all fourteen chapters. Your answer should name at least six specific practices, explain how they connect to each other, and describe what a practitioner looks like who is doing the work versus one who is not. This is the Feynman test for the whole book: can you now teach it to someone else? *(Tests: full-book synthesis and the Feynman criterion)*

### Challenge

**11.** Searle's Chinese Room argument has generated a large philosophical literature, including the "Systems Reply" — the counterargument that even if the person in the room does not understand Chinese, the *system* (person plus rulebook plus room) might. Research the Systems Reply and at least one other major counterargument to Searle. Then write a structured analysis: (a) state each argument's strongest version, (b) explain what it would mean for the operational consequences of the meaning limit if the Systems Reply is correct, and (c) explain why the operational consequences bind for today's deployments regardless of whether the philosophical question is resolved. *(Research and analysis; tests engagement with the contested nature of the limits)*

**12.** Design a validation framework for a deployment that sits at the hardest intersection of all three limits: an AI system used to support parole board decisions, where the system's outputs influence whether a person is released from incarceration. Your framework should: (a) specify what each of the three limits means in this specific context, (b) identify the testing regime for what the system can be tested for, (c) specify in writing what the system cannot be tested for and why, (d) describe the human oversight infrastructure required at each limit, (e) specify the stop conditions — the findings that would lead you to recommend against deployment, and (f) describe what "the work" looks like for the supervisor operating this system over its deployment lifetime. *(Open-ended capstone; integrates the full book)*

---

## Chapter summary

You can now do five things you could not do before this chapter — and that the book has been building toward.

You can name the three categorical limits — meaning, intentionality, the data-world gap — explain why capability scaling does not close them, and trace each limit back to where you first encountered its operational version earlier in the book. You can state what Turing's argument actually claims and what it does not, and do the same for Searle's, and catch the overclaim when you encounter it. You can distinguish deployments where the limits require skepticism as a safety mechanism from those where skepticism is methodology — and specify the five components of the engineering practice the safety-mechanism cases demand. You can design and run a rapid prototype test for a load-bearing deployment assumption, pre-registered and gap-analyzed. And you can stake a position on a specific deployment — proceed, modify, defer, refuse — using the verb taxonomy, and identify what would change your mind.

The system passed every test. The engineers designed the wrong tests. You are now in a position to design different tests, to name the limits the tests cannot reach, and to hold the authority to refuse when the limits and the stakes together make refusal the right call.

Go do the work.

---

*Tags: limits-of-ai, meaning, intentionality, data-world-gap, turing, searle, calibration-baseline, stop-condition, supervisory-engineering*
