---
title: "【マークダウン】Multiple top-level headings in the same documentを解決する"
emoji: "📝"
type: "tech"
topics: ["zenncli", "markdown"]
published: true
---

# 問題発生の背景

Zenn CLIが良いらしいということで実際に使って見たときに発生しました。
メッセージ内容は、「1つのファイルにh1は1つまでだよ」みたいな注意をされています。
![波線出て気になる](/images/markdown-zenn-cli/warning.png)
_波線出て気になる_

Zennにデプロイされると2行目のtitleがh1として変換されるので11行目のh1と被ってるということです。
しかし、ZennのUI的にh1の方が見やすいのでh1を複数使いたいです。

# 結論

ディレクトリの一番上の階層に`.markdownblint.json`を作成して設定すると解決できます。

```json:.markdownlint.json
{
  "MD025": false
}
```

`key`は記法ルール、`value`は`boolean`で設定できます。

![波線消えました](/images/markdown-zenn-cli/normal.png)
_波線消えました_

# 参考記事

記法ルールの一覧とか載ってます。
https://github.com/DavidAnson/vscode-markdownlint
