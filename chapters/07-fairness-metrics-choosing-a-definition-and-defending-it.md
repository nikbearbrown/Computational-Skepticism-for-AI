# Chapter 7 — Fairness Metrics: Choosing a Definition and Defending It
*Three reasonable definitions. One dataset. Pick one.*

I want to give you a problem.

You have built a binary classifier — a model that produces a yes/no prediction. It could be a loan approval tool, a recidivism predictor, a hiring screen. It is being applied to a population that contains two groups, A and B. The model produces predictions; the world produces outcomes; you can compare the two.

You want the model to be fair. You sit down to write the requirement. The first definition you write is *demographic parity*: the rate at which the model says yes should be the same in both groups. Same fraction of group A and group B get the favorable prediction. This sounds like fairness, and in one specific sense — equality of treatment in the prediction itself — it is.

You think a little more. Equal positive-prediction rates seem necessary but not sufficient. What if the model is wrong about A and B in different ways? You add a second requirement: *equalized odds*. The false-positive rate and the true-positive rate should be the same in both groups. The model should make its mistakes evenly. This sounds like fairness too, in a different sense — equality of error patterns.

You think a little more still. Even equal error rates may not be enough. What if the model's stated probabilities mean different things in the two groups? You add a third requirement: *calibration parity*. When the model says "seventy percent," the actual rate of positives is seventy percent in group A and seventy percent in group B. The probabilities mean the same thing for everyone. This too is fairness, in yet a different sense.

You have three reasonable definitions. Each captures something fairness intuitively requires. You set out to satisfy all three.

I have to tell you that you cannot. Not because the tools aren't good enough. Not because the data is insufficient. *You cannot satisfy all three on the same dataset, simultaneously, when base rates differ between the groups* — and on most real datasets they do differ. This is a theorem. It was proved in 2016, independently, by Kleinberg, Mullainathan, and Raghavan, and by Chouldechova. It is the structural fact this chapter is about.

The chapter has two jobs. First, I want you to feel why the impossibility is true — not as a formal result you take on authority, but as a thing that becomes obvious once you see the arithmetic. Second, I want you to come away with a method for what to do about it. The impossibility does not let you avoid the question. It forces a choice. And choices in engineering have to be defended.

---

Let me write the three definitions tighter.

*Demographic parity* — sometimes called statistical parity — says the rate of positive predictions does not depend on group membership. The probability that the model predicts yes, given that the input is from group A, equals the same probability for group B. This is a statement about the model's *outputs*, before anyone has compared them to the ground truth.

*Equalized odds* says the model's error rates do not depend on group. The true-positive rate — among those who actually were positive, the fraction the model correctly flagged — is the same in both groups. The false-positive rate — among those who actually were not positive, the fraction the model incorrectly flagged — is also the same. This is a statement about the model's *errors*, conditional on the truth.

*Calibration parity* — sometimes called predictive parity — says the model's stated probabilities track empirical frequencies equally well in both groups. When the model assigns probability p to a case from group A, the realized positive rate among such cases is p; the same holds for group B. This is a statement about whether the *probability outputs are honest*, in the same way for everyone.

These three look like they should all be compatible. They are not.

<!-- → [TABLE: Three-column definition reference — columns: metric name (with alias), what it measures (one sentence), what it is a statement about (outputs / errors / probability honesty), and what satisfying it feels like as a fairness value. Designed for student reference during the arithmetic section that follows.] -->

---

Let me show you why.

I will use specific numbers to make the arithmetic visible. Suppose the underlying base rate of positives is 0.6 in group A and 0.3 in group B. Sixty percent of group A has the positive ground truth; thirty percent of group B does. Pick any context where this might hold — different prevalence rates of an outcome in two populations, for whatever historical or structural reason. Set the reasons aside for the moment. We are working out what the math forces.

Suppose the classifier is calibrated. Among everyone the model assigns probability p, the realized positive rate is p, and this holds in both groups separately. Among group A members assigned probability 0.7, seventy percent are actually positive. Among group B members assigned probability 0.7, seventy percent are actually positive. The model is honest about probabilities, and equally honest across groups.

Now ask: among everyone the model predicts positive at some threshold, what fraction are *actually* positive? In group A, where the underlying base rate is 0.6, there are many true positives to find; the predicted-positive set will be heavily populated by them. In group B, where the underlying base rate is 0.3, there are fewer true positives in the population at all; whatever the model flags will be drawn from a thinner pool.

Watch what happens. Among those flagged positive, the share that are *actually* positive — the precision — will be higher in group A and lower in group B. By the same arithmetic, among those *not* actually positive, a larger fraction will be wrongly flagged in group B than in group A. The false-positive rate is higher in group B.

Calibration is satisfied. It cannot help being satisfied — we built it in. But equalized odds is now violated, because the false-positive rates are unequal across groups. And they have to be unequal — not because of model design, not because of bad data, but because base rates differ and we required the probabilities to mean the same thing in both groups. The conjunction of those two requirements *forces* unequal error rates.

You can run the same reasoning the other way. Suppose you require equalized odds — false-positive and true-positive rates equal across groups. Then, with different base rates, the precision (the meaning of a positive prediction in terms of actual outcomes) cannot be the same across groups. Calibration is broken.

And demographic parity adds yet another constraint — the rate of positive predictions equal in both groups — that, in general, breaks both calibration and equalized odds.

There is no setting of model parameters that satisfies all three simultaneously, given different base rates. The impossibility is not about which model you train. It is about what is mathematically possible at all.

I want you to sit with this for a moment. The three definitions all sound reasonable. They cannot all hold. *One of them has to give.* And which one gives is not a technical question.

<!-- → [DIAGRAM: Three nodes (demographic parity, equalized odds, calibration parity) arranged in a triangle. Between each pair of nodes, an arrow labeled with what breaks when both are required simultaneously given differing base rates. At the center: "base rates differ." Caption: "You can satisfy any two. The third breaks. The triangle is the theorem."] -->

<!-- → [TABLE: Worked arithmetic with base rates 0.6 / 0.3 — rows: base rate, threshold, true-positive rate, false-positive rate, precision, positive prediction rate. Two columns: group A, group B. One version satisfying calibration (and showing equalized odds violation). One version satisfying equalized odds (and showing calibration violation). Student should see the numbers, not just the argument.] -->

---

The most famous instance of this theorem playing out in public was the COMPAS case in 2016. We met it briefly in Chapter 3. Now we can see exactly what was happening.

COMPAS — Correctional Offender Management Profiling for Alternative Sanctions — is a commercial risk-assessment tool used in some U.S. jurisdictions to estimate a defendant's likelihood of re-arrest. ProPublica analyzed its outputs in Broward County, Florida, and reported that the false-positive rate was substantially higher for Black defendants than for white defendants. Black defendants who did not go on to be re-arrested were misclassified as high-risk more often than white defendants who did not. This is a violation of equalized odds.

Northpointe — the maker of COMPAS — responded that the tool was calibrated. Within each risk-score bucket, the actual rate of re-arrest was approximately the same across racial groups. A score of seven meant about the same likelihood of re-arrest whether the defendant was Black or white. This is a satisfaction of calibration parity.

Both claims were true. They were measuring different fairness properties, and the underlying base rates of re-arrest in the available data differed across racial groups. The impossibility theorem says, given that base-rate difference, you cannot have both. ProPublica and Northpointe were not having a factual disagreement that more data could settle. They were having a values disagreement about which definition of fairness should win.

The COMPAS debate is the canonical case for one reason. It shows that the impossibility theorem is not a chalkboard curiosity. It is the structural fact behind a real public argument with real consequences for real people. And it shows that the choice between metrics is not a technical choice. It is a values claim about which kind of fairness matters more in this deployment.

---

Let me make the values claims explicit, because the chapter's main lesson lives here.

Demographic parity says: equal rates of positive prediction matter, even if the underlying outcome rates differ between groups. The reasoning: the prediction itself has consequences — a job, a loan, a release — and those consequences should be allocated equally, irrespective of accuracy claims about base rates that may themselves reflect upstream injustice.

Equalized odds says: equal error rates matter. The harm of being wrongly flagged should fall equally on both groups. The benefit of being correctly identified should be allocated equally. The model should not make systematic mistakes that differ by group.

Calibration parity says: the probabilities should mean the same thing for everyone. A score should be informationally honest in the same way across groups, so that downstream decision-makers using the score have a consistent input to weigh.

Each of these is a coherent value. None is the obvious correct one. The choice among them depends on the deployment. *Who is using the prediction? What decision are they making? Who bears the cost of error? Is the underlying base-rate difference itself something the deploying organization has a stance on?*

For COMPAS specifically, the answers matter materially. The use case is bail and sentencing. The cost of a false positive is detention or a harsher sentence for someone who would not have re-offended. The cost of a false negative is release of someone who does. These costs fall on different people. Calibration parity treats the probability as informational input and trusts downstream decision-makers to weigh it. Equalized odds says the *errors* should fall equally, regardless of the probability values. The two embed different theories of where in the system the fairness obligation sits — at the level of the score, or at the level of the decision the score informs.

This is the argument. There is no technical resolution. The argument has to be made and defended.

<!-- → [TABLE: COMPAS case mapped to the three metrics — rows: metric name, what ProPublica measured, what Northpointe measured, what each claimed, whether each claim was factually accurate, what each embeds as a values claim. Caption: "Both sides were right about the numbers. The disagreement was about which numbers should matter."] -->

---

A note on tools, because the field has a substantial toolkit for adjusting models to satisfy fairness metrics. The methods generally come in three families. *Pre-processing* modifies the training data — reweighting, resampling, transforming features — so that the model trains on data that already approximates the desired property. *In-processing* modifies the training objective, adding fairness penalties to the loss or using adversarial methods where one network tries to fool another into not being able to predict the protected attribute from the model's representations. *Post-processing* modifies the predictions after the fact — adjusting thresholds, calibrating per group.

These tools can do real work. What they cannot do — and this is the thing to keep clear — is resolve the impossibility theorem. They let you choose which metric to satisfy at the cost of others. They do not give you all three. They also cannot address structural bias upstream of the data you are training on (Chapter 3's lesson, returning), and they cannot make the deployment context fair if the fairness problem lives there. The toolkit is a way to *implement* a values choice. It does not absolve you of making the choice.

---

So we arrive at the question: what do you actually deliver?

The standard answer in machine learning training is a number. A model. A metric satisfying some target. This is not enough. The impossibility theorem makes it not enough, because the choice of metric is not a technical choice and the model alone does not show the work.

The deliverable in this domain is a *defended choice*. It has the following structure, and you will produce something in this form on every fairness deliverable from here forward.

You specify the deployment. Who is using the prediction. What decisions are being made. Who bears the cost of error. What the base-rate distribution looks like, and as much as you can say about why it looks that way. You compute the candidate metrics — demographic parity, equalized odds, calibration parity, others if relevant — and show their values and the trade-offs explicitly. You name where the metrics disagree and what each disagreement embeds as a values claim. You state your choice — which metric you are prioritizing, which others you are accepting trade-offs against. You defend the choice in writing, connecting the metric to the deployment, the cost-bearers, and the construct the deployment is supposed to serve. And you name what would change your mind — what evidence or argument would make you revise the choice.

This is not a familiar deliverable in engineering training. It looks, on the surface, like an essay. It is not. It is the form of an *engineering decision under value pluralism*, and producing it is the supervisory capacity at work. Engineers will defend choices like this in adoption committees, regulatory submissions, and internal review processes for the rest of their careers. The defense is the deliverable not because we are simulating a paper. The deliverable is what the job requires.

<!-- → [INFOGRAPHIC: The defended-choice structure as a checklist template — six labeled sections: (1) deployment specification, (2) base-rate distribution and provenance, (3) candidate metrics computed and shown, (4) where metrics disagree and what each embeds, (5) stated choice with justification, (6) what would change your mind. Designed as a reusable scaffold — the kind of thing students fill in on each assignment.] -->

---

A clean statement of the limits, before we move on.

Fairness metrics tell you whether the model's outputs differ across groups in specified ways. They do not tell you whether the prediction task is itself fair to formulate — a model that perfectly predicts re-arrest is doing well on a metric of re-arrest, but re-arrest is not the construct society cares about, and the gap between re-arrest and "future criminal behavior" is upstream of any metric we can compute. They do not tell you whether the deployment will produce a fair outcome — the metric is on the prediction; the outcome depends on the decision process the prediction feeds into. And they do not tell you whether the affected populations consider the model's behavior fair — the metrics formalize specific senses of *fair*, and people may have other senses (procedural fairness, fairness of opportunity, fairness as participation in the design) that the metrics do not capture.

These are limits on what fairness metrics can do, not failures of the metrics. The supervisory work continues past the metric, into the construct, the deployment, and the participation. We will revisit the construct gap in Chapter 14.

---

Where I am uncertain.

What would change my mind: if a fairness metric were proposed that demonstrably escaped the impossibility theorem in realistic base-rate regimes — without trivializing one of its constituent claims — the framing of "defense as deliverable" would be less essential. I have not seen such a proposal. The metric variants I have seen sit within the trade-off space the theorem defines.

What I am still puzzling about: I do not have a clean way to elicit, from the populations affected by a deployment, which fairness metric they would prioritize for that deployment. Engineering practice tends to make the choice on the engineer's authority, sometimes with input from compliance or ethics review. This is not satisfactory and I do not yet have a working alternative. There is recent work in participatory design that may be the direction this comes from. The integration with the technical defense remains underspecified, and I am working on it.

---

Three formally distinct fairness metrics can be incompatible on the same dataset when base rates differ. The impossibility is structural, not a tooling artifact. Each metric embeds a values claim. The toolkit lets you implement a chosen metric; it does not absolve you of the choice. The defense is the deliverable, and the defense connects the metric to the deployment, the cost-bearers, and the construct the deployment is supposed to serve.

The next chapter takes a different cut at "what does the model know." Fairness metrics ask whether the model treats different inputs equitably. Adversarial robustness asks something else: can a perturbation imperceptible to a human change the model's output entirely? And if so — what does that say about what the model has actually learned?

---

## Exercises

### Warm-Up

**1.** In your own words, state the impossibility theorem this chapter is built around. What three things cannot simultaneously hold, and under what condition does the impossibility apply? Do not use the phrase "base rates differ" without explaining what it means. *(Tests: stating the theorem precisely, not just by name.)*

**2.** The chapter defines demographic parity, equalized odds, and calibration parity. For each one, write: (a) a one-sentence formal definition, and (b) a one-sentence description of the values claim it embeds — what theory of fairness makes this metric the right one to optimize for? *(Tests: distinguishing the formal definition from the values claim, which are different things.)*

**3.** In the COMPAS case, ProPublica and Northpointe were both correct about the numbers they reported. Explain specifically how both can be correct and still be in disagreement. What kind of disagreement were they having? *(Tests: applying the impossibility framework to the canonical case; distinguishing factual from values disagreement.)*

---

### Application

**4.** A healthcare system deploys a model to predict which patients are likely to need intensive follow-up care in the next 30 days. The model is used to allocate limited care-coordinator capacity. The underlying rate of high-need patients is 40% in population X and 20% in population Y — a difference reflecting documented disparities in social determinants of health.

Compute, qualitatively (no specific numbers required), what satisfying calibration parity implies for the false-positive rate across groups. Then state which of the three metrics you would prioritize for this deployment and defend the choice in two paragraphs, using the defended-choice structure from the chapter. *(Tests: applying the impossibility reasoning to a new deployment; producing a defended choice with explicit values claim.)*

**5.** The chapter distinguishes three families of fairness-adjustment tools: pre-processing, in-processing, and post-processing. For each family, give one concrete example of a technique and explain what specific fairness metric it is typically used to implement. Then explain, for each, why the toolkit does not resolve the impossibility theorem — only implements a choice within it. *(Tests: knowing the toolkit without overstating what it can do.)*

**6.** A colleague argues: "We should just use demographic parity everywhere — equal positive-prediction rates is the most intuitive definition of fairness, and it is easy to audit." Construct the strongest counterargument you can. Under what deployment conditions would demographic parity actually produce outcomes that most people would consider *less* fair than a calibrated model would? *(Tests: stress-testing the most appealing-sounding metric; connecting metric choice to deployment context.)*

**7.** The chapter states that the pre-processing, in-processing, and post-processing toolkit "cannot address structural bias upstream of the data you are training on." Explain what upstream structural bias means, give one example of how it would appear in training data, and explain why no fairness metric computed on model outputs can detect or correct it. *(Tests: connecting Chapter 3's data-provenance lesson to Chapter 7's metric framework.)*

---

### Synthesis

**8.** The chapter argues that "the defense is the deliverable." Using Chapter 1's five supervisory capacities — plausibility auditing, problem formulation, tool orchestration, interpretive judgment, and executive integration — map each step of the defended-choice structure onto one or more of those capacities. Which step is most clearly an act of problem formulation? Which is most clearly executive integration? Are any steps in the defense not covered by the five capacities? *(Tests: connecting Chapter 7's deliverable to Chapter 1's framework.)*

**9.** The chapter closes with two acknowledged limits of fairness metrics: they do not evaluate whether the prediction task is fair to formulate, and they do not capture what affected populations actually consider fair. For a hiring-screen classifier, describe concretely what each limit looks like in practice: (a) what would make the prediction task itself unfair to formulate, regardless of which metric the model satisfies, and (b) what a participatory fairness assessment might elicit that none of the three metrics would capture. *(Tests: extending the framework past the chapter's formal scope; connecting to the construct-validity issue previewed for Chapter 14.)*

---

### Challenge

**10.** The chapter frames the choice among fairness metrics as a values decision that engineers must make and defend. But in regulated industries, the regulator may specify which metric to satisfy — removing the engineer's discretion. Does regulatory specification resolve the problem the chapter describes, or does it relocate it? If a regulator specifies equalized odds for a credit-scoring model, what questions remain that the specification does not answer? *(Tests: evaluating the limits of metric specification as a governance mechanism; forces thinking about what "defense" requires even when the metric is mandated.)*

**11.** The chapter's uncertainty section flags an open problem: there is no clean way to elicit, from affected populations, which fairness metric they would prioritize. Propose a method. It does not need to be fully specified — it needs to be specific enough to fail in identifiable ways. Name the method, describe how it would work in one deployment context, and then name two ways it could produce misleading results. *(Open-ended; the point is to engage seriously with a problem the chapter admits is unsolved.)*

---

*Tags: fairness, impossibility-theorem, compas, values-claim, defense-as-deliverable*
