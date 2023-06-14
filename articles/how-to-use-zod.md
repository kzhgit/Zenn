---
title: "具体例で学ぶZodの使い方"
emoji: "💎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zod", "typescript"]
published: true
---

# はじめに

基本的な使い方は公式ページがわかりやすいのでサラッと書いてます。
具体例は実際に Zod を使ってみて気になった部分を書きました。

# 基本的な使い方

```ts:単純な文字列スキーマの作成
import { z } from "zod";

// 文字列のスキーマを作成する
const mySchema = z.string();
// スキーマから型を生成する
type MySchema = z.infer<typeof mySchema>
/*
  生成される型の中身
  type MySchema = string
*/

const input: MySchema = 100; // TypeError (型 'number' を型 'string' に割り当てることはできません！)
const input: MySchema = "hello!"; // OK!
```

`z.infer<typeof T>` とすることでスキーマから型を生成できます。

zod は form と組み合わせて使われることが多く、その場合は以下のようなオブジェクトのスキーマを定義します。

```ts:オブジェクトスキーマの作成
import { z } from "zod";

// オブジェクトのスキーマを作成する
const user = z.object({
  name: z.string(),
  age: z.number(),
  email: z.string().email(),
});
type User = z.infer<typeof user>;
/*
  生成される型の中身
  type User = {
    name: string;
    age: number;
    email: string;
  }
*/
```

そのほかの基本的な使い方は公式ページを参照してください。

# 具体例を見てみよう

## エラーメッセージをカスタムできる

ほとんどのバリデーションで以下のようにエラーメッセージをカスタムできます。

```ts
z.string().min(5, { message: "5文字以上である必要があります。" });
```

状態によってエラーメッセージを出し分けることもできます。

```ts
const name = z.string({
  required_error: "名前は必須です。",
  invalid_type_error: "名前は文字列である必要があります。",
});
```

## 正規表現を使うとき

```ts:例： 郵便番号のバリデーション
const POST_CODE = new RegExp("^[0-9]{3}-[0-9]{4}$");
const postCode = z.string().regex(POST_CODE,"半角数字、ハイフン付きで入力してください（例: 123-4567）")
```

`regex`を使えば正規表現にも対応できます。

## 入力する場合はバリデーションが必要だけど、空のままでもいい

例：fromのinput で、もし入力するなら 2 文字以上の文字列だけど入力せず空のままでもいいとき

```ts
const inputName = z.string().min(2).or(z.literal(""));
type InputName = z.infer<typeof inputName>;
/*
  生成される型の中身
  type InputName = string;
  正確にはこんな感じ
  type InputName = 2文字以上のstring | ""
*/
```

:::message
空の状態と似た意味（falsy な値）に`.optional()`で定義できる`undefined型`があります。
ただ、基本的に input 要素が空の状態で form 送信されると中身は`""`になります。
そのため、以下の書き方でinput要素を空にしてform送信しようとすると、 2 文字以下は許容されないという制約に引っかかるのでform送信ができません。
`""`と`undefined`は別物ということです。
:::

```ts
const inputName = z.string().min(2).optional();
type InputName = z.infer<typeof inputName>;
/*
  生成される型の中身
  type InputName = string | undefined;
  正確にはこんな感じ
  type InputName = 2文字以上のstring | undefined;
*/
```

## オブジェクトの中のオブジェクトも定義できる

```ts
const userSchema = z.object({
  name: z.string(),
  blogs: z
    .object({
      title: z.string(),
      body: z.string(),
    })
    .array(),
});
type UserSchema = z.infer<typeof userSchema>;
/*
  生成される型の中身
  type UserSchema = {
    name: string;
    blogs: {
        title: string;
        body: string;
    }[];
}
*/
```

## transform はオブジェクト自体も変えられる

例：フォームでは名字と名前を別々入力させるが、フルネームとしても持っておきたいとき。

```ts
const schema = z
  .object({
    lastName: z.string(),
    firstName: z.string(),
  })
  .transform((arg) => {
    return {
      ...arg,
      fullName: `${arg.lastName} ${arg.firstName}`,
    };
  });
type User = z.infer<typeof schema>;
/*
生成される型の中身
type User = {
  fullName: string; ←追加されてる
  lastName: string;
  firstName: string;
};
*/
```

## ファイル(画像)のバリデーションもできる

https://zenn.dev/kaz_z/articles/zod-image-file

# おわりに

今後も気になる点があれば追記していきたいと思います。

# 参考記事

https://github.com/colinhacks/zod
