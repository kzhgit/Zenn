---
title: "React Hook Formで「どちらか一方が必須」なフォームの再評価はdepsが便利"
emoji: "🔗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reacthookform", "javascript", "typescript", "react", "zod"]
published: false
---

# はじめに

フォームで「メールアドレスか電話番号のどちらかは必須」としたい場面があるとします。  
しかし実装してみると、片方を入力してももう片方のエラーが残ってしまうなど、思ったように動かず苦戦することがあります。

![depsなし](/images/react-hook-form-deps/without-deps.gif)

こうした「どちらか一方を満たせばOK」というバリデーションは、**`deps` オプションを活用すると、スムーズに実現できます**。

# depsとは？

deps は dependencies（依存関係）の略で、あるフィールドが他のフィールドの値に依存してバリデーションを再評価するための仕組みです。

具体的には、

- 電話番号を入力したら、メールアドレス側のエラー（バリデーション？）も再評価される
- メールアドレスを入力したら、電話番号側のエラーも再評価される

といった動作を簡単に実現できます。

https://react-hook-form.com/docs/useform/register#:~:text=remount%20and%20reorder.-,deps,-string%20%7C%20string%5B%5D

# 実際のコード例

以下は Zodで「どちらか一方が必須」のバリデーションルールを定義し、React Hook Formで `deps` を使ってスムーズに再評価を行う例です。

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

こうすることで、片方を入力するともう片方のバリデーションが再評価されます。

![depsあり](/images/react-hook-form-deps/with-deps.gif)

# watch + triggerとの違い

従来の方法では、watch と useEffect と trigger を使って再評価を行っていました。

```tsx
const email = watch('email');
const phone = watch('phone');

useEffect(() => {
  trigger(['email', 'phone']);
}, [email, phone, trigger]);
```

しかし、deps を使えばシンプルになります。

```tsx
<input {...register('email', { deps: ['phone'] })} />
```


- `deps`: シンプルで直感的に書ける（バリデーションのみの場合おすすめ）
- `watch + trigger`: 複雑なロジックやUIの制御がある場合におすすめ

# おわりに

フォームの相互依存バリデーションは、React Hook Formの deps を使うと簡単で快適になります。バリデーション再評価で困ったらぜひ試してみてください。