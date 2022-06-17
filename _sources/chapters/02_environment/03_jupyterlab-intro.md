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

### Shell commands

A 'shell' is an analogy for the layer that sits between the user and the operating system, which is the 'kernel'. Since some commands are much more efficiently done by the operating system (things like file management, e.g. (un)zipping files), the JupyterLab Python 3 Console that we're using makes it easy to also easily run shell commands by prefixing command with an exclamation mark `!`. 

You can try out some shell commands such as:

- `!echo "Hello!"`: Print 'Hello!' (same functionality as Python command above)
- `!pwd`: Print the `p`resent `w`orking `d`irectory
- `!ls`: Print a list of files and folders in the present working directory

## Wrap up

This concludes the JupyterLab basics tutorial! I'll grow the contents of this page as I uncover aspects of the JupyterLab interface that new users find confusing when going through the Parledoct material.
