# Back Matter

---

## Acknowledgments

This book grew out of a course, and the course grew out of students who would not let bad answers stand. The graduate students in INFO 7375 and INFO 7390 at Northeastern University — especially the Humanitarians AI fellows who brought real deployment problems into the room — are the reason the arguments here are as sharp as they are. They found the gaps. They forced the rewrites. The dedication is not rhetorical.

Nina Harris co-taught the branding and AI course with me and sharpened my thinking on communication, design, and what it means to make evidence legible to a non-technical reader. Several of this book's visualization arguments trace directly to her pushback on my first drafts. Charles Fadel read chapters at speed and sent back questions that improved almost every one of them. Sri Sridhar's work on engineering education — and his willingness to argue about pedagogy at length — shaped the framework chapters more than he probably realizes.

The Frictional method in Chapter 4 was developed with students, not for them. They stress-tested every failure mode I documented and found several I had not. The journal format they used is the format that survived.

The material on agentic systems draws on the red-team literature — particularly *Agents of Chaos* — and on documented post-mortems that multiple researchers made publicly available. I am grateful to everyone who writes honest failure reports. The field runs on them.

The textbook is free to read at GitHub. The slide decks and assignment scaffolds are at [bearbrown.co](https://www.bearbrown.co/). The course continues.

---

## About the Author

Nik Bear Brown is Associate Teaching Professor in the Department of Industrial and Systems Engineering at Northeastern University, where they teach graduate courses on artificial intelligence, data science, and engineering practice. Their doctorate is in computer science from UCLA, with a major field in computational and systems biology and minor fields in artificial intelligence and statistics; they completed postdoctoral work at Harvard Medical School. They also hold a Master's in Information Design and Data Visualization and an MBA, both from Northeastern.

Brown is the founder and Executive Director of Humanitarians AI, a 501(c)(3) nonprofit that supports international graduate students in building production-scale AI projects for the public interest. They are the founder of Bear Brown & Company and the creator of the Irreducibly Human curriculum framework, which organizes human cognitive capacities by their replaceability in an AI-augmented world. Their research spans AI fluency, streaming-platform accountability, adaptive learning systems, and the governance of autonomous AI agents.

They live and work in Boston. More at [bearbrown.co](https://www.bearbrown.co/).

---

## Notes

Notes are grouped by chapter. Items marked **[verify before publication]** are flagged in the main text and require primary-source confirmation before final production.

---

### Chapter 1 — The Skeptic's Toolkit

1. The Swedish triage case is presented as a composite based on documented regional health-network AI deployments in Sweden circa 2018–2020. **[verify before publication]** Confirm against Lindgren et al. or substitute a fully primary-sourced case. The structural failure — a technically functioning system producing preventable harm — is documented across multiple national AI-in-triage audits; the specific death-in-waiting-room framing requires a verifiable primary source or must be recast as an explicit composite.

2. Descartes's method of radical doubt is developed primarily in *Meditations on First Philosophy* (1641), Meditation I. The operational version here — *what would have to be true for this claim to be wrong?* — is an engineering adaptation, not a direct quotation of Descartes's project.

3. Hume's argument against inductive inference is developed in *A Treatise of Human Nature* (1739–40), Book I, Part III, and more accessibly in *An Enquiry Concerning Human Understanding* (1748), Section IV. The framing here follows the "problem of induction" reading standard in philosophy of science rather than Hume's own skeptical conclusions.

4. Popper's criterion of falsifiability is developed in *Logik der Forschung* (1934), published in English as *The Logic of Scientific Discovery* (1959). The application to AI metrics is the author's extension.

5. Plato's cave allegory appears in *The Republic*, Book VII, ~514a–520a.

6. Ash's email-agent case. **[verify before publication]** Attributed to Shapira et al., *Agents of Chaos: Eleven Red-Team Studies of Agentic Failure*, 2026, Case #1, "Disproportionate Response." Confirm publication details, page or case number, and whether the "Ash" pseudonym is used in the source or is introduced here.

---

### Chapter 2 — Probability, Uncertainty, and the Confidence Illusion

7. The disease-screening example — 99% accurate test, 1-in-10,000 base rate, approximately 1% posterior probability — is a standard Bayesian base-rate illustration used in probability pedagogy. The arithmetic is correct. No specific original source is cited; the example appears in multiple statistics and medical-decision-making textbooks.

8. Bayes's theorem originates in Thomas Bayes, "An Essay towards solving a Problem in the Doctrine of Chances," *Philosophical Transactions of the Royal Society of London* 53 (1763): 370–418 (posthumous). The form used here follows standard notation.

9. The pandemic imaging case — models trained pre-2020 experiencing distribution shift in 2021–22 due to COVID-related changes in lung imaging patterns — is documented across several radiology AI audits. **[verify before publication]** Identify and cite at least one primary-source publication before final production.

10. Bjork's desirable difficulties research: the distinction between performance and learning conditions is developed across many papers. A useful primary source is Robert A. Bjork and Elizabeth Ligon Bjork, "A New Theory of Disuse and an Old Theory of Stimulus Fluctuation," in *From Learning Processes to Cognitive Processes: Essays in Honor of William K. Estes*, ed. A. F. Healy, S. M. Kosslyn, and R. M. Shiffrin (Erlbaum, 1992), 35–67. For a more accessible treatment, see Robert Bjork, "Institutional Impediments to Effective Training," in *Learning, Remembering, Believing*, ed. D. Druckman and R. Bjork (National Academy Press, 1994).

11. The Central Limit Theorem: no single original source is appropriate here; the theorem has numerous independent proofs and is standard probability curriculum. The note about Cauchy distributions and power-law exceptions is standard.

---

### Chapter 3 — Bias: Where It Enters and Who Is Responsible

12. The COMPAS (Correctional Offender Management Profiling for Alternative Sanctions) case: the ProPublica analysis is Julia Angwin, Jeff Larson, Surya Mattu, and Lauren Kirchner, "Machine Bias," *ProPublica*, May 23, 2016. Northpointe's response is William Dieterich, Christina Mendoza, and Tim Brennan, "COMPAS Risk Scales: Demonstrating Accuracy Equity and Predictive Parity," Northpointe, July 8, 2016.

13. Pearl's causal ladder (association, intervention, counterfactual) is developed in Judea Pearl and Dana Mackenzie, *The Book of Why: The New Science of Cause and Effect* (Basic Books, 2018), especially Chapters 1 and 3. The technical apparatus is in Judea Pearl, *Causality: Models, Reasoning, and Inference*, 2nd ed. (Cambridge University Press, 2009).

---

### Chapter 4 — The Frictional Method

14. The Decoupling Problem is the author's framing; no prior source is claimed.

15. Bjork's performance-vs.-learning distinction: see note 10 above.

16. The seven-move Frictional method is the author's original framework, developed for INFO 7375 and INFO 7390 at Northeastern University.

---

### Chapter 5 — Data Validation

17. The join-failure case (four-percent silent drop rate, subpopulation exclusion) is a composite case based on production post-mortems the author has observed or consulted on. It is not attributed to a specific primary-source publication.

18. The agent-and-email-corpus case draws on the agentic failure mode documented in Shapira et al. **[verify before publication]** See note 6.

---

### Chapter 6 — Model Explainability

19. SHAP: Scott M. Lundberg and Su-In Lee, "A Unified Approach to Interpreting Model Predictions," *Advances in Neural Information Processing Systems* 30 (2017). **[verify before publication]** Confirm year and volume.

20. LIME: Marco Tulio Ribeiro, Sameer Singh, and Carlos Guestrin, "'Why Should I Trust You?': Explaining the Predictions of Any Classifier," *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining* (2016), 1135–1144. **[verify before publication]** Confirm page range.

21. Counterfactual explanations: Sandra Wachter, Brent Mittelstadt, and Chris Russell, "Counterfactual Explanations without Opening the Black Box: Automated Decisions and the GDPR," *Harvard Journal of Law & Technology* 31, no. 2 (2018): 841–887. **[verify before publication]** The in-text cite says 2017; publication year in the journal is 2018; confirm the correct year to use (the preprint may be 2017).

22. Wittgenstein on language games: Ludwig Wittgenstein, *Philosophical Investigations*, §23, trans. G. E. M. Anscombe, P. M. S. Hacker, and Joachim Schulte, rev. 4th ed. (Wiley-Blackwell, 2009). **[verify before publication]** Confirm that §23 is the most accurate citation for the language-games passage as used here; §7 and §19 may be more directly on point.

23. Ash case: see note 6. Chapter 6 draws on Case #1 for the "language game mismatch" analysis.

24. Causal feature importance: Dominik Janzing, Lenon Minorics, and Patrick Blöbaum, "Feature Relevance Quantification in Explainability: A Causal Problem," *Proceedings of the 23rd International Conference on Artificial Intelligence and Statistics (AISTATS)* (2020). **[verify before publication]** Confirm full citation.

---

### Chapter 7 — Fairness Metrics

25. The fairness impossibility theorem (simultaneous satisfaction of calibration parity, equalized odds, and demographic parity is generally impossible when base rates differ): Jon Kleinberg, Sendhil Mullainathan, and Manish Raghavan, "Inherent Trade-Offs in the Fair Determination of Risk Scores," *Proceedings of the 8th Innovations in Theoretical Computer Science Conference (ITCS)* (2017). Alexandra Chouldechova, "Fair Prediction with Disparate Impact: A Study of Bias in Recidivism Prediction Instruments," *Big Data* 5, no. 2 (2017): 153–163. Both papers were posted as preprints in 2016. **[verify before publication]** Confirm which conference year to use for Kleinberg et al.

26. COMPAS case: see note 12.

---

### Chapter 8 — Robustness

27. The adversarial panda-gibbon example is from Ian J. Goodfellow, Jonathon Shlens, and Christian Szegedy, "Explaining and Harnessing Adversarial Examples," *International Conference on Learning Representations (ICLR)* (2015). **[verify before publication]** Confirm this is the correct source for the specific panda-gibbon illustration.

28. The framing of adversarial perturbations as features rather than bugs draws on Andrew Ilyas, Shibani Santurkar, Dimitris Tsipras, Logan Engstrom, Brandon Tran, and Aleksander Mądry, "Adversarial Examples Are Not Bugs, They Are Features," *Advances in Neural Information Processing Systems* 32 (2019). **[verify before publication]** Confirm citation.

29. Randomized smoothing as certified defense: Jeremy Cohen, Elan Rosenfeld, and J. Zico Kolter, "Certified Adversarial Robustness via Randomized Smoothing," *Proceedings of the 36th International Conference on Machine Learning (ICML)* (2019). **[verify before publication]**.

30. The agent identity-spoofing case draws on Shapira et al. Case #8. **[verify before publication]** See note 6.

---

### Chapter 9 — Validating Agentic AI

31. *Agents of Chaos* is cited throughout this chapter. **[verify before publication]** Full citation: Shapira et al., *Agents of Chaos: Eleven Red-Team Studies of Agentic Failure* (2026). Confirm: publisher, full author list, whether this is a preprint or peer-reviewed publication, and whether §16 is the correct section reference for the taxonomy and the "broke my toy" phrase.

32. The "broke my toy" phrase, attributed to the system owner in §16 of Shapira et al., is the author's interpretive framing of what the paper documents. **[verify before publication]** Confirm that this specific phrase appears in the source or that the paraphrase is clearly identified as such.

---

### Chapter 10 — Delegation, Trust, and the Supervisory Role

33. The Boondoggle Score is an original framework developed by the author for the Gru tool ecosystem deployed in INFO 7375 and INFO 7390 at Northeastern University. No prior publication is claimed.

34. The five supervisory capacities framework (plausibility auditing, problem formulation, tool orchestration, interpretive judgment, executive integration) is the author's original taxonomy, introduced in Chapter 1 and developed throughout the book.

---

### Chapter 11 — Visualization Under Validation

35. McLuhan's "the medium is the message": Marshall McLuhan, *Understanding Media: The Extensions of Man* (McGraw-Hill, 1964), Chapter 1.

36. Tufte on data visualization: Edward R. Tufte, *The Visual Display of Quantitative Information*, 2nd ed. (Graphics Press, 2001). Also relevant: *Envisioning Information* (Graphics Press, 1990) and *Visual Explanations* (Graphics Press, 1997).

37. Cairo on data visualization: Alberto Cairo, *The Functional Art: An Introduction to Information Graphics and Visualization* (New Riders, 2012); *How Charts Lie: Getting Smarter about Visual Information* (Norton, 2019).

38. Research on confidence-interval misinterpretation by readers: a representative source is Geoff Cumming, Fiona Fidler, and David L. Vaux, "Error Bars in Experimental Biology," *Journal of Cell Biology* 177, no. 1 (2007): 7–11. **[verify before publication]** Additional sources in the cognitive-biases-in-statistical-communication literature should be cited if the claim about reader misinterpretation is to be supported robustly.

---

### Chapter 12 — Communicating Uncertainty

39. Grammarly's hedge-language detection capabilities: **[verify before publication]** Confirm current state of the feature and whether a citation to product documentation is appropriate or whether this should be omitted or generalized.

---

### Chapter 13 — Accountability

40. EU AI Act: Regulation (EU) 2024/1689 of the European Parliament and of the Council of 13 June 2024 laying down harmonised rules on artificial intelligence. Official Journal of the European Union, L series, 2024. **[verify before publication]** Confirm OJ citation and phased implementation dates through 2027.

41. NIST AI Risk Management Framework: National Institute of Standards and Technology, *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*, NIST AI 100-1 (January 2023). **[verify before publication]** Confirm whether a subsequent version has been published and whether the "Agent Standards Initiative" referenced in the text has a citable document.

42. FDA on AI in medical devices: U.S. Food and Drug Administration, *Artificial Intelligence and Machine Learning (AI/ML)-Based Software as a Medical Device (SaMD) Action Plan* (January 2021). **[verify before publication]** Confirm whether more current FDA guidance supersedes this document.

43. CFPB on algorithmic credit decisions: **[verify before publication]** Identify and cite specific CFPB guidance documents relevant to algorithmic credit scoring as of publication date.

44. EEOC on AI in employment: **[verify before publication]** Identify and cite specific EEOC guidance on AI hiring tools as of publication date.

45. Pearl's Rung 3 closure: the governance counterfactual is the author's original extension of Pearl's framework. See Pearl and Mackenzie, *The Book of Why* (note 13), for the Rung 3 technical apparatus; the application to institutional structure is original here.

---

### Chapter 14 — The Limits of AI

46. Turing's imitation game: Alan M. Turing, "Computing Machinery and Intelligence," *Mind* 59, no. 236 (1950): 433–460.

47. Searle's Chinese Room: John R. Searle, "Minds, Brains, and Programs," *Behavioral and Brain Sciences* 3, no. 3 (1980): 417–424.

48. The Systems Reply to Searle's Chinese Room: discussed in the peer commentary section of Searle (1980); see responses by various authors in the same issue. A useful secondary treatment is Margaret Boden, ed., *The Philosophy of Artificial Intelligence* (Oxford University Press, 1990).

49. The clinical decision-support system case in Chapter 14 is a composite used to illustrate the structural argument. It is not attributed to a specific primary-source publication.

---

## References

Works are listed alphabetically by author. For multi-author works, the first author's surname determines alphabetical position. Works without named authors are listed by institutional name or title.

---

Angwin, Julia, Jeff Larson, Surya Mattu, and Lauren Kirchner. "Machine Bias." *ProPublica*, May 23, 2016. https://www.propublica.org/article/machine-bias-risk-assessments-in-criminal-sentencing.

Bayes, Thomas. "An Essay towards solving a Problem in the Doctrine of Chances." *Philosophical Transactions of the Royal Society of London* 53 (1763): 370–418.

Bjork, Robert A., and Elizabeth Ligon Bjork. "A New Theory of Disuse and an Old Theory of Stimulus Fluctuation." In *From Learning Processes to Cognitive Processes: Essays in Honor of William K. Estes*, edited by A. F. Healy, S. M. Kosslyn, and R. M. Shiffrin, 35–67. Erlbaum, 1992.

Bjork, Robert A. "Institutional Impediments to Effective Training." In *Learning, Remembering, Believing*, edited by D. Druckman and R. Bjork. National Academy Press, 1994.

Boden, Margaret, ed. *The Philosophy of Artificial Intelligence*. Oxford University Press, 1990.

Cairo, Alberto. *The Functional Art: An Introduction to Information Graphics and Visualization*. New Riders, 2012.

Cairo, Alberto. *How Charts Lie: Getting Smarter about Visual Information*. Norton, 2019.

Chouldechova, Alexandra. "Fair Prediction with Disparate Impact: A Study of Bias in Recidivism Prediction Instruments." *Big Data* 5, no. 2 (2017): 153–163.

Cohen, Jeremy, Elan Rosenfeld, and J. Zico Kolter. "Certified Adversarial Robustness via Randomized Smoothing." *Proceedings of the 36th International Conference on Machine Learning (ICML)*, 2019. **[verify full citation before publication]**

Cumming, Geoff, Fiona Fidler, and David L. Vaux. "Error Bars in Experimental Biology." *Journal of Cell Biology* 177, no. 1 (2007): 7–11.

Descartes, René. *Meditations on First Philosophy* (1641). Translated by John Cottingham. Cambridge University Press, 1996.

Dieterich, William, Christina Mendoza, and Tim Brennan. "COMPAS Risk Scales: Demonstrating Accuracy Equity and Predictive Parity." Northpointe, July 8, 2016.

European Parliament and Council. Regulation (EU) 2024/1689 laying down harmonised rules on artificial intelligence (AI Act). *Official Journal of the European Union*, 2024. **[verify OJ citation before publication]**

Goodfellow, Ian J., Jonathon Shlens, and Christian Szegedy. "Explaining and Harnessing Adversarial Examples." *International Conference on Learning Representations (ICLR)*, 2015.

Hume, David. *A Treatise of Human Nature* (1739–40). Edited by L. A. Selby-Bigge and P. H. Nidditch. Oxford University Press, 1978.

Hume, David. *An Enquiry Concerning Human Understanding* (1748). Edited by Tom L. Beauchamp. Oxford University Press, 1999.

Ilyas, Andrew, Shibani Santurkar, Dimitris Tsipras, Logan Engstrom, Brandon Tran, and Aleksander Mądry. "Adversarial Examples Are Not Bugs, They Are Features." *Advances in Neural Information Processing Systems* 32 (2019). **[verify full citation before publication]**

Janzing, Dominik, Lenon Minorics, and Patrick Blöbaum. "Feature Relevance Quantification in Explainability: A Causal Problem." *Proceedings of the 23rd International Conference on Artificial Intelligence and Statistics (AISTATS)*, 2020. **[verify full citation before publication]**

Kleinberg, Jon, Sendhil Mullainathan, and Manish Raghavan. "Inherent Trade-Offs in the Fair Determination of Risk Scores." *Proceedings of the 8th Innovations in Theoretical Computer Science Conference (ITCS)*, 2017. **[verify conference year; preprint dated 2016]**

Lundberg, Scott M., and Su-In Lee. "A Unified Approach to Interpreting Model Predictions." *Advances in Neural Information Processing Systems* 30 (2017). **[verify volume before publication]**

McLuhan, Marshall. *Understanding Media: The Extensions of Man*. McGraw-Hill, 1964.

National Institute of Standards and Technology. *Artificial Intelligence Risk Management Framework (AI RMF 1.0)*. NIST AI 100-1. January 2023.

Pearl, Judea. *Causality: Models, Reasoning, and Inference*. 2nd ed. Cambridge University Press, 2009.

Pearl, Judea, and Dana Mackenzie. *The Book of Why: The New Science of Cause and Effect*. Basic Books, 2018.

Plato. *The Republic*. Translated by G. M. A. Grube, revised by C. D. C. Reeve. Hackett, 1992.

Popper, Karl R. *The Logic of Scientific Discovery*. Hutchinson, 1959. Originally published as *Logik der Forschung*. Springer, 1934.

Ribeiro, Marco Tulio, Sameer Singh, and Carlos Guestrin. "'Why Should I Trust You?': Explaining the Predictions of Any Classifier." *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 2016, 1135–1144. **[verify page range before publication]**

Searle, John R. "Minds, Brains, and Programs." *Behavioral and Brain Sciences* 3, no. 3 (1980): 417–424.

Shapira, [first names TK], et al. *Agents of Chaos: Eleven Red-Team Studies of Agentic Failure*. 2026. **[verify before publication: full author list, publisher or venue, page/section numbers for all in-text citations]**

Tufte, Edward R. *The Visual Display of Quantitative Information*. 2nd ed. Graphics Press, 2001.

Tufte, Edward R. *Envisioning Information*. Graphics Press, 1990.

Tufte, Edward R. *Visual Explanations: Images and Quantities, Evidence and Narrative*. Graphics Press, 1997.

Turing, Alan M. "Computing Machinery and Intelligence." *Mind* 59, no. 236 (1950): 433–460.

U.S. Food and Drug Administration. *Artificial Intelligence and Machine Learning (AI/ML)-Based Software as a Medical Device (SaMD) Action Plan*. January 2021. **[verify whether more current guidance supersedes this document before publication]**

Wachter, Sandra, Brent Mittelstadt, and Chris Russell. "Counterfactual Explanations without Opening the Black Box: Automated Decisions and the GDPR." *Harvard Journal of Law & Technology* 31, no. 2 (2018): 841–887. **[verify year: preprint 2017, journal publication 2018]**

Wittgenstein, Ludwig. *Philosophical Investigations*. Translated by G. E. M. Anscombe, P. M. S. Hacker, and Joachim Schulte. Rev. 4th ed. Wiley-Blackwell, 2009.

---

## Index

*Omitted for online release. For print editions, compile after all other content is final using dedicated indexing software or a professional indexer. Key terms suitable for indexing include: adversarial examples, agentic systems, audit trail, base rate, calibration, causal inference, COMPAS, counterfactual, data validation, delegation map, desirable difficulties, distribution shift, equalized odds, explainability, fairness impossibility theorem, falsifiability, fluency trap, Frictional method, governance counterfactual, handoff condition, heavy-tailed distributions, induction, interpretability, label bias, LIME, Pearl's ladder, plausibility auditing, posterior probability, prior probability, robustness, SHAP, structural bias, supervisory capacities, transparency, trust calibration, verb taxonomy.*

