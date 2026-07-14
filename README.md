# paper-reader-agent

手动 CLI：抓取学术论文（URL / DOI / PDF）→ Claude 结构化摘要 → 写入本地历史 + Obsidian vault。

> 这是 [macos-llm-agents](https://github.com/jiayi-ThE-CREATOR/macos-llm-agents) 的独立拆分仓库，只含这一个 agent（附带独立运行所需的最小工具集），保留原仓库中该 agent 目录的完整提交历史。仓库内不含任何密钥或个人路径——私人配置以 `*.example` 模板入库，真身由 `.gitignore` 排除。

## 数据流

```
URL / DOI / PDF 路径
  → 全文抽出（PyMuPDF / BeautifulSoup）
  → Claude API 结构化摘要
  → agents/paper_reader/papers.json 追加 + papers.md 更新
  → Obsidian vault Markdown 笔记输出
```

`research_interests.yaml` 的 `excluded_keywords` 命中的论文会被跳过（如书评/教科书介绍）。详见 [agents/paper_reader/CLAUDE.md](agents/paper_reader/CLAUDE.md)。

## 使用

无 launchd 定时任务，纯手动 CLI：

```bash
agents/paper_reader/run.sh <url-or-pdf-path>
```

## 快速开始

```bash
# 1. 克隆
git clone https://github.com/jiayi-ThE-CREATOR/paper-reader-agent.git && cd paper-reader-agent

# 2. 配置密钥
cp .env.example .env
$EDITOR .env

# 3. 配置 vault 路径（唯一定义处，脚本不硬编码路径）
cp vault.paths.example.env vault.paths.env
$EDITOR vault.paths.env

# 4. 配置研究兴趣（用于筛选/排除论文）
cp agents/paper_reader/research_interests.example.yaml agents/paper_reader/research_interests.yaml
$EDITOR agents/paper_reader/research_interests.yaml

# 5. 安装 Python 依赖
pip3 install -r agents/paper_reader/requirements.txt

# 6. 跑一次
agents/paper_reader/run.sh <url-or-pdf-path>
```

需要的 API key 及获取地址见 [.env.example](.env.example)。Python 默认走系统 framework 解释器，可用环境变量覆盖：`PYTHON=/path/to/python3 agents/paper_reader/run.sh <url>`。

## 仓库结构

```
agents/paper_reader/        # 抓取/摘要/写入逻辑 + run.sh + research_interests.example.yaml
tools/load_env.sh           # 作用域密钥注入
tools/vault_paths.py        # vault 路径注册表解析（Python 侧）
tools/vault_index_sync.py   # vault index/log 对账（写入后自动调用）
vault.paths.example.env     # vault 路径模板
scripts/                    # install_launchagents.sh（本 agent 无 plist，保留以与其他拆分仓库结构一致）
```

## 平台要求

- Python 3.14（或用 `PYTHON` 环境变量指向你的解释器）
- 本地一个 [Obsidian](https://obsidian.md/) vault

## License

[MIT](LICENSE)
