---
title: 'ドキュメント修正からはじめるOSSコントリビュート【Expo編】'
emoji: '🔰'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['oss', 'expo', 'reactnative']
published: false
---

# はじめに

前回の記事では、OSS コントリビュートの流れを紹介する記事を書きました。
いわば「入門編」として、実際に Pull Request を出す流れを学ぶような内容です。

前回の記事はこちら 👇
https://zenn.dev/kaz_z/articles/first-contributions

今回はその次のステップとして、実際の OSS である Expo に PR を出したときの体験を紹介します。
内容はドキュメント修正と、それに関連するちょっとしたスクリプト修正だけですが、それでも「コード本体を触らなくても OSS に関われるんだ」と実感できるものでした。

この記事を通して、読んでくださった方に「自分も小さなところから始められる」と思ってもらえたら嬉しいです。

# PR を出すまでの経緯

最近、モバイルアプリ開発でよく使われている React Native のフレームワークである [Expo](https://expo.dev/) について調べる機会がありました。
Expo はオープンソースで開発されており、GitHub 上でソースコードが公開されています。

学び始めるにあたって、まずはチュートリアルに取り組むことにしました。
ただ、チュートリアルを進めていく中で、ドキュメントの一部がスクリプトの内容と一致していないことに気づきました。

簡単に紹介すると、スクリプト実行後のコードは以下のようになるのに対し、

```tsx
import { Text, View } from 'react-native';

export default function Index() {
  return (
    <View
      style={{
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
      }}
    >
      <Text>Edit app/index.tsx to edit this screen.</Text>
    </View>
  );
}
```

ドキュメントでは StyleSheet が最初から定義されているように書かれていました。

```tsx
import { Text, View, StyleSheet } from 'react-native';

export default function Index() {
  return (
    <View style={styles.container}>
      <Text>Edit app/index.tsx to edit this screen.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

簡単なコードなので見過ごせる程度の違いではあったのですが、どうせなら正しい情報が載っていた方が良いと思い、修正を提案することにしました。

# 既存の Issue と PR を確認する

まずは、Expo の GitHub リポジトリにアクセスし、既存の Issue や PR を確認しました。
同じような問題が報告されていないか、またはすでに修正が提案されていないかをチェックしました。

調べたところ同じ問題は報告されていなかったので、自分で修正を提案することにしました。
最初に Issue を立ててから PR を出すことも考えましたが、軽微な問題なので直接 PR を出すことにしました。

# CONTRIBUTING.md を読む

次に、リポジトリにある `CONTRIBUTING.md` を読みました。
ここには、コントリビューションを行う際のガイドラインが記載されています。
各種ルールや手順を確認し、遵守することを心がけました。

# 実際に PR を出す

最初に作成した PR は以下です。

https://github.com/expo/expo/pull/39358

タイトルや説明欄、このあとのレビューの返信などは ChatGPT に手伝ってもらいました。
ありがたい。

# レビューとマージ

最初のレビューはわずか 3 時間後に来ました。

>Adding changes to the template does not publish them to npm, so I don't think the docs changes should go with this PR.
>Also, pinging @kadikraman for technical review. Altentatively, we can just update the tutorial code to match the current reset-project script behavior.

ドキュメントの変更とスクリプトの変更は別々にするか、チュートリアルのコードをスクリプトに合わせるか、という趣旨のコメントでした。
それに対し私もコメントを返します。

しかし、数日経っても返信が来ませんでした。
まぁ忙しんだろうな、、、と諦めかけていたところ、コメントを返してから5日後にコメントが来ました。（私が確認したのがさらにその3日後😑）

レビューで指摘されたとおりにPRを2つに分けて再度レビューをもらうことにしました。
数日後、無事にマージされました。

この変更はVersion 54.0.?でリリースされたようです。

実際にそのバージョンでスクリプトを実行してみて、ドキュメントと一致していることを確認しました。
チュートリアルは多くの人が通る部分なので、そこの修正をできたことは嬉しく思います。

# おわりに
