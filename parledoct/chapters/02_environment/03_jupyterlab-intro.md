# JupyterLab Interface

Let's get familiar with the main components of the JupyterLab interface. As annotated below in {numref}`fig-jupyterlab-home`, we can divide the interface horizontally into three main areas: left sidebar, main work area, and right sidebar (usually collapsed/hidden by default).

On top of the interface is the menu bar (where the `File`, `Edit`, etc. menus are) and the bottom is a status bar (where, for example, you'll see a progress bar indicating file upload status when you upload a large file).

```{figure} saturncloud-jupyterlab-home-annotated.png
---
name: fig-jupyterlab-home
---
Main components of the JupyterLab interface
```

## File browser

The main component you'll be interacting with is the file browser in the left sidebar.

### Navigation

To navigate into a directory, you can simply double click with your mouse. To navigate out of a directory, you can click the parent folder name in the path.

```{figure} saturncloud-jupyterlab-file-browser-nav.gif
---
width: 350px
```

### File download

To download a single file, right-click the file and then click `Download`. Downloading multiple files isn't as simple as selecting multiple files and doing the same thing. In a later tutorial, we'll cover how to create single zip file of the files you want to download so you can right-click and download this zip file.

### File upload

To upload a file, navigate to the folder where you want to upload the file (e.g. `tmp` or `data`) and then click the file upload icon (the up arrow) which is next to the refresh icon (if you file upload completed did not appear immediately, try using the refresh icon).

```{figure} saturncloud-jupyterlab-file-browser.png
---
width: 350px
```

## Python 3 Console

The main component you'll be interacting with in the main work area is the Python 3 console. To create a new one, click `saturn (Python 3)` in the `Console` section of the `Launcher` tab in the main work area:

```{figure} saturncloud-jupyterlab-home-saturn-python3.png
```

### Python commands

To run Python commands in the console, you can type in a Python command such as `print("Hello!")` in the text input area at the bottom of the console and then press `Shift + Enter` to execute the command.

```{figure} saturncloud-jupyterlab-home-saturn-python3-hello.png
```

#### Using Python packages

As mentioned at the start of this chapter, we're going to be using specialised tools already developed by other people to offload the bulk of the data processing work.
Most of these tools will come in the form of Python packages, which are bundles of code written by others that we can use.
The SaturnCloud environments for each tutorial will have all the necessary packages pre-installed, so we'll just have to learn to use them (we'll cover package installation in later tutorials).

In Python, we use the `import` statement to load the code associated with a given package into the current working environment.
For a simple example, the `string` package provides a `capwords` function, which capitalizes each word in a sentence.
So let's go ahead and import the `string` package:

```python
import string
```

With the `string` package imported, you can now make use of the `capwords` function:

```python
string.capwords("hello this is a test")

# 'Hello This Is A Test'
```

### Shell commands

A 'shell' is an analogy for the layer that sits between the user and the operating system, which is the 'kernel'. Since some commands are much more efficiently done by the operating system (things like file management, e.g. (un)zipping files), the JupyterLab Python 3 Console that we're using makes it easy to also easily run shell commands by prefixing command with an exclamation mark `!` (and executing them as usual, with `Shift + Enter`).

You can try out some shell commands such as:

- Print 'Hello!' (same functionality as Python command above)

    ```bash
    !echo "Hello!"
    ```

- Print the `p`resent `w`orking `d`irectory

    ```bash
    !pwd
    ```

Now that you have some basic familiarity with the computation environment, let's get familiar with the basic structure what the tutorials will look like.

<!-- ## Wrap up

This concludes the JupyterLab basics tutorial! I'll grow the contents of this page as I uncover aspects of the JupyterLab interface that new users find confusing when going through the Parledoct material. -->
