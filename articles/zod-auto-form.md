---
title: "Zodとshadcn/uiで簡単にフォーム作成ができるAutoFormの紹介"
emoji: "🦊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zod", "typescript", "tailwindcss", "shadcn", "reacthookform"]
published: false
---

# はじめに

フォームといえば Zod × React-Hook-Form の構成で作成することが多いのではないでしょうか。
ただ、React-Hook-Form はできることが多いのでシンプルなフォームを作成したい時に機能を持て余してる感（？）がありました。
今回紹介する AutoForm は シンプルなフォームを作成する時にもってこいのライブラリです。

# AutoForm とは何か？

READEME より ↓

> AutoForm is a React component that automatically creates a @shadcn/ui form based on a zod schema. A live demo can be found at https://vantezzen.github.io/auto-form/.
> 訳: AutoForm は、zod スキーマに基づいて@shadcn/ui フォームを自動的に作成する React コンポーネントです。ライブデモはhttps://vantezzen.github.io/auto-form/。

AutoForm は以下 3 つのライブラリを作られています。

- フォームライブラリ **React Hook Form**
- バリデーションライブラリ **Zod**
- Radix UI と Tailwind CSS を使用して構築された UI コンポーネント **shadcn/ui**

https://www.react-hook-form.com/
https://zod.dev/
https://ui.shadcn.com/

以下のツイートを見てもらえれば分かると思いますが、シンプルで直感的な記述で簡単にフォーム作成ができています。

https://twitter.com/shadcn/status/1682458131002191872

実際のデモはこちら
https://vantezzen.github.io/auto-form/

# AutoForm を試してみる

:::message
前提として shadcn/ui の設定(init)が必要です。

```
npx shadcn-ui@latest init
```

詳細 ↓
https://ui.shadcn.com/docs/installation/next
:::

## インストール

AutoForm コンポーネントは shadcn/ui のコンポーネントと依存関係にあるので、まずは shadcn/ui から必要なコンポーネントをインストールします。

```
npx shadcn-ui@latest add accordion button calendar card checkbox form input label popover radio-group select separator switch textarea toggle
```

肝心の AutoForm に関しては GitHub から手動でコピーして持ってくる必要があります。
`auto-form.tsx` と `date-picker.tsx` を ダウンロードして、自分のプロジェクトの ui フォルダ(src/components/ui)に入れましょう。
https://github.com/vantezzen/auto-form/tree/main/src/components/ui

### 余談

`auto-form.tsx`を確認してみると React Hook Form が使われていることが分かりますね。

```ts:auto-form.tsx
import {
  ControllerRenderProps,
  DefaultValues,
  FieldValues,
  useForm,
} from "react-hook-form";
```

# 使い方

## 基本形

```tsx:index.tsx
// '/src'を@でエイリアスしているので適宜書き換えてください。
import AutoForm, { AutoFormSubmit } from "@/components/ui/auto-form";
import * as z from "zod";

export default function Home() {
  return (
    <div className="max-w-lg mx-auto my-6 space-y-8">
      <AutoForm
        onSubmit={(data) => console.log(data)}
        formSchema={z.object({
          name: z.string().min(3),
          pass: z.string().min(8),
          terms: z.boolean(),
        })}
        fieldConfig={{
          pass: {
            inputProps: {
              type: "password",
            },
          },
          terms: {
            fieldType: "switch",
            description: "Accept our terms and conditions.",
          },
        }}
      >
        <AutoFormSubmit>Submit</AutoFormSubmit>
      </AutoForm>
    </div>
  );
}
```

![基本的なフォーム](/images/zod-auto-form/basic-used.png)
_あっという間にフォームができました_

## 各 props の紹介

### formSchema

zod を使ってフォームのスキーマを定義します。
エラーメッセージ等カスタムするとコード量が多くなるので、基本的には変数で定義してから AutoForm に渡したほうが見やすいかと思います。

```ts:index.tsx
// .describe()でラベル名を設定することができます。
// 未設定の場合はフィールド名がそのまま表示されます。
const formSchema = z.object({
  name: z
    .string({
      required_error: "名前は必須です。",
    })
    .min(3, {
      message: "名前は3文字以上である必要があります。",
    })
    .describe("名前"),
  pass: z
    .string({
      required_error: "パスワードは必須です。",
    })
    .min(8, {
      message: "パスワードは8文字以上である必要があります。",
    })
    .describe("パスワード"),
  terms: z.boolean().describe("利用規約に同意する"),
});


export default function Home() {
  return (
    <div className="max-w-lg mx-auto my-6 space-y-8">
      <AutoForm
        onSubmit={(data) => console.log(data)}
        formSchema={formSchema}
        fieldConfig={{
          pass: {
            inputProps: {
              type: "password",
            },
          },
          terms: {
            fieldType: "switch",
            description: "Accept our terms and conditions.",
          },
        }}
      >
        <AutoFormSubmit>送信</AutoFormSubmit>
      </AutoForm>
    </div>
  );
}
```

![formSchemaの紹介](/images/zod-auto-form/form-schema.png)
_バリデーションとエラーメッセージが効いているのが確認できます。_

### fieldConfig

各フィールドの設定を追加することができます。

```ts:index.tsx
// descriptionはjsx形式で書くことが可能です。
export default function Home() {
  return (
    <div className="max-w-lg mx-auto my-6 space-y-8">
      <AutoForm
        onSubmit={(data) => console.log(data)}
        formSchema={formSchema}
        fieldConfig={{
          pass: {
            inputProps: {
              type: "password",
              placeholder: "••••••••",
            },
            description: "8文字以上で入力してください。",
          },
          terms: {
            fieldType: "switch",
            description: (
              <p className="text-gray-500 text-sm">
                こちらのリンクからご確認ください。{" "}
                <a href="#" className="text-primary underline">
                  利用規約
                </a>
              </p>
            ),
          },
        }}
      >
        <AutoFormSubmit>送信</AutoFormSubmit>
      </AutoForm>
    </div>
  );
}
```

![formConfigの紹介](/images/zod-auto-form/field-config.png)
_プレースホルダや説明文のカスタムを確認できます。_

# おわりに

Tailwind CSS を使っているなら選択肢として結構ありだなと思います。
Initial commit が 一昨日（2023/7/21）ということもあり今後どうなっていくか期待です！

# 参考

https://github.com/vantezzen/auto-form
