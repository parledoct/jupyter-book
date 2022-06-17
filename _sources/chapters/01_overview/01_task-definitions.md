```{index} Voice activity detection ; Task definition, Speaker diarisation ; Task definition, Spoken language identification ; Task definition, Automatic speech recognition ; Task definition
```
# Tasks as multiple-choice questions

For the kinds of speech processing tasks we're going to be covering, we can think of them as getting a machine to repeatedly answer the same multiple choice question across different time regions in a speech signal. Accordingly, as summarised in {numref}`tab-qlabels`, we can define these speech processing tasks based on the question being asked and choices (i.e. labels) the machine can choose from to try and answer the question for a given speech region.

```{list-table} Selected speech processing tasks as multiple choice questions
:header-rows: 1
:name: tab-qlabels

* - Task
  - Question
  - Example labels/choices
* - Voice activity detection
  - What acoustic activity is happening?
  - Speech (S), Non-speech (N)
* - Speaker diarisation
  - Who is speaking?
  - SpeakerA (A), SpeakerB (B)
* - Spoken language identification
  - What language is being spoken?
  - English (E), French (F)
* - Automatic speech recognition
  - What is being said?
  - a, b, c, d, ..., x, y, z
```

What are these speech regions? They are derived by subdividing an utterance into small time frames and for each frame the machine is tasked with predicting a relevant label.[^overlap] Suppose we have an utterance like below where there are two speakers (A, B) with speaker A asking in English (E) what the French (F) word for *busy* is and speaker B responds that it is *occupé*. We can see from {numref}`fig-qlabels` below that each speech processing task essentially boils down to classifying the speech contents of each of the time frame (1-20) according to the vocabulary of the task at hand (e.g. N = non-speech, S = speech for voice activity detection).[^stages]

```{figure} speech-processing_fig-qlabels.png
---
name: fig-qlabels
---
Selected speech processing tasks as repeated multiple choice questions across time
```

```{note}
The labels used by the French automatic speech recognition (ASR) system are different from those used by the English ASR system (e.g. does not include the label `é`), though there is a large overlap between them (e.g. `a, b, ...y, z`). A large part of preparing transcriptions for training an ASR system involves exploring your dataset and deciding on a plan for addressing non-relevant characters (e.g. numbers, punctuation, etc.), and we will cover this aspect in the ASR tutorial.
```

[^overlap]: In practice, a small window (e.g. 25 milliseconds) is shifted by a certain amount of time (e.g. 10 ms) at each step so that there is a certain amount of overlap between each consecutive time window.

[^stages]: Typically voice activity detection is performed first before the other *downstream* tasks, so that windows in which no voice activity was detected (e.g. 1, 6-13, 20 in {numref}`fig-qlabels`) are not actually processed beyond the first stage. Similarly, spoken language identification (to identify English or French) is done before automatic speech recognition (ASR) so that the right ASR system (English or French) can be used for the subsequent transcription stage.
