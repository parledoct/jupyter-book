# Distill then decide

When we're tasked with first reading some text and then answering a multiple choice question based on that text, we typically don't need to retain all the information in the original text to answer the question. In fact, knowing the question ahead of time also helps you efficiently read the text. Let's say the multiple-choice question is:

- Which of the people mentioned in the text are in high school?
  1. Molly
  2. Molly and James
  3. James and Hanna
  4. Hanna
  5. Molly, James, and Hanna

And the text is:

> Molly, James, and Hanna are three siblings who live in Sydney, Australia. Molly is 24, James is 16, and Hanna is 14. Molly likes dancing and music so on the weekends she goes clubbing, but James and Hanna prefer to stay at home and read comic books.

Given the question, how did you process the text? You probably distilled the text down to the entities of interest (i.e. people) and information about the entity relevant to making your decision (e.g. age, not where they live).

```{list-table}
:header-rows: 1

* - Person
  - Age
  - Prediction (in high school?)
* - Molly
  - 24
  - No
* - James
  - 16
  - Yes
* - Hanna
  - 14
  - Yes
```

```{index} Feature extraction
```
## Feature extraction

The speech signal contains all sorts of rich information but for a given task we may not need all that detail. Feature extraction is the process of distilling from the raw speech signal only the relevant information for the task at hand. Returning to our vowel classification example, let's say all we want to do is classify vowel regions into 4 labels: high-back, high-front, low-back, low-front. For this task, all we might need to extract from each speech region is two features: the means of the first and second formants.

```{list-table} Mean formant values associated with various speech regions
:header-rows: 1
:name: tab-vowels

* - Speech region
  - Mean F1
  - Mean F2
* - 1
  - 512
  - 2200
* - 2
  - 831
  - 1073
* - ...
  - ...
  - ...
```


```{index} Classification
```

Once we have distilled the speech signal down to only relevant information, we can ask a classifier to predict a relevant label based on that information. For our simplistic task of classifying vowels based on mean first and second formant values into 4 labels, a relatively simple algorithm might suffice:

```python
def predict_vowel_label(mean_f1, mean_f2):
    if mean_f1 < 500 and mean_f2 > 1500:
        return "high-front"
    
    if mean_f1 >= 500 and mean_f2 > 1500:
        return "low-front"

    if mean_f1 < 500 and mean_f2 <= 1500:
        return "high-back"

    if mean_f1 >= 500 and mean_f2 <= 1500:
        return "low-back"
```

Taking the extracted features as input, the algorithm would yield a predicted label for each speech region:

```{list-table} Labels predicted by system, given mean F1 and mean F2 as input
:header-rows: 1
:name: tab-predictions

* - Speech region
  - Mean F1
  - Mean F2
  - Prediction
* - 1
  - 512
  - 2200
  - low-front
* - 2
  - 831
  - 1073
  - low-back
* - ...
  - ...
  - ...
  - ...
```

Essentially, as illustrated in {numref}`fig-f1-f2`, the classification algorithm provides a way to 'carve-out' the feature space (which in this example happens to be a 2-dimensional one that is easily to visualise), so that data points that fall into a certain part of the feature space can be associated with a given label.

```{figure} speech-processing_fig-f1-f2.png
---
name: fig-f1-f2
width: 350px
---
Classification as 'carving-out' a feature space
```

More complex tasks require more complex feature extraction and classification systems,[^e2e] some of which require many hundreds of hours of labelled speech in order to train (we cover what 'training' means in the next section). Luckily, it's become relatively easy to use 'pre-trained' systems either in an off-the-shelf manner or to 'fine-tune' it to your needs with a relatively small amount of data.

[^e2e]: Some modern deep learning based systems may also trained 'end-to-end', doing away with explicit feature extraction and classification stages: see [What does end-to-end training mean?](https://ai.stackexchange.com/questions/16575/what-does-end-to-end-training-mean)
