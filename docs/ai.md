# AI Tools for USATLAS Analysis

US ATLAS provides a [marketplace](https://github.com/usatlas/marketplace) of
plugins for AI coding assistants. The plugins give Claude Code, Cursor, and
Codex contextual knowledge about ATLAS analysis software, analysis facilities,
statistics tools, and the Scikit-HEP ecosystem — reducing hallucinations and
surfacing the right tool for each task.

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

BNL and SLAC facility skills are planned.

---

### `atlas`

Full ATLAS analysis plugin covering the workflow from raw data to publication.
Includes five AI subagents and 25 skills.

**Subagents** engage automatically when their domain comes up in conversation,
or can be invoked explicitly:

| Subagent                   | What it does                                                                                                                             |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `atlas-analysis-architect` | Designs end-to-end analysis pipelines and produces a structured analysis specification                                                   |
| `atlas-analysis-coder`     | Writes Python analysis code (uproot, ServiceX, coffea, hist) following ATLAS conventions                                                 |
| `atlas-docs-expert`        | Answers ATLAS software questions; fetches authoritative content from [atlas-software.docs.cern.ch](https://atlas-software.docs.cern.ch/) |
| `atlas-stats-expert`       | Designs statistical models: pyhf/cabinetry workspaces, TRExFitter configs, CLs limits                                                    |
| `atlas-data-explorer`      | Discovers datasets and replicas via the Rucio, AMI, and ATLAS Open Data MCP servers                                                      |

**Skills by category:**

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

Generic Python tooling for HEP workflows.

| Skill               | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| `cli-creator`       | Typer CLI scripts with modern `Annotated` syntax and pixi/uv environment management |
| `standalone-script` | PEP 723 inline-metadata scripts runnable with `uv run --script`                     |

---

## MCP Servers

The `atlas` plugin configures three
[Model Context Protocol](https://modelcontextprotocol.io/) servers that give AI
assistants live access to ATLAS data catalogs. These activate automatically when
the `atlas` plugin is installed and the servers are running.

### Rucio

Provides dataset and file replica discovery.

```bash
# Launch (read-only)
pixi exec rucio-mcp serve --read-only
```

**Required environment variables:**

```bash
export RUCIO_ACCOUNT=yourusername   # your CERN/grid username — no default
export RUCIO_AUTH_TYPE=x509_proxy   # or "oidc" or "userpass"
voms-proxy-init --voms atlas        # obtain a valid proxy first
```

See the [rucio-mcp documentation](https://rucio-mcp.readthedocs.io/en/latest/)
for full setup and authentication options.

### AMI

Provides AMI metadata: dataset tags, cross-sections, generator parameters.

```bash
pixi exec ami-mcp serve
```

See the [ami-mcp documentation](https://ami-mcp.readthedocs.io/en/latest/).

### ATLAS Open Data

Provides the ATLAS Open Data catalog for educational and public datasets.

```bash
uvx atlasopenmagic-mcp serve
```

See the
[atlasopenmagic-mcp repository](https://github.com/atlas-outreach-data-tools/atlasopenmagic-mcp).
