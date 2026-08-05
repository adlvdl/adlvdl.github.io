# PXR Challenge #8: Closing the series

*August 2026*
Tag: blind challenge

---

Eight blog posts, seven notebooks, around 28,000 lines of marimo notebook code and one webinar presentation later, this post closes out my participation in the OpenADMET PXR Blind Challenge.
I finished at rank 46 of 103 participants.
That is behind the state of the art at Tier 2, but not embarrassingly off base.

Going back to what I wrote in [the first post](2026_03_26_pxr_challenge.html):

> I also want my work to be useful beyond the final ranking.
> Each step — data analysis, model selection, split strategy, prospective evaluation — will be documented openly.
> The goal is to create a worked example of how to approach a real ML problem in drug discovery.
> I hope it can be an educational resource.

I think that is where most of the value of my participation ended up, rather than in the ranking itself.
The [previous post](2026_07_30_unblinded_phase2_analysis.html) already delivered the uncomfortable statistical verdict: sixteen of my seventeen submissions were indistinguishable from each other on 260 compounds.
This post steps back from the numbers to the things that analysis cannot measure.

I want to cover:

- a summary of the optimization process: what seemed to work and what didn't
- the paths not taken: what I could have done but didn't
- how my use of Claude Code evolved over the challenge
- what the challenge did for my visibility as an independent consultant
- the next challenge

---

## Part 1 — What worked and what didn't

The caveat from the previous post applies to everything in this section: most of my submissions sat within the uncertainty band of the one I sent.
With that said, two things stand out.

**Ensembling.** The improvement over the best single model was never dramatic, but the post-hoc analysis showed that ensembling reduces the number of badly wrong predictions, as long as the component models are not all wrong on the same compounds.
The ensemble had the lowest count of compounds with an error greater than 1 log unit of any individual model.
That is a different kind of benefit from a lower mean error: averaging does not make the typical prediction much better, it makes the catastrophic prediction rarer.

**Hyperparameter optimization**, but unevenly.
It mattered most for the tree ensembles, where XGBoost went from a baseline around 0.64 MAE to 0.52 in the 5×5 CV, combining the choice of fingerprint with the tuned hyperparameters.
The neural models barely moved: Chemprop stayed close to its 0.51 baseline no matter what I did to it.
The lesson I take is that the return on HPO depends heavily on the model class, and that the [time I spent](2026_05_15_mps_issues.html) tuning a D-MPNN would have been better spent elsewhere.

What did not work:

**Pretraining and multitask training.** Almost every time I added the single-dose or counter-screen data inside the 5×5 CV, whether as an auxiliary task or as pretraining for Chemprop, performance got worse.
This is the failure I find most annoying, because the additional data was clearly informative (most of the top teams used it) and I never found a way to let a model benefit from it.

---

## Part 2 — The paths not taken

Every project with a deadline works the same way: you list what you could do, you rank it, and the bottom of the list never gets done.
Then there are the things that never made the list at all.
Based on what the top-performing teams have described, two of those would likely have improved my model.

**Better use of the additional data.**
This has two tracks.
The first is the competition data itself.
I tried repeatedly to use the single-dose and counter-screen measurements and mostly failed; in the final submission the single-dose data is not used at all, and the counter-screen data only serves to filter non-selective compounds out of the training set.
I still believe there was a way to make that data work, and I am keen to see the OpenADMET seminars from the top teams to find out what it was.
The second track is public PXR data, which I never touched.
It would have been noisy, and I doubt that simply concatenating it with the competition data would have helped.
However, as an auxiliary task in a multitask setup, or as a way to calibrate and filter training compounds, it might have.
And one plain oversight: I could have added the Phase 1 labels to the training data for the final ensemble once they were released, and it simply never occurred to me until the challenge was over.

**Use of 3D information.**
In the recent webinar, OpenADMET previewed a table showing that most of the top ten participants used 3D information of some kind, from docking or cofolding.
This was more natural for teams also entering the structure prediction track, which I was not.
It is something I did not prioritize, but it seems to have been genuinely useful in the final modeling stage.

---

## Part 3 — Claude Code

This was my first substantial cheminformatics project using Claude Code.
At the outset I wrote that "the analysis decisions are mine but I will use Claude Code as a development tool," and early on I held to that very literally.
I went cell by cell, describing exactly what I wanted in each one and checking that it ran before moving on.

As I got more experience with the tool, I became less afraid to let it set up large chunks of code.
For code generation it is genuinely excellent, and it makes standing up an analysis fast.
It is also very good at fighting through Python packaging problems, *cough* openbabel *cough*.

By the end I was comfortable describing the analysis I wanted rather than micromanaging the implementation.
It also began contributing ideas worth keeping.
In the [`ml_optimization_3`](2026_06_25_ml_optimization_3.html) notebook I wanted to reduce the bias at the extremes of the activity distribution, and I asked for two experiments on the usual 5×5 CV: oversampling in the extremes, and sample weighting during training.
I added that it could propose other approaches to the same goal, and it suggested the post-hoc calibration.
As the Phase 2 analysis later showed, that calibration produced the best submission I never sent.

I also use it for a language pass on posts before publishing, checking typos and consistency (capitalization of method names, American versus British spelling).
It is very reliable at that.
Getting a useful *first draft* out of it was a slower story.
The first few attempts were close to worthless and I rewrote them completely.
As the series grew and there were more of my own posts for it to read first, the drafts moved closer to how I actually write.
They still need a careful pass end to end, but for the last two posts the draft was a real starting point rather than something to discard.

The honest summary is that Claude Code let me get through more analyses than I would have managed without it, especially in the later part of the challenge once my first consulting contract had started.
But it was not driving the analysis.
The decisions that determined my ranking (which models to try, how to split the data, which submission to send) were all mine.
That said, LLMs are getting better at working through an optimization problem like this one.
For an interesting talk on using LLM agents to guide the design of experiments and submissions in a similar challenge, see the [presentation](https://www.youtube.com/watch?v=tZeOPcRse8M) by Prof. Anthony Gitter for the ExpansionRx challenge.

---

## Part 4 — What the challenge did for the consultancy

When the challenge was announced I had recently gone independent and had not yet started my first contract, so I had a lot of free time.
I joined partly for the modeling challenge and partly because I expected it to generate material for LinkedIn and raise my visibility.
It did.

The website analytics tell a consistent story: LinkedIn is by far the largest driver of traffic, and the blog accounts for more than half of all page visits.
The posts also drew comments and direct messages, and that exposure led to an invitation to the first webinar OpenADMET organized for the PXR challenge.
My presentation there is a short summary of the whole blog series, and you can watch it on [YouTube](https://www.youtube.com/watch?v=RZyPo_ETHJ0).

The repository saw a smaller but real amount of reuse, with a couple of forks and a star.
Modest numbers, but the repo was always meant as a worked example rather than a library.
For the goal I actually set (content and exposure while building a consulting practice) the challenge delivered.

---

## Part 5 — The next challenge

OpenADMET has announced its next [blind challenge](https://openadmet.ghost.io/announcing-openadmets-cyp-inhibition-blind-challenge/), on CYP inhibition.
It starts on August 17, just before I go on holiday.

I plan to take part, and the repository will again be public, but I will not document it at the level of detail I managed for PXR.
I have much less free time than I did in March, and keeping up with both the competition and the blog was already difficult after my first consulting contract started.
My plan is a post covering the work done before the intermediate leaderboard is calculated (September 24), and another at the end of the challenge (November 3).
The notebooks should still be informative on their own, and they will be committed and pushed as I work through them.

There is one thing I want to carry over.
The most useful result of the PXR challenge was learning that most of the differences I was optimizing were smaller than the uncertainty of the evaluation.
For the new challenge I plan to pay close attention to the confidence intervals around the numbers I compute, so I can tell which improvements are worth chasing.
How best to do that before the labels are known is something I will have to work out as I go.

Thank you to everyone who followed this series through all the blog posts, and to the OpenADMET team for running such a transparent challenge.
