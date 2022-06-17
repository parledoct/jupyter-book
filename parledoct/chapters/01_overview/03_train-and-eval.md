```{index} Model training , Model parameters , Evaluation metric
```
# Training as searching for optimal parameters

Let's return to our simple algorithm from before (repeated below). How did we know 1500 Hz was a good boundary to decide between front and back vowels based on mean F2? How about for 500 Hz for deciding between high and low vowels using mean F1? Just good (educated) guesses? In a way, yes.

<pre>
def predict_vowel_label(mean_f1, mean_f2):
    if mean_f1 < <span style="color:red">500</span> and mean_f2 > <span style="color:green">1500</span>:
        return "high-front"
    
    if mean_f1 >= <span style="color:red">500</span> and mean_f2 > <span style="color:green">1500</span>:
        return "low-front"

    if mean_f1 < <span style="color:red">500</span> and mean_f2 <= <span style="color:green">1500</span>:
        return "high-back"

    if mean_f1 >= <span style="color:red">500</span> and mean_f2 <= <span style="color:green">1500</span>:
        return "low-back"
</pre>

When we train a new system 'from scratch', we first often just randomly set these kinds of *parameters*, i.e. numbers that the system uses to make a decision.
During training, the parameters of the system are repeatedly adjusted until we get a desired level of performance according to an *evaluation metric* (e.g. accuracy).
For example, we can see from {numref}`fig-eval` that a mean F2 decision boundary of 1500 Hz appears to be the sweet spot (not too high not too low) for maximising the accuracy of the algorithm's predictions on this illustrative dataset.

```{figure} speech-processing_fig-eval.png
---
name: fig-eval
---
Training as searching for optimal parameters
```

Even with just two parameters (mean F1 and mean F2), we can already see that there are many combinations to try out.
Some modern deep learning based models have millions or even billions of parameters, which is why *a lot* of data is needed to train them from a randomly-initialised state to a point where they could be useful.

While often more effective than starting from scratch, parameters that perform well for one dataset are not necessarily the ones that are best for another dataset (i.e. yours).
The tutorials on this website will help you get started in using/adapting various off-the-shelf systems for your data and make a relatively informed decision about whether a given system may be useful enough for your needs (sometimes, you may well decide it is not!).
