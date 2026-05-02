
# Chapter 12 — Communicating Uncertainty: Calibrating Claims to Evidence
*The Verb Is Doing Epistemic Work You Are Not Noticing.*

I want to start with the same finding written three different ways. The evidence is identical in each case: a model achieved 87% accuracy on a held-out test set drawn from the deployment distribution, with no significance test reported and no confidence interval computed.

Here is sentence A: *We conclude that the new model is more accurate than the prior model.*

Here is sentence B: *We find that the new model achieves 87% accuracy on the held-out test set, compared to the prior model's 84% on the same set.*

Here is sentence C: *We observe that the new model produces 87% accuracy on this single held-out evaluation; whether the difference from the prior model is statistically significant is not yet established.*

Three sentences. The evidence supports sentence C. The evidence is partly compatible with sentence B — *find* is a stronger verb than *observe*, and it carries some implied generalization, but the implied generalization is not yet warranted by what we have. The evidence does not support sentence A. *Conclude* implies a settled judgment, and a single accuracy number on one test set, without significance testing or confidence intervals, is not a settled judgment.

Engineers default to sentence A. Engineering papers are full of conclusions stated more strongly than the evidence permits. This is not malicious. It is mostly inattention to a particular kind of calibration — the calibration of the *verb* of a claim to the evidence the claim is built on. The verb is doing real epistemic work, and most engineering writers do not notice the verb at all.

The fix is a taxonomy. We are going to build it now.

**Learning objectives.** By the end of this chapter you should be able to:

- Apply the verb taxonomy to any claim in a technical document and identify whether the verb is warranted by the evidence
- Use the verb taxonomy as a fluency-trap detector on AI-generated output
- Structure a validation document in three audience layers — plain English summary, technical detail, reproducibility appendix — without losing critical nuance in translation
- Name specific uncertainty in prose — distinguishing epistemic from aleatoric, quantifying where possible, avoiding throat-clearing hedges
- Give and receive peer critique on the verb-evidence calibration of a peer's validation writing

**Prerequisites.** Chapter 11 (uncertainty visualization — this chapter is its paired textual counterpart). Chapter 4 (the Frictional method and prediction-locking — peer critique is the same epistemic discipline applied collectively). No mathematical prerequisites.

---

## The verb taxonomy

A working hierarchy. Each verb implies a specific epistemic posture toward the evidence. Choose the verb whose posture matches what your evidence actually warrants.

*Hypothesize.* "We hypothesize that X." Posture: an idea, not yet tested, that we are about to investigate. The evidence required is a coherent reason to investigate, not evidence that the hypothesis is true. Used at the front of a paper, in proposals, in framing future work.

*Suggest.* "The data suggest that X." Posture: the data is consistent with X but does not distinguish X from alternatives. The evidence required is an observation that points toward X without ruling out other explanations. Common when an experiment was not designed to test X but X emerged from the analysis.

*Observe.* "We observe that X." Posture: the data shows X under these specific conditions. No claim about generalization beyond the conditions. The evidence required is a clean observation, with the observation conditions stated. The most defensible verb when the evidence is from a single experiment or a single dataset.

*Find.* "We find that X." Posture: across the evidence we have looked at, X holds. Slightly stronger than observe — it implies multiple observations or robustness to some variation in conditions. The evidence required is at least replication, ideally with some sensitivity analysis.

*Show.* "We show that X." Posture: the evidence demonstrates X with sufficient strength that X is the operational reading. The evidence required is replication, robustness, alternative-explanation ruling-out. This is a strong verb. Do not use it lightly.

*Demonstrate.* "We demonstrate that X." Posture: a constructed proof or near-proof. The evidence required is an experiment specifically designed to test X, ideally with formal guarantees or strong inferential control. Rare in deployed engineering work; common in formal verification.

*Conclude.* "We conclude that X." Posture: the question that motivated the work is settled by the evidence. The evidence required is full analysis, alternatives considered and rejected, sensitivity analysis, and a finding that survives the analysis. *This is the strongest verb in the taxonomy short of mathematical proof.* Very few claims warrant it.

*Prove.* "We prove that X." A mathematical proof. Reserved for formal claims with formal evidence.

The taxonomy is not exhaustive — *characterize*, *report*, *describe*, *quantify*, *establish* each occupy specific niches — but it covers most of the useful range. The exercise, every time you draft something, is to read your draft sentence by sentence and ask whether the verb you used matches the evidence the sentence is built on.

| Verb | Epistemic posture | Minimum evidence required | Correct use example | Common misuse |
|---|---|---|---|---|
| Hypothesize | An untested idea we are about to investigate | A coherent reason to investigate; no evidence the hypothesis is true | "We hypothesize that performance degrades under distribution shift and design the following test." | Using "hypothesize" after the test has already been run |
| Suggest | Data is consistent with X but alternatives are not ruled out | An observation pointing toward X without ruling out other explanations | "The data suggest that accuracy is lower on nighttime images." | Using "suggest" when only one data point supports the direction |
| Observe | Data shows X under these specific conditions; no generalization claim | A clean observation with conditions stated | "We observe 87% accuracy on this single held-out test set." | Treating "observe" as if it implies the observation will replicate |
| Find | X holds across the evidence examined | At least replication; ideally sensitivity analysis | "We find that the failure mode appears across three independently collected test sets." | Using "find" for a single-experiment result |
| Show | X is the operational reading; sufficiently strong evidence | Replication, robustness, alternative-explanation ruling-out | "We show that the fairness constraint reduces disparity without degrading overall accuracy." | Using "show" with a single benchmark comparison |
| Demonstrate | A constructed proof or near-proof | An experiment designed specifically to test X; ideally formal guarantees | "We demonstrate, via counterfactual testing, that the model's decisions are race-neutral after controlling for legitimate features." | Using "demonstrate" for a standard benchmark result |
| Conclude | The motivating question is settled by the evidence | Full analysis, alternatives considered and rejected, sensitivity analysis | "We conclude that the model is unfit for deployment on this subpopulation." (after full audit) | Using "conclude" for any finding — the most common misuse in engineering writing |
| Prove | A mathematical proof | Formal derivation with stated axioms | "We prove that equal opportunity and calibration cannot both hold when base rates differ." | Using "prove" for empirical claims |

*Figure 12.1 — Verb taxonomy reference card.*

This is unromantic editing. There is no inspiration in it. You are downgrading verbs because the evidence does not warrant the original verb, and the prose loses some of the punch you put into it. I want to tell you that this is also the most operationally important single skill in technical communication of validation findings. The careful engineer's writing reads, at first, as quieter than the careless engineer's. It also survives reading.

---

## Two readers, one document

Validation findings often get communicated to two audiences at the same time. There are technical readers — peers, reviewers, engineers in adjacent teams — and there are non-technical readers — deployment partners, executive sponsors, regulators, affected populations.

The instinct, when this is recognized, is to write two documents. A technical paper and an executive summary. The two-document approach is sometimes appropriate. It is often inefficient, and the executive summary often loses critical nuance in translation, because the translation happens after the technical work is done and the translator did not have the technical decisions in their bones.

A working alternative is *layered writing*. The technical and non-technical content live in the same document, layered so that each reader can find their level without translation.

The pattern is three layers, and you should be able to point to each one in your draft.

*Layer 1.* A short, prose-paragraph summary at the start of every section. Plain English. No equations. No specialized terminology beyond what the deployment partner already knows. The summary covers what the section finds and why it matters for the deployment decision. A non-technical reader can read only Layer 1 across the document and have a working understanding.

*Layer 2.* The technical detail. Methods, equations, tables, references. Written for the technical reader who needs to evaluate the work. Layer 2 is specific where Layer 1 is general; Layer 2 is rigorous where Layer 1 is plain.

*Layer 3.* Appendices, supplementary materials. The reproducibility detail. Hyperparameters, code, data documentation, full sensitivity analyses. For the reader who wants to reproduce or audit.

The layered approach is harder to write than two separate documents. It is easier to maintain, easier to keep consistent, and produces fewer translation errors. It also mirrors the structure of good validation work: the executive question is addressed by the executive layer, the technical question by the technical layer, and the reproducibility question by the reproducibility layer.

I want to pause on something. Layer 1 is not "dumbed down." Dumbing down is condescending and loses information. Layer 1 is a specific, careful, accurate summary in plain English. Plain English is *harder* to write than jargon. The technical reader benefits from Layer 1 as much as the non-technical reader does — it forces the writer to articulate what the section is really doing, which is sometimes a useful discovery for the writer.

<!-- FIGURE: Layered document structure. A single document shown as a vertical stack. Three clearly labeled bands: Layer 1 (plain English summary, executive reader enters here), Layer 2 (technical detail, peer reviewer enters here), Layer 3 (appendix / reproducibility detail, auditor enters here). Three reader-type labels on the right, each with an arrow pointing to the layer they primarily use. Key callout: "all three layers live in the same document — no translation required." Student should see the structure and be able to replicate it in their own drafts. -->

*Figure 12.2 — Layered document structure (for manual insertion).*

---

## Saying you do not know in writing

Uncertainty visualization is one part of communicating uncertainty. We covered that in the previous chapter. Uncertainty in prose is the other part, and it is harder. Error bars are a known visual primitive. Saying, in writing, *I am uncertain in this specific way for this specific reason* requires more skill, because prose has no error bars.

A few moves that work.

The first move is to specify the kind of uncertainty. *We are uncertain about X because the test set is small / the deployment distribution may differ / the labeling process is noisy / the metric is sensitive to the choice of threshold.* Naming the reason is more useful than asserting the uncertainty.

The second move is to quantify when possible and hedge specifically when not. *The 95% confidence interval is [0.81, 0.89]* is more useful than *the result is approximately 0.85*. When confidence intervals are not available, name what you would need to construct them and why it is not available.

The third move is to distinguish epistemic from aleatoric uncertainty. Epistemic uncertainty is uncertainty in your knowledge — it could be reduced with more evidence. Aleatoric uncertainty is uncertainty in the system itself — it is irreducible. Conflating the two produces hedges that read as either timid or evasive.

The fourth move is to avoid the throat-clearing hedge. *We note that further work is needed* is a phrase that conveys no information. If further work is needed, name the specific further work. If you do not know what further work is needed, the hedge is doing rhetorical work the analysis cannot back. Strike the sentence. The reader is owed a specific gap or no gap claim at all.

The fifth move is one you have seen throughout this book — state what would change your mind. The phrase appears at the bottom of every chapter. It belongs in your validation writing too. *The finding above would be revised in the face of evidence X.* The reader can evaluate, if they have evidence X, whether to expect a revision. The forward commitment is more honest than the bare statement, and — this is the part I keep finding surprising — it is also easier to write, because it gives you a structured place to put the qualifications you would otherwise scatter through the prose.

---

## Why solitary review does not work — and the peer critique protocol

Validation writing is not a solitary activity, and I want to name why, because the field treats it as one.

The reason is structural. You cannot fully evaluate your own writing for verb-evidence calibration mismatches, because you wrote the sentences with the strongest verbs that felt true to you, and the feeling was a function of how the evidence sat in your head when you wrote — which is no longer accessible to you when you re-read. The mismatches you produced are exactly the ones you cannot see. *Your blind spots are blind to you.* This is not a moral failing; it is a fact about how writing works.

The structural solution is collective. Other readers can see the mismatches, because they did not write the sentences and the evidence does not sit in their heads in the way that produced the verbs. This is why peer critique is part of any honest validation infrastructure — not because two opinions are better than one, but because there is a class of error that is invisible to the person who made it and visible to anyone else.

The trick to making it work is that the critique has to be specific. *Your verbs are too strong* is not a useful comment. *In §3.2 you wrote "we conclude" but the supporting evidence is a single test set without significance testing — "we observe" would match the evidence* is useful. The same rule applies to uncertainty hedges, layer separation, and limitations.

The structured peer critique protocol:

1. **Read the draft.** Once for content, once for structure.
2. **Identify three things that work.** Specifically. Not "well-written" but "the data validation section uses the §5 procedure from Chapter 5 effectively because [specific reason]." The praise is diagnostic, not social.
3. **Identify three places where the verb taxonomy is mismatched.** Name the verb, the sentence, the evidence the verb requires, and the verb that would match the evidence.
4. **Identify three places where uncertainty in prose is missing or evasive.** Which hedge is throat-clearing? What specific gap could replace it? Which uncertainty is unnamed?
5. **Identify one structural concern.** Where does the draft's organization make the argument harder to follow than necessary, and what alternative organization would help?
6. **Pose one question the draft does not answer.** A question that, if answered in the next revision, would strengthen the work.

The receiving student gets multiple critiques and is not expected to incorporate every comment. Many comments will conflict. The synthesis is to *integrate the critiques into a sharpened argument* — a revision that addresses the substantive issues and stands behind the choices the writer is making despite some of the comments. The critique-given matters as much as the critique-received: the ability to find calibration mismatches in a peer's work is the same skill as finding them in your own, and the external direction makes the blind spots visible.

This is a Chapter 4 callback in operational form. The Frictional method made the prediction-lock the load-bearing element of individual work. Peer critique makes *the inability to recognize one's own blind spots* the structural problem the cohort solves together.

---

## The verb taxonomy applied to AI output

A useful turn — and this one matters specifically for the kind of work this book is about.

The verb taxonomy is not just a tool for human writers. It applies to AI output as well, and using it as a checking instrument is one of the most operationally useful supervisory moves available.

When an AI produces a paragraph that contains *"this conclusively demonstrates that X"* — what is the verb's evidentiary requirement? *Conclusively demonstrates* is the strongest verb in the taxonomy short of *prove*. The evidentiary requirement is full analysis, alternative-explanation ruling-out, replication, sensitivity analysis. Did the AI perform any of those? Has the AI's evidence been independently checked?

If the answer is no, the AI's verb is unwarranted. The supervisor's move is to replace *conclusively demonstrates* with *suggests* or *observes*, and to explicitly note that the stronger reading is not yet warranted.

I want you to think of this as the verb taxonomy *operating as a fluency-trap detector*. AI outputs reach for strong verbs because the language models behind them were trained on text that frequently uses strong verbs, often when not warranted. The supervisor reading AI output should treat the verb itself as a flag. *What is the evidentiary base for this verb? Is the base sufficient?*

In practice, downgrading verbs in AI output is one of the highest-leverage editing moves you can make. It is also one of the most defensible — the underlying claim does not change. The calibration of how the claim is presented changes. Most AI-generated paragraphs are improved by one or two verb downgrades, and the resulting paragraph reads more like the work of a careful engineer than the work of an enthusiastic press release.

Try this on the next AI-generated paragraph you read. Mark every verb. Ask, of each verb, what evidence the verb requires and whether that evidence has been produced. The exercise is uncomfortable the first few times. It gets faster. After enough repetitions, you will find that you read AI output with the verb-check running automatically, and the output stops feeling as confident as it sounded on first read. This is the desirable end state.

<!-- FIGURE: Annotated AI-generated paragraph. A realistic example paragraph (four to six sentences) with each strong-verb usage circled or highlighted in one color, and the appropriate downgraded verb written above it in another color. Margin annotations show the evidentiary gap that motivates each downgrade. Student should be able to use this as a model when annotating their own AI-output review exercises. -->

*Figure 12.3 — Annotated AI-generated paragraph (for manual insertion).*

---

## What survives review

A clean note for the engineer about to draft anything that an adversarial reader will eventually see.

The structure that survives review tends to look like this. A Layer 1 summary at the top — what the work found, what it did not find, what the deployment implication is, in plain English. A method section with the validation pipeline. A findings section with verbs calibrated to evidence and uncertainty named specifically. A limitations section that is real, not performative — names the actual things the analysis could not catch and what would change to catch them. An explicit *what would change my mind* section. An AI use disclosure as a structured appendix. A reproducibility appendix with code, data, and analysis trails.

The structure is not elegant. It is *legible to an adversarial reviewer*, which is the relevant property. A reviewer can scan, locate any specific claim, find its evidentiary base, find its limitations, find the AI use, find the reproducibility detail. The reviewer's job becomes possible. Most writeups make the reviewer's job harder than it needs to be, often because the writeup was structured for the writer's convenience and not the reader's.

The pivotal move is to write for the adversarial reviewer. Not to defeat them. To *support* them. The reviewer is your validation collaborator. The structure should make their work easy. If your writeup is hard to review, your validation is, in practice, weaker than it would otherwise be — not because the analysis is bad but because the analysis is harder for anyone else to use.

---

## Glimmer 12.1 — The peer critique as collective synthesis

A Glimmer is a longer, higher-stakes exercise. This one is the operational form of the cohort-as-validation-infrastructure claim.

1. Submit your research project draft for peer critique.
2. Receive critiques from two peers using the six-step protocol in the section above.
3. Write the synthesis: which substantive issues you are addressing, which comments you are accepting, which you are pushing back on and why, and how the revised draft will respond to each.
4. Submit the revised draft alongside the synthesis.

Two additional deliverables:

5. Submit the critiques you provided to two other peers.
6. *Lock your prediction* before reading the receiving peer's synthesis: which of your critiques will they accept, which will they push back on, which will they ignore? State the prediction with stakes. Compare against what they actually did. The gap is the learning event.

The grade is on three things: the critique-given (rigor and specificity per the six-step protocol), the synthesis (coherence and honesty about what was changed and why), and the prediction-vs-result gap on step 6.

The cohort is functioning if the critiques surface real calibration issues; the cohort is over-functioning if the critiques are converging on a single preferred voice; the cohort is under-functioning if the critiques are too polite or too generic to be useful. The Glimmer makes each of these failure modes visible — and the failure mode itself is pedagogically useful information about the cohort's collective calibration.

---

## Where this leaves us

The verb of a claim is calibrated to the evidence. The verb taxonomy is the working tool, and most engineering writing reaches for verbs one or two notches stronger than the evidence supports. Audience layering supports technical and non-technical readers in the same document without losing nuance to translation. Uncertainty in prose is harder than uncertainty in visualization and requires specific naming, quantification where possible, and the distinction between epistemic and aleatoric uncertainty. Peer critique is collective validation infrastructure, structurally necessary because your blind spots are blind to you. The structural form of a writeup that survives review is legible to the adversarial reviewer.

This chapter is the second half of the communication act. The previous chapter covered the visual side. This one covers the textual side. Together they are the supervisory capacity for *communicating validation findings*, which is, structurally, where validation work meets its readers.

The next chapter pivots. We have validated a system, mapped our delegation, communicated the findings. But when a system fails — and our validation missed it — *who is responsible?* That question is the next chapter, and it is where the counterfactual we opened earlier closes. The accountability question has been hanging over the book since Chapter 1. We are about to give it the answer the book can give.

---

## What would change my mind — and what I am still puzzling about

**What would change my mind.** If a tool emerged that automatically calibrated verb choices in technical writing against the underlying evidence — across diverse domains, with reliability against engineer-default overclaiming — the verb-taxonomy framing of this chapter would weaken to "the writer's checklist that the tool implements." Some grammar tools (Grammarly's hedge-language flags, for example) are partial steps. They do not yet implement the evidentiary check. \[verify current state.\]

**Still puzzling.** I do not have a clean way to teach the audience layering skill at scale. Some students pick it up quickly; others write for one audience or the other and resist the integration. The instructional pattern that works best, in my experience, is to have students rewrite each other's work for the other audience and observe what is lost. This is labor-intensive. I do not have an efficient version.

---

## Exercises

### Warm-up

**W1.** Below are five sentences from engineering validation reports. For each sentence, identify the verb used, name the minimum evidence that verb requires, and state whether the evidence described in the sentence is sufficient to warrant the verb. If not, write a replacement sentence with a verb that matches the actual evidence.

(a) "We conclude that the model is robust to distribution shift." (Evidence: tested on one out-of-distribution dataset, no significance test.)

(b) "The data suggest that accuracy degrades in low-light conditions." (Evidence: accuracy dropped from 91% to 78% on a single evening-lighting test set.)

(c) "We observe that precision is 0.84 on the held-out evaluation set." (Evidence: one evaluation run on a clean held-out set.)

(d) "We demonstrate that the system outperforms the baseline." (Evidence: the system outperforms the baseline on three of five benchmarks, two benchmarks show no difference.)

(e) "We find that the failure mode does not occur in production." (Evidence: no failures observed in 200 production queries over two weeks.)

*Tests: verb taxonomy, evidence-verb calibration. Difficulty: low.*

**W2.** Explain in plain language the difference between epistemic and aleatoric uncertainty. Give one example of each from a deployed AI system. For your epistemic uncertainty example, name the specific evidence that would reduce it. For your aleatoric uncertainty example, explain why it cannot be reduced.

*Tests: epistemic vs. aleatoric uncertainty, uncertainty in prose. Difficulty: low.*

**W3.** A colleague gives you this sentence as peer critique: "Your uncertainty section is too hedgy." Using the rules for specific critique from this chapter, rewrite the feedback as a critique that is actually actionable. Your rewrite should name the sentence, the hedge, and what a replacement would look like.

*Tests: peer critique standards, throat-clearing hedge vs. specific uncertainty. Difficulty: low.*

---

### Application

**A1.** You are reviewing an AI-generated paragraph from a validation report. The paragraph reads:

> "Our analysis conclusively demonstrates that the model achieves state-of-the-art performance across all evaluated domains. The results clearly show that the proposed approach resolves the fairness concerns raised in prior literature. We therefore conclude that deployment is warranted."

Apply the verb taxonomy to every strong verb in this paragraph. For each: name the verb, state its evidentiary requirement, identify whether the paragraph provides that evidence, and write a replacement sentence with a verb that matches what the paragraph actually supports.

*Tests: verb taxonomy as fluency-trap detector, AI output review. Difficulty: medium.*

**A2.** You are writing a section of a validation report on a medical triage AI. Your primary audience is clinical informaticists who will make the deployment decision. Your secondary audience is the hospital's legal and compliance team. Your tertiary audience is a technical reviewer who may want to reproduce your analysis.

Write the same finding — "The model achieves 89% sensitivity on the held-out test set; sensitivity on the subgroup of patients over 75 is 81%" — in all three layers: Layer 1 (plain English), Layer 2 (technical detail with the appropriate statistical context), and Layer 3 (specify what would go in the reproducibility appendix, without writing the full appendix). Each layer should be written for its actual audience.

*Tests: audience layering, uncertainty in prose, verb calibration across layers. Difficulty: medium.*

**A3.** Your peer reviewer returns your validation report with this comment on the limitations section:

> "§4.3: 'We note that further work is needed to assess performance on underrepresented populations.' This is throat-clearing. Replace."

Write the replacement. Your replacement should name the specific uncertainty, identify whether it is epistemic or aleatoric, quantify it if possible (or state why quantification is not available), and include a "what would change this" commitment.

*Tests: throat-clearing hedge elimination, specific uncertainty, what-would-change-your-mind structure. Difficulty: medium.*

**A4.** A deployment partner reads your validation report and says: "The Layer 1 summary says the model is ready. The Layer 2 findings show sensitivity is 12 points lower on one demographic group. You have contradicted yourself."

Diagnose the specific writing failure and explain what went wrong structurally. Is this a verb-calibration problem, a layer-separation problem, a limitations problem, or some combination? Write a corrected Layer 1 summary that accurately reflects the Layer 2 findings without requiring the deployment partner to read Layer 2 to catch the discrepancy.

*Tests: layered writing, verb calibration, Layer 1 as accurate summary not overclaim. Difficulty: medium.*

---

### Synthesis

**S1.** The chapter argues that peer critique is structurally necessary — not because two opinions are better than one, but because there is a class of error invisible to the writer and visible to anyone else. Identify two other contexts in earlier chapters of this book where the same structural argument appears under a different name. For each one, explain how the logic maps to the peer-critique argument in this chapter.

*Tests: structural reasoning, cross-chapter synthesis, validation infrastructure. Difficulty: high.*

**S2.** You are asked to design a peer critique protocol for a cohort of ten engineers reviewing each other's validation reports. The protocol must produce specific, actionable feedback rather than general impressions, and it must be completable in a 90-minute session. Specify: the structure of the session, the criteria reviewers use, the form a comment must take to be considered actionable under your protocol, and what the protocol cannot catch even when executed correctly.

*Tests: peer critique operationalized, actionable feedback standards, structural limitations of critique. Difficulty: high.*

**S3.** The chapter's "still puzzling" section admits: "I do not have a clean way to teach the audience layering skill at scale." Design an instructional exercise that addresses this gap. Your exercise should force students to experience both the writer's and the reader's side of the layer mismatch, be completable in a single class session, and produce a measurable output that demonstrates whether the student has internalized the skill. Identify what your exercise cannot teach and why.

*Tests: audience layering, instructional design, intellectual honesty about limits. Difficulty: high.*

---

### Challenge

**C1.** The verb taxonomy is presented in this chapter as a tool for human writers editing their own work and reviewing AI output. Propose a formal specification for an automated verb-calibration tool — one that takes a paragraph and its evidence base as input and outputs a verb-calibration assessment. Specify: the representation of "evidence base" the tool would require, the decision rule mapping evidence to warranted verb, the failure modes of the tool (cases where it would produce wrong assessments), and the class of verb mismatches it cannot catch even in principle. Your proposal should make clear why this tool, even if perfectly implemented, would not replace the peer-critique infrastructure the chapter describes.

*Tests: verb taxonomy formalized, tool design, structural limits of automation, peer critique as irreplaceable infrastructure. Difficulty: high.*

---

*Tags: communication, verb-taxonomy, peer-critique, audience-layering, uncertainty-in-prose*
