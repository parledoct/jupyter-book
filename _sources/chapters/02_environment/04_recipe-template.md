# Recipe template

Like certain steps within a set of recipes that form a natural class (e.g. chopping onions for red/yellow/green Thai curries), the speech processing tasks that we'll be learning about in later chapters have many steps in common (e.g. list all wav files, open wav file, create ELAN eaf file, etc.).

In this section, we'll cover these common steps using a very simple text processing task (converting French text into IPA representation).
We'll use a text processing task here so that we can easily open text files right in the JuyterLab environment (unlike audio and ELAN files which you would have to download to view).
Additionally, we'll also introduce the general three-part structure which you will find across all the tutorials.

## Part I: process toy data

As mentioned in the [JupyterLab introduction](03_jupyterlab-intro.html#using-python-packages), we'll be using various Python packages to do the heavy lifting.
In the first part of the tutorial, we'll learn to use a given package with some very simple data.
In this case, we'll be using the `eptrain` package to convert French text to IPA.
Let's start by importing the package:

```python
import epitran
```

Since `epitran` <a href="https://github.com/dmort27/epitran#language-support" target="_blank">supports many languages</a>, the first thing we'll do is set up a French-to-IPA converter as our input orthography is in French (language) using the Latin alphabet (script):

```python
fra2ipa = epitran.Epitran('fra-Latn')
```

Now we can use the `transliterate` function to convert some French text to IPA:

```
fra2ipa.transliterate('je vais à la maison')

# 'ʒə vɛs a la mɛzɔ̃'
```

## Part II: process a single file

Once we've gained some familiarity with a given package, we can now move onto working with a single file that we're interested in processing.
Typically this will involve learning to read the data from the file into the Python environment, processing each sub-part of the file (e.g. lines in a text file or time regions in an audio file), and then writing to a 'sidecar' file (same name as the input file but with a different extension, e.g. `mydata.eaf` for an input file `mydata.wav`).

### Read in data

Let's process the file `data/french-nouns.txt` and create a sidecar file called `data/french-nouns.ipa` (just making up this `.ipa` extension for our tutorial).
To read in a text file into Python, we have to open a file with the `r` specification to indicate that we want to read from the file, then read the file contents and split the data into lines for further processing, and close the file once we're done with it:

```python
f = open('data/french-nouns.txt', 'r')
fra_lines = f.read().splitlines()
f.close()
```

Notice the contents of `fra_lines` is a list of French nouns from the file `data/french-nouns.txt`:

```python
['le cheval',
 'le chien',
 'le livre',
 'le bruit',
 'la colombe',
 'la chemise',
 'la maison',
 'la liberté']
```

### Process data

We can now create a new list, called `ipa_lines` by applying the `fra2ipa.transliterate()` function to each item in the list above:

```python
ipa_lines = [ fra2ipa.transliterate(line) for line in fra_lines ]
```

```{note}
The specific syntax we are using here is called Python '<a href="https://www.learnbyexample.org/python-list-comprehension/" target="_blank">List Comprehensions</a>', which are a very concise way of processing lists in Python in place of for loops:

```python
# Equivalent to list comprehension above

ipa_lines = []                              # Initialise empty list

for line in fra_lines:
    ipa_line = fra2ipa.transliterate(line)  # Process each item in 
                                            #   fra_lines list
    ipa_lines.append(ipa_line)              # Add result to ipa_lines list
```

The contents of `ipa_lines` is:

```python
['lə ʃəval',
 'lə ʃjɛ̃',
 'lə livʀ',
 'lə bʀɥi',
 'la kɔlɔ̃b',
 'la ʃəmiz',
 'la mɛzɔ̃',
 'la libɛʀte']
```

### Write data to sidecar file

We can now write our desired output to a sidecar file called `data/french-nouns.ipa`.
Similar to before, we open up a file that we want to interact with but now with the `w` specification to indicate that we want to write to the file.
Note the contents of the file, where we're merge the items in the `ipa_lines` list above with a new line character in between them `"\n"`, and use `f.write()` to write to the file.
Finally, we close the file once we're done interacting with it.

```python
f = open('data/french-nouns.ipa', 'w')
f.write("\n".join(ipa_lines))
f.close()
```

To see the result of these commands, you can navigate to the `data` folder and open up the `french-nouns.ipa` file in that folder:

```{figure} recipe-template-french-nouns.png
```

Altogether, the code to write to process a single file looks like:

```python
input_file  = 'data/french-nouns.txt'
output_file = 'data/french-nouns.ipa'

# Read in data from input file
f = open(input_file, 'r')
fra_lines = f.read().splitlines()
f.close()

# Process data
ipa_lines = [ fra2ipa.transliterate(line) for line in fra_lines ]

# Write data to output file
f = open(output_file, 'w')
f.write("\n".join(ipa_lines))
f.close()
```

Notice that we've abstracted the file names in the `open()` commands from specific paths like `'data/french-nouns.txt'` to a variable such as `input_file`.
This way if we want to process another file, we can simply change the value of these variables and re-use the same code.

## Part III: process multiple files

By the end of Part II, we should end up with some code that's basically ready to be used on multiple files.
The only thing we have left to do is specify which files we want to read from and which files we want to write the resulting output to.

### Create a list of files of interest

The `glob` function from the `glob` package takes an expression and returns a list of file paths that matches that expression.
In our case, we want to process text files in the `data` folder that are prefixed with `french-` (e.g. `french-adjectives.txt` and `french-nouns.txt`), so we can use the expression `data/french-*.txt` (where `*` is the 'wildcard' character).

```python
# First, import glob if not already present in environment
import glob

# List all files of interest in data directory
input_files = glob.glob('data/french-*.txt')
```

### Derive output file names

Since we'll be mainly targeting sidecar files (same filename as input file but with a different extension), we'll just need to derive the output file path by splitting the input filepath on the first `'.'` from the right side (i.e. `'data/french-nouns.txt' => ['data/french-nouns', 'txt']`), and then suffixing the new extension to the first item in the resulting split (i.e. `'data/french-nouns' + '.ipa'`).

```python
input_file  = 'french-nouns.txt'
output_file = input_file.rsplit('.')[0] + '.ipa'
```

```{note}
Python indexes from 0, so the first item of a list `x` is accessed as `x[0]` (and `x[]` is the second item).

```python
fruits = ['apples', 'oranges']

fruits[0]
# apples

fruits[1]
# oranges
```

### Complete script 

We can now put everything we've covered in this tutorial to read in French text from multiple text files and write out sidecar `.ipa` files which contain the IPA representations:

```python
# Import Python packages that we want to use
import epitran
import glob

# Configure epitran
fra2ipa = epitran.Epitran('fra-Latn')

# List all files of interest in data directory
input_files = glob.glob('data/french-*.txt')

# Process each file in input_files list
for input_file in input_files:

    # Read in data from input file
    f = open(input_file, 'r')
    fra_lines = f.read().splitlines()
    f.close()

    # Process data
    ipa_lines = [ fra2ipa.transliterate(line) for line in fra_lines ]

    # Derive output file name
    output_file = input_file.rsplit('.')[0] + '.ipa'    

    # Write data to output file
    f = open(output_file, 'w')
    f.write("\n".join(ipa_lines))
    f.close()
```

```{important}
If you're new to Python, note that whitespace/indentation is semantically meaningful. Instead of curly braces `{ ... }` which you might have seen in other languages (e.g. in R: `for (input_file in input_files) { ... }`), Python uses whitespace to specify that `f = open ...` and `fra_lines ...` and all down to  `f.close()` are within the loop.
```

Congrats — you're now familiar with the basic template of processing data from various input files of interest and writing out the results to a separate sidecar file.
In our later tutorials, we'll draw on this basic routine to process audio data, where the input files are audio files (e.g. `data/mydata.wav`) and the output files are sidecar ELAN files (e.g. `data/mydata.eaf`).
The main thing that will change between each tutorial is what kind of speech processing task we're performing (e.g. voice activity detection, speaker diarisation, etc.).
