# Coffea Casa

## Prerequisites

The primary mode of analysis with coffea-casa is
[coffea](https://coffea-hep.readthedocs.io/en/latest/). Coffea provides plenty
of examples to users in its
[documentation](https://coffea-hep.readthedocs.io/en/latest/examples.html) A
good starting point may be
[this tutorial](https://github.com/CoffeaTeam/coffea-casa-tutorials/blob/master/analyses/thq/analysis_tutorial.ipynb)
on columnar analysis in coffea.

Knowledge of [Python](https://docs.python.org/3/tutorial/) is also assumed.
Standard coffea analyses are contained within
[Jupyter Notebooks](https://jupyter.org/), which allow for dynamic,
block-by-block execution of code. Coffea-casa employs the
[JupyterLab](https://jupyterlab.readthedocs.io/en/stable/user/interface.html)
interface. JupyterLab is designed for hosting Jupyter Notebooks on the web, and
permits the usage of additional features within its environment, including Git
access, compatibility with cluster computing tools, and much, much more.

If you aren't familiar with any of these tools, please click on the links above
for additional resources, and get acquainted with how they work. If you want
examples of how coffea-casa merges these tools, refer to the gallery of
coffea-casa examples.

## Access

Please use [https://coffea.af.uchicago.edu](https://coffea.af.uchicago.edu) as
an access point to the Coffea-Casa Analysis Facility @ UChicago.

![screenshot of coffea.af.uchicago.edu Hub Login](../images/coffea.af.uchicago.edu_hub_login.png)

## ATLAS AuthZ Authentication Instance

Currently Coffea-Casa Analysis Facility @ UChicago can support any member of
ATLAS.

Sign in with your ATLAS CERN credential:

![ATLAS Authz authentication to Coffea-casa Analysis Facility @ UChicago](../images/atlas-auth.web.cern.ch_login.png)

## Docker Image Selection

For high efficient analysis using _coffea_ package, powered with _Dask_ and
_HTCondor_ please select:

![Coffea Casa analysis image available at Coffea-casa Analysis Facility @ UChicago](../images/coffea.af.uchicago.edu_hub_spawn.png)

After you will be forwarded to your personal Jupyterhub instance running at
Analysis Facility @ UChicago:

![Jupyterhub instance together with Dask Labextention powered cluster available at Coffea-casa Analysis Facility @ UChicago](../images/coffea.af.uchicago.edu_user_fengping.hu.png)

## Cluster Resources in Coffea-Casa Analysis Facility @ UChicago

By default, the Coffea-casa Dask cluster should provide you with a scheduler and
workers, which you can see by clicking on the colored Dask icon in the left
sidebar.

![Default Dask Labextention powered cluster available Coffea-casa Analysis Facility @ UChicago](../images/coffea.af.uchicago.edu_user_clusters.png)

As soon as you will start your computations, you will notice that available
resources at the Coffea-Casa Analysis Facility @ UChicago can easily autoscale
depending on available resources in the HTCondor pool at AF UChicago.

![Autoscaling with Dask Labextention powered cluster available at Coffea-casa Analysis Facility @ UChicago](../images/coffea.af.uchicago.edu_user_clusters.scale.png)

## Opening a New Console or File

There are three ways by which you can open a new tab within coffea-casa. Two are
located within the **File** menu at the very top of the JupyterLab interface:
_New_ and _New Launcher._

![The File menu of the coffea-casa JupyterLab interface](../images/coffea-casa-newtab.png)

The _New_ dropdown menu allows you to open the console or a file of a specified
format directly. The _New Launcher_ option creates a new tab with buttons that
permit you to launch a console or a new file, exactly like the interface you are
shown when you first open coffea-casa.

The final way is specific to the **File Browser** tab of the sidebar.

![The File Browser tab of the coffea-casa JupyterLab interface, showcasing the New Launcher button.](../images/coffea-casa-newlauncher.png)

This behaves exactly like the _New Launcher_ option above. Regardless of the
method you use to open a new file, the file will be saved to the current
directory of your **File Browser.**

## Using Git

Cloning a repository in the Coffea-casa Analysis Facility @ UChicago is simple,
though it can be a little confusing because it is spread across two tabs in the
sidebar: the _File Browser_ and the _Git_ tabs.

In order to clone a repository, first go to the Git tab. It should look like
this:

![The Git tab at Coffea-casa Analysis Facility @ UChicago](../images/git.png)

Simply click the appropriate button (initialize a repository, or clone a
repository) and you'll be hooked up to GitHub. This should then take you to the
_File Browser_ tab, which is where you can see all of the repositories you have
cloned in your JupyterLab instance. The File Browser should look like this:

![The File Browser tab at Coffea-casa Analysis Facility @ UChicago](../images/browser.png)

If you wish to change repositories, simply click the folder button to enter the
root directory. If you are in the root directory, the Git tab will reset and
allow you to clone another repository.

If you wish to commit, push, or pull from the repository you currently have
active in the File Browser, then you can return to the Git tab. It should change
to look like this, so long as you have a repository open in the File Browser:

![The Git tab at Coffea-casa Analysis Facility @ UChicago, after a repository is activated](../images/git2.png)

The buttons in the top right allow for pulling and pushing respectively. When
you have edited files in a directory, they will show up under the _Changed_
category, at which point you can hit the **+** to add them to a commit (at which
point they will show up under _Staged_). Filling out the box at the bottom of
the sidebar will file your commit, and prepare it for you to push.
