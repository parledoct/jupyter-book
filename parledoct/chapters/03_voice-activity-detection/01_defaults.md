# Using default parameters

As mentioned, we'll use the [auditok](https://github.com/amsehili/auditok) Python package, which provides an easy way to detect regions in a sound file that are above a certain threshold of acoustic energy (defaults to `50 dB`). 
We'll then use the [pympi](https://dopefishh.github.io/pympi/index.html) Python package to export the time regions to an ELAN file.

## Part I: process toy example

As per the [recipe template](../02_environment/04_recipe-template.md), let's begin by getting familiar with the `auditok` and `pympi` packages using a toy example.
In the `data` folder, there's a file `one_two_three.wav`, which is a short clip of me saying "One, two, three".

### Detect speech regions

We can use the `split()` function from the `auditok` package to split an audio file and retain only the parts that satisfy certain requirements (e.g. acoustic energy above 50 db, by default).

```python
import auditok

speech_regions = list(auditok.split('data/one_two_three.wav'))
```

The result is a list of three `AudioRegion` objects:

```
[AudioRegion(duration=0.700, sampling_rate=48000, sample_width=2, channels=1),
 AudioRegion(duration=0.650, sampling_rate=48000, sample_width=2, channels=1),
 AudioRegion(duration=0.600, sampling_rate=48000, sample_width=2, channels=1)]
```

According to the [`auditok` package documentation](https://auditok.readthedocs.io/en/latest/examples.html#basic-split-example), the start and end time of each detected region is stored in its metadata:

```python
for region in speech_regions:

    start_time = round(region.meta.start, 2)
    end_time   = round(region.meta.end, 2)

    print(f"Start: {start_time}, End: {end_time}")
```

```
Start: 0.7, End: 1.4
Start: 2.3, End: 2.95
Start: 3.75, End: 4.35
```

### Export speech regions to ELAN

Now that we're able to obtain a list of start and end times where speech activity is detected, let's learn how to use the `pympi` package to export the regions to an ELAN `.eaf` file.
After importing the `pympi`, we can use `pympi.Elan.Eaf()` to create a new Eaf object.
We use `file_path=None` to specify that we're *not* reading in data from an already-existing `.eaf` file.

```python
import pympi

eaf_data = pympi.Elan.Eaf(file_path=None)
```

Now we can loop over the detected speech regions in `data/one_two_three.wav` and add each region as a blank annotation value `''` to the `default` tier:

```python
for region in speech_regions:

    # Note add_annotation() below expects start and end to be in milliseconds
    # so we multiply region.meta.start/region.meta.end by 1000
    start_ms = int(region.meta.start * 1000)
    end_ms   = int(region.meta.end * 1000)

    eaf_data.add_annotation(id_tier='default', start=start_ms, end=end_ms, value='')
```

We're almost ready to write the `eaf_data` out to a sidecar file `'data/one_two_three.eaf'`.
But for convenience, let's also add `'one_two_three.wav'` as a linked file so that ELAN will open the wav file when you open the `.eaf` file.
Note that when we add linked files, the file path is relative to the wav file (we added `'one_two_three.wav'` not `'data/one_two_three.wav'` since `one_two_three.eaf` is already in `data`).
Once the linked file is added, we use the `to_file()` to write the data from the Python environment to the file system.

```python
eaf_data.add_linked_file('one_two_three.wav')
eaf_data.to_file('data/one_two_three.eaf')
```

If you go to the `data` folder, download `one_two_three.eaf` and `one_two_three.wav`, and open `one_two_three.eaf`, you will be able to see the detected speech regions placed on the default tier:

```{figure} vad_one-two-three.png
```

## Part II: Process single file

Let's put together what we've learned from the toy example above to write a script that will process a single file of interest from the Ihanzu data.
Let's start with the `'data/20180518o.wav'` file and create a sidecar file `'data/20180518o.eaf'` with the speech regions written onto the `default` tier as before.

```python
import auditok
import os
import pympi

input_file  = 'data/20180518o.wav'

speech_regions = auditok.split(input_file)

eaf_data = pympi.Elan.Eaf(file_path=None)

for region in speech_regions:

    # Convert start/end times to milliseconds
    start_ms = int(region.meta.start * 1000)
    end_ms   = int(region.meta.end * 1000)

    # Add region as blank annotation on default tier
    eaf_data.add_annotation(id_tier='default', start=start_ms, end=end_ms, value='')

# Add '20180518o.wav' as linked file, derived from 'data/20180518o.wav' using os.path.basename()
eaf_data.add_linked_file(os.path.basename(input_file))

# From 'data/20180518o.wav' derive sidecar filename 'data/20180518o.eaf'
output_file = input_file.rsplit('.')[0] + '.eaf'

eaf_data.to_file(output_file)
```

We have seen all the code snippets individually before (e.g. `auditok.split`, `pympi.Elan.Eaf`, etc.) and learned about what kinds of inputs they require and what the resulting output is.
The one new function we've introduced is `os.path.basename`, which helps isolate the basename (e.g. `20180518o.wav`) from the full file path (e.g. `'data/20180518o.wav'`) so that we can add the wav file as a relative file to the ELAN eaf file.

As you can see a large part of data processing is wrangling the data that you have into an expected form (e.g. you have a full path `'data/20180518o.wav'` but ELAN expects linked files to be relative paths `20180518o.wav`) or wrangling the data produced as a result of some process into the form another process expects (e.g. `auditok.split` returns start/end times in seconds but `eaf_data.add_annotation` expects milliseconds).
This is why it's important to start with almost comically simple toy data first to understand such details (the devil really is *always* in the details) and build up from there.

## Part III: Process multiple files

```python
# Import required packages
import auditok
import pympi
import glob
import os

# Loop through each wav file starting with '20180518' in data folder
for input_file in glob.glob("data/20180518*.wav"):

    # Detect speech regions above 50 dB threshold
    speech_regions = auditok.split(input_file)

    # Create placeholder Eaf Object
    eaf_data = pympi.Elan.Eaf(file_path=None)

    # Loop through each detected region 
    for region in speech_regions:

        # Convert start/end times to milliseconds, expected by add_annotation()
        start_ms = int(region.meta.start * 1000)
        end_ms   = int(region.meta.end * 1000)

        # Add a blank annotation (value='') on the 'default' tier
        eaf_data.add_annotation(id_tier='default', start=start_ms, end=end_ms, value='')

    # Add linked file
    eaf_data.add_linked_file(os.path.basename(input_file))

    # Obtain output Eaf file path from wav file's path
    # e.g. 'data/20180518o.wav' -> 'data/20180518o.eaf'
    output_file = input_file.rsplit('.')[0] + '.eaf'

    # Write Eaf data out to a file
    eaf_data.to_file(output_file)
```

:::{admonition} Tip
:class: tip
When you're developing your own scripts and are almost ready to process all your files, you may want to do some dry runs over a handful of files, for example, by taking just the first 2 items in the list of files returned by `glob.glob`:

```python
for wav_file in glob.glob("data/20180518*.wav")[:2]:
```

In this way, you can gradually develop your script for just 1 file, then a handful of files, and only run it on all your files when you're confident about it.
:::

### Download and view files

Let's zip up all wav and eaf files inside the `data` folder so you can download and view them on your computer:

```
!zip vad02_multiple-files.zip -j data/20180518*.wav data/20180518*.eaf
```

Once the `vad02_multiple-files.zip` file appears in the JupyterLab file browser on the left (you may need to hit the refresh button), right-click and download the file onto your computer. 

Congrats — you've completed your first speech processing task!
