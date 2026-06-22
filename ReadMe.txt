Starter ReadMe for Project 3

Project Desicions:
Chosen Subject: true movies subreddit. I wanted to do this sub-genre because i am a fan of movies in general and would like to do research on the discourse featured here. I think it could be useful to the community because it can differentiate which posts are based on evidence and which posts to disregard.

Taxonomy:
analysis — the post makes a structured argument backed by statistics, historical comparison, or tactical observation. Evidence is specific and verifiable.
hot_take — a bold, confident opinion stated without supporting evidence. The claim might be true, but the post asserts rather than argues.
reaction — an immediate emotional response to a specific event. Little to no argument — the post is expressing a feeling in the moment.

Examples:
-analysis

    -"I think it's overhyped. But it's also still very good.

    Inde Navarette gave a star making performance and was genuinely terrifying at times. And the acting performances all around were better than average.

    However, I think the story could have been told in a tight 90 minutes as opposed to 1 hour and 50. Certain plot beats were repeated to the point where I was like "Okay we get it."

    -"It was nothing groundbreaking but I would definitely recommend it. And there were enough WTF moments here that watching it with a group of friends is a blast.

    I'd give it a solid "B."

    -"I found the writing pretty sloppy as well and it looks like there’s a lot of mental gymnastics to justify actions that are pretty illogical. 

    Technically anyone can do almost anything, I could slap myself randomly just to prove I have free will, that doesn’t mean it’s informative about the characters. 

    We see a lot of contradictory stuff without the WHY. Like why is bear SO passive about everything but also so predatory and entitled? And he’s scared of Nicki but won’t even offload her to a psych ward? And he waits quite a while to call the hotline or try to get another wish stick. After the campy montage it never seems like he’s getting anything positive out of the relationship and its so short I couldn’t figure out why he was just rolling with the dead cat and stuff. Plus he doesn’t seem to care at all about who she is as a person so I couldn’t tell why he wouldn’t try to bail when things got inconvenient.

    The only time someone did something natural was Ian wishing for the billion dollars lol. "

-hot_take
    -"Most horror isn’t as sadistic as Obsession is with the way it revels in the horror of her lost autonomy.I don’t think it’s necessarily in bad taste, but it borders on it at times. It’s definitely trying to provoke, and can come across as salacious at times."

    -"I feel seeing bears purely the villain speaks more with people abilities to empathize with a genuinely socially stunted loner, not that he definitely wasn’t being a fucking pos weirdo once he realized it’s probably because of his wish, but otherwise he wasn’t really the “man bad” archetype as people seem to describe him as, I did see him as pretty nuanced"


-reaction:
    -"Obsession was supremely overrated. I enjoyed Backrooms more but I also thought Backrooms was somewhat overrated. Both films were ok, neither superb."

    -"Citizen Kane is much more entertaining than that cliche gives it credit for being."

Data Collection:
I collected my data only from the r/TrueFilm subreddit, specifically from the comments of posts and sometimes the posts themselves. I lableled them all by hand ang got way more efficient the more I did it.

Difficult labels to explain:
Some labels were difficult to explain because they include multiple aspects of each classification, which I posit may have made the model confused on all the classifications and the context that wasn't included in the posts.

Model setup: I used the model setup specificed in the   google colab and made no changes to the model itself, which I believed will lend to good results. I made changes to the epoch hyperparamter and the learning rate a well which ended up making my model better


# ==========================================================================
# Evaluation Report
# ==========================================================================

## Overall Accuracy

| Model                                | Accuracy   |
|--------------------------------------|------------|
| Baseline (prompted classifier)       | 0.677      |
| Fine-tuned (distilbert-base-uncased) | 0.516      |
| Improvement                          | **-0.161** |

Test set: 31 examples. Fine-tuning REGRESSED performance by 16 points. The
fine-tuned model is meaningfully worse than the baseline prompt, and the
sections below show exactly why.

## Per-Class Metrics — Baseline

| Label        | Precision | Recall | F1   | Support |
|--------------|-----------|--------|------|---------|
| analysis     | 0.69      | 0.92   | 0.79 | 12      |
| hot_take     | 0.83      | 0.42   | 0.56 | 12      |
| reaction     | 0.56      | 0.71   | 0.62 | 7       |
| accuracy     |           |        | 0.68 | 31      |
| macro avg    | 0.69      | 0.68   | 0.66 | 31      |
| weighted avg | 0.71      | 0.68   | 0.66 | 31      |

## Per-Class Metrics — Fine-Tuned

| Label        | Precision | Recall | F1   | Support |
|--------------|-----------|--------|------|---------|
| analysis     | 0.73      | 0.67   | 0.70 | 12      |
| hot_take     | 0.42      | 0.67   | 0.52 | 12      |
| reaction     | 0.00      | 0.00   | 0.00 | 7       |
| accuracy     |           |        | 0.52 | 31      |
| macro avg    | 0.38      | 0.44   | 0.40 | 31      |
| weighted avg | 0.44      | 0.52   | 0.47 | 31      |

The headline: the fine-tuned model scores **0.00 on every reaction metric**.
It never once correctly predicted `reaction`. Compared to the baseline,
`reaction` F1 dropped from 0.62 to 0.00 and `hot_take` precision collapsed
from 0.83 to 0.42. Only `analysis` is roughly comparable across the two models.

## Confusion Matrix — Fine-Tuned Model (written out from confusion_matrix.png)

Rows = true label, columns = predicted label.

|                   | pred: analysis | pred: hot_take | pred: reaction | Total |
|-------------------|----------------|----------------|----------------|-------|
| **true analysis** | 8              | 4              | 0              | 12    |
| **true hot_take** | 3              | 8              | 1              | 12    |
| **true reaction** | 0              | 7              | 0              | 7     |
| **Total pred**    | 11             | 19             | 1              | 31    |

Read the bottom row: the model predicted `hot_take` **19 times out of 31**
(61% of all predictions) and `reaction` only **once**. `hot_take` is acting
as a magnet that swallows the other two classes.

## Error Analysis

### Which labels are being confused?
The errors are NOT spread evenly — they are directional and concentrated:

  - reaction -> hot_take: 7 of 7 reactions (100% of the class) became hot_take.
  - analysis -> hot_take: 4 of 12 analyses leaked into hot_take.
  - hot_take -> analysis: 3 of 12 hot_takes leaked into analysis.

So there are two distinct broken boundaries:
  (1) reaction vs hot_take — completely broken, one direction only.
  (2) analysis vs hot_take — bidirectional confusion at the evidence line.

### Example 1 — reaction misread as hot_take  (the dominant failure)
Text:      "Totally, not even in the conversation with heat. Completely forgettable"
True:      reaction      Predicted: hot_take (conf 0.48)

Why it failed: This is a short, low-confidence reply that expresses a FEELING
("completely forgettable") with no thesis and no evidence — textbook reaction
per our taxonomy. But it is phrased as a flat declarative judgment, and the
model has learned to map "confident declarative opinion" onto hot_take. It has
no feature that captures "this is just a vibe with no argument." This is a
DATA problem, not a labeling problem: with only 7 reaction examples in the
test set (and presumably few in training), the model never learned a stable
reaction representation, so it defaults the whole class to its nearest
neighbor, hot_take. Fix: substantially more reaction training data, especially
opinionated/declarative reactions that LOOK assertive but contain no thesis.

### Example 2 — hot_take misread as analysis, with HIGH confidence
Text:      "...these are technically plot holes, but focusing on that is to miss
           the point of the movie, which is: what does it mean to be human.
           Like, there's no reason at all for replicants to not be ob..."
True:      hot_take      Predicted: analysis (conf 0.83)

Why it failed: The post names concrete film concepts (plot holes, replicants,
theme) so the model treats topical specificity as if it were verifiable
evidence and a structured argument. But the post does not ARGUE — it asserts
"you're missing the point" and moves on. The model has learned a shortcut:
"mentions specific film details => analysis." The dangerous part is the 0.83
confidence — the model is confidently wrong, which means the boundary itself
(specific-but-unsupported assertion vs. specific-AND-supported argument) was
never taught. Fix: add hot_take training examples that are rich in film
specifics but lack a structured argument, to break the "details = analysis"
heuristic.

### Example 3 — analysis misread as hot_take, driven by tone
Text:      "Great sarcasm but you're missing the point. I'm not reviewing a movie
           that isn't out yet. I'm looking at 25 years of Nolan's DNA vs 40
           years of Lean's and how they translate to the Odyssey..."
True:      analysis      Predicted: hot_take (conf 0.61)

Why it failed: This is a genuine structured comparison (Nolan vs. Lean across
decades, mapped onto a specific adaptation) — analysis by definition. But it
opens combatively ("Great sarcasm but you're missing the point"), and the
model keys on aggressive/confident TONE as a signal for hot_take, overriding
the structural argument that follows. This is the mirror image of Example 2:
there the model over-weighted topical nouns; here it over-weights tone. Both
show the model is classifying on surface style, not on the evidence/structure
criteria the taxonomy actually defines.

### Is this a labeling problem or a data/prompt problem?
Primarily a DATA problem. The labels themselves are applied consistently with
the taxonomy (reactions are feelings-without-thesis, the hot_takes do lack
structured argument). The model fails because:
  - The classes are tiny and imbalanced (reaction = 7), so the minority class
    collapsed entirely (0.00 F1) and the model fell back to the hot_take magnet.
  - The hard boundaries (reaction/hot_take, analysis/hot_take) hinge on
    abstract criteria — "is there a thesis?", "is the evidence verifiable and
    structured?" — that are not learnable from a handful of short examples, so
    distilbert latched onto surface proxies (declarative tone, presence of
    film-specific nouns) instead.
That said, several reaction/hot_take cases are genuinely borderline under our
own thesis/no-thesis rule, so a secondary audit of the reaction gold labels
for consistency is warranted.

### What would need to change to fix it?
  1. More data overall, and rebalance the classes — reaction is starved.
  2. Targeted hard-case examples that break the surface shortcuts: reactions
     that sound assertive (vs hot_take), hot_takes loaded with film specifics
     (vs analysis), and analyses written in a combative tone (vs hot_take).
  3. Tighten/operationalize the reaction-vs-hot_take definition (thesis vs.
     feeling) and re-audit those gold labels for annotation consistency.
  4. Add conversational context for reply turns ("Totally", "Great sarcasm
     but...") — many failures are replies whose true label only resolves
     against the parent comment, which the model never saw.
  5. Given the -0.161 regression, the prompted baseline is currently the
     better classifier; fine-tuning should not ship until the data issues
     above are addressed.


Markdown table:

True label

    Analysis    8   4   0

    hot_take    3   8   1

    reaction    0   7   0   

                analysis    hot_take    reaction
                            Predicted label


Sample Classification:     


Text:      These are, technically I guess, "plot holes," but focusing on that is to miss the point of the movie, which is: what does it mean to be human. Like, there's no reason at all for replicants to not be ob...
True:      hot_take
Predicted: analysis  (confidence: 0.83)

Explanation:  This is a decent prediction because it does say "plot holes" in the text which is a common description of movie weaknesses but it fails to back it up at all by specifying the movie or anything specific meaning that there is no clear evidence here,

Text:      Yes I agree completely! It’s like being taken along on a rollercoaster, obviously that is a true experience with value but it doesn’t teach you anything to move forward with your life
True:      reaction
Predicted: hot_take  (confidence: 0.56)

Explanation: I saw this as a reaction because the thesis is describing the users' emotional reaction to the given film. But, this was misclassified because the model misinterpreted the text as a thesis statement that describes a movie which may have been missing from my data


Text:      I guess we are ignoring the fact that all the female characters in HEAT are paper thin. I love HEAT but boy, are women an afterthought. Halle Berry is great in CRIME 101 (though the movie lets her dow...
True:      hot_take
Predicted: hot_take  (confidence: 0.76)

Explanation: This example was correctlty identified by the classifier, even though it includes the users' reaction it states an explicit thesis ("all female characters in HEAT are paper_thin).

What I intended vs What my model captured:
I intended for my model to differentiate between three different types of posts depending on what the posts are saying and how much evidence there is to back it. What my model captured was differnet than that, the worst part of the model was due to the specific context of the posts, especailly the shorter ones. It got the general jist of what I wanted to capture however, it was bogged down by not knowing the specific criteria required for analysis and a lot of the post had qualities of all three criteria. Eg: a reaction, followed by an analysis

Spec Guides:
The spec helped guide me by helping me determine the criteria for the classifications and even gave the classifiers that worked for my chosen topic. It really helped me think about how to classify posts.
My implementation deviated from my spec by changing some of the variables used to train the model I changed the learning rate the number of epochs and some other minor variables to help find the isssue with my training model

AI Tool Plan:

Label Stress testing: I will give Claude Code some examples of edge cases, then ask it to generate 5-10 more so i can specifically test them on my model and see the accuracy

Annotation Anallysis: I will try to do speed up the process by giving claude code some examples that I have not annotated yet. I will give it my taxonomy definitions, edge cases and examples then ask it to try to come up with the correct taxonomies. I will validate myself and see if it can succeed in classifying before implementing.

Failure Analysis: After the models run I will hand off the wrong prediction batches to claude code and see if it can identify any patterns in the wrong predictions.    
