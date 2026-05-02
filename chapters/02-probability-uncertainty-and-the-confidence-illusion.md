# Chapter 2 — Probability, Uncertainty, and the Confidence Illusion
*Why your intuition forgets the prior, and what to do when it does.*

## Learning objectives

By the end of this chapter, you will be able to:

- Apply Bayes' theorem to calculate the actual probability of a rare event given a positive test result, and explain why the result differs from naive intuition
- Identify the base rate in a deployment scenario and explain how ignoring it produces systematically wrong conclusions
- Distinguish between model accuracy and model calibration, and describe what a calibration curve reveals that accuracy metrics hide
- Recognize the conditions under which the Central Limit Theorem does not apply, and identify when a deployment's loss distribution is likely to be heavy-tailed
- Explain distribution shift as an instance of Hume's problem of induction, and name at least two mechanisms by which a model can become confidently wrong after deployment

## Prerequisites

Chapter 1 (AI systems and supervisory capacities). Familiarity with basic probability notation (P(A), P(A|B)) is helpful but not required — the notation is introduced from scratch where used.

---

## Why this chapter

The failures we examined in Chapter 1 were not, for the most part, failures of model competence. The models were doing what their training permitted. The failures were failures of interpretation — of engineers and operators reading model outputs through intuitions that were not built for the problem at hand.

This chapter builds the apparatus for repairing those intuitions. We do one calculation on the page — the disease test — open one diagnostic — the calibration curve — and point at one structural worry — heavy tails and the limits of CLT-based thinking. We use all three on every subsequent chapter.

---

## The test that is 99% accurate, and the patient it kills

I want to tell you about a test.

It is a screening test for some disease — pick whichever one makes you uncomfortable. The disease is rare. About one person in ten thousand has it. The test is, the company says, ninety-nine percent accurate. A patient walks in, takes the test, and the test comes back positive.

Now: how confident are you that this patient has the disease?

Stop. Don't read on yet. Pick a number. Say it out loud if you have to. Lock it in. The whole point of what follows is wrecked if you peek without committing.

I have put this question to a lot of engineers, smart ones, and almost everybody answers somewhere above ninety percent. Some hedge — "maybe ninety-five." A few brave souls go to seventy. Almost nobody goes lower. The reasoning sounds airtight: the test is ninety-nine percent accurate, the test came back positive, therefore the patient probably has the disease. What could be more obvious?

The actual answer is about one percent.

I want to walk you through why, because the calculation is simple enough that you will do it on the back of an envelope and remember it forever. Imagine ten thousand people taking the test. One of them, on average, actually has the disease — that is the base rate, one in ten thousand. The test catches that person. Call it one true positive. The other 9,999 do not have the disease. The test, which is ninety-nine percent accurate, makes a mistake on one percent of them. One percent of 9,999 is about a hundred. So we get one true positive, and a hundred false positives, in the same pool of positive results. If you pull a positive result out of that pool, the chance it came from someone who is actually sick is one out of a hundred and one. About one percent.

<!-- FIGURE: A population of 10,000 people shown as a grid of dots — one red (true positive), ~100 orange (false positives), ~9,899 gray (true negatives). Arrow shows: "you pull one positive result from the red + orange pool — 1 in 101 is red." Puts the arithmetic viscerally before the student. Caption: Figure 2.1 — Most of the positive results come from the large healthy population, not the tiny sick one. -->

Now, isn't that something? The test is doing exactly what its specification said it would do. The number on the box is honest. Nothing is broken. And yet your intuition was off by two orders of magnitude.

I want to slow down here, because most of this chapter is just unpacking what went wrong in your head. You weren't dumb. The math you were doing in your head was a perfectly reasonable calculation — for a different question. You were computing something like *given the disease, how often does the test get it right?* And the answer to that is indeed ninety-nine percent. But the question being asked was *given a positive test, how often is the disease there?* Those are not the same question, and you cannot get from one to the other without one extra ingredient.

The thing your intuition forgot was the base rate — the fact that, before the test, you already knew this disease was rare. Almost nobody who walks into the clinic has it. So almost all the positives are going to come from the much larger pool of healthy people, where even a small error rate generates a lot of mistakes in absolute terms. The disease is rare; the errors are common; the errors win.

This is not a parlor trick. The structure is everywhere. Fraud detection: most transactions are not fraud. Security alerts: most logins are not attackers. Medical screening: most patients do not have the rare condition you are screening for. Agentic-system jailbreaks: most user inputs are not trying to break your model. Whenever the thing you are looking for is rare, even an excellent detector produces mostly false alarms, because there is so much *not-the-thing* for the detector to misclassify.

And what happens, in practice, when you build a system like this and deploy it? The clinicians, or the analysts, or the on-call engineers — they get a flood of alarms. They check the first few. They are false. They check the next few. False again. Within a week or two they learn, in their bones, that the alarms do not mean anything. They start ignoring them. They miss the real one when it comes. Not because they are careless. Because the system's signal-to-noise ratio is too low, by a structural mathematical fact that nobody warned them about.

So this is where we have to start, before any of the rest of this stuff makes sense. Probability is not just numbers attached to events. Probability is what you should believe given everything you know — and *everything you know* always includes the prior, the base rate, the thing that was true before you ran the test. If you forget the prior, the math will lie to you politely, with three significant figures.

---

## From Boolean to probabilistic — and why the move is harder than it looks

Most of you came up through a Boolean education. A bit is one or zero. A program either runs or doesn't. A test passes or fails. A theorem is proved or it isn't. Even when you allow uncertainty in — error bars, tolerances, noise floors — it tends to come in as a small wobble around an essentially yes-or-no answer.

Probability is a different animal. The output is not "true" or "false." The output is a degree of belief. Two engineers can look at the same evidence, end up at different probabilities, and both be reasoning correctly — because they walked in with different priors. There is no fact of the matter, separate from your information, that says exactly how confident you should be. Your confidence is downstream of what you believed before plus what you have now seen.

This bothers people, and it should bother you a little, because it is a real shift. The Boolean instinct works fine in deterministic systems. It misleads you, sometimes catastrophically, in any system where you are working from a model rather than from the world itself. And every machine learning system, every AI deployment, every statistical inference is a model rather than the world. So the move you have to make — and you will make it over and over for the rest of this book — is this: every model output is a probabilistic claim, and every decision based on a model output is a decision under uncertainty. The triage score is not a number. It is a probabilistic claim about what kind of patient is in front of you. The agent's "task complete" message is not a fact. It is the agent's probabilistic claim about its own behavior, conditional on the agent's model of itself, which is also fallible.

When you make this move, a lot of the rest of the book gets easier.

---

## Bayes' theorem — the bookkeeping for what we already did

The calculation we did with the disease has a name. The formal version, written out, is called Bayes' theorem:

$$P(\text{disease} \mid \text{positive}) = \frac{P(\text{positive} \mid \text{disease}) \times P(\text{disease})}{P(\text{positive})}$$

<!-- FIGURE: Three-column table — Term | Plain English Name | Value in the Disease Example. Rows: P(positive | disease) = sensitivity = 0.99; P(disease) = base rate / prior = 0.0001; P(positive) = total positive rate ≈ 0.0101; P(disease | positive) = posterior ≈ 0.0099. Students should see that the prior is an equal ingredient, not an afterthought. Caption: Figure 2.2 — The prior is not a footnote. It sits in the numerator with the same weight as the test's sensitivity. -->

I am not going to make a fuss over this. It is just the bookkeeping for what we already did. The vertical bar means "given." On the left, what we want — probability of disease, given that the test came back positive. On the right, the ingredients — how often the test is right when the disease is there, how often the disease is there in the first place, and how often the test comes back positive at all (which we got from adding up the true and false positives). Plug in our numbers: 0.99 times 0.0001, divided by about 0.01. You get a hair under one percent. Same answer.

The reason this little equation matters is that it tells you, exactly, where your intuition went off the rails. The posterior — what you believe after seeing the evidence — depends on the prior — what you believed before. Ignore the prior and you have thrown away half the equation. The most common error in interpreting any probabilistic output, in any field I have ever looked at, is forgetting the prior.

There is, by the way, a long-running argument about whether probability "really" means a long-run frequency or a degree of belief. I want to spare you that argument. They are different tools for different jobs. **Frequentist probability** treats probability as a long-run frequency — *if we ran this experiment many times, what fraction of the time would we observe this outcome?* This is natural for repeatable processes: quality control, A/B testing, hypothesis tests on i.i.d. samples. **Bayesian probability** treats probability as a degree of belief updated by evidence — *given my prior belief and the evidence I have just observed, what should my posterior belief be?* This is natural for one-off events and for any deployment where the question is "given this specific output, how should my belief about this specific case update?"

For AI deployment, you will use both. Use whichever fits the question. Don't get tribal about it.

---

## Hume's induction problem, re-translated as distribution shift

There is a problem from philosophy that you have to know about, because it shows up, in disguise, in every deployed system.

David Hume, in the eighteenth century, asked an awkward question. You have watched the sun come up every morning of your life. So has everyone you know. So has every record-keeper in human history. Does any of that *guarantee* the sun will come up tomorrow? Hume's answer, which infuriated people: no. Logically, the future is free to differ from the past. Induction works because the world cooperates, not because the laws of logic force it to.

Engineers translate this into our own language. The training set is the past. The deployment is the future. *Distribution shift* is the technical name for the world declining to cooperate.

Every machine learning deployment is a bet that the world you are going to operate in looks enough like the world you were trained on that the patterns you learned still apply. Sometimes the bet pays off and pays off for years. Sometimes the world quietly changes — the inputs drift, the base rates move, the relationship between cause and effect mutates — and the model keeps producing confident outputs that have come unstuck from reality. The model does not know. It cannot know. Its confidence is calculated under the assumption that the world is the world it learned, and that assumption is no longer true.

There is a documented case from the early pandemic. Models trained on pre-2020 medical imaging were deployed in 2021. The imaging signatures had been altered by COVID's effect on the lungs and on the population that ended up in scanners. The models produced confident predictions that no longer matched the underlying disease distribution. [Verify: DeGrave et al. 2021, *Nature Machine Intelligence* — confirm specific citation and scope before publication.]

<!-- FIGURE: Two overlapping distributions — training distribution (blue, labeled "World the model learned") and deployment distribution (orange, shifted right, labeled "World the model operates in"). The gap is labeled "distribution shift." A confidence curve sits atop both, unchanged — showing the model does not know it has drifted. Caption: Figure 2.3 — The model's confidence scores do not move when the world moves. -->

You will not, in general, be able to detect this kind of drift just by watching the model's outputs. That is the whole point. The model continues to look confident through the shift; the shape of its outputs stays similar; the dashboards stay green. Detection requires watching the inputs, watching the outputs, and — most of all — watching the actual outcomes the model was supposed to predict, when those outcomes eventually become observable. Most deployments do not budget for that. Most deployments find out about distribution shift through the harm.

Hume on the page, in our register: the model's track record tells you about the past. It does not guarantee the future. The honest question is *what would have to remain true about the deployment for the track record to keep being informative?* If you cannot answer that, you do not actually trust the track record. You just have not noticed yet.

You can see why we started Chapter 1 with supervisory capacities. Plausibility auditing, on any probabilistic system, requires asking *what is the base rate?* and *what might have changed since training?* before asking *what did the model say?*

---

## Calibration — the most operationally important diagnostic in this chapter

I want to spend some time on a specific, operational idea — the most useful diagnostic in this chapter. It is called *calibration*, and a model can be very accurate without being calibrated, and the difference matters.

A model is calibrated when its stated probabilities match what actually happens. If the model says "seventy percent confident" on a thousand cases, you would want roughly seven hundred of those cases to turn out positive. If only four hundred do, the model is overconfident — its stated number is bigger than it has any right to be. If nine hundred do, the model is underconfident, which is a less common failure but a real one.

You can see this in a picture. Put the model's stated probability on the horizontal axis. Put the actual frequency of positives among cases where the model stated that probability on the vertical axis. A perfectly calibrated model traces the diagonal — the line where stated and actual are equal. A miscalibrated model peels off the diagonal, and the *shape* of how it peels off tells you something about how the model is wrong.

<!-- FIGURE: Calibration curve — x-axis "Model's stated probability (0 to 1.0)," y-axis "Observed frequency of positive outcomes (0 to 1.0)." Diagonal reference line labeled "Perfect calibration." A second curve bowing above the diagonal at high confidence values, labeled "Typical deep learning overconfidence." Note: the model is close to calibrated in the 0.3–0.7 range, then peels off badly toward 0.9–1.0. Caption: Figure 2.4 — A calibration curve. The well-calibrated region in the middle is where the model's stated confidence is informative. The overconfident tail is where it lies. -->

Here is a pattern that turns up everywhere in modern deep learning. The model is reasonably calibrated in the middle of its range — when it says "sixty percent" it is about right. But at the extremes, especially toward the high end, it is badly overconfident. It says "ninety-nine percent" when it should be saying "eighty-five." The mathematics of how these models are trained — the use of softmax outputs and certain loss functions — actively rewards extreme confidence even when the underlying decision is not really that confident. [Verify: Guo et al. 2017, *On Calibration of Modern Neural Networks* — primary source for this finding.] You get models that have learned to *sound* sure of themselves regardless of whether they should be.

Here is the move I want you to internalize: *do not trust a model's stated probability without seeing its calibration curve on data drawn from the actual deployment distribution.* The number on the screen, by itself, is decorative. It might mean what you think it means; it might be off by a lot; you have no way of telling without checking. Calibration is what lets you read the number as a real number.

And calibration is not just a property of models. It is a property of forecasters in general — of *you*. If you sit down and write ninety-percent confidence intervals for a stack of forecasting questions, then later check how many of those intervals actually contained the truth, you will almost certainly discover that your intervals contained the answer about half the time, not nine times out of ten. Most people, including most very smart people, walk around significantly overconfident in their own beliefs, and they do not know it because nobody runs the experiment. The mathematics of probability lets you run the experiment on a model. The same mathematics lets you run it on yourself. The discomfort of the answer is the point.

We will return to this. The *calibration baseline* — a self-assessment exercise introduced later this chapter — is something you will complete three times over the course of the book. The expected pattern is that calibration improves over the semester, not because you learn more facts, but because you learn to take your own uncertainty more seriously. If your second baseline is the same as your first, the course is not landing on you.

### Glimmer 2.4 — Calibration curves you can trust and ones you cannot

The setup:

1. Take a publicly available pretrained classifier — a sentiment classifier, an image classifier, anything with a confidence output.
2. Find or construct a held-out dataset with ground truth labels.
3. Bin the model's predictions by stated confidence (0.0–0.1, 0.1–0.2, …, 0.9–1.0).
4. For each bin, compute the empirical accuracy.
5. Plot.
6. *Lock your prediction* before plotting: do you expect the curve to track the diagonal, sag below it (overconfidence), or rise above it (underconfidence)? Where on the x-axis?
7. Compare your prediction to what you actually see.
8. Now change the held-out set to one drawn from a slightly different distribution (different domain, different time period, different demographic skew). Re-plot. Predict where the calibration breaks.

The deliverable is the two plots and the gap analysis between your predictions and the curves. The first plot teaches calibration. The second teaches distribution shift. The gap between what you predicted and what you got is the learning event.

---

## When the Central Limit Theorem politely declines to help

There is one more thing I have to tell you about, because it is the place where your standard-issue probability training will betray you.

If you have taken a stats class, you have met the Central Limit Theorem. It says, more or less, that if you add up a lot of independent random variables with finite variance and normalize them, the result looks like a Gaussian — the bell curve. This is a beautiful theorem and it is the reason averages are useful, confidence intervals work, and quality control exists as a discipline.

The theorem has two requirements buried in it. *Independent.* And *finite variance.*

When either requirement fails, the theorem fails, and the failure is usually quiet. **Independence** breaks down in network-structured data, in time series with autocorrelation, in any system where one event's outcome shifts the probability of subsequent events. **Finite variance** breaks down in heavy-tailed distributions — distributions where extreme events are not rare enough for averaging to dampen them. The Cauchy distribution is the textbook example, but it is not just a freak. Power-law distributions are common in real systems. Wealth. File sizes. Network connection counts. Earthquake magnitudes. Training-loss spikes during model fitting. The cost of a deployed AI system being wrong about a single decision.

<!-- FIGURE: Two distributions overlaid — a Gaussian (thin tails, labeled "CLT applies") and a power-law distribution (fat tail extending right, labeled "Heavy-tailed — CLT does not apply"). A vertical line at some large x-value shows: the Gaussian probability is near zero; the power-law probability is still non-trivial. Annotation: "This is where your confidence intervals lie to you." Caption: Figure 2.5 — In the heavy-tailed regime, the next observation can move your average dramatically no matter how many observations you have already collected. -->

In a heavy-tailed regime, the sample mean does not settle down as you collect more data. You compute the average of a thousand observations and the next observation moves the average by a lot. Confidence intervals computed under Gaussian assumptions are nonsense here — but they look exactly like normal confidence intervals, so you have to know to be suspicious.

For deployment, the question that matters is: what is the distribution of the *cost* of being wrong? If most of your wrong outputs are minor inconveniences but one in a thousand kills a patient or wipes a database — your loss distribution is heavy-tailed, and a deployment evaluated on average loss is being evaluated on a quantity that does not converge in any useful sense. You need tail-aware metrics. You need worst-case analysis. You need to design adversarial inputs deliberately, so the rare catastrophes show up in testing instead of in production.

Which brings us back, by a winding road, to the triage system from the last chapter. That system was operating in a heavy-tailed loss world. Most of its low-acuity classifications were correct or close to correct. A few were catastrophic. The average loss across deployments was tiny. The catastrophic loss was the only one that mattered. The system had been validated on average-loss metrics, and the validation came back beautiful. The validation was also irrelevant to the question that should have been asked.

Hume on the page, one more time, in the right key: the past is informative about the past. The next observation can break your average. Plan accordingly.

### The other Glimmers in this chapter

Three more, briefly structured around prediction-lock — the prediction is the work; the gap is the learning event.

**Glimmer 2.1 — The convergence that always happens until it doesn't.** Run a Monte Carlo simulation of sample means from a Gaussian. Watch the convergence. Then run the same simulation from a Cauchy. Lock your prediction of where the Cauchy mean settles. Watch it not settle. Explain why.

**Glimmer 2.2 — The Cauchy break.** Take any analysis tool from your course's quantitative apparatus that assumes finite variance. Apply it to Cauchy-distributed data. Predict, before running, what kind of nonsense it will produce. Compare your prediction to the actual nonsense.

**Glimmer 2.3 — The 99% accurate test.** Take the disease example from this chapter. Vary the base rate from 1/100 to 1/100,000. Plot positive predictive value as a function of base rate. Predict, before plotting, where the curve goes through 50%. Identify, from a current AI deployment in your field, which base rate regime you are in.

---

## The calibration baseline

*This exercise returns in Chapters 8 and 14.*

You are given a set of forecasting questions across several domains — some technical, some general. For each question, provide a 90% confidence interval for the answer. After the truth is revealed, compute the fraction of your intervals that contained the true value.

If you are well-calibrated at 90%, that fraction should be 0.9. Most engineers, on first attempt, score between 0.4 and 0.6 — meaning their 90% intervals contained the answer about half the time. They were operating as though they were two to three times more sure of themselves than the evidence warranted.

This number is uncomfortable. The discomfort is the point. We will return to it in Chapter 8, after the robustness chapter has done its work, and in Chapter 14, at the end.

---

## Synthesis — four questions that prevent most deployment disasters

Most of the failures of confidence in deployed AI systems are not failures of model competence. The models are doing what their architectures and training data permit them to do. The failures are failures of *interpretation* — of humans reading model outputs through intuitions not built for the problem at hand. The intuition forgets the prior. The intuition trusts the stated number. The intuition averages over a heavy-tailed loss. The intuition assumes the deployment world is the training world.

The technical content of this chapter — Bayes, calibration, heavy tails — is the apparatus for repairing those intuitions. Not replacing them; you cannot run on equations alone in a noisy room. But the apparatus is what you reach for when the intuition starts to feel too confident.

Four questions, in order:

1. **What is the base rate?** Before the model saw this case, how likely was the positive class in this population?
2. **Is the model calibrated?** Does its stated confidence match empirical frequencies on data drawn from this deployment distribution?
3. **What is the cost distribution?** Is the loss of being wrong roughly uniform, or are there rare catastrophic outcomes that average-loss metrics will miss?
4. **What changed since training?** What would have to remain true about the deployment distribution for the training-era track record to remain informative?

These four questions, asked in the right order, would have prevented most of the deployment disasters I have watched unfold. We use all four on every system we analyze in the remaining chapters.

---

## What would change my mind

If a deployment-monitoring methodology emerged that reliably detected distribution shift in production from output statistics alone — without access to ground-truth outcomes — the calibration framing in this chapter would need revision. Current methods detect a subset of shifts; the general problem is open. [Verify: Krell et al. 2024 on out-of-distribution detection — confirm citation before publication.]

I do not have a clean diagnostic for when a deployment's loss distribution is heavy-tailed *before* the catastrophic event reveals it. Tail estimation from a sample is mathematically impolite. The honest answer is: assume heavy tails when the cost-of-error includes harm to people, and engineer accordingly. That is a heuristic, not a calculation, and I have not solved this.

---

## Connections forward

A calibration curve tells you how confident the model should be. It does not tell you what the model is confident *about* — what it has noticed, what it has missed, whose face it works on and whose it does not. The model's outputs are shaped by its training data, and the training data was shaped by people who made choices, often without noticing they were making them.

That is Chapter 3. We will need it before we can talk honestly about what a calibrated model is calibrated *for*. Chapter 8 returns to calibration in the context of adversarial robustness — can a model be calibrated on clean inputs and wildly miscalibrated on perturbed ones? Chapter 14 closes the loop with the calibration baseline.

---

## Exercises

### Warm-up

**1.** A spam filter flags incoming email as spam or not-spam. The filter is 98% accurate. Your email server receives 10,000 emails per day; on average, 200 of them are actually spam. How many false positives should you expect per day? What fraction of flagged emails are false positives? *(Tests: base-rate reasoning, Bayes arithmetic)*

**2.** A model reports 0.91 probability on a classification. You have no calibration data. What can you legitimately conclude from this number, and what can you not conclude? Write a two-sentence answer. *(Tests: understanding of what calibration is and isn't)*

**3.** Write out Bayes' theorem from memory, label each term in plain English, and identify which term is the prior. *(Tests: mechanical retention of the theorem structure)*

### Application

**4.** A fraud-detection model is 97% accurate and flags 0.5% of all transactions as fraudulent. Actual fraud affects 0.1% of transactions. Using Bayes' theorem, calculate: of all flagged transactions, what fraction are genuinely fraudulent? Show your work and interpret the result in one sentence. *(Tests: applying Bayes to a real-world deployment scenario)*

**5.** You generate ninety-percent confidence intervals for twenty forecasting questions — ten about sports outcomes, ten about economic figures. Six months later, you check. Fourteen of the twenty intervals contained the actual answer. Are you overconfident, underconfident, or well-calibrated? How would you use this result to improve future forecasts? *(Tests: calibration concept applied to human forecasting)*

**6.** A medical imaging model was trained on data from 2018–2019 and deployed in 2022. Performance metrics from 2018–2019 validation look excellent. Describe two specific mechanisms by which distribution shift could have degraded real-world performance without appearing in the model's stated confidence scores. *(Tests: distribution shift reasoning)*

**7.** A classification system for security alerts has an average false-positive rate of 2% and an average false-negative rate of 0.5%. The average cost of a false positive is $10 (analyst time). The cost of a false negative is $500,000 (breach). Explain why evaluating this system on average-loss metrics is misleading, and name the right framework to use instead. *(Tests: heavy-tailed loss reasoning)*

### Synthesis

**8.** You are asked to evaluate a new AI triage tool before deployment. The vendor presents an accuracy figure of 94% on a validation set. List the four questions from the synthesis section, and for each one, describe what information you would need from the vendor to answer it. *(Tests: integrating base rate, calibration, distribution shift, and loss-distribution thinking)*

**9.** A model's calibration curve shows that it is well-calibrated in the 0.4–0.6 confidence range but systematically overconfident above 0.85. Describe two deployment scenarios where this pattern is low-risk and two where it is dangerous. What does your answer reveal about why calibration alone is not sufficient for safe deployment? *(Tests: applying calibration insight to real design decisions)*

**10.** The chapter draws a structural analogy between Hume's problem of induction and machine learning deployment. Construct a parallel argument: take any ML system you are familiar with and describe (a) what "the past" consists of for that system, (b) what assumption about continuity is being made, and (c) one specific change in the world that would invalidate that assumption. *(Tests: transferring the induction framework to a new domain)*

### Challenge

**11.** Suppose you are building a content moderation system for a platform where 0.01% of posts are genuinely harmful. You have a budget for human review of 0.5% of all posts. Design a two-stage system — a fast classifier followed by human review — that maximizes the fraction of genuinely harmful content that gets reviewed, while staying within budget. State any assumptions you make, and identify the key trade-off your design encodes. *(Open-ended; tests base-rate reasoning, system design, and explicit trade-off articulation)*

**12.** This chapter argues that most AI deployment failures are failures of interpretation, not model competence. Find a documented real-world case of an AI deployment failure (not the pandemic imaging case from the chapter). Identify which of the four diagnostic questions — base rate, calibration, cost distribution, distribution shift — best explains the failure, and defend your choice. *(Research and synthesis; tests ability to apply framework to novel cases)*

---

## Chapter summary

You can now do four things you could not do before this chapter.

You can calculate what a positive test result actually means, given a population base rate — and explain why the answer is almost never what the test's accuracy number implies. You can read a calibration curve and identify whether a model's stated probabilities are trustworthy on data drawn from the deployment distribution. You can explain why average-loss metrics are the wrong tool for deployments where the cost of rare errors is catastrophic. And you can frame distribution shift as an instance of Hume's induction problem — a bet that the deployment world resembles the training world — and name what you would need to check to evaluate whether that bet is still paying off.

The four questions at the end of the synthesis section are not a framework to be memorized. They are the questions that were missing from the failure cases in Chapter 1. Carry them into every system you evaluate.

