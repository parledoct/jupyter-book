# Machine-assisted speech processing

If you've done any speech data annotation in Praat or ELAN, congrats — you're already an expert in speech processing! The tutorials on this website will provide an introduction to how some of the more tedious parts of annotation work can be offloaded to machines.

Let's review what's typically involved in annotating speech. Suppose like in {numref}`fig-praat` below we're interested in American English vowels, and so we want to annotate the type of vowel uttered at various time regions of interest. Accordingly, this task will involve only vowel labels such as 'a' or 'æ' (i.e. not consonantal labels such as 'p' or 'b') and, to complete the task, you will listen to various speech excerpts of interest and decide which of the vowel labels apply to that excerpt.

```{figure} speech-processing_praat.png
---
name: fig-praat
---
Annotating for American English vowels in Praat
```

[^stages]: Typically voice activity detection is performed first before the other *downstream* tasks, so that windows in which no voice activity was detected (e.g. 1, 6-13, 20 in {numref}`fig-qlabels`) are not actually processed beyond the first stage. Similarly, spoken language identification (to identify English or French) is done before automatic speech recognition (ASR) so that the right ASR system (English or French) can be used for the subsequent transcription stage.
