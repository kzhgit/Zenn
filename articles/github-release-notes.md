---
title: "色々なライブラリやフレームワークのリリースノート（GitHub Releases）をまとめてみた"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["github", "githubactions", "ci", "yaml"]
published: false
---

# はじめに

業務でリリースノートを作成することがあり、どういうふうにまとめると見やすいのだろうかと考えたのがきっかけでこの記事を書きました。  

書き方の参考にするため、いくつかの人気のあるライブラリやフレームワークのGitHub Releasesを調査してみたところ、それぞれで書き方が違っていたのが面白かったので紹介します。

この記事では、様々なライブラリのGitHub Releaseでのリリースノートの作り方を紹介し、効果的なリリースノートのパターンを探ります。

この記事が皆さんのリリースノート作成の参考になれば幸いです。

# GitHub Releasesとはなにか？

GitHub ReleasesはGitHubのリポジトリにあるリリースノートを管理するための機能です。
リリースノートでは、ソースコードやリリースされたアセットのダウンロードも可能です。

## GitHubでリリースノートを管理する方法

詳しくは公式ドキュメントを見てください。
https://docs.github.com/ja/repositories/releasing-projects-on-github/managing-releases-in-a-repository

# 各ライブラリのGitHub Releaseを紹介

筆者が普段使用しているもの、または興味のあるライブラリやフレームワークのGitHub Releaseを調査しました。

## Next.js

Next.jsはVercelが開発するReactフレームワークで、多くの開発者に支持されているフロントエンドフレームワークの一つです。

### リリースノートの構成

Next.jsのGitHub Releaseノートは、明確な構造と簡潔な説明が特徴的です。以下のような構成になっています：

- リリースタイトル（バージョン番号）
- 簡単な説明や重要な変更点のサマリー（ある場合）
- コアの変更点（Core Changes）
- ドキュメントの変更点（Documentation Changes）
- 例示コードの変更点（Example Changes）
- その他の変更点（Misc Changes）

各セクションでは、変更内容とそれに対応するプルリクエスト番号が記載されています。
例えば：

```
- Fix white screen when navigating to pages in certain cases: #51866
```

このようにシンプルな形式で、何が変更されたのかとその参照先が分かりやすく表示されています。

### リリースノートの例

最近のリリース（v14.0.0）を例に挙げると、以下のように整理されています：

```md
### Core Changes

* perf: fix server trace file logic : #56898
* feat: drop Node.js 16: #56896
* Update React from d900fadbf to 09fbee89d. Removes server context and experimental prefix for server action APIs: #56809
* feat(env): upgrade `dotenv`: #38481
...
```

このように、変更内容がカテゴリ別に整理され、それぞれの変更に対応するPRへのリンクも付与されているため、詳細を確認したい開発者は容易に元のコードを参照することができます。

### リリースノート作成プロセス

Next.jsのGitHub Releaseノート作成プロセスは、次のような流れになっていると考えられます：

1. **PR管理とラベリング**: マージされるPRには適切なラベル（バグ修正、機能追加、ドキュメント更新など）が付けられます

2. **カテゴリ分け**: GitHub ActionsやRelease Drafterなどのツールを使用して、ラベルに基づき変更をカテゴリごとに自動分類します

3. **変更内容の整形**: 各PRのタイトルや説明から、簡潔な変更説明文を作成します。Next.jsのチームは「動詞: 変更内容」というフォーマット（例: `fix: handle 404 errors in HotReload client`）を多用しています

4. **最終確認と公開**: リリース担当者が自動生成されたリリースノートを確認し、必要に応じて編集を加えた後、公開します

実際、Next.jsのリポジトリでは、リリースノートの生成を効率化するためのスクリプトも用意されており、`chore(script): improve markdown changelog output in sync-react.js`というコミットが見られることから、チームはリリースノート生成のプロセスを継続的に改善していることがわかります。

### 開発者にとってのメリット

Next.jsのリリースノートの特徴は以下の点が挙げられます：

1. **シンプルで簡潔な記述**: 各変更点は1行で簡潔に説明されているため、スキャンしやすい
2. **明確なカテゴリ分け**: Core/Documentation/Example/Miscなど、明確なカテゴリ分けにより関心領域だけをチェックできる
3. **PRリンクの明示**: すべての変更に対応するPR番号がリンクされており、詳細な変更内容やコード差分を確認できる
4. **重要な変更の強調**: 特に重要な変更がある場合、リリースノートの冒頭に記載される

### 独自の工夫

また、最近のNext.jsコミュニティでは、リリースノートを見やすく整理するための独自のウェブサイトも開発されています。例えば、[openchangelog](https://openchangelog.com/blog/nextjs-changelog)のようなサービスを使用すると、Next.jsのリリースノートをGitHubと連携して、より見やすい形式で表示することができます。

これにより、単なるGitHub Releaseの形式を超えて、カスタマイズされた表示やフィルタリング機能を持つ専用のリリースノートページを作成することが可能になっています。

このようなNext.jsのリリースノート作成方法は、大規模なプロジェクトでの効率的な情報伝達の良い例となっており、他のプロジェクトでも参考にできる点が多いでしょう。

## React

追加予定

## Slack（ユニークなリリースノートの例）

追加予定

## Angular

追加予定

## Svelte

追加予定

## TypeScript

追加予定

## TailwindCSS

追加予定

# 4. おわりに

[Keep a Changelog](https://keepachangelog.com/ja/1.0.0/)プロジェクトが述べているように、リリースノートは単なる変更履歴ではなく、「なぜ」その変更が行われたのかをユーザーに伝える重要なコミュニケーションツールです。GitHub Releaseを活用した様々なライブラリのリリースノートからは、それぞれ特徴的なアプローチがあることがわかりました。

特にSlackのような例は、GitHub Releaseという形式にとらわれずとも、エンドユーザーにとって価値のある情報発信の仕方があることを教えてくれます。

今後さらに多くのライブラリやフレームワークのGitHub Releaseノートを調査し、この記事を充実させていく予定です。良いリリースノートは、単に変更点を列挙するだけでなく、変更の意図や影響範囲を明確に伝え、詳細を知りたい開発者が簡単に情報にアクセスできるようにするものです。

自分のプロジェクトでGitHub Releaseノートを作成する際は、今回紹介したような様々なアプローチを参考にし、GitHubの自動生成機能やRelease Drafterなどのツールを活用することで、効率的に分かりやすいリリースノートを作成できるでしょう。

## 参考資料

- [Atlassian - リリースノートのベストプラクティス](https://www.atlassian.com/ja/software/jira/guides/releases/best-practices)
- [GitHub Docs - 自動生成リリースノート](https://docs.github.com/ja/repositories/releasing-projects-on-github/automatically-generated-release-notes)
- [Keep a Changelog](https://keepachangelog.com/ja/1.0.0/)
- [GitHub Blog - Release Radar](https://github.blog/2021-10-04-release-radar-sep-2021/)
- [Stack Overflow - Auto-generate release notes with GitHub Actions](https://stackoverflow.com/questions/75679683/how-can-i-auto-generate-a-release-note-and-create-a-release-using-github-actions)
