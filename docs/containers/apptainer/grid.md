# Container-based Jobs on the Grid

There are more resources available on the grid, you can run container-based jobs
on the grid. Both `prun` and `pathena` provide an option `--containerImage` to
allow jobs to run inside a specified container on the grid. Check the following
page for more details:

<https://twiki.cern.ch/twiki/bin/view/PanDA/PandaRun#Run_user_containers_jobs>

You can also run `prun --helpGroup=containerJob` for more container-related
options.

Please note that you should test your job interactively first as documented
above, prior to submitting them to the grid.
