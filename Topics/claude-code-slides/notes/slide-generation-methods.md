---
title: "Claude Codeでスライド生成する方法とその例"
created: "2026-03-08"
topic: "claude-code-slides"
tags:
  - topic/claude-code-slides
---

# Claude Codeでスライド生成する方法とその例

## Summary
- Claude Codeでスライドを作る方法は大きく4つ: **Marp連携**、**reveal.js Skill**、**PPTX Skill**、**MCP Server**
- 最も手軽なのはMarp Skill/MCP、最高品質を目指すならClaude Code + Marp + 品質検証パイプライン
- 企業テンプレート必須ならPPTX Skill、Webプレゼンならreveal.js Skillが最適

## Notes

### 方法1: Marp Skill（最も人気）

MarpはMarkdownからスライドを生成するツール。Claude CodeのSkillとして定義することで、自然言語でスライドを作れる。

#### セットアップ
`.claude/skills/marp/SKILL.md` を作成し、Marpの構文ルールやベストプラクティスを記述する。

#### Skill定義の例

```markdown
---
name: marp-slide
description: Create professional Marp presentation slides
---

# Marp Slide Generator

## Rules
- Use `---` to separate slides
- First slide must have `marp: true` in frontmatter
- Use `<!-- _class: lead -->` for title slides
- Keep content concise: max 5-7 bullet points per slide
- Use `![bg right:40%](image.png)` for side images

## Themes
Available themes: default, gaia, uncover

## Output
Write slides to `slides.md` in the current directory.
```

#### 使い方の例

```
> スライドを作って：Claude Codeの活用事例、10枚程度
```

Claude Codeが以下のようなMarpファイルを生成する:

```markdown
---
marp: true
theme: default
paginate: true
---

<!-- _class: lead -->

# Claude Codeの活用事例

2026年版

---

## Claude Codeとは

- AnthropicのCLIベースAIアシスタント
- ターミナルから直接コードを読み書き
- Git操作、テスト実行、リファクタリングを自動化

---

## 活用事例1: バグ修正

- エラーログを貼り付けるだけで原因特定
- 修正コードの提案と適用
- テスト実行まで一気通貫

![bg right:40%](https://example.com/bugfix.png)

---

## 活用事例2: コードレビュー

- PR単位で差分を分析
- セキュリティリスクの自動検出
- リファクタリング提案

---
```

#### PDFへの変換

```bash
# Marp CLIでPDFに変換
npx @marp-team/marp-cli slides.md --pdf

# HTMLに変換
npx @marp-team/marp-cli slides.md --html

# PPTXに変換
npx @marp-team/marp-cli slides.md --pptx
```

### 方法2: Marp MCP Server

MCP（Model Context Protocol）を使い、Claude CodeやClaude DesktopからMarpスライドを生成する。

#### インストール

`claude_desktop_config.json` または `.claude/settings.json` に追加:

```json
{
  "mcpServers": {
    "marp": {
      "command": "npx",
      "args": ["-y", "@masaki39/marp-mcp", "-t", "default"]
    }
  }
}
```

#### 特徴
- 7種類のレイアウト: title, section, content, table, multi-column, figure, image
- テーマ: default, gaia, uncover, academic
- PDF/HTML/PPTX形式へのエクスポート対応

### 方法3: reveal.js Skill

Webベースのインタラクティブなプレゼンテーションを生成する。ビルド不要でHTMLファイルをそのまま開ける。

#### インストール

```bash
# Claude Code Pluginとして
/plugin marketplace add ryanbbrown/revealjs-skill
/plugin install revealjs@revealjs-skill
npm install --prefix ~/.claude/plugins/cache/revealjs
```

#### 使い方の例

```
> Create a 10-slide presentation about renewable energy trends
> Make a pitch deck for a SaaS startup
```

#### 特徴
- カスタムCSSテーマ
- マルチカラムレイアウト
- Chart.jsによるデータ可視化
- スピーカーノート、アニメーション
- オーバーフロー自動検出
- PDF出力対応
- ブラウザ上でのインライン編集

### 方法4: PPTX生成 Skill（ppt-creator）

PowerPointファイルを直接生成する方法。企業テンプレートとの互換性が高い。

#### 特徴
- Marp経由とdocument-skills経由の2パスで、2種類のPPTXを同時生成
- ピラミッド原則やアサーション・エビデンス手法を自動適用
- データドリブンなチャート挿入
- スピーカーノート付き

### 方法5: Subagent構成（上級者向け）

Claude Codeの `.claude/agents/` にスライド生成専用のSubagentを定義する高度な方法。

#### Subagent定義例（`.claude/agents/slide-creator.md`）

```markdown
---
name: slide-creator
description: Marpスライドを自動生成するエージェント
skills: marp
---

# Slide Creator Agent

## Workflow
1. ユーザーの要求を分析し、スライド構成を提案
2. 承認後、Marp形式でスライドを生成
3. 品質チェック（文字量、レイアウト）
4. 必要に応じてスクリーンショットで視覚確認
```

### 選択ガイド

| 方法 | 最適な用途 | 難易度 | 出力形式 |
|------|-----------|--------|----------|
| Marp Skill | 技術発表、社内勉強会 | 低 | MD → PDF/HTML/PPTX |
| Marp MCP | Claude Desktopとの連携 | 低 | MD → PDF/HTML/PPTX |
| reveal.js | Webプレゼン、インタラクティブ | 中 | HTML |
| PPTX Skill | 企業テンプレ必須の場面 | 中 | PPTX |
| Subagent | カスタムワークフロー | 高 | 任意 |

## Questions
- Slidevとの連携も有力な選択肢になりうるか？
- 品質検証（SlideGaugeなど）の自動化はどこまで実用的か？
- 画像生成（DALL-E等）との組み合わせはどうするか？

## References
- [reveal.js Skill - GitHub](https://github.com/ryanbbrown/revealjs-skill)
- [Marp MCP Server](https://glama.ai/mcp/servers/@masaki39/marp-mcp)
- [AIエージェントと協働してmarpでスライドを作る2026 - Qiita](https://qiita.com/hirokidaichi/items/243bd176b84900f4cc0d)
- [「スライド作って」で本当にスライドができる - Qiita](https://qiita.com/toku345/items/11158328ca098957ff27)
- [AIスライド作成の最適解を探る 4大ツール比較 - Zenn](https://zenn.dev/mjinia/articles/cef5337a4f177f)
- [creating-md-slides Skill - LobeHub](https://lobehub.com/skills/vladolaru-claude-code-plugins-creating-md-slides)
- [marp-slide Skill - Playbooks](https://playbooks.com/skills/davila7/claude-code-templates/marp-slide)
