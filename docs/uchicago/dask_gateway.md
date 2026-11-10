# Dask Gateway

[Dask Gateway](https://gateway.dask.org/) gives you your own Dask cluster at the
UChicago Analysis Facility on demand. You create a cluster from Python, choose
how many workers you want, and connect a Dask client to it. The workers run as
HTCondor jobs on the AF batch nodes, under your AF username.

You can use it from:

- a [BinderHub](binderhub.md) notebook launched from the `AB-STABLE` or `AB-DEV`
  image, where everything is already set up, or
- the AF login nodes (`login.af.uchicago.edu`), with a JupyterHub API token.

It does not work from outside the AF, for example from your laptop.

## Cluster options and limits

You can set these options when you create a cluster:

| Option          | Default           | Allowed values | Description                                                                                |
| --------------- | ----------------- | -------------- | ------------------------------------------------------------------------------------------ |
| `image`         | depends on client | an image name  | Container image for the scheduler and the workers. It must include `dask-gateway`.         |
| `worker_cores`  | `2`               | 1 to 4         | CPU cores per worker                                                                       |
| `worker_memory` | `4`               | 1 to 8         | Memory per worker, in GiB                                                                  |
| `debug_logging` | `False`           | `True`/`False` | Save each worker's output in your home directory (see [Troubleshooting](#troubleshooting)) |

Clusters and workers are stopped automatically:

- A cluster with no activity for **1 hour** is shut down.
- Every cluster is shut down **24 hours** after it starts.
- Workers run in the batch system's
  [short queue](batch.md#short-and-long-queues), so each worker is stopped after
  **4 hours**.

Please shut your cluster down when you are done, so its workers are free for
other users.

### Pick an image that matches your Python environment

Your client, the scheduler and the workers need the same Python, `dask` and
`distributed` versions, and the worker image must have every package your tasks
use. Always set `image` to the one that matches where your client runs:

| Where your client runs                                                       | `image`                                                   |
| ---------------------------------------------------------------------------- | --------------------------------------------------------- |
| `AB-STABLE` notebook                                                         | `hub.opensciencegrid.org/usatlas/analysis-dask-base:main` |
| `AB-DEV` notebook                                                            | `hub.opensciencegrid.org/usatlas/analysis-dask-base:dev`  |
| Login node, set up as in [From the AF login nodes](#from-the-af-login-nodes) | `hub.opensciencegrid.org/usatlas/analysis-dask-base:main` |

Once you have a client, `client.get_versions(check=True)` raises an error if the
required versions don't match.

## From a BinderHub notebook

Launch `AB-STABLE` or `AB-DEV` from [BinderHub](binderhub.md). These images
include the Dask Gateway client, the Dask JupyterLab extension and the settings
for the AF gateway, and they log in with your notebook's own JupyterHub token.
Other images, such as `ml_platform`, don't include the Dask Gateway client.

### With the Dask sidebar

1. Click the Dask logo in JupyterLab's left sidebar.
2. Click **+ NEW**. The new cluster uses the worker image that matches your
   notebook image.
3. Click **SCALE** to choose the number of workers.
4. Click **<>** on the cluster to insert the code that connects a client to it
   into your notebook.

The panels in the sidebar, such as **Task Stream** and **Progress**, show plots
from the cluster's dashboard. Click **SHUTDOWN** on the cluster when you are
done.

### From code

```python
from dask_gateway import Gateway

gateway = Gateway()  # the image's settings point at the AF gateway

cluster = gateway.new_cluster(
    image="hub.opensciencegrid.org/usatlas/analysis-dask-base:main",  # AB-STABLE
    worker_cores=2,
    worker_memory=4,
)
cluster.scale(4)
client = cluster.get_client()
print(cluster.dashboard_link)
```

## From the AF login nodes

On the login nodes, Dask Gateway logs you in with a JupyterHub API token instead
of a browser login.

### 1. Create a JupyterHub API token

Go to
[https://jupyterhub.af.uchicago.edu/hub/token](https://jupyterhub.af.uchicago.edu/hub/token)
and log in with your AF account. Under **Request new API token**:

- add a note, for example `dask-gateway login nodes`,
- choose when the token expires, and
- under **Permissions**, enter `read:users:name!user`.

Dask Gateway only uses the token to find out who you are, so it doesn't need any
other permissions. A token with no permissions entered gets full access to your
JupyterHub account, including your notebook servers.

Copy the token: JupyterHub shows it only once.

### 2. Save the token on the login node

Save it in a file that only you can read, then export it in the shell where you
will run Python:

```bash
(umask 077 && cat > ~/.jupyterhub-token)  # paste the token, press Enter, then Ctrl-D
export JUPYTERHUB_API_TOKEN=$(cat ~/.jupyterhub-token)
```

Run the `export` line again in every new shell. Treat the file like a password,
and delete the token on the token page when you no longer need it.

### 3. Install the Dask Gateway client

The login nodes' system Python doesn't include the Dask Gateway client. Create a
virtual environment with versions that match the `analysis-dask-base:main` image
(Python 3.9 and `dask` 2024.3.1):

```bash
python3 -m venv ~/venvs/dask-gateway
source ~/venvs/dask-gateway/bin/activate
pip install "dask-gateway==2024.1.0" "dask==2024.3.1" "distributed==2024.3.1"
```

Install the other packages your analysis needs in the same environment. Next
time, just run `source ~/venvs/dask-gateway/bin/activate`.

### 4. Start a cluster

```python
from dask_gateway import Gateway

gateway = Gateway(
    address="https://daskgateway.af.uchicago.edu",
    proxy_address="gateway://10.101.14.69:80",
    public_address="https://jupyterhub.af.uchicago.edu/services/dask-gateway/",
    auth="jupyterhub",  # uses JUPYTERHUB_API_TOKEN
)

cluster = gateway.new_cluster(
    image="hub.opensciencegrid.org/usatlas/analysis-dask-base:main",
)
cluster.scale(2)
client = cluster.get_client()
print(cluster.dashboard_link)  # open this link in your browser
```

- `address` is the gateway's API.
- `proxy_address` is how your client reaches your cluster's scheduler. It can
  only be reached from inside the AF, which is why this works on the login nodes
  but not from your laptop.
- `public_address` is used for the dashboard links.

!!! warning "Keep these settings out of `~/.config/dask`"

    Your home directory is shared with your BinderHub notebooks, and they read
    `~/.config/dask` too. Settings saved there would replace the ones the notebook
    images come with, so pass them in your code as shown above.

## Managing your clusters

```python
# List your running clusters and reconnect to one
clusters = gateway.list_clusters()
cluster = gateway.connect(clusters[0].name)

# Shut it down when you are done
cluster.shutdown()
```

If your Python process ends without shutting its cluster down, for example
because the kernel died or your connection dropped, the cluster keeps running
until one of the [limits](#cluster-options-and-limits) stops it. Use
`gateway.list_clusters()` to find it and shut it down.

In scripts, context managers shut the cluster down even if the script fails:

```python
image = "hub.opensciencegrid.org/usatlas/analysis-dask-base:main"

with gateway.new_cluster(image=image) as cluster:
    cluster.scale(4)
    with cluster.get_client() as client:
        client.wait_for_workers(4)
        ...  # your analysis
```

## Troubleshooting

- **`ValueError: No JupyterHub API token provided`**: `JUPYTERHUB_API_TOKEN`
  isn't set in this shell. Run the `export` line from
  [step 2](#2-save-the-token-on-the-login-node).
- **`401, message='Unauthorized'`**: the token has expired or was deleted.
  Create a new one.
- **Version mismatch warnings, or errors when tasks run on the workers**: your
  client environment doesn't match the cluster image. See
  [Pick an image that matches your Python environment](#pick-an-image-that-matches-your-python-environment).
- **Timeouts connecting to the scheduler**: `proxy_address` can only be reached
  from inside the AF. Run your client on a login node or in a BinderHub
  notebook.
- **Workers don't start, or stop unexpectedly**: workers are batch jobs, so they
  wait for free slots when the facility is busy, and they are stopped after 4
  hours. To see their output, create the cluster with `debug_logging=True`. Each
  worker's output is then saved in `~/.dask-gateway/<id>/`, where `<id>` is the
  part of `cluster.name` after `dask-gateway.`.

For anything else, see [Getting Help](../getting_help.md).
