
# Chapter 7 — Fairness Metrics: Choosing a Definition and Defending It
*Three reasonable definitions. One dataset. Pick one.*

---

I want to give you a problem.

You have built a binary classifier — a model that produces a yes/no prediction. It could be a loan approval tool, a recidivism predictor, a hiring screen. It is being applied to a population that contains two groups, A and B. The model produces predictions; the world produces outcomes; you can compare the two.

You want the model to be fair. You sit down to write the requirement. The first definition you write is *demographic parity*: the rate at which the model says yes should be the same in both groups. Same fraction of group A and group B get the favorable prediction. This sounds like fairness, and in one specific sense — equality of treatment in the prediction itself — it is.

You think a little more. Equal positive-prediction rates seem necessary but not sufficient. What if the model is wrong about A and B in different ways? You add a second requirement: *equalized odds*. The false-positive rate and the true-positive rate should be the same in both groups. The model should make its mistakes evenly. This sounds like fairness too, in a different sense — equality of error patterns.

You think a little more still. Even equal error rates may not be enough. What if the model's stated probabilities mean different things in the two groups? You add a third requirement: *calibration parity*. When the model says "seventy percent," the actual rate of positives is seventy percent in group A and seventy percent in group B. The probabilities mean the same thing for everyone. This too is fairness, in yet a different sense.

You have three reasonable definitions. Each captures something fairness intuitively requires. You set out to satisfy all three.

I have to tell you that you cannot. Not because the tools aren't good enough. Not because the data is insufficient. *You cannot satisfy all three on the same dataset, simultaneously, when base rates differ between the groups* — and on most real datasets they do differ. This is a theorem. It was proved in 2016, independently, by Kleinberg, Mullainathan, and Raghavan, and by Chouldechova. It is the structural fact this chapter is about.

The chapter has two jobs. First, I want you to feel why the impossibility is true — not as a formal result you take on authority, but as a thing that becomes obvious once you see the arithmetic. Second, I want you to come away with a method for what to do about it. The impossibility does not let you avoid the question. It forces a choice. And choices in engineering have to be defended.

---

**What you will be able to do after this chapter:**

- State the impossibility theorem precisely — what three properties cannot coexist, and under what condition
- Define demographic parity, equalized odds, and calibration parity in formal terms and explain the values claim each embeds
- Work through the arithmetic that shows why satisfying calibration parity forces a violation of equalized odds when base rates differ
- Apply the COMPAS case as an instance of the theorem playing out with real consequences
- Produce a defended metric choice for a specified deployment — the structured format the chapter defines

**Prerequisites:** Chapter 2's probability foundations and Chapter 3's data-provenance material are both relevant here. Chapter 2 gives you the probabilistic language the metrics require. Chapter 3 established why base-rate differences exist in real data and why they may not be correctable within the model.

**Where this fits:** Chapters 5 and 6 audited data and model performance. This chapter asks a different question: not whether the model is accurate, but whether its errors and predictions are distributed equitably. The fairness question is not downstream of the accuracy question. It is structurally distinct from it.

---

## Three definitions

Let me write the three definitions tighter, with the formal notation.

*Demographic parity* — sometimes called statistical parity — says the rate of positive predictions does not depend on group membership:

P(Ŷ = 1 | group = A) = P(Ŷ = 1 | group = B)

The probability that the model predicts yes, given that the input is from group A, equals the same probability for group B. This is a statement about the model's *outputs*, before anyone has compared them to the ground truth.

*Equalized odds* says the model's error rates do not depend on group. The true-positive rate — among those who actually were positive, the fraction the model correctly flagged — is the same in both groups. The false-positive rate — among those who actually were not positive, the fraction the model incorrectly flagged — is also the same:

P(Ŷ = 1 | Y = 1, group = A) = P(Ŷ = 1 | Y = 1, group = B)

P(Ŷ = 1 | Y = 0, group = A) = P(Ŷ = 1 | Y = 0, group = B)

This is a statement about the model's *errors*, conditional on the truth.

*Calibration parity* — sometimes called predictive parity — says the model's stated probabilities track empirical frequencies equally well in both groups:

P(Y = 1 | P̂ = p, group = A) = P(Y = 1 | P̂ = p, group = B), for all p

When the model assigns probability p to a case from group A, the realized positive rate among such cases is p; the same holds for group B. This is a statement about whether the *probability outputs are honest*, in the same way for everyone.

These three look like they should all be compatible. They are not.

<!-- FIGURE: Figure 7.1 — Three-column definition reference table: metric name (with alias) / what it measures (one sentence) / what it is a statement about (outputs / errors / probability honesty) / the values claim it embeds. Designed for student reference during the arithmetic section. [Empty placeholder in Doc 5 — needs content before publication.] -->

---

## The arithmetic

Let me show you why. I will use specific numbers to make the arithmetic visible. Suppose the underlying base rate of positives is 0.6 in group A and 0.3 in group B. Sixty percent of group A has the positive ground truth; thirty percent of group B does. Pick any context where this might hold — different prevalence rates of an outcome in two populations, for whatever historical or structural reason. Set the reasons aside for the moment. We are working out what the math forces.

Suppose the classifier is calibrated. Among everyone the model assigns probability p, the realized positive rate is p, and this holds in both groups separately. Among group A members assigned probability 0.7, seventy percent are actually positive. Among group B members assigned probability 0.7, seventy percent are actually positive. The model is honest about probabilities, and equally honest across groups.

Now ask: among everyone the model predicts positive at some threshold, what fraction are *actually* positive? In group A, where the underlying base rate is 0.6, there are many true positives to find; the predicted-positive set will be heavily populated by them. In group B, where the underlying base rate is 0.3, there are fewer true positives in the population at all; whatever the model flags will be drawn from a thinner pool.

Watch what happens. Among those flagged positive, the share that are *actually* positive — the precision — will be higher in group A and lower in group B. By the same arithmetic, among those *not* actually positive, a larger fraction will be wrongly flagged in group B than in group A. The false-positive rate is higher in group B.

Calibration is satisfied. It cannot help being satisfied — we built it in. But equalized odds is now violated, because the false-positive rates are unequal across groups. And they have to be unequal — not because of model design, not because of bad data, but because base rates differ and we required the probabilities to mean the same thing in both groups. The conjunction of those two requirements *forces* unequal error rates.

You can run the same reasoning the other way. Suppose you require equalized odds — false-positive and true-positive rates equal across groups. Then, with different base rates, the precision cannot be the same across groups. Calibration is broken.

And demographic parity adds yet another constraint — the rate of positive predictions equal in both groups — that, in general, breaks both calibration and equalized odds.

The full proof is in Kleinberg, Mullainathan, and Raghavan (2016) and Chouldechova (2017). ([verify] both references, dates, and titles.) The intuition is what I want you to carry: *the theorem is not about which model you train. It is about what is mathematically possible at all.* You can satisfy any two of the three, given non-trivial base-rate differences. You cannot satisfy all three.

I want you to sit with this for a moment. The three definitions all sound reasonable. They cannot all hold. *One of them has to give.* And which one gives is not a technical question.

<!-- FIGURE: Figure 7.2 — Three nodes (demographic parity, equalized odds, calibration parity) arranged in a triangle. Between each pair of nodes, an arrow labeled with what breaks when both are required simultaneously given differing base rates. At the center: "base rates differ." Caption: "You can satisfy any two. The third breaks. The triangle is the theorem." Insert image: images/07-fairness-metrics-choosing-a-definition-and-defending-it-fig-02.jpg -->

<!-- FIGURE: Figure 7.3 — Worked arithmetic table with base rates 0.6 / 0.3. Rows: base rate, threshold, true-positive rate, false-positive rate, precision, positive prediction rate. Two columns: group A, group B. Two versions: one satisfying calibration (showing equalized-odds violation), one satisfying equalized odds (showing calibration violation). Student should see the numbers, not just the argument. [Empty placeholder in Doc 5 — needs content before publication.] -->

---

## The COMPAS case

The most famous instance of this theorem playing out in public was the COMPAS case in 2016. We met it briefly in Chapter 3. Now we can see exactly what was happening.

COMPAS — Correctional Offender Management Profiling for Alternative Sanctions — is a commercial risk-assessment tool used in some U.S. jurisdictions to estimate a defendant's likelihood of re-arrest. ProPublica analyzed its outputs in Broward County, Florida, and reported that the false-positive rate was substantially higher for Black defendants than for white defendants. Black defendants who did not go on to be re-arrested were misclassified as high-risk more often than white defendants who did not. This is a violation of equalized odds.

Northpointe — the maker of COMPAS — responded that the tool was calibrated. Within each risk-score bucket, the actual rate of re-arrest was approximately the same across racial groups. A score of seven meant about the same likelihood of re-arrest whether the defendant was Black or white. This is a satisfaction of calibration parity.

Both claims were true. They were measuring different fairness properties, and the underlying base rates of re-arrest in the available data differed across racial groups. The impossibility theorem says, given that base-rate difference, you cannot have both. ProPublica and Northpointe were not having a factual disagreement that more data could settle. They were having a values disagreement about which definition of fairness should win.

The COMPAS debate is the canonical case for one reason. It shows that the impossibility theorem is not a chalkboard curiosity. It is the structural fact behind a real public argument with real consequences for real people. And it shows that the choice between metrics is not a technical choice. It is a values claim about which kind of fairness matters more in this deployment.

<!-- FIGURE: Figure 7.4 — COMPAS case mapped to the three metrics. Rows: metric name / what ProPublica measured / what Northpointe measured / what each claimed / whether each claim was factually accurate / the values claim each embeds. Caption: "Both sides were right about the numbers. The disagreement was about which numbers should matter." [Empty placeholder in Doc 5 — needs content before publication.] -->

---

## Each metric is a values claim

Let me make the values claims explicit, because the chapter's main lesson lives here.

*Demographic parity* embeds the claim: equal rates of positive prediction matter, independent of underlying base-rate differences. This is a strong claim. It says, in effect, that we will not let the model's positive-prediction rate diverge across groups even if the underlying outcome rates differ — because the prediction itself, irrespective of accuracy, has consequences (a job, a loan, a medical procedure) that should be allocated equally.

*Equalized odds* embeds the claim: equal error rates matter. The harm of being wrongly flagged should fall equally on both groups. The benefit of being correctly identified should be allocated equally. The model should not make systematic mistakes that differ by group.

*Calibration parity* embeds the claim: the probabilities should mean the same thing for everyone. A 70% prediction should mean a 70% chance regardless of group membership, so that downstream decision-makers using the probability as input can treat it the same way.

Each of these is a coherent value. None is the obvious correct one. The choice among them depends on the deployment: who is using the prediction, what decisions they are making, who bears the cost of error, whether the underlying base-rate difference is itself something the deploying organization has any stance on.

For COMPAS specifically, the answers matter materially. The use case is bail and sentencing. The cost of a false positive is detention or a harsher sentence for someone who would not have re-offended. The cost of a false negative is release of someone who does. These costs fall on different people. Calibration parity treats the probability as informational input and trusts downstream decision-makers to weigh it. Equalized odds says the *errors* should fall equally, regardless of the probability values. The two embed different theories of where in the system the fairness obligation sits — at the level of the score, or at the level of the decision the score informs.

This is the argument. There is no technical resolution. The argument has to be made and defended.

---

## The toolkit and what it cannot do

There are tools for adjusting models to satisfy fairness metrics. They generally fall in three families.

*Pre-processing* modifies the training data — reweighting, resampling, transforming features — so that the model trains on data that already approximates the desired property. The advantage: the fairness property is built into the training distribution. The disadvantage: the pre-processing makes a values choice about how to alter the data, and that choice is not always principled.

*In-processing* modifies the training objective, adding fairness penalties to the loss or using adversarial methods where a discriminator tries to predict the protected attribute from the model's representations and the model is trained to fool it. The advantage: tighter coupling between training and the fairness target. The disadvantage: fairness becomes a constrained optimization, and the constraint trades off against accuracy.

*Post-processing* modifies the predictions after the fact — adjusting thresholds, calibrating per group. The advantage: simple, easy to explain. The disadvantage: explicit group-conditional thresholds may not be legally permissible in some jurisdictions, and the post-processing does not change the model — it changes the deployment.

What this toolkit cannot do is resolve the impossibility theorem. It can let you choose which metric to satisfy, at the cost of others. It cannot give you all three. It also cannot address structural bias upstream of the data you are training on — Chapter 3's lesson, returning here with a specific application. And there is a harder version of that limit: *Agents of Chaos* Case #6 concerns a deployment where the bias is in the model provider's training, upstream of any deploying engineer's pipeline. No item in the debiasing toolkit touches that bias, because the toolkit operates on the deploying engineer's data and model. The bias is not in those.

The toolkit is a way to implement a values choice. It does not absolve you of making the choice.

---

## The defense as deliverable

So we arrive at the question: what do you actually deliver?

The standard answer in machine learning training is a number. A model. A metric satisfying some target. This is not enough. The impossibility theorem makes it not enough, because the choice of metric is not a technical choice and the model alone does not show the work.

The deliverable in this domain is a *defended choice*. It has the following structure, and you will produce something in this form on every fairness deliverable from here forward.

1. **Specify the deployment.** Who is using the prediction. What decisions are being made. Who bears the cost of error. What the base-rate distribution looks like, and as much as you can say about why it looks that way.
2. **Compute the candidate metrics.** Demographic parity, equalized odds, calibration parity, and any others relevant. Show the values. Show the trade-offs explicitly.
3. **Name the conflict.** Where do the metrics disagree? What does each disagreement embed as a values claim?
4. **State your choice.** Which metric you are prioritizing. Which others you are accepting trade-offs against.
5. **Defend the choice in writing.** Why this metric, in this deployment, given who bears the costs and who benefits. The defense connects the metric to the deployment, the cost-bearers, and the construct the deployment is supposed to serve.
6. **Name what would change your mind.** What evidence or argument would lead you to revise the choice.

This is not a familiar deliverable in engineering training. It looks, on the surface, like an essay. It is not. It is the form of an *engineering decision under value pluralism*, and producing it is the supervisory capacity at work. Engineers will be defending choices like this in adoption committees, regulatory submissions, and internal review processes for the rest of their careers. The defense is the deliverable not because we are simulating a paper. The defense is what the job requires.

<!-- FIGURE: Figure 7.5 — The defended-choice structure as a six-section checklist template: (1) deployment specification, (2) base-rate distribution and provenance, (3) candidate metrics computed and shown, (4) where metrics disagree and what each embeds, (5) stated choice with justification, (6) what would change your mind. Designed as a reusable scaffold for student assignments. Insert image: images/07-fairness-metrics-choosing-a-definition-and-defending-it-fig-05.jpg -->

---

## Two Botspeak pillars

Two of the nine Botspeak pillars (treated in full in Appendix A) do specific work in this chapter.

*Ethical Reasoning* is the capacity to engage with the values question — which fairness do we prioritize? — as a structural part of the engineering work, rather than offloading it to ethicists or compliance teams. The defense in the section above is Ethical Reasoning made operational. The defense is not a supplement to the engineering work. It is the engineering work.

*Stochastic Reasoning* is the capacity to think about base rates, error distributions, and the statistical structure that produces the impossibility theorem. Without it, the metric choice is an arbitrary preference. With it, the choice is grounded in what the metrics are actually measuring and why they conflict.

The two pillars together produce the supervisory move: understand the math, choose the values, defend the integration. Engineers who skip either step produce defenses that are either rigorous and arbitrary or coherent and unmoored from the numbers.

---

## The persistent limits

A clean statement of what fairness metrics cannot do, before moving on.

Fairness metrics tell you whether the model's outputs differ across groups in specified ways. They do not tell you whether the prediction task is itself fair to formulate — a model that perfectly predicts re-arrest is doing well on a metric of re-arrest, but re-arrest is not the construct society cares about, and the gap between re-arrest and "future criminal behavior" is upstream of any metric we can compute. They do not tell you whether the deployment will produce a fair outcome — the metric is on the prediction; the outcome depends on the decision process the prediction feeds into. And they do not tell you whether the affected populations consider the model's behavior fair — the metrics formalize specific senses of fair, and people may have other senses (procedural fairness, fairness of opportunity, fairness as participation in the design) that the metrics do not capture.

These are limits on what fairness metrics can do, not failures of the metrics. The supervisory work continues past the metric, into the construct, the deployment, and the participation. We will revisit the construct gap in Chapter 14.

---

## The shape of the rest

Three formally distinct fairness metrics can be incompatible on the same dataset when base rates differ. The impossibility is structural, not a tooling artifact. Each metric embeds a values claim. The toolkit lets you implement a chosen metric; it does not absolve you of the choice. The defense is the deliverable, and the defense connects the metric to the deployment, the cost-bearers, and the construct the deployment is supposed to serve.

The Pebble makes a brief appearance in this chapter — *Agents of Chaos* Case #6, where the bias sits in the model provider's training rather than in the deploying engineer's pipeline. We return to that contrast in Chapter 13's accountability discussion: who is responsible when the bias is structurally upstream of everyone in the deployment chain?

The next chapter takes a different cut at what the model knows. Fairness metrics ask whether the model treats different inputs equitably. Adversarial robustness asks something else: can a perturbation imperceptible to a human change the model's output entirely? And if so — what does that say about what the model has actually learned?

---

**What would change my mind.** If a fairness metric were proposed that demonstrably escaped the impossibility theorem in realistic base-rate regimes — without trivializing one of its constituent claims — the "defense as deliverable" framing would be less essential. To my reading, no such metric has been proposed. The metric proposals I have seen (see Mehrabi et al. 2021 for a survey — [verify] full citation) all sit within the trade-off space the theorem defines.

**Still puzzling.** I do not have a clean way to elicit, from affected populations at scale, which fairness metric they would prioritize for a deployment that affects them. Engineering practice tends to make the choice on the engineer's authority, sometimes with input from compliance or ethics review. This is not satisfactory and I do not yet have a working alternative. Some recent work on participatory design (e.g., Sloane et al. 2022 — [verify]) may be the direction this comes from. The integration with the technical defense remains underspecified, and I am working on it.

---

## Exercises

### Glimmers

**Glimmer 7.1 — Two definitions, one dataset, one defended position**

1. Take a real dataset where the impossibility shows up. The COMPAS dataset is canonical and openly available. ([verify] dataset link from ProPublica's GitHub.) Other options: the German credit dataset, the adult-income dataset, the Folktables benchmark.
2. Specify a deployment context. Be specific: who is using the prediction, what decisions are being made, who bears the cost of error. If the dataset's natural deployment is clear, use that. If not, construct a plausible one and document your construction.
3. *Lock your prediction:* before computing, predict which two of the three metrics will conflict most sharply, and which one you intend to prioritize.
4. Compute all three metrics on the dataset. Document the values. Show the trade-offs explicitly.
5. Apply at least one debiasing intervention. Re-compute. Document what changed.
6. Write the defense using the six-step structure from the chapter. Use the numbered structure literally.
7. Reflect on the gap between your locked prediction and what the metrics actually showed, and on whether your defended choice survives the actual numbers.

The deliverable is the metrics, the intervention, the defense, and the reflection. The grade is on the defense and the reflection. The metric values are easy. *The defense is the work.*

---

### Warm-Up

**1.** In your own words, state the impossibility theorem this chapter is built around. What three things cannot simultaneously hold, and under what condition does the impossibility apply? Do not use the phrase "base rates differ" without explaining what it means.

**2.** The chapter defines demographic parity, equalized odds, and calibration parity. For each one, write: (a) a one-sentence formal definition, and (b) a one-sentence description of the values claim it embeds — what theory of fairness makes this metric the right one to optimize for?

**3.** In the COMPAS case, ProPublica and Northpointe were both correct about the numbers they reported. Explain specifically how both can be correct and still be in disagreement. What kind of disagreement were they having?

---

### Application

**4.** A healthcare system deploys a model to predict which patients are likely to need intensive follow-up care in the next 30 days. The model is used to allocate limited care-coordinator capacity. The underlying rate of high-need patients is 40% in population X and 20% in population Y — a difference reflecting documented disparities in social determinants of health.

Compute, qualitatively (no specific numbers required), what satisfying calibration parity implies for the false-positive rate across groups. Then state which of the three metrics you would prioritize for this deployment and defend the choice in two paragraphs, using the defended-choice structure from the chapter.

**5.** The chapter distinguishes three families of fairness-adjustment tools: pre-processing, in-processing, and post-processing. For each family, give one concrete example of a technique and explain what specific fairness metric it is typically used to implement. Then explain, for each, why the toolkit does not resolve the impossibility theorem — only implements a choice within it.

**6.** A colleague argues: "We should just use demographic parity everywhere — equal positive-prediction rates is the most intuitive definition of fairness, and it is easy to audit." Construct the strongest counterargument you can. Under what deployment conditions would demographic parity actually produce outcomes that most people would consider *less* fair than a calibrated model would?

**7.** The chapter states that the pre-processing, in-processing, and post-processing toolkit "cannot address structural bias upstream of the data you are training on." Explain what upstream structural bias means, give one example of how it would appear in training data, and explain why no fairness metric computed on model outputs can detect or correct it.

---

### Synthesis

**8.** The chapter argues that "the defense is the deliverable." Using Chapter 1's five supervisory capacities — plausibility auditing, problem formulation, tool orchestration, interpretive judgment, and executive integration — map each step of the defended-choice structure onto one or more of those capacities. Which step is most clearly an act of problem formulation? Which is most clearly executive integration? Are any steps in the defense not covered by the five capacities?

**9.** The chapter closes with two acknowledged limits of fairness metrics: they do not evaluate whether the prediction task is fair to formulate, and they do not capture what affected populations actually consider fair. For a hiring-screen classifier, describe concretely what each limit looks like in practice: (a) what would make the prediction task itself unfair to formulate, regardless of which metric the model satisfies, and (b) what a participatory fairness assessment might elicit that none of the three metrics would capture.

---

### Challenge

**10.** The chapter frames the choice among fairness metrics as a values decision that engineers must make and defend. But in regulated industries, the regulator may specify which metric to satisfy — removing the engineer's discretion. Does regulatory specification resolve the problem the chapter describes, or does it relocate it? If a regulator specifies equalized odds for a credit-scoring model, what questions remain that the specification does not answer?

**11.** The chapter's uncertainty section flags an open problem: there is no clean way to elicit, from affected populations, which fairness metric they would prioritize. Propose a method. It does not need to be fully specified — it needs to be specific enough to fail in identifiable ways. Name the method, describe how it would work in one deployment context, and then name two ways it could produce misleading results.

---

*Tags: fairness, impossibility-theorem, compas, values-claim, defense-as-deliverable*

