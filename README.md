Curriculum-Vitae
================

![lint text](https://github.com/ymzkryo/Curriculum-Vitae/workflows/lint%20text/badge.svg)
![build pdf](https://github.com/ymzkryo/Curriculum-Vitae/workflows/build%20pdf/badge.svg?branch=master)

ymzkryo の職務経歴書を Markdown で管理し、textlint による校正と PDF 生成を自動化したリポジトリです。

- **職務経歴書本体**: [docs/README.md](docs/README.md)
- **Web 版**: https://ymzkryo.github.io/Curriculum-Vitae/ （GitHub Pages / Cayman テーマ）
- **PDF 版**: [Releases](https://github.com/ymzkryo/Curriculum-Vitae/releases) の各タグに添付（例: タグ `v26.7.0` → `resume_v26-7-0.pdf`。ドットはハイフンに置換される）

Requirements
------------

| ツール | バージョン | 備考 |
|:-------|:-----------|:-----|
| [mise](https://mise.jdx.dev/) | - | Node と pnpm のバージョン管理 |
| Node.js | 24.18.0 | `mise.toml` で固定 |
| pnpm | 11.15.0 | `mise.toml` と `packageManager` で固定 |

Node 25 以降では `md-to-pdf` が依存する `yargs@17` が `require(ESM)` の挙動変更で動作しないため、LTS の 24 系に固定しています。

Install
-------

```sh
mise install     # mise.toml に従って Node / pnpm を導入
pnpm install     # 依存パッケージを導入
```

`pnpm install` 時に puppeteer が Chromium をダウンロードします。pnpm はデフォルトで postinstall を実行しないため、`pnpm-workspace.yaml` の `allowBuilds` で明示的に許可しています。

Usage
-----

```sh
pnpm lint            # textlint で docs/README.md を校正
pnpm lint --fix      # 自動修正できるものを修正
pnpm build:pdf         # docs/README.pdf を生成
pnpm build:pdf:release # 上記に加えて resume_<年>-<月>.pdf を生成（例: resume_2026-07.pdf）
```

配布用に渡すときは `build:pdf:release` を使う。生成物は `.gitignore` の対象で、リポジトリには含めない。

### 更新からリリースまで

```sh
# 1. 職務経歴書を編集
$EDITOR docs/README.md

# 2. 校正と PDF ビルドを確認
pnpm lint
pnpm build:pdf

# 3. コミットして master へ
git switch -c update/YYYY-MM
git commit -m "Update: ..."
gh pr create

# 4. マージ後、タグを打って push（CI が Release を作成し resume_v26-7-0.pdf 形式で添付）
git switch master && git pull
git tag -a vYY.M.PATCH -m "職務経歴書 YYYY年M月版"
git push origin vYY.M.PATCH
```

タグは `v26.7.0` のように `v{西暦下2桁}.{月}.{パッチ}` の形式で運用しています。

Structure
---------

```
.
├── docs/
│   ├── README.md        # 職務経歴書本体（textlint の対象）
│   ├── README.pdf       # 生成物（git 管理外）
│   └── _config.yml      # GitHub Pages の設定
├── pdf-configs/
│   ├── config.js        # md-to-pdf の設定（A4 / 余白 / ページ番号）
│   └── style.css        # PDF 用スタイル
├── .github/workflows/
│   ├── lint-text.yml    # push / PR で textlint を実行
│   ├── build-pdf.yml    # v* タグの push で PDF をビルドし Release に添付
│   └── create_issue.yml # 3ヶ月ごとに更新リマインドの Issue を作成
├── .textlintrc          # 校正ルール
└── mise.toml            # Node / pnpm のバージョン固定
```

Lint
----

`.textlintrc` で以下のプリセットを中心に設定しています。

- `preset-ja-technical-writing` — 技術文書向けの日本語ルール（漢字連続、一文の長さなど）
- `preset-jtf-style` — JTF 日本語標準スタイルガイド
- `preset-ja-spacing` — 全角と半角の間のスペース
- `spellcheck-tech-word` / `prh` — 技術用語の表記ゆれ

CI でも同じルールが走るため、push 前に `pnpm lint` を通しておくと手戻りがありません。

License
-------

MIT

Author
------

ymzkryo
