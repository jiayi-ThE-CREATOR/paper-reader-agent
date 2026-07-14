# paper-reader-agent

[简体中文](README.md) | [English](README.en.md) | **日本語**

手動 CLI：学術論文（URL / DOI / PDF）を取得 → Claude による構造化要約 → ローカル履歴 + Obsidian vault に書き込みます。

> これは [macos-llm-agents](https://github.com/jiayi-ThE-CREATOR/macos-llm-agents) から分割された独立リポジトリで、このエージェント 1 つだけを含みます（単独で動かすための最小ツールセット付き）。元のリポジトリでこのエージェントのディレクトリが持っていた完全なコミット履歴を保持しています。リポジトリにシークレットや個人パスは一切含まれません。プライベートな設定は `*.example` テンプレートとしてのみ入っており、実ファイルは `.gitignore` で除外しています。

## データフロー

```
URL / DOI / PDF パス
  → 全文抽出（PyMuPDF / BeautifulSoup）
  → Claude API による構造化要約
  → agents/paper_reader/papers.json に追記 + papers.md を更新
  → Obsidian vault へ Markdown ノートを出力
```

`research_interests.yaml` の `excluded_keywords` に該当する論文（書評・教科書紹介など）はスキップされます。詳細は [agents/paper_reader/CLAUDE.md](agents/paper_reader/CLAUDE.md) を参照。

## 使い方

launchd の定期実行はなく、純粋な手動 CLI です：

```bash
agents/paper_reader/run.sh <url-or-pdf-path>
```

## クイックスタート

```bash
# 1. クローン
git clone https://github.com/jiayi-ThE-CREATOR/paper-reader-agent.git && cd paper-reader-agent

# 2. シークレット設定
cp .env.example .env
$EDITOR .env

# 3. vault パス設定（唯一の定義場所。スクリプトはパスをハードコードしない）
cp vault.paths.example.env vault.paths.env
$EDITOR vault.paths.env

# 4. 研究興味の設定（論文の絞り込み/除外に使用）
cp agents/paper_reader/research_interests.example.yaml agents/paper_reader/research_interests.yaml
$EDITOR agents/paper_reader/research_interests.yaml

# 5. Python 依存関係のインストール
pip3 install -r agents/paper_reader/requirements.txt

# 6. 1 回実行
agents/paper_reader/run.sh <url-or-pdf-path>
```

必要な API key と取得先は [.env.example](.env.example) を参照してください。Python は既定でシステムの framework インタプリタを使います。環境変数で上書きできます：`PYTHON=/path/to/python3 agents/paper_reader/run.sh <url>`。

## リポジトリ構成

```
agents/paper_reader/        # 取得/要約/書き込みロジック + run.sh + research_interests.example.yaml
tools/load_env.sh           # スコープ付きシークレット注入
tools/vault_paths.py        # vault パスレジストリ解決（Python 側）
tools/vault_index_sync.py   # vault index/log 整合（書き込み後に自動呼び出し）
vault.paths.example.env     # vault パスのテンプレート
```

## 動作要件

- Python 3.14（または `PYTHON` 環境変数で自分のインタプリタを指定）
- ローカルの [Obsidian](https://obsidian.md/) vault

## ライセンス

[MIT](LICENSE)
