---
title: "React Hook Formで「どちらか一方が必須」なフォームの再評価はdepsが便利"
emoji: "🔗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reacthookform", "javascript", "typescript", "react", "zod"]
published: true
---

# はじめに

フォームで「メールアドレスか電話番号のどちらかは必須」としたい場面があるとします。  
しかし実装してみると、片方を入力してももう片方のバリデーションエラーが残ってしまうなど、思ったように動かず苦戦することがあります。

![depsなし](/images/react-hook-form-deps/without-deps.gif)_メールアドレスを入力しても、電話番号側のエラーが残ってしまう例_

こうした「どちらか一方を満たせばOK」というバリデーションは、**`deps` オプションを活用すると、スムーズに実現できます**。

# depsとは？

`deps` は dependencies（依存関係）の略で、あるフィールドが他のフィールドの値に依存してバリデーションを再評価するための仕組みです。

React Hook Formでは、フィールドごとに個別にバリデーションが実行されるため、別のフィールドが変わっても自動で再評価はされません。
そこで `deps` を使うと、特定のフィールドが変更されたときに、それに関連する他のフィールドのバリデーションも自動で再評価されるようになります。

具体的には、

- 電話番号を入力したら、メールアドレス側のバリデーションも再評価される
- メールアドレスを入力したら、電話番号側のバリデーションも再評価される

といった動作を簡単に実現できます。

https://react-hook-form.com/docs/useform/register#:~:text=remount%20and%20reorder.-,deps,-string%20%7C%20string%5B%5D

> deps:
string | string[]	
Validation will be triggered for the dependent inputs,it only limited to register api not trigger.

# 実際のコード例

以下は Zodで「どちらか一方が必須」のバリデーションルールを定義し、React Hook Formで `deps` を使ってバリデーションの再評価を行う例です。

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z
  .object({
    email: z.string().optional(),
    phone: z.string().optional(),
  })
  .superRefine((data, ctx) => {
    // メールアドレスと電話番号のどちらも入力されていない場合はエラー
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
      {/* depsにphoneを設定し、emailの変化に依存させる */}
      <input {...register('email', { deps: ['phone'] })} />
      {errors.email && <p>{errors.email.message}</p>}

      {/* depsにemailを設定し、phoneの変化に依存させる */}
      <input {...register('phone', { deps: ['email'] })} />
      {errors.phone && <p>{errors.phone.message}</p>}

      <button type="submit">送信</button>
    </form>
  );
};
```

こうすることで、片方を入力するともう片方のバリデーションが再評価されます。

![depsあり](/images/react-hook-form-deps/with-deps.gif)_メールアドレスを入力すると、電話番号側のエラーも消える例_

# watch + triggerとの違い

`watch` と `trigger` と `useEffect` を組み合わせることでも、同じような動作は実現可能です。

```tsx
// emailとphoneを監視
const email = watch('email');
const phone = watch('phone');

// emailまたはphoneが変わったら、両方のバリデーションを再評価
useEffect(() => {
  trigger(['email', 'phone']);
}, [email, phone, trigger]);
```

ただ今回の例では、`deps` を使ったほうがシンプルに書けます。

```tsx
<input {...register('email', { deps: ['phone'] })} />
```

参考までに、使い分けの目安を挙げておきます。

- **deps**: バリデーションの再評価だけが目的で、シンプルに書きたいとき
- **watch + trigger**: UI制御や複雑なロジックを実行したいとき

# おわりに

今回初めて `deps` を知り、あまり情報がなかったのでまとめてみました。
シンプルなフォームでは便利に使えそうなので、参考になれば嬉しいです。
