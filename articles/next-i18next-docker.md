---
title: "next-i18nextを導入したが本番環境だけエラーになる"
emoji: "🌏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["i18n", "i18next", "nextjs"]
published: true
---

# はじめに

グローバル展開を想定したプロジェクトで、多言語対応が必要となりました。
ライブラリは[next-i18next](https://github.com/i18next/next-i18next)を使用して開発を進めていましたが、途中でハマったポイントがあったので紹介します。

## 使用技術

前提として、Dockerを使用してCloud Runにアプリケーションをデプロイしています。

- Next.js
- next-i18next
- Cloud Run
- Docker

# 発生した問題

ローカルでは正常に動くのに、開発環境や本番環境だと404エラーや500エラーが発生する問題が発生しました。  

また、Sentryでは以下のエラーを検知しました：
https://github.com/i18next/next-i18next/issues/1091

# どうやって解決したか

問題の原因を調査した結果、設定ファイルがDockerイメージに含まれていないことが分かりました。これにより、多言語対応やルーティングが正しく動作していませんでした。

また、READMEには以下のように記載されていました：

> For Docker deployment, note that if you use the Dockerfile from Next.js docs do not forget to copy next.config.js and next-i18next.config.js into the Docker image.
> ```bash
> COPY --from=builder /app/next.config.js ./next.config.js
> COPY --from=builder /app/next-i18next.config.js ./next-i18next.config.js
> ```

https://github.com/i18next/next-i18next?tab=readme-ov-file#docker

つまり、Dockerイメージに`next.config.js`と`next-i18next.config.js`をコピーする必要がありました。

Dockerfileに設定ファイルをコピーする記述を加えたところ、開発環境と本番環境でも正常に動作するようになりました。

ローカル環境ではDockerを使用していなかったため、設定ファイルが含まれていなくても正常に動作していたということでした。

# おわりに

ドキュメントをしっかり読みましょう（自戒）
