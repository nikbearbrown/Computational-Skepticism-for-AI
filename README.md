# Computational Skepticism for AI

**Author:** Nik Bear Brown  
**Publisher:** Bear Brown, LLC  
**Course:** INFO 7375 — Computational Skepticism for AI — Northeastern University  
**Status:** Draft — actively developed alongside the course

---

## About This Book

*Computational Skepticism for AI* is the textbook for INFO 7375 at Northeastern University. It teaches practitioners how to interrogate AI systems — not how to build them. The working assumption is that anyone reading this book will spend more of their career evaluating, auditing, and defending decisions made by AI systems than they will spend training models. That asymmetry is the reason this book exists.

The book does not assume you will always have access to model weights, training data, or the engineers who built the system. It assumes you will be handed an output and asked whether to trust it.

---

## Structure

```
chapters/
    00-frontmatter.md                                                          copyright, dedication, preface
    00-introduction.md                                                         Chapter 0 — Introduction
    01-the-skeptics-toolkit.md                                                 Chapter 1
    02-probability-uncertainty-and-the-confidence-illusion.md                  Chapter 2
    03-bias-where-it-enters-and-who-is-responsible.md                         Chapter 3
    04-the-frictional-method-evidence-of-learning-when-ai-can-generate-the-artifact.md   Chapter 4
    05-data-validation-reconstructing-the-epistemic-frame-behind-a-dataset.md Chapter 5
    06-model-explainability-distinguishing-explanation-from-the-appearance-of-explanation.md  Chapter 6
    07-fairness-metrics-choosing-a-definition-and-defending-it.md              Chapter 7
    08-robustness-what-understanding-means-when-a-pixel-can-break-the-model.md Chapter 8
    09-validating-agentic-ai-when-autonomous-systems-misbehave.md             Chapter 9
    10-delegation-trust-and-the-supervisory-role.md                            Chapter 10
    11-visualization-under-validation-honest-misleading-and-the-choices-between.md  Chapter 11
    12-communicating-uncertainty-calibrating-claims-to-evidence.md             Chapter 12
    13-accountability-who-is-responsible-when-the-system-fails.md             Chapter 13
    14-the-limits-of-ai-what-the-tools-cannot-do.md                           Chapter 14
    99-back-matter.md                                                          acknowledgments, about the author, notes, references, index
```

Chapter 0 (Introduction) functions as a full roadmap chapter — it establishes the skeptical frame, defines terms used throughout the book, and tells the reader how the chapters build on each other.

---

## For Students

This repository contains the working draft of the textbook. Chapters are updated throughout the semester. If you find an error, open an issue. If a section is unclear, open an issue. Student feedback is how this book improves.

The book is free to read here. The compiled EPUB is available in Releases.

**You do not need to clone this repository to use the book.** Read it here on GitHub or download the EPUB from Releases.

---

## For Instructors

The book is designed to be taught chapter-by-chapter in sequence, but Chapters 1–3 (toolkit, probability, bias) can also serve as a standalone unit on critical foundations. Chapters 4–9 cover specific failure modes and can be assigned selectively. Chapters 10–14 address human responsibility and governance and work well as a capstone unit.

Slide decks and assignment scaffolds are maintained separately. Contact [bear@bearbrown.co](mailto:bear@bearbrown.co) for access.

---

## Build

Requires [Pandoc](https://pandoc.org/).

```bash
./build.sh
```

Produces `output/computational-skepticism-for-ai.epub` and `output/computational-skepticism-for-ai.html`.

### Figures

```bash
./graphs.sh
```

Processes `<!-- → [TYPE: description] -->` comments in chapter files and generates placeholder tables and images. Review `chapters/*-updated.md`, then promote:

```bash
for f in chapters/*-updated.md; do mv "$f" "${f/-updated/}"; done
```

---

## License

Text © 2026 Nik Bear Brown. Published by Bear Brown, LLC.  
All rights reserved. Instructors may reproduce individual chapters for classroom use with attribution.

---

## Citation

```
Brown, Nik Bear. Computational Skepticism for AI. Bear Brown, LLC, 2026.
https://github.com/nikbearbrown/computational-skepticism-for-ai
```
