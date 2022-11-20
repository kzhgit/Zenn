---
title: "React Hook FormとZodの組み合わせで<select>を使った時にハマったメモ" # 記事のタイトル
emoji: "💎" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reacthookform", "zod", "react", "nextjs"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# やりたいこと

セレクトボックスで選択した id を form で送りたいです。
例として、都道府県を選択して`prefCode`を number型で送りたいとします。

```ts
const prefectures = [
  {
    prefCode: 1,
    prefName: "北海道",
  },
  {
    prefCode: 2,
    prefName: "青森県",
  },
  // 続く...
  {
    prefCode: 47,
    prefName: "沖縄県",
  },
];
```

# React Hook Form と Zod

React Hook Form と Zod を組み合わせて簡単なフォームを用意しました。
ドキュメントからコードを引用したのをちょっと変えてます。
(※この記事の最後に関係あるリンク一覧を載せてます。)

```tsx:index.tsx
import { useForm, SubmitHandler } from "react-hook-form";
import { z } from "zod";
import { zodResolver } from "@hookform/resolvers/zod";

const schema = z.object({
  name: z.string(),
  prefectureId: z.number(),
});

type User = z.infer<typeof schema>;

export default function Home() {
  const {
    register,
    handleSubmit,
  } = useForm<User>({
    resolver: zodResolver(schema),
  });
  const onSubmit: SubmitHandler<User> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>名前</label>
      <input {...register("name")} />
      <label>出身県</label>
      <select {...register("prefectureId")}>
        {prefectures.map((pref) => {
          return (
            <option key={pref.prefCode} value={pref.prefCode}>
              {pref.prefName}
            </option>
          );
        })}
      </select>
      <input type="submit" />
    </form>
  );
}
```

# ハマったポイント

**selectタグの value は string型しか送れないようです。**

```html
<option value={1}>北海道</option>
```

value に number の`1` を入れても`"1"`に変換されます。
そのため、id は number型 で欲しいからといって zod で number型を指定してしまうとバリデーションがかかって form送信をすることができません。

# 解決策
Zod の [.tramsform](https://github.com/colinhacks/zod#transform) という機能を使えば、解析後にデータを変換できます。
たとえば、以下のように書くと string で受け取った値を number に変更できます。

```ts
const schema = z.object({
  name: z.string(),
  prefectureId: z.string().transform((val) => Number(val)),
});
```

こうすることで入力はstringで受けるけど、型はnumberになります。

```ts:.tramsformを使って型生成してみる
const schema = z.object({
  name: z.string(),
  prefectureId: z.string().transform((val) => Number(val)),
});
type User = z.infer<typeof schema>;

// ↓Userにホバーするとこうなってる
// type User = {
//     name: string;
//     prefectureId: number;
// }
```

```ts:出力結果もnumberになる
{name: "たろう", prefectureId: 1}
```

# どうすればよかったか

エラーハンドリングを書いてたらすぐに気づけたと思います。
全然解決できなくて半日は潰しました。

```tsx
export default function Home() {
  const {
    register,
    handleSubmit,
    // これを追加↓
    formState: { errors },
  } = useForm<User>({
    resolver: zodResolver(schema),
  });
  const onSubmit: SubmitHandler<User> = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label>名前</label>
      <input {...register("name")} />
      <label>出身県</label>
      <select {...register("prefectureId")}>
        {prefectures.map((pref) => {
          return (
            <option key={pref.prefCode} value={pref.prefCode}>
              {pref.prefName}
            </option>
          );
        })}
      </select>
      {/* これを追加↓ */}
      {errors.prefectureId?.message && <p>{errors.prefectureId?.message}</p>}
      <input type="submit" />
    </form>
  );
}
```

# 参考記事

**React Hook Form**
https://react-hook-form.com/get-started
https://www.npmjs.com/package/react-hook-form
https://www.npmjs.com/package/@hookform/resolvers

**Zod**
https://www.npmjs.com/package/zod
https://github.com/colinhacks/zod