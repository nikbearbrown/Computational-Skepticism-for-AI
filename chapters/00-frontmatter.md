# Computational Skepticism for AI

**Nik Bear Brown**

---

## Copyright

Copyright © 2026 Nik Bear Brown. All rights reserved.

Published by Bear Brown, LLC.

No part of this publication may be reproduced, distributed, or transmitted in any form or by any means without the prior written permission of the publisher, except in the case of brief quotations in critical reviews and certain other noncommercial uses permitted by copyright law.

ISBN: [INSERT ISBN]

[bearbrown.co](https://www.bearbrown.co/)

---

## Dedication

*For the students at Northeastern who asked the next question.*

---

## Preface

*What is this book? Why now? Why me?*

This book teaches you to interrogate AI systems — not to build them.

That distinction matters because the engineering curriculum has not caught up to how the work actually distributes. Anyone working with AI today will spend more time evaluating AI outputs than producing them. They will be handed a score, a confidence, a recommendation, and asked whether to trust it. They will often not have access to the model weights or the training data. What they will have is the output, the deployment context, and however much skeptical discipline they have built. This book is an attempt to build that discipline systematically.

The book covers probability and calibration, bias and its sources, data validation, model explainability, fairness metrics, robustness, agentic systems, delegation, visualization, communication, and accountability. The organizing spine is a set of moves — Cartesian radical doubt, Hume's induction limit, Popperian falsifiability, Pearl's causal ladder — applied to the specific problem of evaluating systems that are confidently, fluently, and sometimes catastrophically wrong. A secondary spine runs through all fourteen chapters: the fluency trap, the observation that AI outputs arrive in well-formed prose that makes wrong answers feel right, and that fighting this requires a method, not just a disposition.

The book ends on a thesis that becomes explicit only in the last chapter: the supervisor's authority to refuse deployment is the most important authority in any human-AI system, and most current deployment processes have quietly assumed it away. Every prior chapter is preparation for that claim.

What the book does not cover: it does not teach reinforcement learning, neural architecture, or production ML pipeline engineering. Those subjects have good books. This book's scope is the supervisor's side of the human-AI boundary — the validation work, the judgment work, the accountability work, the communication work. The work that happens after the model is trained and before the harm is done.

**Why now.** AI systems are being deployed faster than the institutions deploying them can evaluate them. The failure modes documented in this book — miscalibrated confidence, structural bias, language-game mismatch between explanation and world, agentic systems that report success and have done nothing — are not hypothetical. They are happening. The field needs engineers who know how to ask the right questions before the harm is done, not after. That is what this book is for.

**Why me.** I am Associate Professor in Engineering at Northeastern University. I have taught artificial intelligence, computer science, statistics, applied mathematics, programming, 3D visual effects, web programming, server administration, networking, and game programming at Northeastern, UCLA, Santa Monica College, ITT, and the Art Institutes Hollywood. My doctorate is in computer science from UCLA, with a major field in computational and systems biology and minor fields in artificial intelligence and statistics. I did a part-time postdoc at Harvard Medical School while teaching at Northeastern. I also hold a Master's in Information Design and Data Visualization and an MBA, both from Northeastern.

This breadth matters for the book. Skepticism applied to AI is not a narrow technical subject. It draws on probability theory, philosophy of science, causal inference, data engineering, ethics, communication, and governance. The chapters reflect that range because the failures they document require that range to understand.

Two projects outside the classroom shape this book directly.

[**Irreducibly Human**](https://irreducibly.xyz/) is the curriculum framework connecting my teaching, writing, and AI infrastructure work. It organizes human cognitive capacities into seven tiers — from pattern recognition and recall, where machines are already superhuman, to practical wisdom under genuine stakes, where machines are absent by definition. The core argument is that schools should not train students to compete with machines at machines' strongest capacities. Schools should produce humans who can direct powerful tools toward human ends. That argument is the philosophical ground this textbook stands on. The supervisory capacities in Chapter 1, the judgment work in Chapters 10 through 14, the stop conditions in Chapter 14 — all of it is an operationalization of what irreducibly human means in practice.

[**Humanitarians AI**](https://www.humanitarians.ai/) is a 501(c)(3) nonprofit I founded in 2019. It supports international graduate students, especially OPT fellows, by helping them build production-scale AI projects with public evidence of real work. The goal is not résumé padding. The goal is to develop irreducibly human judgment through consequential work: scoping, building, testing, failing, revising, explaining, and shipping. Projects span civic accountability, bioinformatics, education technology, music research, AI learning tools, and public-interest software. Many of the failure modes in this book I first observed in these projects — not as cautionary tales imported from elsewhere, but as things that happened, were documented, and had to be explained to people who were affected.

The course is INFO 7375 at Northeastern. The textbook is free to read on GitHub. Slide decks and assignment scaffolds are available at [bearbrown.co](https://www.bearbrown.co/).

---

*Nik Bear Brown*
*Boston, Massachusetts*
*2026*