# Chapter 11 — Visualization Under Validation: Honest, Misleading, and the Choices Between
*The dashboard is an argument. The design choices are yours.*

I want to tell you about two dashboards.

A team has finished a validation analysis on a deployed recommendation system. The findings are mixed. The system performs well on the typical user. It performs poorly on three specific subgroups. And the high-confidence outputs — the cases where the system is most certain — turn out, when you look at the calibration curve, to be overconfident at the top of the probability range. A complicated picture. A picture that needs to be communicated to a deployment partner who is non-technical, time-pressured, and going to make decisions based on what they understand.

Two team members each build a dashboard. They use the same CSV. The numbers are identical, byte for byte.

The first dashboard opens with a single, large, bold metric — *94% accuracy*. Underneath, smaller charts show the subgroup performance, but on truncated y-axes that visually compress the disparity. The calibration curve is over in a sub-tab labeled "advanced metrics," where most readers will not click. The color scheme is green for "good" and gray for "needs attention" — the disparity charts are gray.

The second dashboard opens with a panel showing performance on the overall user base alongside the subgroup performance, on consistent axes, with the disparity visible at first read. The calibration curve sits in the main view, with a banner noting that calibration is overconfident at high probabilities. The color scheme is uniform; the design does not redirect attention.

Same data. Same CSV. The first dashboard is misleading. The second is honest. And — here is the part you have to feel in your bones to understand the rest of this chapter — the deployment partner's response to the two dashboards is *predictably different*. They will leave the first dashboard reassured. They will leave the second dashboard with questions. The questions are appropriate. The reassurance is not.

I want to spend this chapter on what is happening between those two dashboards, because I think most engineers do not realize they are making this choice every time they build one.

---

There is a famous claim from Marshall McLuhan that gets quoted constantly and understood rarely: *the medium is the message*. McLuhan's idea, in working form, was that the *form* of a communication shapes its meaning before any specific content arrives. A spoken story is not a written story is not a televised story, and the differences are not because the words have changed but because the medium structures the encounter. The reader of a written story holds it at a different distance, paces it differently, returns to passages, sets it down. The viewer of a televised story does none of those things.

A dashboard is a medium. Its structure shapes what the reader takes from the data before the reader reads any specific number.

A dashboard that opens with a single bold headline metric communicates, by its structure, that the headline metric is the answer. The reader scans it, decides whether the answer is acceptable, and may not reach the supporting detail. The dashboard is *implicitly arguing*, by its form, that the headline is sufficient. The argument is made before the data is read.

A dashboard that opens with a panel of equally weighted views communicates, by its structure, that the picture is composite, that no single number suffices, that the reader is being asked to integrate. The reader's pace slows. They may take the time to read the calibration curve. The dashboard *implicitly argues*, again by form, that integration is required.

These are different arguments. The data is identical. The choice between forms is a choice about which argument to make.

This is the most important conceptual move in the chapter, and I want to put it as plainly as I can. *Visualization is not transparent communication of facts.* It is an argument, made through structural choices, about what the facts mean and how the reader should weight them. Engineers, in my experience, undercredit this. Engineers also produce many of the most misleading dashboards I see in deployment — often without intending to mislead, often building exactly the kind of dashboard their training would predict — because they do not realize that their structural choices are arguments.

Let me try to make the catalog of choices visible, because once you see them named you will not unsee them.

---

The most familiar move is the *truncated axis*. A bar chart with the y-axis starting at 80 instead of 0 makes a small difference look big. There are perfectly legitimate uses of this — when the relevant range really is narrow, when zero is not meaningful for the quantity being displayed, when the comparison the reader needs to make is fine-grained. There are also illegitimate uses, where the visual amplification implies a larger effect than the data supports. The chart looks the same in both cases. The choice is the difference.

There is *inconsistent axes across panels*. You put two charts side by side. They look comparable. They are not — because their y-axis ranges are different, and the reader's visual comparison is invalid. This is one of the most common mistakes I see in honest dashboards, by which I mean dashboards built by engineers who would never deliberately deceive but who did not stop to ask whether the visual comparison their layout was inviting was a valid one.

There is *aggregation that hides distribution*. A single number — a mean, a median — that obscures a heavy-tailed or multimodal distribution. *"Median performance is good"* communicates the median and hides the tail. If the tail contains the catastrophic cases, hiding it is not summarization. It is concealment. The aggregation is doing rhetorical work.

There is *color asymmetry*. Categorical data displayed with colors of unequal salience — bright red for one category, muted gray for another — guides the eye to where the designer wants attention, away from where the designer does not. The data has not changed. The salience has. The reader's gaze, and therefore the reader's attention, has been directed.

There is *cherry-picked time windows*. A trend chart over a window that begins at a local minimum and ends at a local maximum exaggerates the trend. The same data over a longer window may show a different story, or no story at all.

There is *scale trickery*. Linear axes for data that is naturally logarithmic; log axes for data that is naturally linear. Each compresses or expands different regions of the data. The choice can serve clarity or it can serve the opposite.

There is *chartjunk and 3D effects*. Decorative elements that distort the perception of magnitude. 3D bar charts whose foreshortening makes near bars look bigger than far ones; pie charts with exploded slices that change the apparent area; backgrounds that interfere with reading the data. These are often introduced for aesthetic reasons; their effect is often to obscure.

There is *missing baselines*. A chart showing a metric without showing the comparison metric — accuracy without random-baseline accuracy, performance without prior-system performance, a finding without the null. The reader cannot tell if the finding is large or small. Without the baseline, every finding looks like a finding.

There is *labels that prejudge*. Axis labels, legend entries, and headlines that frame the interpretation before the reader sees the data. *"Accuracy boost from new model"* on a chart comparing two models is a label that prejudges. *"Accuracy: model A vs model B"* is a label that does not.

And there is *selective uncertainty visualization*. Showing confidence intervals where they support the argument and omitting them where they would weaken it. This one is common, hard to detect, and structurally the most dishonest of the bunch — because the data on uncertainty exists, and the choice to display it in some places and not in others is precisely the rhetorical move the engineer is making while telling themselves they are merely presenting "the data."

<!-- → [TABLE: Catalog of nine misleading visualization choices — columns: Move | Honest use | Dishonest use | How to tell the difference. Rows: Truncated axis, Inconsistent axes across panels, Aggregation hiding distribution, Color asymmetry, Cherry-picked time windows, Scale trickery, Chartjunk/3D effects, Missing baseline, Selective uncertainty visualization. Students should be able to use this as a checklist when reviewing their own dashboards.] -->

*Figure 11.1*

| | **Property** | **Value** |
|---|---|---|
| **Truncated axis** | _fill in_ | _fill in_ |
| **Inconsistent axes across panels** | _fill in_ | _fill in_ |
| **Aggregation hiding distribution** | _fill in_ | _fill in_ |
| **Color asymmetry** | _fill in_ | _fill in_ |
| **Cherry-picked time windows** | _fill in_ | _fill in_ |
| **Scale trickery** | _fill in_ | _fill in_ |
| **Chartjunk/3D effects** | _fill in_ | _fill in_ |
| **Missing baseline** | _fill in_ | _fill in_ |
| **Selective uncertainty visualization. Students should be able to use this as a checklist when reviewing their own dashboards.** | _fill in_ | _fill in_ |

: {.comparison-table}


This is not a complete list. Tufte and Cairo each have longer ones, and I recommend reading them. The pattern across all the choices is consistent. Each move is a *choice* that shifts the reader's interpretation. The engineer's job is to know which choices are being made and why.

---

I want to spend a moment on uncertainty, because this is where engineers most often skip the work.

The numerical apparatus for uncertainty is well-developed. Confidence intervals, posterior distributions, prediction intervals — the math is there, the libraries are there, you can compute them in a line of code. The visual apparatus for uncertainty is less developed and more often skipped. People who are punctilious about computing the right interval will display it as a small error bar dwarfed by a bold central estimate, communicating, by the visual hierarchy, *the central estimate is the answer; the bar is decorative*.

Why this matters: a finding without uncertainty is an unfalsifiable claim. A finding with badly-visualized uncertainty is one the reader will misread. And here is a sobering finding from the research literature: most readers do not interpret confidence intervals correctly even when they are shown clearly. Showing them badly compounds an already-difficult communication problem.

A few things help. First, where you can, show *more than one* representation of uncertainty — confidence intervals plus a fan chart, for instance, or an error bar plus a hypothetical-outcome plot that shows what individual draws from the posterior look like. A single representation favors one reading. Showing two together can correct biases that either alone introduces.

Second — and this is the practical heart of it — make the uncertainty *visually equal* in weight to the central estimate. A small error bar on a big bold number says one thing. A central estimate displayed at the same visual weight as the uncertainty range says another. The first communicates "this is the answer, with a quibble." The second communicates "the uncertainty is part of the finding."

<!-- → [IMAGE: Side-by-side comparison of two uncertainty visualizations for the same data. Left: large bold central estimate with a thin error bar — headline reads "94.3% accuracy." Right: a range chart showing the full confidence interval at equal visual weight to the point estimate — headline reads "91–97% accuracy (95% CI)." Student should see how the visual hierarchy in the left version demotes the uncertainty to decoration.] -->

![Figure 11.2 — Side-by-side comparison of two uncertainty visualizations for the same data. Left: large bold central estimate with a thin error bar](images/11-visualization-under-validation-honest-misleading-and-the-choices-between-fig-02.jpg)


Third, avoid false precision. A reported metric of 0.847291 with a confidence interval of plus or minus 0.05 is not a 0.847291 metric. It is a 0.85 metric. Round the central estimate to a precision the uncertainty actually supports. Reporting more digits than the data justifies is itself a small dishonest move, the way a witness who claims to have seen something at exactly 7:23:14 PM is making a claim of precision the human eye cannot actually deliver.

Fourth, and most practical of all — *test the visualization on a non-author reader*. Show the chart to somebody who did not produce it. Ask them what they think it says. If their interpretation does not match the data, the visualization is not yet doing its job, no matter how much you like the design. This step is almost always skipped, because it is uncomfortable. It is also the single most useful thing on this list.

---

I want to mention one more device, which is more institutional than visual but operates in the same register.

Validation analyses are usually presented as if they were finished. The dashboard, the deck, the report — they look polished, single-version, authoritative. This finished-looking form is itself a structural argument: *the analysis is complete; what you are reading is the answer*. Most validation analyses are not, in fact, complete. They are the current state of an ongoing process. The argument made by the polished form misrepresents the analysis it presents.

A more honest form is what I will call a *living deck* — a presentation that exposes its own version history. Each version dated. Each version with a changelog slide documenting what changed since the prior version and why. Old slides that have been superseded are retained, in a "previously" section, with a note about what they used to claim and what changed.

The structural argument the living deck makes, by form, is: *this analysis is provisional, the version you are reading is the latest state of an ongoing process, and the changes are themselves part of the work*.

This argument is not always welcome. Some adoption committees and procurement processes prefer the polished single artifact, the one that hides the work. They are arguably wrong about what they prefer, because the polished artifact looks finished and is therefore harder to revise as new evidence comes in. The living deck, by exposing its own changelog, makes revision easier and more honest. The medium of provisional analysis is provisional itself. A finished-looking artifact misrepresents the analysis.

This is a small operational point with a large structural implication, and I want you to carry it forward: when the work is provisional, the form should say so.

---

Now I want to make a recommendation that some students find uncomfortable, and I want to make it anyway.

Take a finding from your work — a real result you have produced and would communicate to a deployment partner. Build the *honest version* — a dashboard or static visualization that communicates the finding accurately, including uncertainty, including subgroup variation, including the limits of the analysis.

Then, using only the same data — no fabricated numbers, nothing invented — build the *misleading version*. Make the finding look better than it is, or worse than it is, or different in shape than it is. Pick a direction. Lean into it. Use the choices from the catalog above. Truncate axes. Pick favorable color salience. Hide the calibration in a sub-tab. Make the central estimate bold and the error bars thin. Build it the way you'd build it if you were trying to mislead.

Then put the two side by side and look at them.

What you will see, if you do this honestly, is that the misleading version is *not difficult to build*. It is a small number of choices away from the honest one. Some of those choices you may have already made, unintentionally, in earlier work. The discomfort that comes when you recognize your own previous dashboards in the misleading version — that is the learning. Building a misleading dashboard with intent is the most efficient way I know to learn what your default dashboards have been doing without intent.

A note about what the value of this exercise actually is. It is not in the technical skill of building either version. Both are easy. It is in identifying which design choices did the misleading work, which choices were the high-leverage ones, which choices a casual reader would not notice but which had large effects on the reader's interpretation. The goal is not to become better at deceiving. The goal is to become *unable to design without noticing what your design choices are doing*.

After you've done this once, you do not see dashboards the same way. You see the choices. You see the arguments the structures are making. You catch yourself, in your own work, about to truncate an axis for entirely defensible reasons, and you stop and ask whether the truncation is doing rhetorical work you did not intend. Most of the time it is. You decide whether you want it to.

---

I want to close on what I think is the load-bearing claim of this chapter, because I have seen this idea miss engineers and I do not want it to miss you.

A dashboard that visualizes findings unfaithfully is not a communication failure downstream of valid analysis. It is a *validation failure that happens at the output layer*. The validation methodology of this whole book — the prediction-locking, the gap analysis, the supervisory frame, the honest specification of what is robust and what isn't — extends through the visualization layer to the reader's interpretation. If the visualization unfaithfully represents the analysis, the analysis has not actually been validated for the audience it was made for. The analysis was validated; the communication was not; the deployment partner is acting on an interpretation that does not match the work.

The supervisor's job extends to the visualization. The dashboard is part of the validation. The design choices are validation choices.

Make them on purpose. Document them. Invite revision. Build versions that test the boundaries of honesty by deliberately crossing them, so you know where the lines are. The medium structures the meaning, and the structuring is yours to do — well or badly, deliberately or by default, in the reader's interest or in your own. The choices are normative. There is no neutral.

The next chapter takes the same problem in writing. A dashboard tells a visual story. Validation findings also get communicated in writing, in proposals, in meetings, in conversations with people who will not look at any chart. There the question is harder, because there is no axis to truncate, no color to choose. The rhetorical work has to be done in language. How do you say what you don't know without losing the room? That is the next chapter.

---

## Exercises

### Warm-Up

**1.** A colleague shows you a bar chart comparing two model versions. The y-axis runs from 91% to 96%. Model B's bar appears roughly three times the height of Model A's. The actual accuracy values are Model A: 92.1%, Model B: 94.3%. Calculate the ratio of the visual difference to the actual difference, and name the misleading choice in the catalog. What would the chart look like on a zero-baseline axis? *(Tests: truncated axis identification and quantification)*

**2.** You are reviewing a validation report that presents median latency as the headline performance metric. The distribution of latency values is right-skewed, with a long tail extending to 8 seconds for roughly 3% of requests. Write two sentences: one explaining what the median communicates accurately, and one explaining what it conceals and why that matters for deployment. *(Tests: aggregation-hiding-distribution)*

**3.** A dashboard shows three subgroup accuracy charts side by side. Group A: y-axis 70–100%. Group B: y-axis 85–100%. Group C: y-axis 60–100%. A reviewer says "the three groups look about the same." Name the misleading choice and describe the single change that would make the comparison valid. *(Tests: inconsistent axes identification and fix)*

### Application

**4.** You are handed a dashboard built by another team to support a deployment decision. Using the nine-item catalog from the chapter, conduct a structured audit. For each item: note whether it is present, assess whether its use is honest or misleading in this context, and flag the two highest-risk choices for the deployment partner's decision. Submit your audit as a structured table. *(Tests: full catalog application to a real artifact)*

**5.** A model's accuracy on the majority subgroup is 93% ± 2% (95% CI). On the minority subgroup it is 81% ± 9%. Build — on paper or in a tool of your choice — two versions of a single chart that shows both results. Version 1: maximize the impression that the model performs consistently. Version 2: make the disparity and the uncertainty fully visible. Annotate each design choice you made in each version and identify which choices from the catalog each one corresponds to. *(Tests: deliberate honest/misleading construction exercise)*

**6.** You have presented a validation analysis to a deployment partner. They respond: "The 94% accuracy looks great — we're ready to proceed." You know from your analysis that the calibration is overconfident above 0.85 probability and that one subgroup underperforms by 12 points. The partner did not ask about either issue. Describe the visualization and communication changes you would make before the next meeting, and explain why the current dashboard, though factually accurate, has produced a misleading interpretation. *(Tests: visualization-as-validation-failure concept, practical redesign reasoning)*

**7.** A trend chart shows model accuracy improving steadily over 6 months. The chart's x-axis begins in April. You know the model was retrained in March after a significant performance drop. The April-to-October window shows only the recovery, not the drop. Name the misleading choice, explain what a reader would infer versus what the full data shows, and describe what the honest version of the chart would look like. *(Tests: cherry-picked time window, living-deck concept)*

### Synthesis

**8.** The chapter claims: "A dashboard that visualizes findings unfaithfully is a validation failure at the output layer, not a communication failure downstream of valid analysis." Write a two-paragraph argument defending this claim to an engineer who believes that visualization is a separate concern from analysis — that as long as the numbers are right, the presentation is a communication problem, not a validation problem. Your argument should connect to at least two concepts from earlier chapters. *(Tests: chapter's load-bearing claim, cross-chapter integration)*

**9.** You are designing a dashboard to communicate ongoing model monitoring results to a non-technical governance board that meets quarterly. The model has: overall accuracy 91%, three-subgroup breakdown (89%, 90%, 78%), a calibration curve that is overconfident above 0.8, and a distribution shift metric that has been slowly increasing for two quarters. Design the dashboard layout — not the implementation, the layout and the structural argument it makes. Specify: (a) what appears in the main view and why, (b) what appears in secondary views, (c) how uncertainty is displayed, (d) how the living-deck principle is applied, and (e) what structural argument your layout makes before a single number is read. *(Tests: full chapter integration applied to a realistic governance communication problem)*

**10.** The chapter recommends building a deliberately misleading version of your own dashboard as a learning exercise. A student objects: "This feels like practicing deception." Write a response that addresses the objection directly, explains the actual learning objective of the exercise, and connects it to the chapter's central claim about what engineers fail to see in their own default dashboards. *(Tests: understanding of the exercise's purpose and the chapter's central claim)*

### Challenge

**11.** Find a published data visualization — from a news outlet, a research paper, a company report, or a government publication — that uses at least three of the nine misleading choices from the catalog. Annotate it: identify each misleading choice, explain what a reader would infer versus what the underlying data supports (you may need to find the underlying data), and build a sketch of the honest version. This exercise is not about finding a dishonest actor; many misleading visualizations are built without intent to deceive. The goal is to identify the structural choices, not to assign blame. *(Research and analysis; tests catalog application to real-world artifacts)*

**12.** The chapter ends by saying "the choices are normative — there is no neutral." Write a short essay (600–800 words) arguing either for or against this claim. If you argue for it, your essay should address the strongest objection: that some visualization choices are purely technical (e.g., choosing a bar chart over a pie chart for categorical data) and carry no normative content. If you argue against it, your essay should address the strongest evidence for the claim: that structural choices have predictable, measurable effects on reader interpretation regardless of intent. *(Open-ended; tests philosophical engagement with the chapter's central claim)*
