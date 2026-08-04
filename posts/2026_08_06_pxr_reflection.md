# PXR Challenge #8: Closing the series

*August 2026*
Tag: blind challenge

---

After 8 blog posts, 7 notebooks, X lines of code, 1 webinar presentation; I use this post to close out my participation in the OpenADMET Blind Challenge.
I am very happy with the end result, ranking 46 out of 10X submissions.
The submission performance was behind the state of the art at Tier 2, but not embarrasingly off base.

Going back to the first post I wrote:
"I also want my work to be useful beyond the final ranking. 
Each step — data analysis, model selection, split strategy, prospective evaluation — will be documented openly. 
The goal is to create a worked example of how to approach a real ML problem in drug discovery. 
I hope it can be an educational resource."

I think there is where a lot of value of my submission comes from. 
As I go through this reflection I want to focus in:
- summary of the optimisation process: what seemed to work and what didn't
- the paths not taken: what I could have done but didn't
- how I evolved my use of Claude Code during the challenge
- the impact of the content in my visibility as an independent consultant
- the next challenge

---

## Part 1: What worked and what didn't

Keeping in mind the caveat from the previous post, that showed most of my submissions were likely within the uncertainty range of the final submission, there were a few things that worked well.
- Ensembling: while its improvements over the best single model were not amazing, the posthoc analysis shows that ensembling is able to reduce the amount of errors, so long as not all models are mistaken on the same set of compounds. The ensemble had the lowest amount of badly predicted compounds (error > 1 unit) out of all individual models
- Hyperparameter optimization: especially important for the tree ensemble models, which went from a MAE value of 0.64 to 0.52 in the 5x5 CV. That is a significant improvements coming from choice of fingerprint and optimizing hyperparameters. Improvements for other models were not as significant over the whole process, with chemprop barely budging from a its baseline score of 0.51

What didn't work so well:
- Pretraining and multitask training: almost anytime I added the single dose and/or counter screen data within the 5x5 CV setting, either in a multitasking scenario or as pretraining for chemprop, the performance decreased. 

---

## Part 2: The paths not taken

As with every project with a deadline, you first think of things you can do and then you prioritize.
Some things you thought of end up not at the end of the list and are not done.
There are also things you didn't think of.
In my case there are two major things I think would have improved the model, based on reports from top performing teams, that I didn't pursue due to lack of time or expertise.

- Better use of additional data: this comes in two tracks. On the one hand, while I tried to make use of both single dose and counter screen data, most of the time I failed at it. In the final submission, the single dose data is not used at all and the counter screen data is only used to filter non-selective compounds out of the training data. I still think that there was a better way to include this data into the analysis to push performance and I am eager to see future OpenADMET seminars from top teams to see what exactly they did. The other track of this is that I never checked public PXR data to add to the analysis. While public data would have been noisy, it might have helped calibrate and/or filter training compounds. I don't think it would have worked well to just add the public data to the competition data, but maybe as an additional task on multitasking. Finally, I could have used Phase 1 data for the final submission and I never thought of including it in the training of the final ensemble (duh!). 

- Use of 3D information: this was likely easier for those participant that were joining the structure prediction track, which was not my case. But in the recent webinar I presented in, OpenADMET shows a table they are preparing where almost all of top10 participants used some form of 3D information coming from docking and/or cofolding results. This is not my area of expertise and as I was not participating in the structure prediction track it is not something I prioritized, but it is something that seems to have been useful to the best teams.

---

## Part 3: Claude Code

This was my first big project in cheminformatics where I used Claude Code.
I said at the beginning "The analysis decisions are mine but I will use Claude Code as a development tool.".
And at the beginning I was very cautious, almost going cell by cell with a very clear description of what I wanted in that cell and making sure it ran before continuing.
Over time, as I used the tool in this project and my consulting work, I have grown more confident in what it can do.
For code generation, it is an amazing tool that makes it very easy to quickly set up an analysis.
It is also amazing at working through issues installing Python packages (I am looking at you, openbabel).

Towards the end, I felt more confident in describing the analysis that I wanted to run, rather than micromanaging it to write cell by cell.
I also found it would sometimes provide good suggestions to add to the analysis.
As an example, in the ml_optimization_3 notebook, I wanted to try ways to reduce the bias in the extremes of the activity distribution.
I asked it to set up two experiments on the typical 5x5 CV: one looking at oversampling within those extremes and one looking at using some form of sample weighting during training. I wrote that it could suggests other approaches to this goal and it suggested the posthoc calibration.

In addition, I typically use Claude Code for a language check on blog posts before publishing, asking it both for typo correction and consistency in language (capitalization of techniques, use of American or British english).
It works very well at those.
I also tried several times to make Claude generate a useful first draft of the blog post based on the notebook.
The first few times it was basically worthless, I had to rewrite it the whole thing.
But over time, especially as more posts were available for it look through first, it became better at writing something closer to my writing style.
It still needs a careful pass over the whole blog post before final checks, but it was very useful in the last two blog posts.

---

## Part 4: Impact of the challenge on my consultancy business

When the challenge was announced I had recently started to work as an independent consultant.
I had not yet started my first contract so I had quite a bit of free time.
I thought joining the challenge and posting about it would provide me with content for LinkedIn posts to increase visibility.
And it did help.

Looking at the analytics from the website, LinkedIn is the major driver of traffic. 
And looking at all pages, the blog accounts for more than half of visits. 
In addition, I did get some comments and DMs regarding the content in LinkedIn, leading to be invited to the first webinar that OpenADMET organized for the PXR Challenge.

In addition to the LinkedIn exposure, the repository itself saw a small amount of reuse and attention, with two forks and one star. 
I would say it did serve the goal of providing content and generating exposure.

---

## Part 5: The next challenge

OpenADMET has already announced the next [challenge](https://openadmet.ghost.io/announcing-openadmets-cyp-inhibition-blind-challenge/) focused on CYP inhibition. 
It starts on August 17, just before I go on holiday.
I plan to take part on the challenge and still keep my repo for it public, but I will likely not be as detailed with the blog posts and progress updates as I was for the PXR challenge.
I have much less free time now than I had when I started the challenge, and it has already been a bit of challenge to keep up with the blog and the competition after starting my first consulting contract.
I will likely make a post with work I have done before the intermediate leaderboard is calculated (September 24) and then at the end of the challenge (November 03).
But I still expect the notebooks to be informative in and of themselves and those will be commited and pushed as I work on them. 