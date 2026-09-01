# AI tools for USATLAS analysis

US ATLAS maintains a [marketplace](https://github.com/usatlas/marketplace) of
plugins for AI coding assistants. The plugins load ATLAS-specific context into
Claude Code, Cursor, and Codex: what tools exist, how they fit together, and
when to use each one. In practice this means the assistant already knows that
ATLAS NTuples use MeV, how to write a pyhf workspace, and how to find datasets
on the grid, so you spend less time correcting it.

## Installation

=== "Claude Code"

    ```bash
    /plugins marketplace add usatlas/marketplace
    ```

    Then install whichever plugins you need from the marketplace browser.

=== "Cursor"

    Each plugin ships a `.cursor-plugin/plugin.json`. Add the marketplace
    repository as a plugin source in Cursor's settings, then enable individual
    plugins.

=== "Codex"

    ```bash
    git clone https://github.com/usatlas/marketplace.git ~/usatlas-marketplace
    mkdir -p ~/.agents/skills
    ln -s ~/usatlas-marketplace/plugins/atlas/skills ~/.agents/skills/atlas
    ln -s ~/usatlas-marketplace/plugins/analysis-facilities/skills \
          ~/.agents/skills/analysis-facilities
    ln -s ~/usatlas-marketplace/plugins/hep-python-tools/skills \
          ~/.agents/skills/hep-python-tools
    ```

    See [`.codex/INSTALL.md`](https://github.com/usatlas/marketplace/blob/main/.codex/INSTALL.md)
    for Windows instructions and per-plugin selective install.

## Plugins

### `analysis-facilities`

Skills for working at each USATLAS Analysis Facility.

| Skill         | Description                                                                                          |
| ------------- | ---------------------------------------------------------------------------------------------------- |
| `uchicago-af` | HTCondor batch jobs, JupyterLab, XCache, Rucio, ServiceX, Coffea-Casa, and Triton at af.uchicago.edu |

BNL and SLAC facility skills are in progress.

---

### `atlas`

The main ATLAS analysis plugin. Five subagents and 25 skills, covering
everything from dataset discovery to publication-ready statistical fits.

The subagents activate when their domain comes up, or you can invoke them
directly:

| Subagent                   | What it does                                                                                                       |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `atlas-analysis-architect` | Designs analysis pipelines; produces a structured specification                                                    |
| `atlas-analysis-coder`     | Writes Python analysis code (uproot, ServiceX, coffea, hist) following ATLAS conventions                           |
| `atlas-docs-expert`        | Answers ATLAS software questions, pulling from [atlas-software.docs.cern.ch](https://atlas-software.docs.cern.ch/) |
| `atlas-stats-expert`       | Builds statistical models: pyhf/cabinetry workspaces, TRExFitter configs, CLs limits                               |
| `atlas-data-explorer`      | Finds datasets and replicas via the Rucio, AMI, and ATLAS Open Data CLI tools                                      |

Skills by category:

| Category    | Skills                                                                            |
| ----------- | --------------------------------------------------------------------------------- |
| Orientation | `atlas-software`                                                                  |
| Statistics  | `pyhf`, `cabinetry`, `pyhs3`, `histfitter`, `trexfitter`, `roounfold`             |
| Frameworks  | `topcptoolkit`, `fastframes`                                                      |
| Data access | `servicex`, `analysis-spec-builder`, `fsspec-xrootd`                              |
| Core tools  | `uproot`, `awkward`, `coffea`, `hist`, `vector`                                   |
| Scikit-HEP  | `iminuit`, `fastjet`, `particle`, `hepunits`, `decaylanguage`, `pyhepmc`, `pylhe` |
| C++ interop | `cpp-bindings`                                                                    |

---

### `hep-python-tools`

Two skills for writing self-contained Python scripts and CLIs.

| Skill               | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| `cli-creator`       | Typer CLI scripts with modern `Annotated` syntax and pixi/uv environment management |
| `standalone-script` | PEP 723 inline-metadata scripts runnable with `uv run --script`                     |

---

## MCP servers: letting your assistant act on live systems

Everything above (the plugins) teaches your assistant _how_ ATLAS analysis
works. MCP ([Model Context Protocol](https://modelcontextprotocol.io/)) goes a
step further: it lets the assistant actually _run_ things on real, live systems
on your behalf, in the same conversation, instead of just describing commands
for you to copy and run yourself.

For example, without MCP, you'd ask the assistant "where can I find dataset X?",
it would tell you to run `rucio list-file-replicas ...`, and you'd copy that
command, run it in a terminal, then paste the output back so it could continue.
With MCP connected, the assistant runs that Rucio lookup itself and uses the
real result directly — one less round trip, and no need for you to already know
the right command.

### AF MCP Platform (UChicago)

UChicago AF runs one such MCP server: the **AF MCP Platform**. It gives a
connected assistant secure, on-your-behalf access to Rucio (find/download
datasets), AMI (dataset metadata), HTCondor (check or submit batch jobs), your
running JupyterLab notebook, and the AF filesystem — without you having to run
any commands yourself.

To use it:

1. **Install the `af-uchicago` plugin in Claude Code** (see
   [Installation](#installation) above) — it already comes configured with this
   server, so there is nothing else to set up. (Using Cursor, Codex, or another
   tool, or want to connect without installing the plugin? Point it at
   `https://mcp.af.uchicago.edu/mcp` directly — see your tool's docs for how to
   add an MCP server by URL.) The first time it's actually used, most tools will
   pop open a browser window asking you to log in with your usual AF credentials
   — that's it, no passwords or tokens to copy anywhere.
2. **Link your identity**, once, at
   `https://mcp-portal.af.uchicago.edu/identities/`. This tells the platform
   which ATLAS/CERN account to act as when it talks to Rucio, AMI, or HTCondor
   for you. Skip this step and the connection still works, but grid-dependent
   tools (Rucio, AMI, HTCondor) will fail with an authentication error until you
   link your identity — link it before relying on those.

If your AI tool can't open a browser to log in (for example, it's running on a
remote server with no display), generate a long-lived access token instead at
`https://mcp-portal.af.uchicago.edu/tokens/` and use it in place of the browser
login. The same portal (`https://mcp-portal.af.uchicago.edu/`) is also where you
can review and manage your connection.

### Jupyter (your running notebook at UChicago)

If you have a JupyterLab session running at `af.uchicago.edu`, your MCP client
can drive it directly — listing cells, executing code, reading kernel output.
The server runs inside your singleuser pod, so authentication reuses your
existing notebook credential (JupyterHub API token with the `access:servers`
scope for `jupyterhub.af.uchicago.edu`, or the per-pod URL token for the
`af.uchicago.edu/jupyterlab` portal).

See
[Connecting an MCP client to your notebook](uchicago/jupyter.md#connecting-an-mcp-client-to-your-notebook)
for the full setup, including the difference in token lifetime between the two
launch surfaces.

---

## Example: Claude Code with NRP models + Jupyter MCP

Claude Code does not have to talk to Anthropic's API. You can point it at the
[National Research Platform](https://nrp.ai/) (NRP) LLM endpoint to run
open-weight models such as `qwen3`, and — in the same session — attach the
[Jupyter MCP server](#jupyter-your-running-notebook-at-uchicago) so the model
can drive a live notebook. This walkthrough wires both together against a
BinderHub-launched notebook.

You need three things:

1. Claude Code, configured to use NRP's LLM endpoint
2. An NRP account with an LLM API token
3. A BinderHub repo with
   [`jupyter-server-proxy`](https://github.com/jupyterhub/jupyter-server-proxy)
   and `jupyter-server-mcp` installed (the MCP server exposes the notebook on
   port 3001)

### Step 1 — get an NRP LLM token

Create an account at [nrp.ai](https://nrp.ai/), then mint an LLM token at
[https://nrp.ai/llmtoken/](https://nrp.ai/llmtoken/). This token is your
`ANTHROPIC_AUTH_TOKEN` in the steps below.

### Step 2 — configure Claude Code

Edit your Claude Code config (`~/.claude.json` or equivalent) to route requests
to NRP and register the Jupyter MCP server:

```json
{
    "env": {
        "ANTHROPIC_BASE_URL": "https://ellm.nrp-nautilus.io/anthropic",
        "ANTHROPIC_API_KEY": "YOUR_NRP_TOKEN_HERE",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "qwen3",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "qwen3",
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "qwen3",
        "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
        "CLAUDE_CODE_DISABLE_FEEDBACK_SURVEY": "1",
        "CLAUDE_CODE_ENABLE_TELEMETRY": "0",
        "API_TIMEOUT_MS": "3000000",
        "DISABLE_TELEMETRY": "1"
    },
    "mcpServers": {
        "jupyter": {
            "command": "npx",
            "args": [
                "mcp-remote",
                "https://<YOUR_JUPYTERHUB_URL>/user/<your-username>/<your-servername>/proxy/3001/mcp?token=<YOUR_JUPYTER_TOKEN>",
                "--transport",
                "http-only"
            ]
        }
    }
}
```

The three model slots all map to `qwen3` so every Claude Code model tier is
served by the same NRP model.

### Step 3 — launch a BinderHub notebook

Launch your Binder at
[https://binderhub.ssl-hep.org/](https://binderhub.ssl-hep.org/), wait for the
server to start, and note the server name from the launch URL. Once it is
running, construct the MCP URL:

```text
https://<jupyterhub-host>/user/<your-username>/<server-name>/proxy/3001/mcp?token=<token>
```

- The **server name** comes from your BinderHub launch URL.
- The **token** is a JupyterHub API token from
  `https://<jupyterhub-host>/hub/token`.

Plug this URL into the `jupyter` MCP entry from Step 2.

### Step 4 — start Claude Code

Launch without triggering the Anthropic login flow:

```bash
export ANTHROPIC_AUTH_TOKEN="YOUR_NRP_TOKEN_HERE"
export ANTHROPIC_BASE_URL="https://ellm.nrp-nautilus.io/anthropic"
unset ANTHROPIC_API_KEY
claude
```

!!! note "Why `unset ANTHROPIC_API_KEY`?"

    Claude Code uses `ANTHROPIC_AUTH_TOKEN` when `ANTHROPIC_API_KEY` is absent.
    Unsetting it ensures the NRP token is used without triggering a login
    prompt.

### Reference

| What          | Where                                     |
| ------------- | ----------------------------------------- |
| NRP token     | `https://nrp.ai/llmtoken/`                |
| BinderHub     | `https://binderhub.ssl-hep.org/`          |
| LLM endpoint  | `https://ellm.nrp-nautilus.io/anthropic`  |
| Model name    | `qwen3` (for the opus/sonnet/haiku slots) |
| MCP transport | `http-only` via `mcp-remote`              |
| MCP port      | `3001` (proxied through JupyterHub)       |
