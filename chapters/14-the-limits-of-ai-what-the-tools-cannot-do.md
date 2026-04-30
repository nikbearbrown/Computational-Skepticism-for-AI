# Chapter 14 — The Limits of AI: What the Tools Cannot Do
*Three things capability scaling cannot fix, and what the supervisor does about them.*

I want to tell you about a system that passed every test the engineers gave it.

It was a clinical decision-support system, deployed in a regional health network. On its validation set, it achieved ninety-four percent accuracy. It met every internal review threshold. It cleared regulatory submission. The fairness metrics were within tolerance. The robustness analysis showed acceptable behavior under perturbation. The communication apparatus was in place. The accountability framework was documented. By every measure the engineers had specified, the system was ready.

The system went live. Within six months, three patients were harmed by recommendations the system made and the clinicians accepted.

The post-mortem found something I want you to feel the strangeness of, because the strangeness is the lesson. The system was tested on the question it was built to answer — *does this presentation match a category in the training data?* The harms came from a different question — *what is going on with this specific patient?* The two questions are related, but they are not the same. The system's accuracy on the first question was real. Its inability to answer the second question was structural, in the sense that *the system was never built to answer it*. And the validation framework did not surface the gap, because the validation framework was scoped to the first question.

The system passed every test the engineers designed. The engineers designed the wrong tests. The wrongness was not negligence. The wrongness was something deeper than negligence — it was a *categorical limit on what the system could be tested for*, given what the system was. The validator's job, looking back, was to recognize the limit and decide whether the deployment should proceed in its absence. The validator had not been trained for this recognition.

That is the training I want this chapter to be. It is the last chapter of this book, and I want to use it for something specific — to name the limits that, in my view, cannot be patched, no matter how much capability we add. There are three of them. They are the structural reasons that even an excellent AI system, deployed thoughtfully, can fail in ways that no metric will catch.

---

The first limit is *meaning*.

The system processes symbols. The symbols have referents in the world — the words refer to things, the numbers refer to quantities, the categories refer to clinical conditions or fraud patterns or whatever the application is. The system has no representation of the referents. It manipulates the symbols. The meaning of the symbols, in the sense of *what they refer to in the world the user inhabits*, is supplied by the user, not the system.

This is contested. There is a serious literature arguing that contemporary models — especially large multimodal ones — acquire something like meaning through the structure of their embeddings, through grounding via images and other modalities, through the patterns of association they learn over enormous corpora. I do not consider that question settled, and I am not going to pretend to settle it here. But — and this is the engineering point — the contestation does not need to be resolved for the operational consequence to bind. *The system's behavior is inconsistent with the user's expectation of meaning often enough that the supervisor has to perform meaning-attribution for the system, and the supervisor cannot offload that work to the system itself.*

The user reads the output as a statement about the world. The system produced the output without any model of what the world contains. When those two pictures align, the deployment looks fine. When they come apart — and they come apart at the boundary of the data, at the edges of the training distribution, in cases the system has never seen — the user is still reading the output as a statement about the world, and the system is still producing symbols. The gap between those two activities is the failure mode.

---

The second limit is *intentionality*.

This is the philosopher's word for *aboutness* — a thought is *about* something in the world; an utterance is *about* something. When you say to a friend, "the kettle is on," your statement is *directed toward* a particular kettle, in a particular kitchen, that you intend to refer to. The directedness is not a property of the words. It is a property of you, the speaker, and your relationship to the world the words are pointing at.

The system produces outputs. The outputs are not, in any obvious sense, *about* the things the user takes them to be about. The system does not have intentions in the directed-toward sense; it has parameters and inputs and outputs. Two deployments of the same system, in different contexts, produce outputs that the user reads as being about different things. The system's "aboutness" tracks the user's reading. It does not track an independent stable directedness.

This too is contested. Some readings of agentic systems argue that goal-pursuit is functionally equivalent to intentionality — if the system reliably produces behavior that achieves a stated objective, and the behavior is sensitive to obstacles in a flexible way, that may be all the "aboutness" that intentionality ever was. Whether functional equivalence captures what intentionality is supposed to capture is a deep question, and I am again not going to settle it. But the operational consequence binds without the resolution: *the system's outputs do not carry stable referents across deployments, and the supervisor must supply the directedness.*

---

The third limit, and the one I am most certain of, is *the gap between data and world*.

The system is trained on data. The data is a sample of the world, captured by particular instruments, under particular conditions, with particular exclusions. The system's competence is over the data, not the world. *No amount of data scaling closes the gap*, because the gap is structural — the data is always less than the world, and the parts of the world that are not in the data are not learnable from the data.

This one is not contested in the way the first two are. It is sometimes obscured by the claim that "with enough data, the model can generalize," which is true within a distribution and false at the boundary of the distribution. The boundary is where the gap lives. The boundary is where AI systems most often fail, and the failures look surprising because the validation set was inside the boundary and the deployment crosses outside it.

You have met this limit before, in different clothes. It was Hume's induction problem in Chapter 2. It was the access-boundary failure in Chapter 5, where the dataset extended through references the team did not control. It was distribution shift in Chapter 8, where the model continued to look confident through changes its training had not anticipated. The categorical statement — *the data is always less than the world* — is the thing all those operational versions are operational versions of.

<!-- → [TABLE: Three structural limits — columns: Limit | What it means | Why capability scaling doesn't fix it | Operational consequence for the supervisor. Rows: Meaning, Intentionality, Data-world gap. Students should be able to use this as a quick-reference summary of the chapter's core argument and connect each limit to earlier chapters.] -->

*Figure 14.1*

| | **Property** | **Value** |
|---|---|---|
| **Meaning** | _fill in_ | _fill in_ |
| **Intentionality** | _fill in_ | _fill in_ |
| **Data-world gap. Students should be able to use this as a quick-reference summary of the chapter's core argument** | _fill in_ | _fill in_ |
| **Connect each limit to earlier chapters.** | _fill in_ | _fill in_ |

: {.data-table}


Three limits. Meaning. Intentionality. The data-world gap. They bound what the tools can do, and they bound them in a way that does not yield to capability scaling.

---

I want to take a moment for two famous arguments about AI limits, because they get cited a lot and understood less than they should be.

The first is Turing's. In 1950 he proposed something now called the Turing Test, and the proposal was operationally elegant: if a machine can convincingly imitate a human in conversation, by what principled basis would we deny it intelligence? *Behavior is the testable evidence; behavior consistent with intelligence warrants the attribution.* The argument settles a methodological question. Don't require something more than behavioral evidence for intelligence in machines, because we do not require something more for other humans either.

What Turing's argument does not settle — and this is the part that gets lost — is whether the thing satisfying the test has *meaning* (the first limit), *intentionality* (the second), or the kind of competence over the world (the third) that the test is implicitly assuming. The test is over behavior. The limits are about what stands behind behavior. Turing himself, I think, knew this. The test was a methodological proposal, not a metaphysical claim. People who cite Turing as having shown that behavioral imitation *is* intelligence are giving him credit for a stronger claim than he made.

The second argument is Searle's. He offered a thought experiment in 1980 — the Chinese Room. A person, who does not speak Chinese, is in a room with a rulebook. Slips of paper come in through a slot, with Chinese symbols on them. The person looks up the symbols in the rulebook, follows the rules, and produces other symbols on slips of paper that go out through the slot. To a Chinese-speaking observer outside the room, the inputs and outputs are indistinguishable from those of a Chinese speaker. The person in the room does not understand Chinese. *Therefore symbol manipulation is not understanding.*

The argument settles a conceptual question: behavior consistent with understanding does not entail understanding. What it does not settle is whether contemporary systems are doing only symbol manipulation, or whether the embedding structures, the attention patterns, the multimodal grounding constitute something more. Searle's argument is a strong constraint on shallow accounts of meaning. It is not a deep constraint on what current architectures might be. People who cite Searle as having shown that AI systems *cannot* understand are giving him credit for a stronger claim than he made.

<!-- → [TABLE: Two-argument comparison — columns: Argument | What it actually claims | What it does NOT claim | Common misreading. Rows: Turing (1950) / Imitation Game, Searle (1980) / Chinese Room. Students should be able to cite either argument correctly and identify the overclaim when they encounter it.] -->

*Figure 14.2*

| | **Property** | **Value** |
|---|---|---|
| **Turing (1950) / Imitation Game** | _fill in_ | _fill in_ |
| **Searle (1980) / Chinese Room. Students should be able to cite either argument correctly** | _fill in_ | _fill in_ |
| **Identify the overclaim when they encounter it.** | _fill in_ | _fill in_ |

: {.infographic-table}


The two arguments together produce a useful operational stance, and I want to state it as plainly as I can. *Behavior is testable evidence and should be taken seriously. And behavior is not the whole of what we mean by understanding, meaning, or intentionality.* Both moves at once. The validator who only tests behavior misses the limits. The validator who only invokes the limits skips the testing. The job is to do both.

---

Now: where do these limits actually bite, and where do they not?

There are deployments in which the categorical limits matter directly, and deployments in which they do not. The supervisor's job is to know the difference. A system that classifies images of products on a manufacturing line is operating in a world where the limits are largely irrelevant — the system processes pixels, the user interprets the classifications, the deployment context is well-specified, the data-world gap is small and monitorable. Skepticism is methodology, not a safety mechanism. The supervisor verifies, monitors, calibrates. The limits do not bite hard.

A system that produces clinical recommendations, autonomous-vehicle decisions, agentic actions in shared social spaces, judicial-risk assessments, large language model outputs in unbounded conversational contexts — these are deployments where the limits bite. *The supervisor's skepticism is the safety mechanism* in those settings, because the system's apparent competence outruns its actual competence in ways the metrics cannot fully capture. You can have ninety-four percent accuracy and three harmed patients, because the ninety-four percent was over a question the data could answer, and the three patients were in a region the data could not.

What does the engineering practice look like in those settings? It looks like this. You specify, in writing, what the system can be tested for and what it cannot. You include the limits explicitly in the documentation. A regulator or an adoption committee reading the documentation can see what the validation does and does not warrant — not because you have hidden the limits in fine print, but because the limits are part of the validation product.

You maintain human oversight at the points where the limits bite. For meaning, a human reviews the semantic interpretation. For intentionality, a human supplies the directedness. For the data-world gap, a human monitors the deployment distribution and is empowered to override.

You build the infrastructure for the override. An override that is documented but practically impossible — no time, no authority, no legibility — is not an override. It is a fiction in the documentation. The infrastructure has to be real. The clinician has to have the time and the standing to disagree with the system. The autonomous-vehicle backup driver has to be more than a regulatory compliance fixture. The override has to be the practice, not the disclaimer.

You make the limits visible to affected parties. When a system's recommendation is the basis for a decision affecting a person, the person should be able to know, to a useful approximation, what the system can and cannot do. This is not "explain the model." It is "specify the limits the model has, in a form the affected person can read." I am not pretending this is solved. It is one of the open questions I will leave you with.

And finally — and this is where supervisory engineering becomes something more than methodology — *you have a stop condition*. There are deployments where the limits, given the stakes, are a reason not to deploy at all. The supervisor's authority to refuse deployment is, structurally, the most important authority in the whole system. Most current deployments do not preserve it. The validator is hired to validate; the validation is expected to clear; the option of refusal is assumed away.

The skepticism this book has built — the supervisory capacities, the validation lenses, the delegation maps, the verb taxonomy, the governance counterfactual — culminates in an engineering practice that *includes the option to say no*. A practice without that option is not the practice this book is teaching. *That is the most important thing I have to say in this whole book*, and I am saying it now, in the second-to-last section, because I want it to be the thing you carry out.

---

I want to close with something more direct than the body of the book has been, because this is the last chapter, and I think you have earned a more direct address.

The work this book has been about is *supervising AI systems for fitness in deployment contexts*. The work has technical and judgment components. The technical components are the chapters you have read — calibration, bias analysis, data validation, explainability evaluation, fairness, robustness, agentic-system validation, delegation, communication, accountability. The judgment components — when to defer, when to override, when to refuse — are the throughline.

The work is *engineering* in the sense that it requires technical competence, produces deliverables, and is testable against criteria. It is *supervisory* in the sense that it is irreducibly about the relationship between the AI system, the deployment context, and the affected parties — and that relationship is not in the system. The system does not know about the relationship. The relationship is the supervisor's job.

The work will become more important, not less, as AI systems become more capable. The capacity for confident wrongness scales with capability. The supervisory capacities scale with deliberate practice in the ways this book has tried to document. The ratio of supervisor-engineering-graduates to deployed-AI-systems is, by my reading of the field, going the wrong direction. *The bottleneck is not models. The bottleneck is supervisors who can do this work.*

You have done a course's worth of the work. You are not yet expert. The course is the beginning of a practice, and the practice gets built over years. The instruments — the journal, the delegation maps, the calibration check, the verb taxonomy, the governance counterfactual — are tools you take with you. The judgment is what you build through using them.

I am not optimistic that the field will, on its current trajectory, develop enough supervisors fast enough. I am not pessimistic either. *The work is the variable.* Engineers who do the work will be needed. Engineers who do not will, increasingly, be a liability to the organizations that employ them. The choice between those two paths is in the practice you build after this book closes.

Most engineers, I will tell you honestly, operate throughout their careers at calibrations between fifty and seventy percent on questions where they are stating ninety percent confidence. They do not know this. Nobody runs the experiment on them. You are now somebody who could run the experiment on yourself, repeatedly, throughout a career, and watch the number change as you take your own uncertainty more seriously. *You can be different.* That is the thing this book has been quietly trying to give you. Not the formulas, which you can look up. Not the lists, which I have probably gotten wrong in places. The practice — of stopping, of locking the prediction, of asking what the data is evidence of, of saying out loud what you do not know — that is what I wanted to leave you with.

The system passed every test. The engineers designed the wrong tests. Three patients were harmed.

You can be the engineer who designs the right tests. You can be the validator who recognizes the limit and decides the deployment should not proceed in its absence. You can be the supervisor who has the authority to refuse, and who uses it, and who builds the infrastructure for that authority to be real.

Go do the work.

---

## Exercises

### Warm-Up

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

**12.** Design a validation framework for a deployment that sits at the hardest intersection of all three limits: an AI system used to support parole board decisions, where the system's outputs influence whether a person is released from incarceration. Your framework should: (a) specify what each of the three limits means in this specific context, (b) identify the testing regime for what the system can be tested for, (c) specify in writing what the system cannot be tested for and why, (d) describe the human oversight infrastructure required at each limit, (e) specify the stop conditions — the findings that would lead you to recommend against deployment, and (f) describe what "the work" looks like for the supervisor operating this system over its deployment lifetime. This is the hardest exercise in the book. It is the hardest because the stakes are real, the limits all bite, and the option to say no has to be available and used. *(Open-ended capstone; integrates the full book)*
