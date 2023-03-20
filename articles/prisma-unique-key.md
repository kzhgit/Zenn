---
title: 'Prismaで複合主キーを設定する'
emoji: '🔑'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['prisma']
published: false
---

# 複合主キーとは

複合主キーがわからない人は以下のサイトがわかりやすくまとめられています。
https://medium-company.com/%E8%A4%87%E5%90%88%E4%B8%BB%E3%82%AD%E3%83%BC/
https://wa3.i-3-i.info/word1993.html

一言でいうと、**複数の項目の組み合わせを 1 つの主キーとして定義すること** です。

例として学校の名簿を見てみます。  
「学年」「組」「出席番号」それぞれ単体では重複しますが、3 つを組み合わせたときに重複することはないはずです。  
つまり、「学年」「組」「出席番号」を組み合わせることで一意の複合主キーができます。

| 学年 | 組  | 出席番号 | 名前 |
| ---- | --- | -------- | ---- |
| 1    | 1   | 1        | 一郎 |
| 1    | 1   | 2        | 二郎 |
| 1    | 1   | 3        | 三郎 |
| 1    | 3   | 1        | 花子 |

# 実装する

prismaで複合主キーを定義するには`@@unique`を使います。  

```prisma:schema.prisma
model Student {
  id        Int     @id @default(autoincrement())
  grade     Int
  group     Int
  number    Int
  name      String

  @@unique(fields: [grade, group, number], name: "student_identifier")
}
```

ちなみに fields や name を省略した書き方も可能です。

```prisma:schema.prisma
@@unique(fields: [grade, group, number])
@@unique([grade, group, number])
```

name オプションを付けない場合、デフォルトは fields をアンダースコアでつなげた形になります。  
（上2つの name は `grade_group_number` になる）

## 注意

:::message
複合主キーとして使いたいフィールドは、すべて必須フィールドでなければなりません。  
以下のモデルは number が NULL になる可能性があるので無効です。
:::

```prisma:schema.prisma
model Student {
  id        Int     @id @default(autoincrement())
  grade     Int
  group     Int
  number    Int?
  name      String

  @@unique([grade, group, number], name: "student_identifier")
}
```

# 使い方

where オプションで他のカラムと同じように使えます。

```ts
const student = await prisma.student.findUnique({
  where: {
    student_identifier: {
      grade: 1,
      group: 1,
      number: 1,
    },
  },
})
```

# 参考

https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference#unique-1
