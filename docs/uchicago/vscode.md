# Using VSCode at UChicago

For general information about VSCode and its features, see the
[VSCode Overview](../tools/vscode.md).

This page covers UChicago-specific setup instructions for using VSCode with the
UChicago Analysis Facility.

## Connecting VSCode to JupyterLab Kernel

You can follow these steps to connect your Visual Studio Code to a JupyterLab
kernel that you launch with BinderHub on the University of Chicago Analysis
Facility (UC AF). This allows you to work on your Jupyter notebooks using VS
Code while utilizing the computational resources of UC AF.

!!! note

    Make sure your BinderHub server is running and remains active while you are
    using VS Code to connect.

### Steps

1. **Launch Your Server with BinderHub**:
    - Follow [How to launch a notebook](binderhub.md#how-to-launch-a-notebook)
      to start a JupyterLab server from
      [BinderHub](https://binderhub.af.uchicago.edu/).
    - Your running servers are listed on the
      [JupyterHub home page](https://jupyterhub.af.uchicago.edu/hub/home).

2. **Get an API Token**:
    - Visit
      [https://jupyterhub.af.uchicago.edu/hub/token](https://jupyterhub.af.uchicago.edu/hub/token),
      create a token, and request the **`access:servers`** scope (or
      **`access:servers!user=<your-username>`** to restrict it to your own
      servers).
    - Save the token; you will not see it again. The same token keeps working
      for servers you launch later.

3. **Get the Server URL**:
    - Copy the address of your running server from your browser, up to and not
      including `/lab`. This typically looks like
      `https://jupyterhub.af.uchicago.edu/user/<your-username>/`.
    - Append your token to it, so it looks like
      `https://jupyterhub.af.uchicago.edu/user/<your-username>/?token=<your-api-token>`.

4. **Configure VS Code**:
    - Open Visual Studio Code.
    - Install the **Python** and **Jupyter** extensions if you haven't already.

5. **Select Your Server and Kernel**:
    - Open the notebook file you wish to work on in VS Code.
    - Click on the kernel picker in the top right corner of the notebook editor
    - Click the kernel dropdown → click "Select Another Kernel..." → then
      "Existing Jupyter Server...".
    - Paste your server URI there.
    - Select the kernel you want to use from your JupyterLab.

6. **Using the Remote Kernel**:
    - Once the correct Kernel is selected, you can execute your notebook code
      within VS Code, utilizing the UC AF's computational resources.
    - If you encounter issues, ensure that VS Code is allowed through your
      firewall if applicable, and your network allows communicating with the UC
      AF nodes.
    - If your server is stopped (idle servers are stopped after 24 hours),
      launch it again from BinderHub and reconnect with the same token.

## Getting help

!!! note "Need help?"

    See our [Getting Help](../getting_help.md) page for support options and how to
    reach the ATLAS AF team.
