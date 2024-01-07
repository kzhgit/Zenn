---
title: "これで君もOSSコントリビューター！"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["oss"]
published: false
---

# はじめに

2024年の目標の一つに、**OSSコントリビュート**に挑戦してくことを決めました。
初めてのコントリビュート系の記事はすでに先人たちが書かれていたので本記事を書こうか迷ったのですが、誰かのきっかけになればいいと思い書くことにしました。

OSSコントリビュートを始めたい方の最初のステップとして参考になれば幸いです 🙏

## 対象読者

- OSSコントリビュートをしてみたいが何から始めればいいかわからない方

# コントリビュートするリポジトリ

https://github.com/firstcontributions/first-contributions

このリポジトリは、初心者がオープンソースプロジェクトに貢献する方法を簡単に学ぶことを目的としています。
現時点（2024/01/07）で、Fork 数が**70.3k**と非常に人気なのが分かりますね。
各言語の README が用意されており、もちろん[日本語 🇯🇵](https://github.com/firstcontributions/first-contributions/blob/main/translations/README.ja.md)での説明もあります。

では、実際に README の手順に沿ってやってみましょう。
:::message
README に手順が丁寧に記載されているので詰まるポイントはほぼないです。
:::

## 1. リポジトリをフォークする

右上から**Fork**を選択します。
![リポジトリをフォークする1](/images/first-contributions/fork-step1.png)
_右上の「Fork」を選択します_

特に変更する点はないのでそのまま右下の**Create fork**を選択します。
![リポジトリをフォークする2](/images/first-contributions/fork-step2.png)
_右下の「Create fork」を選択します_

すると自分の GitHub アカウントに first-contributions リポジトリがフォークされました 🎉
![リポジトリをフォークする3](/images/first-contributions/fork-step3.png)

## 2. リポジトリをクローンする

![リポジトリをクローンする](/images/first-contributions/clone.png)
_中央の「Code」を選択してクローンの準備をします_

ターミナルを開いてリポジトリをクローンします。
```
git clone <コピーしたリポジトリurl>
```

例：（SSHの場合）
```
git clone git@github.com:kzhgit/first-contributions.git
```

## 3. ブランチを作成する

クローンしてきたリポジトリに移動します。
```
cd first-contributions
```

`git switch`コマンドを使用して作業用ブランチを作成します。
```
git switch -c <add-your-name>
```

例：
```
git switch -c add-kzhgit
```

今回の変更内容（コントリビュート内容）は自分の名前をリストに追加することなので、「add-your-name」のような形式になっています。

## 4. コードを変更してその変更をコミットする

`Contributors.md`ファイルを開くと、これまでコントリビュートした人たちの GitHub アカウントがズラーっと並んでいます。

```md:Contributors.md
# Contributors
- [Satyajit Patra](https://github.com/SatyajitPatra06)
- [Shahmeer malik](https://github.com/shahmeermalik1)
- [Karuppaiah](https://github.com/akdinesh124)
...省略...
```

どこでも良いので自分の名前を追加してみましょう。
```diff md:Contributors.md
 # Contributors
...省略...
+ - [Kazuho](https://github.com/kzhgit)
...省略...
```

追加できたら add して commit します。
```
git add Contributors.md
git commit -m "Add <あなたの名前> to Contributors list"
```

例：
```
git commit -m "Add Kazuho to Contributors list"
```

## 5. GitHub に変更を push する

`git push`コマンドを使用して変更をリモートリポジトリに push します。
```
git push origin <ブランチ名>
```

例：
```
git push origin add-kzhgit
```

## 6. レビューのためにプルリクエストを送る

リポジトリを再度確認するとプルリクエストの作成ボタンが表示されるので選択します。
![プルリク1](/images/first-contributions/pr-step1.png)
_「Compare & pull request」を選択_

特に追記することはないのでそのまま**Create pull request**を選択します。
![プルリク2](/images/first-contributions/pr-step2.png)
_右下の「Create pull request」を選択_

## 7. マージされる 🎉

数分待つと自動でマージされました！
これでOSSコントリビューターの第一歩を踏み出せましたね 😎

![マージされた](/images/first-contributions/merge.png)

# おわりに

OSSコントリビュートを始めたい方の最初のステップとして、このようなリポジトリがあるのは非常にありがたいと思いました。

また、[必ずしもコードを書くことがコントリビュートとは限らない](https://opensource.guide/ja/how-to-contribute/#%E3%81%8B%E3%81%AA%E3%82%89%E3%81%9A%E3%81%97%E3%82%82%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E5%BF%85%E8%A6%81%E3%81%AF%E3%81%82%E3%82%8A%E3%81%BE%E3%81%9B%E3%82%93)とあるように、
以下の行動も立派なコントリビュートとされています。

- ドキュメントの作成や改善
- 翻訳
- コミュニティサポート（フォーラムやチャットでの質問対応）
- 他の人のコードをレビューする...etc

個人的にリポジトリのissue内の会話を見てエラー解決できたことも結構多いので、そういった会話に参加するだけで誰かの役に立てることもあるんだな〜なんて思いました。

## さらにハードルを下げる（OSS ではないが...）

例えば、Zenn にはユーザが要望や質問などを投稿できるリポジトリがあります。
https://github.com/zenn-dev/zenn-community

本記事を書いている時、過去に issue をあげたことがあるのを思い出しました。
https://github.com/zenn-dev/zenn-community/issues/527

このように、まずは普段使っているサービスやコミュニティにバグ報告や機能追加の提案リクエストをしてみることから始めても良いのではないでしょうか？

# 参考

https://opensource.guide/ja/how-to-contribute/
