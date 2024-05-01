---
title: 'Zodでファイル(画像)のバリデーションをする'
emoji: '💎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['zod', 'typescript']
published: true
---

# 結論

```tsx
const IMAGE_TYPES = ['image/jpg', 'image/png'];
const MAX_IMAGE_SIZE = 5; // 5MB

// バイト単位のサイズをメガバイト単位に変換する
const sizeInMB = (sizeInBytes: number, decimalsNum = 2) => {
  const result = sizeInBytes / (1024 * 1024);
  return +result.toFixed(decimalsNum);
};

const schema = z.object({
  file: z
    // z.inferでSchemaを定義したときに型がつくようにするため
    .custom<FileList>()
    // 必須にしたい場合
    .refine((file) => file.length !== 0, { message: '必須です' })
    // このあとのrefine()で扱いやすくするために整形
    .transform((file) => file[0])
     // ファイルサイズを制限したい場合
    .refine((file) => sizeInMB(file.size) <= MAX_IMAGE_SIZE, { message: 'ファイルサイズは最大5MBです' })
    // 画像形式を制限したい場合
    .refine((file) => IMAGE_TYPES.includes(file.type), {
      message: '.jpgもしくは.pngのみ可能です',
    }),
});

type Schema = z.infer<typeof schema>;
/*
  type Schema = {
    file: File;
  }
*/
```

:::details React Hook Formと組み合わせた例
```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import * as z from "zod";

const IMAGE_TYPES = ["image/png", "image/jpg"];
const MAX_IMAGE_SIZE = 5;

const sizeInMB = (sizeInBytes: number, decimalsNum = 2) => {
  const result = sizeInBytes / (1024 * 1024);
  return +result.toFixed(decimalsNum);
};

const schema = z.object({
  image: z
    .custom<FileList>()
    .refine((file) => file.length !== 0, { message: "必須です" })
    .transform((file) => file[0])
    .refine((file) => sizeInMB(file.size) <= MAX_IMAGE_SIZE, {
      message: "ファイルサイズは最大5MBです",
    })
    .refine((file) => IMAGE_TYPES.includes(file.type), {
      message: ".jpgもしくは.pngのみ可能です",
    }),
});
type Schema = z.infer<typeof schema>;

const SamplePage = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<Schema>({
    resolver: zodResolver(schema),
  });

  return (
    <form
      onSubmit={handleSubmit((data) => console.log(data))}
      className="p-[100px] bg-gray-400"
    >
      <input type="file" {...register("image")} accept="image/*" />
      {errors.image?.message && <p>{errors.image?.message}</p>}
      <br />
      <input type="submit" />
    </form>
  );
};

export default SamplePage;
```
:::


# 解説

## `z.custom<FileList>()`

`z.custom()` を使う理由は明示的に型を指定したいのと、ぱっと見でどんな型かわかるようにしたいからです。
`custom` の型は以下のように定義されておりジェネリクスを受け取ります。
受け取ったジェネリクスは各メソッドの引数の型として使われています。

```ts:types.d.ts
export declare const custom: <T>(check?: ((data: unknown) => any) | undefined, params?: Parameters<ZodTypeAny["refine"]>[1], fatal?: boolean | undefined) => ZodType<T, ZodTypeDef, T>;

export declare abstract class ZodType<Output = any, Def extends ZodTypeDef = ZodTypeDef, Input = Output> {
    readonly _type: Output;
    readonly _output: Output;
    readonly _input: Input;
    readonly _def: Def;
    get description(): string | undefined;
    abstract _parse(input: ParseInput): ParseReturnType<Output>;
    _getType(input: ParseInput): string;
    _getOrReturnCtx(input: ParseInput, ctx?: ParseContext | undefined): ParseContext;
    _processInputParams(input: ParseInput): {
        status: ParseStatus;
        ctx: ParseContext;
    };
    _parseSync(input: ParseInput): SyncParseReturnType<Output>;
    _parseAsync(input: ParseInput): AsyncParseReturnType<Output>;
    parse(data: unknown, params?: Partial<ParseParams>): Output;
    safeParse(data: unknown, params?: Partial<ParseParams>): SafeParseReturnType<Input, Output>;
    parseAsync(data: unknown, params?: Partial<ParseParams>): Promise<Output>;
    safeParseAsync(data: unknown, params?: Partial<ParseParams>): Promise<SafeParseReturnType<Input, Output>>;
    /** Alias of safeParseAsync */
    spa: (data: unknown, params?: Partial<ParseParams> | undefined) => Promise<SafeParseReturnType<Input, Output>>;
    refine<RefinedOutput extends Output>(check: (arg: Output) => arg is RefinedOutput, message?: string | CustomErrorParams | ((arg: Output) => CustomErrorParams)): ZodEffects<this, RefinedOutput, Input>;
    refine(check: (arg: Output) => unknown | Promise<unknown>, message?: string | CustomErrorParams | ((arg: Output) => CustomErrorParams)): ZodEffects<this, Output, Input>;
    refinement<RefinedOutput extends Output>(check: (arg: Output) => arg is RefinedOutput, refinementData: IssueData | ((arg: Output, ctx: RefinementCtx) => IssueData)): ZodEffects<this, RefinedOutput, Input>;
    refinement(check: (arg: Output) => boolean, refinementData: IssueData | ((arg: Output, ctx: RefinementCtx) => IssueData)): ZodEffects<this, Output, Input>;
    _refinement(refinement: RefinementEffect<Output>["refinement"]): ZodEffects<this, Output, Input>;
    superRefine<RefinedOutput extends Output>(refinement: (arg: Output, ctx: RefinementCtx) => arg is RefinedOutput): ZodEffects<this, RefinedOutput, Input>;
    superRefine(refinement: (arg: Output, ctx: RefinementCtx) => void): ZodEffects<this, Output, Input>;
    constructor(def: Def);
    optional(): ZodOptional<this>;
    nullable(): ZodNullable<this>;
    nullish(): ZodOptional<ZodNullable<this>>;
    array(): ZodArray<this>;
    promise(): ZodPromise<this>;
    or<T extends ZodTypeAny>(option: T): ZodUnion<[this, T]>;
    and<T extends ZodTypeAny>(incoming: T): ZodIntersection<this, T>;
    transform<NewOut>(transform: (arg: Output, ctx: RefinementCtx) => NewOut | Promise<NewOut>): ZodEffects<this, NewOut>;
    default(def: util.noUndefined<Input>): ZodDefault<this>;
    default(def: () => util.noUndefined<Input>): ZodDefault<this>;
    brand<B extends string | number | symbol>(brand?: B): ZodBranded<this, B>;
    catch(def: Output): ZodCatch<this>;
    catch(def: () => Output): ZodCatch<this>;
    describe(description: string): this;
    pipe<T extends ZodTypeAny>(target: T): ZodPipeline<this, T>;
    isOptional(): boolean;
    isNullable(): boolean;
}
```

今回は画像を添付したかったので`FileList`をジェネリクスに渡しました。

https://developer.mozilla.org/ja/docs/Web/API/FileList

ちなみに`z.any()`でスキーマを作成することもできますが型はつきません。

```ts
const schema2 = z.object({
  file: z.any().transform((file) => file[0]),
});
type Schema2 = z.infer<typeof schema>;
/*
  type Schema2 = {
    file?: any;
  }
*/
```

引数で明示的に型をつけてあげることで`any`を避けることができますが、このやり方だと後述する`.refine`でバリデーションを拡張する際、毎回引数に型を書く必要があるので面倒です。

```ts
const schema2 = z.object({
  file: z.any().transform((file: FileList) => file[0]),
});
type Schema2 = z.infer<typeof schema>;
/*
  type Schema2 = {
    file: File;
  }
*/
```

## `.transform((file) => file[0])`

`FileList`の型を見てもらえればわかりますが、データを扱う時に毎回`file[0]`で取り出すのが面倒だったので`.transform`で変換しています。

https://developer.mozilla.org/ja/docs/Web/API/FileList


## `.refine()`

https://zod.dev/?id=refine

`.refine`を使うことで任意のバリデーションを設定できます。
条件が`false`の際にバリデーションが効いてメッセージを出力できます。


# （補足）HTMLでも多少の制御はできる
https://developer.mozilla.org/ja/docs/Web/HTML/Attributes/accept

例えばファイル選択を画像ファイルだけに制限したい場合、以下のように`accept属性`を指定することで、ユーザーのファイル選択を画像ファイルのみに絞らせることができます。

```html
<input type="file" accept="image/*" />
```

![accept属性をつけていない場合](/images/zod-image-file/all.png)
_accept属性を書いていない場合_

![accept属性をつけている場合](/images/zod-image-file/select.png)
_accept="image/*"を記述している場合_


> accept 属性は、選択されたファイルの種別を検証するものではありません。これはブラウザーがユーザーに対して正しいファイル種別を選択できるようにするためのガイドをするためのヒントを提供するだけです。ユーザーがファイルセレクターのオプションを切り替え、これを上書きして任意のファイルを選択し、不正なファイル種別を選択することは (ほとんどの場合) 可能です。このため、期待される要件をサーバー側で検証するようにしてください。

ただ、MDNにも書いてあるようにフロント側でのバリデーションは補助的なものと捉え、あくまでサーバー側で検証をすることが必要です。

# 参考

https://github.com/colinhacks/zod/issues/387

https://github.com/colinhacks/zod