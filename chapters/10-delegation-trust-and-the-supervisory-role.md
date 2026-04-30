# Chapter 10 — Delegation, Trust, and the Supervisory Role
*Write the contract before you trust the handoff.*

I want to tell you about two engineering teams.

Both teams have built validation pipelines for an AI system that scores loan applications. The pipelines are close to identical at the technical level. Same data. Same model. Same fairness metrics. Same monitoring infrastructure. They differ in one thing.

The first team's documentation says: *AI scores the application, the loan officer reviews and decides, the system logs both.* On every component, the human and AI roles are described at this level of generality.

The second team's documentation says something different. *The AI computes the score and produces a brief explanation. The loan officer is required to verify that the explanation is consistent with the application data, to check whether any of three named edge-case patterns are present using a checklist embedded in the interface, and to record their decision with one of four documented justification codes. If the AI score is below 0.3 or above 0.7, the loan officer's decision can match the AI without further review. If the score is between 0.3 and 0.7, a second loan officer must independently review before either can record a decision.*

Both pipelines deploy. Both work, in the sense that loans get scored and decided.

Then both teams go to the adoption committee for an expanded rollout. The committee asks the same question of each team: *who is responsible if a loan decision is challenged?*

The first team cannot answer cleanly. The AI produced the score. The loan officer made the decision. The documentation says *the loan officer reviews*, but does not specify what review means. In the case of a particular challenged decision, was review performed? At what depth? Against what criteria? The team has to go back and figure out what their own pipeline was supposed to do. The committee tables the rollout.

The second team produces the documentation. The committee can read, in writing, what the AI did, what each loan officer was required to verify, and what triggered escalation. The committee may still reject the rollout — they have substantive reasons available to them. But they reject it for substantive reasons, not for documentation reasons. They are not waiting on the team to figure out what their own pipeline does.

The difference between the two pipelines is not a difference in the technology. It is a difference in the *delegation map*. And that is what this chapter is about.

I want you to come away holding one idea: a delegation is not "the AI does this part." A delegation is a *contract* with explicit boundaries — what the AI does, what the human does, the testable handoff between them, and what happens when the handoff fails. That formulation sounds dry. It is the difference between a pipeline that passes adoption review and one that does not.

---

Let me start with the load-bearing piece, because everything else depends on it.

A delegation map describes, for each step in a pipeline, who owns the step (AI, human, or hybrid), what goes in, what comes out, and — crucially — what signals that the step has completed correctly and the next step can proceed. That signal is called the handoff condition. It is the load-bearing element of the entire map, and it has one essential property: *it has to be testable*.

Testable means a non-author of the pipeline can read the condition, observe the system, and determine whether the condition is met. Untestable conditions sound like English but do not survive contact with a reviewer.

Let me show you the contrast.

Untestable handoff condition: *"The AI produces a reasonable result and the human reviews it."* The word "reasonable" is not specified — what counts as reasonable for this kind of output? The word "reviews" is not specified — what does the human do when reviewing? Five different loan officers reading this condition will execute five different behaviors and call all of them reviewing. The pipeline will run. The documentation will say it ran. The audit committee will not be able to tell what actually happened.

Testable handoff condition: *"The AI produces a score in the interval zero to one with a confidence interval. The human accepts the score, requests re-scoring, or overrides. In the override case, the human records one of four documented justification codes."* Every term has a specifiable operational form. Five different loan officers reading this condition execute the same behaviors. An external reviewer can examine a recorded decision and determine whether the condition was met.

The procedure for getting from the first to the second is unromantic. You write your handoff condition in your first attempt. You identify every term in it that requires interpretation. For each interpretive term, you either replace it with a specifiable criterion or you document a guideline that pins down the interpretation. You hand the documentation to someone who did not write it and ask them, for a sample case, to determine whether the condition was met. If they cannot, the condition is not testable, and you go again.

This is documentation work. It is not glamorous. It is the difference between a pipeline that survives adoption review and one that does not.

<!-- → [TABLE: Side-by-side comparison of untestable vs. testable handoff conditions — three pairs of examples across different pipeline types (loan scoring, medical triage, content moderation). Columns: domain, untestable version, testable version, what interpretation was pinned down. Students should use this as a template for the rewrite exercise.] -->

*Figure 10.1*

| | **Side-by-side comparison of untestable** | **Testable handoff conditions** |
|---|---|---|
| **Row 1** | _fill in_ | _fill in_ |
| **Row 2** | _fill in_ | _fill in_ |

: {.infographic-table}


---

There are five supervisory capacities I introduced in Chapter 1, and this is the chapter where each becomes a concrete job in the pipeline rather than a personality trait. Engineers sometimes hear "supervisory capacity" and reach for words like *judgment* and *experience* — words that describe a person rather than a task. The reframe of this chapter is that each capacity has an operational form, and the operational form is something the pipeline either has or does not.

Plausibility auditing is the capacity that asks: *given what I know about the world this output describes, is this output the kind of thing that could be true?* Operationalized, it is a checklist embedded in the workflow, with specific verification questions matched to the AI's output type. The audit is not a feeling. It is a series of yes/no questions the supervisor answers and the system records. If the checklist is missing, the auditing did not happen, regardless of what the supervisor felt about the output.

Problem formulation is the capacity that, before the AI is invoked, specifies the question being asked, the evaluation criteria for the answer, and the conditions under which the AI is the right tool. Operationalized, it is a written specification — usually a paragraph or two — included in the deployment documentation. An external reviewer can read the specification and either accept or reject it. If the specification is missing, the formulation did not happen.

Tool orchestration is the capacity that, for each step in the pipeline, decides which tool is responsible, why that tool is the right one, and what the handoff between tools is. Operationalized, it is the delegation map itself.

Interpretive judgment is the capacity that reads the AI's output in the deployment context and interprets what the output means for the decision being made. Operationalized, it is a documented interpretation in the audit trail, with reference to the deployment-specific factors that shaped it. *The decision was X because the score was Y in this context, where this context means Z.*

Executive integration is the capacity that synthesizes outputs from multiple tools, multiple humans, and multiple monitoring signals into a decision the integrating system can stand behind. Operationalized, it is a decision document with named contributors and a clear authority structure.

Each of these is a *job* in a pipeline, with a documented operational form. The first team in the opening had no documented operational forms. The second team had all of them.

<!-- → [TABLE: The five supervisory capacities as pipeline jobs — columns: capacity name, operational form in a pipeline, what artifact documents it, failure mode if absent. Rows: plausibility auditing, problem formulation, tool orchestration, interpretive judgment, executive integration. This is the Chapter 1 vocabulary re-stated as engineering deliverables — students should be able to audit a pipeline against this table.] -->

*Figure 10.2*

| | **Property** | **Value** |
|---|---|---|
| **Plausibility auditing** | _fill in_ | _fill in_ |
| **Problem formulation** | _fill in_ | _fill in_ |
| **Tool orchestration** | _fill in_ | _fill in_ |
| **Interpretive judgment** | _fill in_ | _fill in_ |
| **Executive integration. This is the Chapter 1 vocabulary re-stated as engineering deliverables — students should be able to audit a pipeline against this table.** | _fill in_ | _fill in_ |

: {.comparison-table}


---

Now the question that comes before the map: which sub-tasks in your pipeline should the AI do, and which should it not? This is not an aesthetic decision. It is a structured assessment with five questions you ask of each sub-task. I call the assessment the Boondoggle Score, and it sits inside one of the tools that accompanies this textbook. The score is less important than the questions, so let me walk through the questions.

The first question is verification cost. Can a human verify the AI's output cheaply, or does verification require resources comparable to producing the output from scratch? Tasks that are cheap to verify — proofreading a generated summary against a source document, sanity-checking a numeric calculation, scanning a list of candidates — are good AI tasks. The asymmetry between solving and verifying is what makes the AI useful in the first place. Tasks that are expensive to verify are dangerous, because the cost structure quietly pushes the supervisor toward accepting the output rather than checking it.

The second question is stakes. What is the cost of being wrong on this sub-task? Low-stakes sub-tasks tolerate higher AI error rates because the downside is bounded. High-stakes sub-tasks require human review or human-only decision-making, even if the AI's average performance is good — because the average is not the relevant statistic when a single bad outcome is catastrophic.

The third question is distribution match. Is the input to this sub-task within the AI's training distribution, or is it likely to land in a region of the input space where the AI is undertrained? In-distribution sub-tasks are AI-friendly. Out-of-distribution sub-tasks are dangerous regardless of average performance, because the average was computed on cases that look unlike the case in front of the supervisor right now.

The fourth question is reversibility. Can the action be undone if it is wrong? Reversible actions tolerate higher AI error rates. Irreversible actions — deletes, sends, financial transfers, anything that touches the world in a way that cannot be retracted — should be gated behind explicit human approval, almost without exception.

The fifth question is audit trail clarity. Does the AI's action leave a trail that can be reviewed after the fact? Actions with clear trails are AI-friendly. Actions whose effects are entangled with downstream state changes — such that you cannot tell, after the fact, what the AI did and what propagated from it — are not.

The five questions, taken together, sort sub-tasks into three buckets. Some are appropriate for AI execution. Some should not be delegated. Some are hybrids — AI-assisted with human verification at specified handoff conditions. The output of the assessment is not a recommendation per se. It is *a documented justification for the delegation choice*, which becomes part of the pipeline documentation. The justification is what the adoption committee, the regulator, and the future engineer reviewing the pipeline will read.

<!-- → [TABLE: The Boondoggle Score as a decision matrix — rows: the five questions (verification cost, stakes, distribution match, reversibility, audit trail clarity). Columns: question, what low risk looks like, what high risk looks like, which of the three delegation buckets a high-risk answer pushes toward. Designed as a worksheet — students fill it in for each sub-task they are assessing.] -->

*Figure 10.3*

| | **Property** | **Value** |
|---|---|---|
| **The five questions (verification cost** | _fill in_ | _fill in_ |
| **Stakes** | _fill in_ | _fill in_ |
| **Distribution match** | _fill in_ | _fill in_ |
| **Reversibility** | _fill in_ | _fill in_ |
| **Audit trail clarity). Columns: question** | _fill in_ | _fill in_ |
| **What low risk looks like** | _fill in_ | _fill in_ |
| **What high risk looks like** | _fill in_ | _fill in_ |
| **Which of the three delegation buckets a high-risk answer pushes toward. Designed as a worksheet — students fill it in for each sub-task they are assessing.** | _fill in_ | _fill in_ |

: {.data-table}


---

There is a piece of this that is monitorable in deployment, and that most deployments do not monitor.

When a human supervisor and an AI work together on a stream of tasks, the supervisor's reliance on the AI ought to match the AI's actual reliability for that kind of input in that deployment context. This is what *calibrated trust* means in the operational sense. It does not mean trusting the AI in general. It means trusting the AI to about the degree the AI's actual error rate warrants, on this kind of case.

There are three failure modes, and each produces different downstream errors.

Undertrust — sometimes called distrust — is when the supervisor systematically discounts AI output, even when the AI is reliable. The deployment loses the value the AI was supposed to add. This often coexists with backlash against AI deployments — *we tried it, it didn't help* — when the failure was not the AI's accuracy but the supervisor's calibration.

Overtrust is when the supervisor accepts AI output when they should not. The fluency trap from Chapter 1 is the canonical case: outputs that look right are accepted, and the verification work the supervisor was supposed to do is silently skipped. In my reading, *overtrust is the dominant failure mode in current AI deployments*. It is also the failure mode the documentation makes least visible, because overtrust does not produce immediate flags. It produces uncaught errors that are discovered later, often by the affected user.

Calibrated trust is the case where the supervisor's reliance matches the AI's actual reliability. Achieving it requires two things: knowing the AI's actual reliability for this kind of case, which usually requires monitoring infrastructure, and acting on that knowledge consistently, which requires discipline and supportive workflow design.

Now here is the part that is monitorable. Over the last fifty AI outputs in the pipeline, how often did the supervisor accept, override, request re-processing, or escalate? And how does that distribution match the AI's actual error rate? If the supervisor accepts 95% of outputs and the AI's error rate is 10%, the supervisor is overtrusting. If the supervisor overrides 60% of outputs and the AI's error rate is 5%, the supervisor is undertrusting. Calibrated trust shows up as override rates approximately matched to AI error rates, with the overrides concentrated on the cases where the AI was actually wrong.

This is data that is sitting in the audit trail. Every accept-override-escalate event is recorded; the AI's error rate can be estimated from sampled ground-truth checks. The analysis that compares the two distributions is not technically difficult. It is just rarely done. It is one of the cleanest gaps between what current deployments could be measuring and what they actually measure.

<!-- → [CHART: Three panels showing trust calibration — panel 1: overtrust (supervisor accept rate 95%, AI error rate 10% — the gap is visible); panel 2: undertrust (supervisor override rate 60%, AI error rate 5%); panel 3: calibrated trust (override rate tracks error rate, overrides concentrated on cases where AI was wrong). Horizontal axis: cases in deployment order. Vertical axis: accept/override rate vs. AI error rate. Student should see the shape of the three failure modes.] -->

*Figure 10.4*

| | **Three panels showing trust calibration — panel 1: overtrust (supervisor accept rate 95%, AI error rate 10% — the gap is visible); panel 2: undertrust (supervisor override rate 60%, AI error rate 5%); panel 3: calibrated trust (override rate tracks error rate, overrides concentrated on cases where AI was wrong). Horizontal axis: cases in deployment order. Vertical axis: accept/override rate** | **AI error rate. Student should see the shape of the three failure modes.** |
|---|---|---|
| **Row 1** | _fill in_ | _fill in_ |
| **Row 2** | _fill in_ | _fill in_ |

: {.data-table}


---

Where I am uncertain.

What would change my mind: if a tool emerged that automated the construction of testable delegation maps from pipeline code — across diverse domains, with handoff conditions an external reviewer could verify without manual work — the framing of *the map is documentation work the engineer must do* would weaken. The closest tools today are workflow-management platforms, which are infrastructure for execution rather than tools for documenting handoff testability. The construction work remains the engineer's.

What I am still puzzling about: I do not have a clean way to integrate the delegation map with the audit trail at scale. The map specifies what *should* happen at each step. The audit trail records what *did* happen. Reconciling the two — automatically detecting deviations from the map in the trail — would close a major monitoring gap. Some cases yield to straightforward implementation. The general case, especially across multi-agent systems with thousands of steps, is hard. I do not yet have a working general approach.

---

Delegation is a contract, not a partition. The handoff condition is the load-bearing element; testability is its standard. The five supervisory capacities have operational forms — checklist audits, written specifications, the delegation map, documented interpretations, decision documents with authority structures — and the pipeline either has them or does not. The Boondoggle questions sort sub-tasks by where the AI can responsibly act and where it cannot. Trust calibration is a monitorable property of the deployment, and the gap between what could be monitored and what is monitored is the field's most easily closable failure.

The two pipelines from the opening are no longer indistinguishable. The second team's pipeline has the contract written down. The first team's pipeline is held together by what the team members happen to remember, and that is not a basis for adoption review.

The next chapter pivots from the pipeline to the *communication* of what the pipeline found. A dashboard tells a visual story. The same data can produce a dashboard that communicates accurately or one that misleads. The design choices are choices, with normative weight. We will treat them that way.

---

## Exercises

### Warm-Up

**1.** The chapter defines a delegation map and identifies one load-bearing element. Name the element, state its essential property, and explain in two sentences why that property is load-bearing — what fails specifically if the element is present but the property is not. *(Tests: understanding the structure of the chapter's central technical claim.)*

**2.** Below are two handoff conditions from a content-moderation pipeline. Identify which is testable and which is not. For the untestable one, name the specific terms that require interpretation and rewrite the condition so a non-author of the pipeline could verify it on a sample case.

- *Condition A:* "The AI flags content for review. The moderator evaluates the flag and takes appropriate action."
- *Condition B:* "The AI assigns one of five severity codes to the content. The moderator confirms or changes the code and, if the code is 4 or 5, must record a policy citation from the documented list before the decision is logged."

*(Tests: applying the testability criterion directly; practicing the rewrite procedure.)*

**3.** The chapter argues that each of the five supervisory capacities has an "operational form" — something a pipeline either has or does not. For plausibility auditing and executive integration, describe what the operational form is and what artifact documents it in the pipeline. *(Tests: understanding the reframe from personality trait to pipeline job.)*

---

### Application

**4.** You are assessing a sub-task in a hiring-screen pipeline: the AI ranks the top 20 candidates from a pool of 200 and produces a brief justification for each ranking. Apply the five Boondoggle questions to this sub-task. For each question, state your assessment (low risk / high risk / uncertain) and your reasoning. Then classify the sub-task into one of the three delegation buckets and write the one-paragraph documented justification you would include in the pipeline documentation. *(Tests: operationalizing the Boondoggle assessment on a concrete sub-task; producing the actual deliverable.)*

**5.** A deployed medical-imaging pipeline has the following accept/override data from the last 200 cases: the AI's outputs were accepted 186 times and overridden 14 times. A sampled ground-truth audit of 40 randomly selected cases found 6 AI errors. Compute the supervisor's override rate and the estimated AI error rate. Identify which of the three trust-calibration failure modes this deployment is in, and explain what the downstream consequences of this failure mode look like in practice — not in the abstract, but for this specific pipeline. *(Tests: applying the calibrated-trust framework with concrete numbers; connecting the metric to deployment consequences.)*

**6.** You are reviewing the first team's pipeline from the chapter's opening. The documentation says: "AI scores the application, the loan officer reviews and decides, the system logs both." Your job is to rewrite the delegation map so it would pass the adoption committee's challenge. Produce: (a) a testable handoff condition for the AI-to-loan-officer handoff, (b) the operational form of at least two supervisory capacities visible in the documentation, and (c) a specification for when escalation to a second reviewer is triggered. *(Tests: constructing a delegation map from scratch on the chapter's anchor case.)*

**7.** The chapter claims that overtrust is the dominant failure mode in current AI deployments, and that it is the failure mode the documentation makes least visible. Why does documentation make overtrust less visible than undertrust? What would documentation designed to surface overtrust actually look like? *(Tests: distinguishing the visibility properties of the two failure modes; connecting monitoring design to the documentation structure.)*

---

### Synthesis

**8.** Chapter 1 introduced the five supervisory capacities. Chapter 10 operationalizes each one as a pipeline job with a documented form. Chapter 7 introduced the defended-choice deliverable as the form of an engineering decision under value pluralism. Using all three frameworks, describe what the delegation map and the fairness-defense document have in common — structurally, not just thematically. What is the general form that both are instances of? *(Tests: synthesizing the major deliverable frameworks across the book; identifying the structural pattern.)*

**9.** The chapter identifies a monitoring gap: the audit trail records what did happen, but the delegation map specifies what should happen, and most deployments do not reconcile the two. Design a monitoring protocol — at whatever level of specificity is achievable — that would detect, in a loan-scoring pipeline, deviations between the delegation map and the actual audit trail. What specific signals would it look for? What would a deviation look like in the data? *(Tests: connecting the delegation map to live monitoring; forcing the student to specify what reconciliation looks like operationally.)*

---

### Challenge

**10.** The chapter's uncertainty section flags a genuinely hard problem: reconciling delegation maps with audit trails at scale across multi-agent systems with thousands of steps. The partial solution the chapter gestures toward covers the easy cases. Characterize what makes the hard cases hard. Specifically: what properties of a multi-agent system make delegation-map-to-audit-trail reconciliation difficult in ways that do not appear in a single-agent pipeline? And what would a general solution need to do that a case-by-case solution does not? *(Tests: extending the chapter's reasoning to a harder technical context; requires the student to identify what the partial solution is actually solving.)*

**11.** The chapter frames delegation as a contract between the AI and the human supervisor. But contracts require parties with interests, the capacity to make and honor commitments, and accountability when they don't. An AI system has none of these in the ordinary sense. In what sense is "delegation as contract" a useful framing, and in what sense is it a metaphor that obscures the actual engineering problem? Where does the framing do work, and where does it break down? *(Open-ended; tests critical engagement with the chapter's central organizing metaphor.)*

---

*Tags: delegation, supervisory-capacities, boondoggle-score, gru, ai-use-disclosure*
