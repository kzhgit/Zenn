---
title: 'Zodでファイル(画像)のバリデーションをする'
emoji: '💎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['zod', 'typescript']
published: true
---

# 結論

```ts:基本形
const schema = z.object({
  file: z.custom<FileList>().transform((file) => file[0]),
});

type Schema = z.infer<typeof schema>;
/*
  type Schema = {
    file: File;
  }
*/
```

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

# もっと拡張したい場合

`.refine`を使うことで任意のバリデーションを設定できます。
条件が`false`の際にバリデーションが効いてメッセージを出力できます。

```ts
const IMAGE_TYPES = ['image/jpeg', 'image/png'];

const schema = z.object({
  file: z
    .custom<FileList>()
    .refine((file) => file.length !== 0, { message: '必須です' })
    .transform((file) => file[0])
    .refine((file) => file.size < 500000, { message: 'ファイルサイズは最大5MBです' })
    .refine((file) => IMAGE_TYPES.includes(file.type), {
      message: '.jpgもしくは.pngのみ可能です',
    }),
});
```

# 参考

https://github.com/colinhacks/zod/issues/387

https://github.com/colinhacks/zod