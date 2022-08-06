# Voice activity detection

As we've seen in the [task definitions](01_task-defs), voice activity detection (VAD) is the task where we classify the acoustic signal into regions of speech and non-speech.
In this sequence of tutorials, we'll learn how to perform VAD on a folder of wav files and create accompanying ELAN eaf files whose annotations on the 'default' tier are the time regions detected as speech.

For the VAD, we'll use the [auditok](https://github.com/amsehili/auditok) Python package, which provides an easy way to detect regions in a sound file that are above a certain threshold of acoustic energy (defaults to `50 dB`). We'll then use the `pympi` Python package to export the time regions to an ELAN file.

```{figure} vad.png
```

## Environment

Assuming you've already [created a SaturnCloud account](../02_environment/01_saturncloud-account) and have familiarised yourself with creating and [(re-)starting a Resource on SaturnCloud](../02_environment/02_saturncloud-launch), click the button below to create the `voice-activity-detection` resource you'll need for this tutorial.
Start the resource and once it's ready [launch a JupyterLab interface](../02_environment/02_saturncloud-launch.html#launch-jupyter-lab-interface) and then [launch a Python 3 Console](../02_environment/03_jupyterlab-intro.html#python-3-console) within JupyterLab.

<p align="center">
<a target="_blank" href="https://app.community.saturnenterprise.io/dash/o/community/resources?recipeUrl=https://gist.githubusercontent.com/fauxneticien/68313eb494337f60f69d140c8270f3d7/raw/c9b08bd9e2be52a5e27844167596b1a41027bd74/voice-activity-detection.json">
<button class="saturn" role="button">Create Resource on SaturnCloud</button>
</a>
</p>

```{important}
Recall that you cannot create two resources with the same name on the same account, so you will get an error if you tried to create the same Resource more than once. If you're resuming this tutorial and have already created the resource, go to your <a href="https://app.community.saturnenterprise.io/dash/o/community/resources" target="_blank">SaturnCloud Resources</a> page to re-start the previously created `voice-activity-detection` Resource.
```

### Data

We'll be using some real fieldwork data on Ihanzu (a Bantu language spoken in Tanzania) collected by [Andrew Harvey](https://www.andrewdtharvey.com/), publicly available with a [CC-BY license](https://creativecommons.org/licenses/by/4.0/) on the [Endangered Languages Archive](https://www.elararchive.org/index.php?name=SO_87014498-be98-4698-82fc-8fac58578d57). For this tutorial, we'll be using versions of the recordings that I've modified (resampled, converted to mono, etc.) and uploaded to Zenodo.

First let's download the data from Zenodo into the `tmp` folder using `wget` [Shell commands](../02_environment/03_jupyterlab-intro.html#shell-commands).
Copy and paste the commands below into the text input area and press `Shift + Enter` to execute them.

`````{tip}
To copy code, you can hover your mouse over the top right corner of a code block until the a `Copy` button appears and then click the button to copy the contents of a code block.
`````

- **Get** data from the **w**eb url `http://zenodo.org/...` and write the data to the **o**utput file `tmp/data.zip` (specified `-O`):

    ```bash
    !wget 'https://zenodo.org/record/6519000/files/ihanzu-harvey-0596_20180518opq.zip?download=1' \
        -O tmp/data.zip
    ```

- **Unzip** the file `tmp/data.zip` into the output **d**irectory `data/` (specified by `-d`):

    ```bash
    !unzip tmp/data.zip -d data/
    ```

Now that you have the environment and data ready, let's use `auditok` to perform voice activity detection!
