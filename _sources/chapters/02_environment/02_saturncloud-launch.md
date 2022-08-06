# Resources on SaturnCloud

On SaturnCloud, a *Resource* is their term for a complete compute environment (a virtual computer allocated a certain amount of compute resources, e.g. 2 vCPUs, 4 GB vRAM, etc.) which you can start and stop on demand.

```{important}
On the free plan, **SaturnCloud will automatically stop a Resource after an hour of usage**. This should however be plenty of time for each tutorial, which we'll keep relatively short to make the content digestable. 
```

## Create Resource (once per tutorial)

Every tutorial will have an `Environment` section with a 'Create Resource on SaturnCloud' button. Let's make a new resource for this introduction, so go ahead and click the one below:

<p align="center">
<a target="_blank" href="https://app.community.saturnenterprise.io/dash/o/community/resources?recipeUrl=https://gist.githubusercontent.com/fauxneticien/68313eb494337f60f69d140c8270f3d7/raw/0c51d3d5d7fbe33f096f4565b633c1a8709d6feb/jupyter-introduction.json">
<button class="saturn" role="button">Create Resource on SaturnCloud</button>
</a>
</p>

The link will take you to SaturnCloud, where you'll see the `Create Resource` prompt:

```{figure} saturncloud-create-resource.png
```

Click `Create` to add the specifically-configured computation environment to your list of available Resources.

```{important}
You cannot create two resources with the same name on the same account, so you will get an error if you tried to create the same Resource more than once. Since Resources can be started and stopped, **you only need to do the creation step once for each tutorial** and then re-start a previously created one if/when you want to come back to it.
```

## (Re-)start Resource 

When you create a new Resource, you will be immediately taken to the page associated with that Resource where you can manage it (i.e. start and stop it). The SaturnCloud home page after you log in will have a list of previously created Resources, so you can get back to this same page by clicking on the one you want to re-start.

To (re-)start a Resource, Click `Start`:

```{figure} saturncloud-start-resource.png
```

## Launch Jupyter Lab interface

Once the Resource is ready (which may take a couple of minutes if there is high demand, since free account users may get lower priority), click the `Jupyter Lab` button:

```{figure} saturncloud-jupyterlab-start.png
```

You will now get to a web page that looks like:

```{figure} saturncloud-jupyterlab-home.png
```

We'll now familiarise ourselves with this JupyterLab interface.
