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
| `atlas-data-explorer`      | Finds datasets and replicas via the Rucio, AMI, and ATLAS Open Data MCP servers                                    |

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

## MCP servers

The `atlas` plugin configures three
[Model Context Protocol](https://modelcontextprotocol.io/) servers. Start them
alongside your assistant session and it can query live ATLAS catalogs rather
than rely on training data.

### Rucio

Dataset and file replica discovery.

```bash
pixi exec rucio-mcp serve --read-only
```

`RUCIO_ACCOUNT` has no default. Set it before launching:

```bash
export RUCIO_ACCOUNT=yourusername   # your CERN/grid username
export RUCIO_AUTH_TYPE=x509_proxy   # or "oidc" or "userpass"
voms-proxy-init --voms atlas
```

See the [rucio-mcp documentation](https://rucio-mcp.readthedocs.io/en/latest/)
for authentication options.

### AMI

Dataset tags, cross-sections, and generator parameters from AMI.

```bash
pixi exec ami-mcp serve
```

See the [ami-mcp documentation](https://ami-mcp.readthedocs.io/en/latest/).

### ATLAS Open Data

The ATLAS Open Data catalog, for educational and public datasets.

```bash
uvx atlasopenmagic-mcp serve
```

See the
[atlasopenmagic-mcp repository](https://github.com/atlas-outreach-data-tools/atlasopenmagic-mcp).
