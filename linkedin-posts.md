2026-09-15
Leaders from OpenAI and Anthropic are suggesting the AI field should slow down. This comes after several months of models breaking containment and hacking external servers, and of the US government imposing export controls on frontier models. Dario Amodei and Sam Altman argue we need to decelerate and focus on alignment to protect against future harms.

My reaction is cynical, the same as it was to the earlier stories. I think that the request serves two goals that have little to do with alignment. First, framing it as a safety concern puts pressure on politicians to regulate Chinese open weight models that are a few months behind their frontier models. Second, it cuts training costs. Both would likely improve their valuations ahead of their upcoming IPOs, which is what most of this marketing is ultimately for.

What I am genuinely curious about is what a deceleration would do to the data center boom. I don't know the training-to-inference compute ratio at OpenAI or Anthropic, but I expect training is a large share of it, and a large part of the justification for building more data centers. Slow the training down and a lot of that capital expenditure stops making sense. My hope, selfishly, is that it ends the RAMpocalypse and the electronics market gets back to something like normal.

Am I being too cynical? Do you think the risks OpenAI and Anthropic claim are realistic in the near future? Let me know in the comments.



2026-09-11

OpenADMET started their new competition on CYP inhibition in mid-August. Between being on holiday and returning to Seville in 40°C heat (over 100°F), it has taken me some time to get started.

As in the previous competition, I am making all the code available in an open repository (https://github.com/adlvdl/cyp_competition), but I won't be writing as many blog posts this time. So far I have focused on getting some simple baseline models running and making sure I could submit without issues.

The competition has two tracks, regression and classification. On the regression track my overall performance is being dragged down by CYP2D6, which seems to be the hardest of the four CYPs to model, judging by the discussions on Discord. On the classification track my position on the live leaderboard is better than my 5x5 CV results led me to expect.

Next up: explore the provided datasets in more detail and adapt my modeling strategy based on what I learned in the PXR competition

2026-08-05
Eight blog posts and seven notebooks later, I have written the last post on my participation in the OpenADMET PXR Blind Challenge. I finished at rank 46 of 103 (Tier 2). Behind the state of the art, but not embarrassingly off base.

This post is the retrospective: what worked, what didn't, and what I never got around to. Ensembling did not lower the mean error much, but it made the badly wrong predictions rarer, which I think is valuable. Hyperparameter optimization paid off for the tree models and did essentially nothing for the neural ones. Pretraining and multitask training didn’t work for me at all.

What I never got working was the auxiliary data. Every attempt to use the single-dose and counter-screen measurements inside cross-validation made things worse, and the top teams clearly managed it. Nearly all of them also used 3D information, which I never touched. And one plain oversight: I could have added the Phase 1 labels to the final training set once they were released, and it simply never occurred to me until the challenge was over (duh!).

Thank you to everyone who followed the series through all the blog posts, and to the OpenADMET team for running such a transparent challenge.

Link: https://www.delavega.ai/posts/2026_08_05_pxr_reflection.html

2026-07-31
I finally published the post-mortem of my final submission to the OpenADMET PXR Blind Challenge, reusing the same paired-bootstrap comparison the organizers used to rank participants.

The results were disappointing. Seventeen submissions, several months of work — hyperparameter optimization, ensembling, data augmentation, dataset filtering — and sixteen of them are statistically indistinguishable from each other on the Phase 2 test set. That includes the untuned CheMeleon baseline I trained in an afternoon back in April.

Worse: I used the Phase 1 labels to pick between my three final candidates, and picked the worst of the three. It cost me five places on the leaderboard.

My final reflection on the challenge, expanding on what I covered in the webinar, is coming next week. And none too soon, as OpenADMET have already announced the next competition for mid-August. No rest for the wicked.

Link to the blog: https://www.delavega.ai/posts/2026_07_30_unblinded_phase2_analysis.html

2026-07-30
OpenADMET has published the recording of the webinar where I presented my experience with the PXR Blind Challenge. I heard there were technical issues with Zoom and some people couldn’t join the webinar, so I am very glad the organizers uploaded the webinar so quickly for those who couldn’t join. If you have any questions about my section of the presentation, feel free the ask in the comments or reach out in a DM. Thanks to the organizers for the opportunity to present and for the challenge itself.

Link: https://www.youtube.com/watch?v=RZyPo_ETHJ0

July 2026
The results of the OpenADMET PXR competition are in and my final model falls into Tier 1! Tier 1 models are not statistically significantly different from the best submission. My final performance was 0.4573, better than my last notebook analysis suggested (which showed MAE of 0.4916 on the unblinded Phase 1 set).

I think the organizers have been too strict with the model comparisons. The best model (matcha-croissant, great name!) has a final MAE of 0.4061. The OpenADMET team made a small app available to compare submissions, and I compared mine to the best model. The image shows the bootstrap comparison results. Out of hundreds of bootstrap comparisons, my model outperformed matcha-croissant on only 3 occasions. Apparently, that was enough to land in Tier 1.

With the final part of the test set unblinded, I will be working on an analysis of the results similar to what I did at the end of Phase 1. I also want to write a retrospective post about the whole experience. Stay tuned!

2026-06-25
New blog post: the one where I tried five fixes for my PXR prediction models and most didn't work.

After the OpenADMET Phase 1 labels were unblinded, I had two clear problems to chase. Every model regressed to the mean at the activity extremes (overpredicting inactive compounds, underpredicting the rare potent ones, by up to a full log unit in the hit zone), and I still hadn't managed to use the extra assay data the organizers released.

I tested five remedies under the same 5×5 cross-validation as previous posts, with only the best CV-chosen strategy ever applied to the now unblinded Phase 1 test set. The honest result: almost nothing moved the needle. Most techniques increased MAE overall even if they reduced bias among highly active and inactive compounds. At best, data-based remedies reduced MAE by tiny amounts.

But the reasons they failed turned out to be the interesting part:
- The bias is conditional on activity, so a single global calibration map is structurally unable to remove it.
- Re-weighting doesn't remove error, it relocates it from the dense middle to the sparse extremes; whether that trade is worth it depends on your use case, not your MAE.
- Oversampling isn't model-agnostic even when it's mechanically applicable: it massively hurt TabPFN, increasing bias in the extremes where we were oversampling.
- A real chunk of the residual error is just irreducible label noise (estimated from 5 of the new semi-pure data points that have same structure as dose response data points) that no model can touch.

This is the last post I'll spend trying to improve the submission because the competition closes on July 1. I still expect to post an analysis on the final unblinded test set and my thoughts on the whole event.

Link to the blog: https://lnkd.in/e33giV2Z

2026-06-18
New blog post in the PXR series: Unblinding the Phase 1 test set. Now that OpenADMET unblinded part of the test set, I wanted to answer: where did the models do well and where did they fail? The main results was that the failures I saw in the models turned out to be systematic:

– Every model regresses to the mean. They overpredict inactive compounds and underpredict the handful of genuinely potent ones. For a screening campaign that is the most consequential bias possible: the compounds you most want to find are called less active than they really are.

– About half the test compounds are activity cliffs relative to their nearest training neighbor: structurally very similar, but a log unit or more apart in potency. Those were predicted roughly twice as badly as the rest, and always overpredicted, because the model leans on the close analog it already knows.

- Chemeleon (my cross-validation champion for most of Phase 1) generalized the worst of the group with the largest cumulative bias of all models

The full write-up is on the blog. Next I want to calibrate the bias away and make better use of the other assay data before the final phase closes in July.

https://lnkd.in/eXCDSP-T

June 2026
Phase 1 of the OpenADMET PXR competition closed last week. The organizers unblinded one half of the test set and released an interim leaderboard based on the full test set, which will stay fixed until the end of the competition.

The interim leaderboard brought a pleasant surprise. The live leaderboard during Phase 1 was based only on the half that has now been unblinded; the other half was completely hidden. My best submission had an MAE of 0.495 and ranked 78 of 252 (within the top third of the ranking) on the live leaderboard. On the interim leaderboard, my MAE is 0.470 and my rank is 83 of 338 (within the top quarter). My models generalized better than I expected, which I am happy with.

I am now analyzing the unblinded data to understand where the models failed. The biggest prediction errors are concentrated among lowly active compounds, which is not surprising as they are activity cliffs (structurally similar to highly active compounds in the training set but with very different potency). Other teams have reported similar patterns.

As always, I will post a write-up once the analysis is done and push the code to the competition repository. There is still work to do before the competition closes. Thanks again to the organizers as I have been enjoying the experience.

May 2026
I recently went self-employed, and one of the hardest things to figure out was my hourly rate. Putting a monetary value on your skills and experience is genuinely difficult. It's very easy to undersell yourself.

My first instinct was to work backwards from the gross yearly salary I thought I could achieve on the market. Assume 250 working days a year, 8 hours a day, divide, and there's your hourly rate. Simple.

It's also wrong. I was lucky that a mentor pushed back hard on this reasoning and helped me see all the hidden costs I wasn't accounting for.

When you're self-employed, the benefits disappear. No paid vacation. No employer-subsidized health insurance. No unemployment safety net if work dries up. No severance if a contract ends early. On top of that, you're unlikely to have work filling every week of the year consistently; and you'll probably need to pay an accountant to handle your taxes. All these costs need to be accounted for.

I'll admit I still wince a little when I quote my rate out loud. It feels like a new flavor of the imposter syndrome I've been wrestling with since my early days in science. But I expect it'll get easier as I gather success stories in this consulting chapter. Honestly, I'm looking forward to the challenge.

2026-04-22
New post in my OpenADMET PXR challenge series: benchmarking ML baseline models and my first submission.

A few things worth sharing:

Scaffold splits don't always help. When a dataset is structurally very diverse, scaffold-based cross-validation are too similar to random splitting. All three splitting strategies I tested — random, scaffold, and pseudo-temporal — gave almost identical train/test similarity profiles. Worth checking before assuming scaffold CV gives you a harder benchmark.

How you compare models matters as much as which models you run. I followed the statistical framework from a recent J. Chem. Inf. Model. paper (DOI: 10.1021/acs.jcim.5c01609) by the PolarisHub team on rigorous ML model comparison, which uses nested cross-validation and repeated-measures statistics rather than a single train/test split. One concrete result: XGBoost and Random Forest are statistically indistinguishable on most metrics, despite XGBoost consistently looking better in raw numbers. Easy to over-interpret without the right framework.

Pre-trained graph neural networks win convincingly. CheMeleon (a Chemprop model fine-tuned from pre-trained weights) came out significantly ahead of everything else. The ranking of tested models: CheMeleon > Chemprop > tree-based models > baselines.

Always look at multiple metrics to gain the full picture of the predictions of a model. There's a notable gap between cross-validation and leaderboard performance. R² drops from 0.62 to 0.34, but MAE drops “only” from 0.50 to 0.574. The big drop in R² could be due to a big shift in activity distribution.

Full post + interactive notebook: https://lnkd.in/exXvvg9b

2026-04-15
New post in my PXR challenge series: this one covers the data exploration before any modeling.

The analysis spans chemical space visualization (UMAP, t-SNE, similarity networks), activity cliff detection, and a closer look at how similar the test set is to the training data. A few things came out differently than I expected:

The chemical space is highly diverse, both UMAP and t-SNE show a single diffuse cloud with no dominant scaffold families. More surprisingly, high-potency compounds (pEC50 > 5) are scattered throughout that space rather than concentrated in a small number of regions.

The test set also turned out to be more diverse than I had assumed. I had mentally filed it as an analog series based on the OpenADMET blog posts, but the scaffold analysis shows the test compounds are structurally spread out even though most of them have a highly similar compound in the training data.

Overall I'd describe the dataset as mildly SAR discontinuous; activity cliffs exist but make up only 4–12% of similar pairs depending on how you define similarity.

Full post with plots and links to the marimo and HTML notebooks to allow some interactive exploration of the dataset: https://lnkd.in/eH__xPEX

April 2026
I've been participating in the OpenADMET PXR blind challenge, and wanted to share a quick update.

After some participants (including myself) flagged inconsistencies in the datasets, the organizers reviewed the reports, and updated the datasets on HuggingFace. They have also published a detailed document explaining every change and the steps they're taking to prevent similar issues in future challenges.

Data curation is genuinely hard. The way the organizers responded, transparently and quickly, is the kind of rigorous response that should be more common. Kudos to the OpenADMET team.

April 2026
It has been a year since I was laid off from BMS, following a large reorganization of its research division. Since then I have seen several other organizations go through layoffs. While it seems to have slowed down this year, I recently saw a large series of layoffs from Evotec.

I was lucky to have a very good support network around me, and to not have anyone financially dependent. I was able to take six months off (spring and summer of 2025), the first time I had allowed myself a big break between jobs. Even after the break, I have taken my time when looking for new opportunities and the time to develop myself in different areas.

The break was important for me. It gave me time to reset and rethink what type of opportunities I was looking for. I decided early on I didn’t want to relocate, and living in Seville that meant focusing on remote possibilities. I have had positive contacts with several companies and will soon start my first contract as an external consultant in the chemoinformatics and ML/AI space.

If you are currently between jobs, my main advice is to be kind to yourself and take whatever time you can based on your personal and financial situation. Lean on your friends and family, and find whatever support or benefits you are entitled to. You will come out stronger from this experience. I know I did.

If you are a company looking for external expertise in chemoinformatics and AI/ML applied to drug discovery, have a look at my profile and webpage (https://www.delavega.ai/). Reach out so we can discuss opportunities to work together.

2026-03-26
I am entering the OpenADMET blind challenge for predicting human PXR induction. PXR is a nuclear receptor that, when activated, upregulates CYP3A4 and other drug-metabolizing enzymes. PXR activation is a common and costly failure mode in drug development. Predicting which compounds will trigger this from structure alone is genuinely difficult.

My plan is to document my work openly in posts here, in more detail in blog posts and in an open code repository. My goal is to provide a worked example of how to approach a real ML problem in drug discovery.

Full post with details my planned workflow and setup here: https://lnkd.in/ebwBEw93

Looking forward to the beginning of the challenge next week.

March 2026
A recent editorial on JMC about AI in drug discovery starts with “Artificial Intelligence (AI) has emerged as a transformative technology in contemporary drug discovery and development, fundamentally reshaping the processes by which novel drugs are identified, optimized, and translated into clinical applications”. This reminded a discussion I had with my previous manager, who asked me if chemoinformatics had fundamentally changed in the years since AI was introduced to the field.

My answer then was no, and I still stand by it. In my opinion, the core tasks have not changed, just the tools we use to achieve those tasks. In drug discovery, it has always been about finding the correct drug as quickly and safely as possible. With more data and better algorithms we are better at this, but I don’t think our mission has fundamentally changed.

The broader impact of AI in this field is a realignment of the value that a chemoinformatician brings to the table. Technical skills like coding will become less valued as AI becomes better at generating useful code. What will make the difference will be critical thinking and decision making, as that is something I don’t think AI is able to do. At least not based on the current LLM technology.

October 2025

I recently read a very interesting paper (https://lnkd.in/euVYkUvk) from the team at Polaris on how best to compare ML models. Their main point is that you should do statistical testing on a distribution of performance values, for example, taken from a 5x5 cross validation experiment. I decided to implement this by asking a classic question in chemoinformatics: which fingerprint is better to train ML models? 

I trained a large set of activity prediction models based on the Papyrus dataset, both as classification and regression. I tested 4 different fingerprints implemented in RDKit: Morgan, RDKit, Torsion and MACCS.

The result? At first not very surprising: MACCS is almost always the worst fingerprints, while Morgan and RDKit have generally better performance values. But once you start testing for significant differences? It doesn’t matter in most cases. Outside of MACCS, all three other fingerprints are not significantly different in most models trained.

Next steps? I want to compare traditional, fingerprint-based models against chemprop, which seemed generally better when I was testing it in my last job. But is the difference significant? I hope to have answers soon.

I want to thank the team at Polaris and Pat Walters for the large amount of code they make available. It made this work easy to implement.

More details about the methodology and results here:
https://lnkd.in/evdFA4eh

March 2025
Today was my last day at BMS. I leave convinced of the impact that AI\ML can have in drug discovery, but only when it is driven by a clear vision, integrated into a meaningful strategy, and tracked through informative metrics. I am very proud of all the work my team did to demonstrate this in the context of using ML to drive the selection strategy for a proteomics screening campaign.

A lot of great talent is leaving BMS today but I wanted to give a shoutout to Alexander B. Dürr, Mirko Torrisi, Nelson Monteiro, PhD and Giorgio Tamò. They have been great colleagues and I am sure they will go on to do great things in the future. I also wanted to thank all of my colleagues at Informatics & Predictive Science and Discovery & Development Sciences organizations, all the great people at the CITRE site and the PRIDE colleagues from the Madrid office. 

As for me, I look forward to taking some time off to develop myself further and to reflect on how best to push my career to the next level. I am open to chat with companies looking to develop ML that push biomedical research forward.