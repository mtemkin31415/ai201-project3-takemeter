Community: Creating a fine-tuned model for the True Film community, this is a good fit for a classification model because the community is full of opinion either supported by evidence or not at all. This makes it so that a classifier can distinuguish if a post has enough evidence to have merit or not, which may be useful for the memebers of the community.

Labels: See ReadMe.txt

Taxonomy:
analysis — the post makes a structured argument backed by statistics, historical comparison, or tactical observation. Evidence is specific and verifiable.
hot_take — a bold, confident opinion stated without supporting evidence. The claim might be true, but the post asserts rather than argues.
reaction — an immediate emotional response to a specific event. Little to no argument — the post is expressing a feeling in the moment.


Edge Cases: It may be difficult to differentiate between a hot_take and a reaction post as both posts require that not much evidence is used to back it up. I can differentiate by having a rule:  Hot_take if the user has a thesis or a general point that they are asserting without any evidence and a reaction if the user is only talking about how a film made them feel and has no thesis besides that point.

Data Collection Plan: I will browse the r/TrueFilm subreddit and look at posts and comments to use as data. If one example is underrpreesented I will collect more data for that taxonomy.

Evaluation: I will use Accuracy, Precision, Recall and F1-Score. I won't use just accuracy because  that doesn't differentiate between false positives and true negatives, so these will give me a better, more dynamic view of my models' success.

Definitiion of Success: I will think that my tool would be successful if it is more than 95% accurate and can clearly differentiate taxaonomies based on my definitions and edge cases. I will specifically detect my edge cases too and see if they are accurate  .


AI Tool Plan:

Label Stress testing: I will give Claude Code some examples of edge cases, then ask it to generate 5-10 more so i can specifically test them on my model and see the accuracy

Annotation Anallysis: I will try to do speed up the process by giving claude code some examples that I have not annotated yet. I will give it my taxonomy definitions, edge cases and examples then ask it to try to come up with the correct taxonomies. I will validate myself and see if it can succeed in classifying before implementing.

Failure Analysis: After the models run I will hand off the wrong prediction batches to claude code and see if it can identify any patterns in the wrong predictions.    


Hard Edge Cases:
"Going to just add to your comment, Maybe Kamikaze Girls?"

in the context of the conversation between comments this is clearly a hot_take as it is adding something to the previous commentators' opinion. But with no context this could only be a reaction as there is no concrete thesis the comment is pushing

"Chris Nolan’s Odyssey is nothing like the source material at all. Dogs**t adaptation. From what I’ve seen, it’s only like 2 mins long and just repeatedly cuts from scene to scene.

What were they thinking??"

This is clearly a sarcastic and emotional reaction to a previous comment. But it does assert a hypothesis so I marked it as a hot_take due to lack of specific evidence

"Nolan is my favorite filmmaker but this does touch on something that is a legit criticism of his: I’ve never given a  about a character in any of his movies. Even Interstellar, where there is emotional resonance, isn’t due to the characters but moreso the movie as a whole, if that makes sense. I can get over this, don’t really care, but I can see how someone who wants to emotionally connect with a film but dislike him."

This comment can either be an analysis or a reaction. I choose to label as a analysis because it does mention specific evidence from interstellar, even though very briefly



Baseline Test Results:   
    🎯 Baseline accuracy: 0.677  (evaluated on 31/31 parseable responses)

Per-class metrics (baseline):
              precision    recall  f1-score   support
    
    analysis       0.69      0.92      0.79        12
    hot_take       0.83      0.42      0.56        12
    reaction       0.56      0.71      0.62         7

    accuracy                           0.68        31
   macro avg       0.69      0.68      0.66        31
weighted avg       0.71      0.68      0.66        31


It struggled a little bit on all 3 labels and had an okay accuracy/precision. This may be because there are no set way for the model to handle the edge cases unlike the fine-tuned model, which will learn how to deal with edge cases. 


