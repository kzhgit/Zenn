---
title: "【GitHub Releasesの書き方まとめ】人気OSSはどう書いている？"
emoji: "🎯"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["github", "githubactions", "ci", "yaml"]
published: false
---

# はじめに

普段からGitHub Releasesを使う中で、「どう書けば見やすく、伝わりやすいのか？」と気になったことがきっかけで、いくつかのプロジェクトのリリースノートを調べてみました。

すると、プロジェクトによって書き方や構成の違いがあり、工夫の方向性もさまざまでした。
また、比較していくうちに、共通するパターンや表現、構造の特徴が少しずつ浮かび上がってきました。

本記事では、GitHub Releasesの書き方に注目し、以下のような内容をまとめています。

- GitHub Releasesの基本的な機能
- ライブラリやフレームワークごとのリリースノート構成の比較
- よく使われている表現や分類スタイル
- 自分のプロジェクトに応用するための実践ガイド

リリースノートの書き方に迷ったときや、他のプロジェクトを参考にしたいときに、少しでも役立つ情報になれば幸いです。

:::message
本記事では、ReactやNext.jsのようなライブラリ・フレームワークを含めて、すべて「プロジェクト」と呼びます。

また、GitHubのリリース管理機能を「GitHub Releases」、その中に書かれる更新内容の記述を「リリースノート」と呼びます。

種類によって呼び方が変わってしまうと、読んでいる際に混乱を招くことがあるため、統一した用語で進めることにしました。これにより、内容に集中しやすくなると考えています。
:::

# GitHub Releasesの基本的な機能と使い方

GitHub Releasesは、リポジトリの「Releases」セクションから簡単にアクセスでき、次のような機能があります。

- **バージョン管理**
  - リリースごとに固有のバージョン番号をつけ、コードの状態を記録します。
- **リリースノート**
  - 変更内容や修正内容、機能追加などの詳細をリリースノートとして記述し、ユーザーに告知できます。
- **アセットのアップロード**
  - バイナリファイルやインストーラー、ドキュメントなど、リリースに関連するファイルをアップロードし、ダウンロードリンクとして提供します。
- **タグ付け**
  - Gitのタグ機能と連携して、特定のコミットをリリースに紐付けることができます。

公式ドキュメントに画像付きで説明が載っているので、詳しくはこちらを参照してください。

https://docs.github.com/ja/repositories/releasing-projects-on-github/managing-releases-in-a-repository

# プロジェクトの選定基準と調査方法

## プロジェクト選定

リリースノートの特徴を把握するために、いくつかのプロジェクトを選んでみました。今回は**筆者が普段使っているものや興味があるもの**を中心にピックアップしています。

## 調査方法

リリースノートを調査する際には、以下のポイントを意識して調べました。

- **構成の特徴**
リリースノートの構成がどのように整理されているかを調査しました。例えば、変更内容がどれだけ詳しく書かれているのか、どの情報が最初に目に入るようになっているのか、といった点に注目しました。

- **GitHub Releasesの位置づけ**
プロジェクト内でGitHub Releasesがどのような役割を担っているのかを見ました。例えば、GitHub Releasesが補足的な位置づけなのか、公式サイトからも案内されている主要な情報源なのか、といった違いです。これによって、リリースノートの扱われ方や重要度がプロジェクトごとにどう異なるかが見えてきます。

これらの視点を元に、それぞれのプロジェクトのリリースノートがどのような特徴を持っているのかを深掘りしていきます。

:::message
GitHub Actionsを使ったリリースの自動化についても気になるところではありますが、本記事では調査の範囲を絞るため、今回は取り上げていません。
気になるプロジェクトがあれば、`.github/workflows` ディレクトリなどを覗いてみると面白い発見があると思います。
:::

# 各プロジェクトのリリースノートまとめ

## React

![ReactのGitHub Releases](/images/github-release-notes/react_github.png)
*直近のGitHub Releases*

### 構成の特徴

Reactのリリースノートは、**機能ごとのカテゴリと、モジュール単位での分類**を組み合わせた構成が特徴です。
特にメジャーアップデートでは、「New Features」「Breaking Changes」「Deprecations」といったカテゴリごとに情報が分けられ、その中で「React」「React DOM」「React DOM Server」などのモジュール単位の小見出しで整理されています。

パッチやマイナーアップデートでは、各PRのタイトルがそのまま箇条書きで並べられるシンプルな形式です。一方で、メジャーアップデートでは概要や背景説明を含んだ、より丁寧な記述となっており、詳細な解説は公式ブログやアップグレードガイドへ誘導するスタイルになっています。

### GitHub Releasesの位置づけ

Reactのリリース情報は、主に以下の3つの場所で提供されています。

#### 1. GitHub Releases

https://github.com/facebook/react/releases

#### 2. CHANGELOG.md

https://github.com/facebook/react/blob/main/CHANGELOG.md

GitHub Releasesとは別にCHANGELOG.mdがある理由については、[Remixのリポジトリ](https://github.com/remix-run/remix/blob/main/CHANGELOG.md#remix-releases)や[React Routerの公式サイト](https://reactrouter.com/changelog)でされていた説明がしっくりきたので紹介します。（Reactも同じ理由かは分かりませんが...）

> We manage release notes in this file instead of the paginated Github Releases Page for 2 reasons:
> - Pagination in the Github UI means that you cannot easily search release notes for a large span of releases at once
> - The paginated Github interface also cuts off longer releases notes without indication in list view, and you need to click into the detail view to see the full set of release notes

（和訳↓）
> 私たちは2つの理由から、ページ分割されたGithub Releases Pageの代わりにこのファイルでリリースノートを管理しています：
> - Github UIのページネーションでは、一度に多くのリリースのリリースノートを簡単に検索することができません。
> - ページ分割されたGithubのインターフェイスでは、長いリリースノートはリストビューでは表示されずに切り捨てられ、リリースノートの全セットを見るには詳細ビューをクリックする必要があります。

そのため、基本的には**CHANGELOG.mdが中心的な情報源**として管理されているように思えます。それに対しGitHub Releasesは、通知機能やリアクションなど、周辺機能の利便性もあることから、**外部向けに公開される「発表の場」** として活用されている印象でした。

#### 3. 公式サイト（Versionsページ）

https://react.dev/versions

一方で公式サイト（React.dev）は、各メジャーバージョン（例：React 18、19）の概要や機能、アップグレードガイドを掲載する**ナビゲーション的な位置づけ**です。リリースノートで説明するにはちょっと長すぎるような、具体的なコードを交えての解説がブログ形式で紹介されていました。

### 所感とまとめ

Reactでは、リリース情報を以下の3つの情報源に分けて提供することで、

- 変更の一覧性（CHANGELOG.md）
- 通知・配布の即時性（GitHub Releases）
- 概要と文脈の理解（公式サイト）

といった目的ごとに役割を明確にし、それぞれに最適な形で情報を届けている印象を受けました。

また、「カテゴリ → モジュール単位」で整理するリリースノートの構成は、変更点が把握しやすく、自分のプロジェクトでも取り入れやすいスタイルだと感じました。
シンプルながらも読み手にとって親切な設計で、丁寧な情報提供の好例と言えるのではないでしょうか。

## Remix & React Router

RemixとReact Routerは、同じ開発チームによってメンテナンスされていることもあり、リリースノートの構成やスタイルにも一貫性がありました。そのため、このパートでは2つまとめて紹介します。

![RemixのGitHub Releases](/images/github-release-notes/remix_github.png)
*RemixのGitHub Releases*

![React RouterのGitHub Releases](/images/github-release-notes/react_router_github.png)
*React RouterのGitHub Releases*

### 構成の特徴

どちらのプロジェクトも、リポジトリ直下にある `CHANGELOG.md` にリリース情報をまとめており、バージョン番号と日付の見出しごとに時系列で整理されています。

変更内容は、「Patch Changes」「Minor Changes」「Breaking Changes」といった**変更の種類に応じたカテゴリ**に分けられ、それぞれの項目が簡潔に箇条書きで記述されています。

さらに、複雑な変更や仕様の補足が必要な箇所には、箇条書きの中で段落を変える形で簡単な背景説明や移行ガイドが記載されていることもあります。

また、各エントリの末尾に「Changes by Package」というセクションが用意されており、どのパッケージにどんな変更があったのかが一覧で把握できるようになっている点も特徴的でした。

なお、React Routerの[公式サイトのリリース情報](https://reactrouter.com/changelog)も、内容はCHANGELOG.mdのものがそのまま掲載されており、CHANGELOG.mdが主要な情報源として位置づけられていることがわかります。

### GitHub Releasesの位置づけ

両プロジェクトとも、GitHub Releasesは**CHANGELOG.mdへのリンクを提供する場**として活用されています。

リリースノートの本文には「See the changelog for the release notes.」といった一文と該当バージョンへのリンクのみが記載されており、変更点の詳細はそちらを参照する形になっています。

ちなみに、この運用スタイルはRemixでは`v2.3.0`以降、React Routerでは`v6.19.0`以降から始まっていました。

### 所感とまとめ

RemixとReact Routerのリリースノートは、情報の種類ごとに丁寧に整理されており、ユーザーが必要な情報にすばやくアクセスしやすい構成が特徴的でした。

- 変更内容のカテゴリ分け（Patch, Minor, Breakingなど）
- PRやIssueへのリンクが併記されており、変更の背景が追いやすい
- 背景説明や移行ガイドの補足
- 複数パッケージの変更をまとめる「Changes by Package」セクション

といった工夫により、リリースの意図や影響範囲が把握しやすくなっています。

また、GitHub ReleasesとCHANGELOG.mdの役割を明確に分離し、すべての情報をCHANGELOG.mdに集約する方針も印象的でした。

Reactのように情報源が複数に分かれているスタイルとはまた違った、統一感と集中管理を重視した運用スタイルとして、参考になる点が多いリリースノートだと感じました。

## Next.js