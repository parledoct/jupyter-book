# Using default parameters

As mentioned, we'll use the [auditok](https://github.com/amsehili/auditok) Python package, which provides an easy way to detect regions in a sound file that are above a certain threshold of acoustic energy (defaults to `50 dB`). 
We'll then use the [pympi](https://dopefishh.github.io/pympi/index.html) Python package to export the time regions to an ELAN file.

Because there's 'admin' work when processing multiple files (e.g. getting the right file paths, deciding what to name the output file on-the-fly), we'll focus on processing just a single file in Part I.
Then in Part II, we'll learn to set up the 'admin' work and apply the process we learned about to multiple files.

## Part I: A single file

First let's process just a single file to make sure each step of the process results in the output we're expecting.

### Import required packages

Let's start by importing the packages we'll need:

```python
import auditok
import pympi
```

### Detect speech regions

Now that we have imported `auditok` into our computation environment, we can use `auditok.split()` to obtain a list of speech regions where the acoustic activity is above the default 50 dB threshold in the `data/20180518o.wav` file.

```python
speech_regions = auditok.split('data/20180518o.wav')
```

### Export speech regions to ELAN

#### Create and configure an `Eaf` object

We can now export these speech regions to an ELAN file.
First, we'll create a new, empty `Eaf` object by specifying `file_path=None` (if you specify a real file path, it'll load in the data from that file) and, for convenience, also add the wav file as a linked file so when we view the resulting `.eaf` file in ELAN later, the wav file will also be loaded in ELAN.

```python
annot_data = pympi.Elan.Eaf(file_path=None)
annot_data.add_linked_file('20180518o.wav')
```

#### Add detected speech regions to `default` tier

We'll loop through each detected region in `speech_regions` (which we just created using auditok) and add a blank annotation onto the `default` tier (automatically created by `pympi.Elan.Eaf`).
The only thing we have to do to make the time regions detected by auditok (specified in seconds) to be compatible is to convert the times into milliseconds (as expected by the `add_annotation` function).

```python
for region in speech_regions:
    # auditok stores the start and end times (in seconds)
    # as the metadata of each detected region. Convert to
    # milliseconds to be compatible with the time format
    # the add_annotation function is expecting

    start_ms = int(region.meta.start * 1000)
    end_ms   = int(region.meta.end * 1000)

    # Add a blank annotation (value='') on the 'default' tier
    annot_data.add_annotation(id_tier='default', start=start_ms, end=end_ms, value='')
```

```{important}
If you're new to Python, note that whitespace/indentation is semantically meaningful. Instead of curly braces `{ ... }` which you might have seen in other languages (e.g. in R: `for (region in speech_regions) { ... }`), Python uses whitespace to specify that `start_ms = ...`, `end_ms = ...` and `annot_data.add_annotation(...)` are within the loop.
```

#### Write data to an Eaf file

Now we can export the data to an `eaf` file:

```python
annot_data.to_file('data/20180518o.eaf')
```

### Download and view files

To make it easy to download multiple files from the SaturnCloud server, let's zip up the `data/20180518o.eaf` we just created and the accompanying audio file `data/20180518o.wav`.

```
!zip vad01_single-file.zip -j data/20180518o.wav data/20180518o.eaf
```

Once the `vad01_single-file.zip` file appears in the JupyterLab file browser on the left (you may need to hit the refresh button), right-click and download the file onto your computer. Unzip the files and open up `20180518o.eaf` in ELAN. As expected, you should be able to see on the `default` tier speech regions that `auditok` have detected as being above the 50 dB threshold:

```{figure} vad_elan-single-file.png
```

## Part II: Multiple files in folder

Once we're confident about a sequence of commands, we can have Python run those commands for each file we want to process.
But now that we want to abstract away from a set of commands that worked on a fixed file path (i.e. `data/20180518o.wav`) to ones that work on an arbitrary list of files, we'll need to bring in some other packages to help handle these file paths.

### Prerequisites: File paths in Python

Let's take a moment to go over and familiarise ourselves with common file path maniupation routines since getting data from files you want to process and modifying/creating files that contain the processed data is a fundamental part learning to process your own data (even when you can treat the main processing function as a blackbox).

#### Use `glob.glob()` to locate files of interest

The `glob` function from the `glob` package takes an expression and returns a list of file paths that matches that expression. For example:

```python
# First, import glob if not already present in environment
import glob

# To see the output of each command, run each of them individually instead of
# copying and pasting the entire codeblock.

# List all files in current directory
glob.glob('*')

# List all files in data directory
glob.glob('data/*')

# List all wav files in data directory
glob.glob('data/*.wav')

# List all wav files in data directory ending with 'o' or 'p'
glob.glob('data/*[o|p].wav')
```

In this way, when you're having to process a corpus with hundreds or thousands of files, you can use such expressions to target only the files you're interested in processing. It's *always* a good idea to at least eyeball the list of files returned, especially before running commands that modify or delete the files!

#### Manipulating file paths

##### Get basename from full path

To create and configure the Eaf files, we also need to derive additional file paths from the wav file's 'full' path (e.g. `data/20180518o.wav`).

First, we need to extract only the wav file's name (e.g. `20180518o.wav`) since linked files in Elan are expected to be relative paths (we don't want the a relative path of `data/20180518o.wav` which implies that the wav file is in `data/data/20180518o.wav` for the Elan file in `data/20180518o.eaf`).
Across many programming languages, a file name without any path(s) prefixed to it is called the *basename*.
In Python, we can use the `basename` function located in the `os` package (under the path-related functions):

```python
import os

# From 'data/20180518o.wav', derive '20180518o.wav'
os.path.basename('data/20180518o.wav')
```

##### Create full path for eaf file

Second, we need to create a 'full' path to the Eaf file we'll be writing to (e.g. `data/20180518o.eaf`). So, we'll use the `rsplit('.')` function to split the wav path `data/20180518o.wav` based on the first `.` from the right side, yielding a list of two items `['data/20180518o', 'wav']`.
Then, we'll take only the first element (`data/20180518o`) and postpend a new extension `.eaf` to get `data/20180518o.eaf`.

```python
file_path = 'data/20180518o.wav'

# Split 'data/20180518o.wav' into 'data/20180518o' and 'wav'
file_path.rsplit('.')

# From 'data/20180518o.wav', derive 'data/20180518o'
# Note Python indexes from 0, so the first element is at position 0
file_path.rsplit('.')[0]

# From 'data/20180518o.wav', derive 'data/20180518o.eaf'
file_path.rsplit('.')[0] + '.eaf'
```

### Final script

Based on Part I and what we've learned about file maniupation, you should be able to understand the gist of the following script, which will detect speech regions above 50 db and export these regions to accompany Eaf files for each wav file in the `data` folder.
Copy the code below (hover over the top-right corner of the codeblock then click the `copy`), paste it into your console, and execute it.

```python
# Import required packages
import auditok
import pympi
import glob
import os

# Loop through each wav file in data folder
for wav_file in glob.glob("data/*.wav"):

    # Detect speech regions above 50 dB threshold
    speech_regions = auditok.split(wav_file)

    # Create placeholder Eaf Object
    annot_data = pympi.Elan.Eaf(file_path=None)
    # Add linked file
    annot_data.add_linked_file(os.path.basename(wav_file))

    # Loop through each detected region 
    for region in speech_regions:
        # auditok stores the start and end times (in seconds)
        # as the metadata of each detected region. Convert to
        # milliseconds to be compatible with the time format
        # the add_annotation function is expecting

        start_ms = int(region.meta.start * 1000)
        end_ms   = int(region.meta.end * 1000)

        # Add a blank annotation (value='') on the 'default' tier
        annot_data.add_annotation(id_tier='default', start=start_ms, end=end_ms, value='')

    # Obtain output Eaf file path from wav file's path
    # e.g. 'data/20180518o.wav' -> 'data/20180518o.eaf'
    eaf_name = wav_file.rsplit('.')[0] + '.eaf'

    # Write Eaf data out to a file
    annot_data.to_file(eaf_name)
```

:::{admonition} Tip
:class: tip
When you're developing your own scripts and are almost ready to process all your files, you may want to do some dry runs over a handful of files, for example, by taking just the first 2 items in the list of files returned by `glob.glob`:

```python
for wav_file in glob.glob("data/*.wav")[:2]:
```

In this way, you can gradually develop your script for just 1 file, then a handful of files, and only run it on all your files when you're confident about it.
:::

### Download and view files

Let's zip up all wav and eaf files inside the `data` folder so you can download and view them on your computer:

```
!zip vad02_multiple-files.zip -j data/*.wav data/*.eaf
```

Once the `vad02_multiple-files.zip` file appears in the JupyterLab file browser on the left (you may need to hit the refresh button), right-click and download the file onto your computer. 

Congrats — you've completed your first speech processing task!
