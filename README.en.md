# paper-reader-agent

[简体中文](README.md) | **English** | [日本語](README.ja.md)

Manual CLI: fetches an academic paper (URL / DOI / PDF) → Claude structured summary → writes to a local history file + an Obsidian vault.

> This is an independent split-out of [macos-llm-agents](https://github.com/jiayi-ThE-CREATOR/macos-llm-agents), containing only this one agent (plus the minimal toolset it needs to run standalone), with the full commit history of that agent's directory in the original monorepo preserved. The repo contains no secrets or personal paths — private config lives only as `*.example` templates, with the real files excluded via `.gitignore`.

## Data flow

```
URL / DOI / PDF path
  → full-text extraction (PyMuPDF / BeautifulSoup)
  → Claude API structured summary
  → append to agents/paper_reader/papers.json + update papers.md
  → Markdown note written to the Obsidian vault
```

Papers matching `excluded_keywords` in `research_interests.yaml` are skipped (e.g. book reviews, textbook intros). Details: [agents/paper_reader/CLAUDE.md](agents/paper_reader/CLAUDE.md).

## Usage

No launchd schedule — pure manual CLI:

```bash
agents/paper_reader/run.sh <url-or-pdf-path>
```

## Quick start

```bash
# 1. Clone
git clone https://github.com/jiayi-ThE-CREATOR/paper-reader-agent.git && cd paper-reader-agent

# 2. Configure secrets
cp .env.example .env
$EDITOR .env

# 3. Configure vault path (the single source of truth; scripts never hardcode paths)
cp vault.paths.example.env vault.paths.env
$EDITOR vault.paths.env

# 4. Configure research interests (used to filter/exclude papers)
cp agents/paper_reader/research_interests.example.yaml agents/paper_reader/research_interests.yaml
$EDITOR agents/paper_reader/research_interests.yaml

# 5. Install Python dependencies
pip3 install -r agents/paper_reader/requirements.txt

# 6. Run once
agents/paper_reader/run.sh <url-or-pdf-path>
```

The required API keys and where to get them are listed in [.env.example](.env.example). Python defaults to the system framework interpreter; override it with an environment variable: `PYTHON=/path/to/python3 agents/paper_reader/run.sh <url>`.

## Repository layout

```
agents/paper_reader/        # fetch/summarize/write logic + run.sh + research_interests.example.yaml
tools/load_env.sh           # scoped secret injection
tools/vault_paths.py        # vault path registry resolver (Python side)
tools/vault_index_sync.py   # vault index/log reconciliation (auto-called after writes)
vault.paths.example.env     # vault paths template
```

## Platform requirements

- Python 3.14 (or point the `PYTHON` environment variable at your own interpreter)
- A local [Obsidian](https://obsidian.md/) vault

## License

[MIT](LICENSE)
