# Chapter 1 — The Skeptic's Toolkit
*The moves you perform before you trust what the machine just told you.*

In 2018, a 49-year-old woman walked into an emergency department in Sweden with a swollen leg. The triage system — an AI scoring tool that had been running across the regional health network for eighteen months — categorized her as low-acuity. Her vital signs, history, and presenting complaint matched the patterns the model had been trained to associate with less urgent cases. She waited four hours. The clot moved to her lung. She died in the waiting room. ([verify] composite case based on documented Swedish triage AI deployments; confirm against Lindgren et al. or substitute a primary-sourced case before publication.)

Now, the system was not broken. I want you to sit with that for a second, because it is the whole point. The system worked exactly as designed. Its training data reflected the patterns of who had previously presented at this hospital. The model had learned its training data. It had been validated on its training data. The metrics had been published. The deployment had passed every internal review. By every measure the engineers had built into the system, the system had succeeded.

A patient was dead. The system had succeeded.

This is the gap I want to spend a chapter teaching you to see. A statistical artifact can be technically correct, and the world it claims to describe can be a place where a person dies in a waiting room because the artifact was the wrong description. The output and the world are not the same thing. They are never the same thing. Pretending they are the same thing is the most expensive mistake in modern engineering.

## What I mean by skepticism

When I say *skepticism*, I do not mean the disposition. I do not mean the rolling of the eyes, the cynicism dressed up as a virtue, the man at the back of the meeting who is against everything. Engineers know what to do with that, which is not very much, and they are right to know it.

I mean a method. Skepticism in this book is a set of moves you can perform on a claim, and the moves can be taught, and you can see whether someone did them. There are three I want to give you up front.

The first comes from Descartes, and it is the move of *radical doubt*. You take a claim — say, the triage score of "low-acuity" — and you ask: what would have to be true for this claim to be wrong about the patient in front of me? Not as a ranking exercise. As a diagnostic. If the answer is "well, the score is the score," then you have confused the artifact for the world, and we are back where we started. If the answer is "the training data would have had to underweight cases like this one, or the input features would have to be missing the relevant signal, or the deployment context would have to differ from the validation context" — *now* you have something. You have a list of conditions you can check.

The second move comes from Hume, and it is the limit of induction. Here is the thing about induction that I want you to feel in your bones, because most engineers have heard the words and not really felt the thing. The model has been right thousands of times. Each one of those correct predictions adds *zero logical guarantee* that the next prediction will be right. None. Zero. The reason induction works in practice is that the world is doing some of the work for you — the distribution is stable, the patterns persist — and the working is invisible until it stops working. When it stops working, the model is exactly as confident as it was the day before, and the confidence is now a lie. Hume's move is to remember, every time, that the model's confidence is a property of the model and not a property of the world.

The third move comes from Popper. A claim that cannot be wrong is not yet a claim. "The model performs well in production." Well — what would performing badly look like? On which metric? With what threshold? Counted how, over what window? If you cannot specify the conditions under which the claim would be falsified, then the claim is not engineering. It is rhetoric. We are going to use Popper's move on a great many claims in this book, and a surprising number of them will turn out to be rhetoric.

I want to be clear about something. You do not have to think Descartes was right about anything to use his move. You do not have to be a Humean or a Popperian. The moves are tools. A structural engineer does not have to believe in the metaphysics of steel to perform a load test on a beam. You perform the move. The move either reveals something or it does not. If it does, you have learned something about the system. If it does not, you have learned that this particular check came back clean. Either is useful.

