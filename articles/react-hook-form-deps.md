---
title: "React Hook Formで「どちらか一方の入力を必須」にしたいときはdepsが便利"
emoji: "⛓️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reacthookform", "javascript", "typescript", "react", "zod"]
published: false
---

# はじめに

フォームで「メールアドレスか電話番号のどちらかは必須」のようなバリデーションを実装したいとき、React Hook Formでは `deps` という便利なオプションがあります。

今回は、この `deps` の使い方を解説し、シンプルで分かりやすいバリデーションの実装方法を紹介します。

# depsとは？

`deps` はdependencies（依存関係）の略で、あるフィールドが他のフィールドの値に依存してバリデーションを再評価したいときに使います。

具体的には、

- 電話番号を入力したら、メールアドレス側のエラーが消える
- メールアドレスを入力したら、電話番号側のエラーが消える

といったことを簡単に実現できます。

# 実際のコード例

以下が具体的なコード例です。

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z
  .object({
    email: z.string().email().optional(),
    phone: z.string().optional(),
  })
  .superRefine((data, ctx) => {
    if (!data.email && !data.phone) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: 'メールアドレスか電話番号のどちらかを入力してください',
        path: ['email'],
      });
      ctx.addIssue({
        code: z.ZodIssueCode.custom,
        message: 'メールアドレスか電話番号のどちらかを入力してください',
        path: ['phone'],
      });
    }
  });

const FormComponent = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <input {...register('email', { deps: ['phone'] })} />
      {errors.email && <p>{errors.email.message}</p>}

      <input {...register('phone', { deps: ['email'] })} />
      {errors.phone && <p>{errors.phone.message}</p>}

      <button type="submit">送信</button>
    </form>
  );
};
```

# watch + triggerとの違い

今までの方法だと `watch` と `useEffect` と `trigger` を使って実装していましたが、`deps` を使うとシンプルに書けます。

- `deps`: シンプルで直感的に書ける（バリデーションのみの場合おすすめ）
- `watch + trigger`: 複雑なロジックやUIの制御がある場合におすすめ

# まとめ

フォームの相互依存バリデーションはReact Hook Formの `deps` を使うと簡単に実装できます。どちらか一方を必須にしたいときは、ぜひ試してみてください。
