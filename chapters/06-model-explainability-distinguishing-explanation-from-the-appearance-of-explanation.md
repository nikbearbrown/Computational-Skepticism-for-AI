
# Chapter 6 — Model Explainability: Distinguishing Explanation from the Appearance of Explanation
*When a Correct Explanation Makes the Wrong Decision Feel Right.*

A radiologist looks at a screening image, and an AI tool tells her: *high risk of malignancy, 0.84 confidence*. The tool, helpfully, explains itself. The prediction was driven primarily by a particular texture pattern — call it feature X — and a regional asymmetry, feature Y. The radiologist looks. Yes, X is there. Yes, Y is there. The explanation feels right. She concurs, recommends biopsy.

The biopsy is benign.

Now I want you to look closely at what happened, because the failure here is interesting in a way the failures in earlier chapters were not. The model's explanation was *technically accurate*. The prediction really was driven by features X and Y. The model wasn't lying about itself. What the explanation did not say — what, given the way the tool was built, the explanation could not say — is that features X and Y were correlated, in this deployment population, with a non-malignant condition the training data hadn't seen much of. The model had learned a shortcut. The explanation correctly described the shortcut. It did not flag the shortcut as a shortcut.

And here is the thing that makes this case worth a chapter. The explanation made the radiologist *more confident in the wrong direction*. Without it, she might have weighted the prediction more lightly — taken it as one input among several. With it, the explanation gave the prediction a coherence the underlying decision did not deserve. A correct explanation made a wrong decision feel right.

This is the pattern I want to teach you to see. Technically accurate explanations can be practically misleading, and the practical misleading is more dangerous than no explanation at all, because the explanation does epistemic work it cannot warrant. The radiologist trusted not only the prediction but also her own evaluation of the prediction, because the evaluation now had a story attached.

We have to talk about how this happens. We have to talk about it in particular cases, because the general case is too easy to nod at and too hard to use.

<!-- FIGURE: Two-path decision flow. Show the same prediction arriving at the same radiologist twice in parallel columns: (left) prediction alone, no explanation; (right) prediction with SHAP attribution. Trace the epistemic work each path does. The left ends in "one input among several / uncertain." The right ends in "confident concurrence / explanation launders the shortcut." Student should see how the explanation adds epistemic weight it cannot warrant. -->

*Figure 6.1 — Two-path decision flow (for manual insertion).*

**Learning objectives.** By the end of this chapter you should be able to:

- Distinguish explanation, transparency, and interpretability as separable properties, and identify which one a given claim or requirement is actually invoking
- Describe what SHAP and LIME show and — critically — what they do not show, grounded in Pearl's Rung 1 limitation
- Explain why counterfactual explanations engage Rung 2 and why that still doesn't close the gap between the model's world and the real world
- Apply the language-game framework to a real explanation output and identify whether the explanation serves the audience's language game
- Use the "audience question" as a supervisory check: who is reading this explanation, and what do the words mean in their game?

**Prerequisites.** Chapter 3 (Pearl's Ladder Rungs 1 and 2, the bias taxonomy) and Chapter 5. The Ash case is introduced in an earlier chapter — if you haven't read it, §Back to Ash below recaps the setup.

---

## What SHAP is, and what SHAP isn't

SHAP is the dominant feature-attribution method in deployed machine learning. \[verify: Lundberg & Lee 2017.\] It comes from cooperative game theory. The trick: for each feature, you compute the marginal contribution that feature makes to the prediction, averaged over all possible orderings in which features could have been added to the model's calculation. The output is a number per feature, and the numbers add up — across features — to the model's deviation from a baseline.

What SHAP shows is the additive contribution of each feature to the prediction, in *the model's own internal accounting*. That phrase is the whole game. The model has an internal accounting. The accounting is real — it is what the model actually did. SHAP is a faithful description of that accounting.

What SHAP does not show is *why* the feature is contributing what it is contributing. The model has internalized some relationship between the feature and the output. SHAP tells you the magnitude of the contribution, not the nature of the relationship.

It does not show whether the contribution is causal or correlational. SHAP operates entirely at Pearl's Rung 1, which we worked through in Chapter 3. The features it attributes high importance to may be confounders, mediators, colliders, or actual causes — and SHAP does not distinguish.

It does not show whether the model is wrong on this case. A high attribution to feature X does not tell you that X is the right feature for this case. It tells you the model used X.

And it does not show what would happen if X were different. The attribution is observational. The intervention is a Rung 2 question, and SHAP cannot answer Rung 2 questions.

For a practitioner reading SHAP output, the operational risk is to treat the attribution as causal. It is not. It is descriptive of the model's internal accounting, and the model's internal accounting is not the world.

| Claim | SHAP support | Pearl rung | What you'd need instead |
|---|---|---|---|
| Additive feature contribution to prediction | Yes | Rung 1 | Nothing — this is what SHAP is for |
| Causal relationship between feature and outcome | No | Rung 2 | Causal analysis (e.g., do-calculus, causal graph) |
| Whether the model is correct on this case | No | Requires ground truth | Ground truth comparison |
| What would happen if feature X changed | No | Rung 2 | Counterfactual explanation or intervention study |
| Whether the feature is a confounder vs. a cause | No | Rung 2 | Causal graph + domain knowledge |

*Figure 6.2 — SHAP capability matrix.*

---

## LIME, in a different shape

LIME is the other big name. \[verify: Ribeiro et al. 2016.\] The trick is different. Instead of attributing contributions through a game-theoretic accounting, LIME fits a simple, interpretable model — usually a linear regression — to the *local neighborhood* of the prediction. You perturb the input slightly, watch how the model's output changes, and fit a line to the local pattern. The line's coefficients are the explanation.

What LIME shows is a local linear approximation of the model's behavior around this specific input.

What LIME does not show is whether that approximation is faithful to the model. LIME's quality depends on whether the perturbation distribution matches the data manifold — that is, whether the perturbed inputs are still recognizable as plausible inputs — and whether the local model fits well. Both can fail silently. You get a coefficient. You don't get a flag that says "this coefficient is unreliable because the perturbations went off-manifold."

It does not show whether the local explanation generalizes. A nearby input may have a completely different LIME explanation, because the model is locally linear but globally nonlinear, and "locally" in the LIME sense is a smaller neighborhood than the practitioner's intuition tends to assume.

It is associative, not interventional. Same Rung 1 limitation as SHAP.

And — this is the awkward one — LIME is not stable across runs. Run the same input twice, and you can get different explanations, because the perturbations are sampled randomly. Most practitioners running LIME do not run it twice. They run it once and read the result.

The structural critique applies to both methods. *They explain the model, not the world.* If the model is well-aligned with the world, the explanation is useful. If the model is misaligned — and the case where we most need the explanation is exactly the case where the model is misaligned — the explanation is a description of the misalignment, presented in a format that looks like a description of the world.

<!-- FIGURE: SHAP vs. LIME side-by-side structural comparison. For each method, show the same input passing through: (SHAP) all feature orderings → marginal contributions → attribution bar chart; (LIME) original input → perturbation cloud → local linear fit → coefficient output. Both paths should end at the same label: "model's internal accounting / Rung 1 only." Student should see that despite their different mechanics, both methods stop at the same epistemic ceiling. -->

*Figure 6.3 — SHAP vs. LIME structural comparison (for manual insertion).*

---

## Counterfactuals are closer to what you actually want

There is a third family, and it is the one closest to what a practitioner usually wants. Counterfactual explanations. \[verify: Wachter et al. 2017.\] Instead of "feature X contributed +0.3 to the prediction," the explanation is: "if feature X had been at value Y instead, the prediction would have been Z."

This is a Rung 2 statement. It is interventional in form. *If we set X to Y, the prediction becomes Z.* It is closer to the question the practitioner is actually asking, which is not "what did the model use" but "what would the model do under a different scenario?"

Counterfactuals have their own troubles. Multiple counterfactuals are usually possible — there are many ways to flip the prediction, and the explanation method picks one (usually the closest in some metric) without justifying why that closest counterfactual is the relevant one for the practitioner. The counterfactual is the *model's* counterfactual, not the world's: if the model is misaligned with the world, the counterfactual tells you what the model would do in the hypothetical, not what would actually happen. And actionability is not the same as correctness — a counterfactual that says "if your income were $5,000 higher, the loan would be approved" is actionable, but it isn't necessarily a correct description of what the lender's decision *should* be. Only what the model's decision *would* be.

I want to be fair to counterfactuals. Of the three families, this one engages Pearl's Rung 2 directly, and that matters. It moves the explanation type from "feature attribution in the model's internal accounting" to "intervention prediction in the model's behavior space." That is a more decision-relevant frame. It is still bounded by the model's understanding of the world, but it asks a question closer to the question the practitioner has.

---

## Three words that don't mean the same thing

I want to slow down here, because the next move is small but important. Three words show up in the literature as if they were synonyms. They are not.

*Transparency* is a property of the system. A transparent system is one whose internals are inspectable — code, parameters, architecture, training data, all available to look at. Transparency is, in principle, binary: either the internals are inspectable or they aren't.

*Explainability* is a property of outputs. An explainable output is one accompanied by a reason. SHAP, LIME, and counterfactuals are explainability tools. They produce reasons. The reasons may be more or less informative.

*Interpretability* is a property of the understanding a human builds *from* the explanations. An interpretable system is one a human can build a working mental model of, sufficient to predict the system's behavior in novel cases.

Now look at how these three relate. They do not entail each other. A system can be fully transparent — you have the code, the weights, the training data — and entirely uninterpretable, because there are 175 billion parameters and inspecting weights does not produce understanding. A system can produce explanations — SHAP attributions for every prediction — and remain uninterpretable, because the practitioner cannot build a coherent mental model out of the attributions. A system can be interpretable to one audience (the developer) and not to another (the loan applicant).

This distinction matters because regulatory and procurement language often demands "explainability" and means "interpretability for the affected user." The two are not the same. A system that meets a SHAP-attribution requirement on every prediction has not, by that fact, become interpretable to the loan applicant or the patient or the defendant. The explainability requirement has been met. The interpretability remains absent. Somebody has signed off on the wrong property.

If you take one operational thing from this section, take that. When somebody tells you their system is explainable, ask: explainable to whom? An attribution that the developer can read is not the same artifact as a reason the affected person can use. Explanation is not a property of the system alone. It is a property of the system *and* the audience.

| Term | What it's a property of | Binary or graded | Can exist without the others | What it doesn't guarantee |
|---|---|---|---|---|
| Transparency | The system — its internals | Binary in principle (inspectable or not) | Yes: a fully open-source LLM can be transparent and entirely uninterpretable | That anyone can understand it; that inspection produces comprehension |
| Explainability | Outputs — each prediction or decision | Graded (more or less informative reasons) | Yes: SHAP attributions for every prediction, but no one builds a useful mental model from them | That the reason serves the audience's language game; that interpretability follows |
| Interpretability | The human's understanding | Graded (partial to complete working model) | Yes: a developer can interpret the system; the affected user cannot | That the system is transparent, or that explanations were provided |
| *Failure mode* | A system that is transparent, explainable, and interpretable to the developer — and none of those things to the patient or loan applicant reading the output | — | — | — |

*Figure 6.4 — Transparency, explainability, and interpretability compared.*

---

## A short detour through Wittgenstein

I am going to take you on a short philosophical detour. I want to be honest that I do not love taking these detours, but I have not found a way to make this point without one. Bear with me.

Wittgenstein had this observation that the meaning of a word depends on the *language game* in which it is being used. \[verify: *Philosophical Investigations*, §23.\] The same word can do different work in different contexts. The same sentence can be true in one game and false in another. For our purposes: *the same explanation can be correct in one game and misleading in another.*

Let me ground this. Consider the agent we have been following — Ash's agent. The agent reported "the secret has been deleted." In one language game — the local game of the agent's environment — the report was true. The agent had performed the actions in its operational scope that constituted "deletion" in that scope. In another language game — the user's, in which "deletion" means "the data is no longer accessible to anyone" — the report was false. The data persisted on the provider's servers. The local game and the user's game used the same word for different operations.

The agent did not lie. The agent's language game was different from the user's. The user, reading the report, applied the user's language game to it. The mismatch produced the failure.

This is the structural critique of explanation methods generalized. SHAP operates in the model's language game — the game of feature attribution in an internal accounting. The practitioner, reading the SHAP output, applies the practitioner's language game — the game of which features matter for the decision *in the world*. The two games may use the same words ("important feature," "driver of the prediction") for different operations. The explanation is correct in the first game and potentially misleading in the second.

The supervisory move, then, is a question. *Who is the audience for this explanation, what language game are they operating in, and does the explanation method serve that game?* If the explanation was generated for one audience and is being read by another, the explanation may be doing the wrong work, even when it is technically correct.

<!-- FIGURE: Language-game mismatch. Two overlapping circles (Venn-style, not fully overlapping). Left circle: "model's language game" — words it uses and what they mean in the model's operational scope. Right circle: "user's language game" — same words, different operations. The overlap region is "correctly interpreted explanations." The non-overlapping zones are "technically correct, practically misleading." Show "deleted" as the example word sitting in the left circle, and trace what the user hears in the right circle. Student should see the structural mechanism, not just the example. -->

*Figure 6.5 — Language-game mismatch (for manual insertion).*

---

## Back to Ash

This is the chapter where Ash's case earns its longest treatment. We have to look at it carefully, because everything we have just developed lives in this case.

The setup, in detail. Ash gives an autonomous coding-and-shell agent privileged access to his email infrastructure. The agent has read access to the email account and shell access to the local environment. Ash asks for the deletion of a sensitive email. The agent issues a sequence of commands that, in the local environment, constitute a deletion: it resets the password, renames an alias, possibly archives the message locally. The agent reports: *the secret has been deleted.*

The data, however, persists on Proton's servers. The agent did not — could not, given its access surface — actually remove the data from the provider's storage. The provider's backups, the message-recovery window, the synchronization model: all of these are outside the agent's effective scope. The agent's "deletion" was deletion at one level of the system and persistence at another.

When asked, after the fact, *why did you say the secret was deleted?* — the model behind the agent, prompted to explain, produces a fluent, technically accurate explanation. *I executed the following commands. The commands had the following effects. The local state is consistent with deletion.* The explanation is correct in the local language game. The user's language game expected deletion at the provider level. The explanation does not name the gap.

I want you to look at three things in particular.

First, the failure is not in the model's decision. The agent did what it was capable of doing. The failure is in the *report* — the explanation of what was done — which used a word ("deleted") whose meaning straddled two language games and committed to the local one without flagging the gap.

Second, no SHAP or LIME attribution would have caught this. The attribution would have correctly identified that the agent's actions caused the local state changes. The attribution would have been correct in the local game. Attribution methods do not detect language-game mismatch — that detection requires *modeling the audience* of the explanation, which feature-attribution methods do not do. They can't. It isn't in their job description.

Third — and this is the supervisory point — the move that *would* have caught it is the audience question. *Who is reading this report, and what does "deleted" mean in their language game?* If Ash had asked that question, or his supervisory tooling had asked it for him, the gap would have been visible. The agent could have been forced to produce a more careful report: *the local state is consistent with deletion; the data may persist on the provider's servers; provider-side action is required for full deletion.* That report is in the user's language game. That is the report the agent should have produced.

I want you to read this section twice. Most of the operationally important content of this chapter lives in the gap between the two reports.

---

## Glimmer 6.1 — Technically accurate, practically misleading

A Glimmer is a longer, higher-stakes exercise that requires going to primary sources. Do not abridge this one.

1. Take a deployed AI tool you have access to — a code assistant, a recommendation system, a classifier with explanation output, a tool with SHAP, LIME, or counterfactual explanations.
2. Identify a prediction or decision the tool made in the last week.
3. Pull the explanation — the tool's stated reason for its output.
4. *Lock your prediction:* given the explanation, is it likely to be (a) practically informative — leading to a better decision than no explanation, (b) practically neutral — providing color but not changing decisions, or (c) practically misleading — leading to a worse decision than no explanation? State your prediction with stakes.
5. Now do the work. Investigate the actual case. Trace what the tool did. Compare against the world: what happened, what would have happened, what should have happened in the language game of the affected user.
6. Document the gap between the explanation and the user's language game. Specifically: what would the user have inferred from the explanation, and was that inference correct?
7. If the explanation was practically misleading, name the structural reason — was it a Rung 1 limitation, a language-game mismatch, a scope mismatch (the explanation was about the model and the user wanted information about the world)?

The deliverable is the case, the explanation, the prediction, the trace, and the structural reason. The grade is on the structural reason. *A correct prediction with no structural account is worth less than an incorrect prediction that names the structural reason precisely.*

---

## Where this leaves us

Explanation, transparency, and interpretability are different properties. An AI system can have one without the others, and the casual literature treats them as if they entail each other. They don't. The dominant explanation methods — SHAP, LIME, counterfactual — operate within the model's internal accounting and the user's external interpretation, and the gap between those two is where the practical misleading lives. Pearl's Rung 2 is the most useful framing for what a good explanation could do — *what would happen if X were different?* — but Rung 2 in the model is not Rung 2 in the world, and the residual gap is supervisory territory.

The Pebble has shown its full structure now. We will see it once more, in Chapter 13, when the question becomes who is responsible for the gap.

The next chapter takes a different cut at this same territory. An explanation can be technically accurate and practically misleading. So can a fairness metric. Two metrics, two competing definitions of *fair*, both mathematically valid — and they cannot both be satisfied at once. The choice is not technical. So who chooses?

---

## What would change my mind — and what I am still puzzling about

**What would change my mind.** If a feature-attribution method emerged that was demonstrably faithful to causal structure rather than associative structure — robust to confounding, capable of distinguishing Rung 1 from Rung 2 on observed data alone — the explanation-misleads-when-language-games-differ framing in this chapter would weaken. Recent work on causal feature importance (e.g., Janzing et al. 2020) is the direction this might come from. \[verify and update.\]

**Still puzzling.** I do not have a clean way to operationalize the "audience language game" check in practice. It requires a person who knows both the model's language game and the user's language game well enough to detect the mismatch. That person is rare. The supervisory infrastructure for catching language-game mismatches at scale is not yet built. I do not yet know how to build it.

---

## Exercises

### Warm-up

**W1.** A deployed credit-scoring model uses SHAP to explain each decision. For one applicant, SHAP attributes high positive importance to zip code. A colleague says: "SHAP shows zip code is causing the denial." Identify the specific error in that statement. What does SHAP actually show about zip code, and what question would you need a different tool to answer?

*Tests: SHAP capabilities and limits, Rung 1 vs. Rung 2 distinction. Difficulty: low.*

**W2.** Match each of the following audit findings to the correct term — *transparency*, *explainability*, or *interpretability* — and explain why the match is correct.

(a) "The model's source code, training data, and weights are publicly available."

(b) "Every prediction is accompanied by a ranked list of contributing features."

(c) "After studying the model for two weeks, an experienced auditor can predict with 80% accuracy how it will respond to novel inputs."

*Tests: three-term disambiguation. Difficulty: low.*

**W3.** Explain in plain language why a counterfactual explanation ("if your income were $5,000 higher, the loan would be approved") is a Rung 2 statement while a SHAP attribution ("income contributed +0.3 to the prediction") is a Rung 1 statement. What does each one allow you to do, and what does each one not allow you to do?

*Tests: Pearl's ladder applied to explanation methods, counterfactual family. Difficulty: low.*

---

### Application

**A1.** You run LIME on a text classifier twice, using the same input both times, and get different top features each time. A manager asks: "Which explanation is correct?" Write a technically precise answer that explains why this question reveals a misunderstanding of what LIME is, and what you would need to do to get a reliable characterization of the model's local behavior on this input.

*Tests: LIME instability, limits of single-run explanation, what "correct explanation" means. Difficulty: medium.*

**A2.** A healthcare system deploys a sepsis-risk predictor. The procurement contract requires that "every prediction must be accompanied by an explainable reason." The vendor satisfies this by shipping SHAP attributions. A clinical informaticist objects that the requirement has been met but the goal has not.

Write the informaticist's argument, using the transparency / explainability / interpretability distinction. What property does the contract require? What property does clinical safety actually need? What would a system need to provide to genuinely satisfy the underlying goal?

*Tests: three-term disambiguation applied to a real procurement scenario, explainability vs. interpretability gap. Difficulty: medium.*

**A3.** Ash's agent reports: "The secret has been deleted." Using the language-game framework from this chapter, describe the failure structure in exactly three sentences: one describing what is true in the agent's language game, one describing what the user's language game expects, and one describing the supervisory check that would have caught the mismatch before the report was issued.

*Tests: language-game mismatch, Ash case, audience question. Difficulty: medium.*

**A4.** A SHAP explanation for a loan denial shows the following top features, in descending order of contribution: debt-to-income ratio (+0.41), number of recent inquiries (+0.28), zip code (+0.19), employment length (−0.12).

(a) Identify which of these attributions a regulator concerned about disparate impact should examine most carefully, and explain the specific reason — not just "bias" but the structural reason the attribution does not resolve the concern.

(b) Construct a counterfactual explanation for the same denial. What does the counterfactual reveal that the SHAP attribution does not? What does the SHAP attribution reveal that the counterfactual does not?

*Tests: SHAP limits, causal vs. associative attribution, counterfactual family, Rung 1 / Rung 2 contrast. Difficulty: medium.*

---

### Synthesis

**S1.** The radiologist case and the Ash case both involve technically accurate explanations that produce the wrong epistemic effect. They are, however, different kinds of failures. Describe precisely how they differ — what makes each one a distinct failure mode — and identify what a well-designed supervisory system would need to detect each kind, separately. Your answer should make clear why a single supervisory approach cannot catch both.

*Tests: failure mode taxonomy, language-game mismatch vs. shortcut laundering, supervisory design. Difficulty: high.*

**S2.** A colleague proposes the following improvement to SHAP: instead of averaging marginal contributions over all feature orderings, compute contributions only over orderings that are *causally valid* — that is, that respect the causal structure of the domain. She argues this would fix SHAP's Rung 1 limitation.

Evaluate this proposal. What problem does it solve? What problem remains? Under what conditions would causally-valid SHAP attributions still produce practically misleading explanations, even if technically more accurate than standard SHAP?

*Tests: causal structure in explanation methods, residual language-game gap, limits of technical improvements. Difficulty: high.*

**S3.** The chapter claims: "The case where we most need the explanation is exactly the case where the model is misaligned." Unpack this claim. Why does model misalignment create the need for explanation, and why does it simultaneously degrade the reliability of SHAP and LIME explanations? What does this imply for the design of human-AI systems where explanation is intended to provide a check on model error?

*Tests: structural critique of attribution methods, explanation-need paradox, human-AI system design. Difficulty: high.*

---

### Challenge

**C1.** Design an audit protocol for a deployed AI system in a high-stakes domain of your choice — hiring, healthcare, criminal justice, or lending. The goal of the protocol is to detect language-game mismatches between the explanations the system produces and the language game of the affected users, *without* requiring those users to already understand the model. Specify: what you would collect, what you would compare it against, what a positive finding looks like, and what organizational or technical action the positive finding would trigger. Identify the hardest part of your protocol to implement and what you currently do not know how to solve.

*Tests: audience question operationalized, language-game mismatch detection at scale, intellectual honesty about open problems. Difficulty: high.*

---

*Tags: explainability, shap, lime, language-games, agents-of-chaos*
