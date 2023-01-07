---
title: "useStateの空配列に型をつける"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript", "react", "usestate"]
published: true
---

# はじめに

useStateは初期値から型推論をしてくれるので明示的に型をつけなくてもいい感じにやってくれます。

```ts:index.tsx
const [text, setText] = useState("");
const [count, setCount] = useState(0);
const [isDone, setIsDone] = useState(false);
/**
 * 推論される型
 * const text: string;
 * const count: number;
 * const isDone: boolean;
 */
```

上記のようなプリミティブ型は問題ないのですが、配列の場合はどうなるでしょうか。

```ts:初期値が空配列の場合
const [todos, setTodos] = useState([]);
```

このとき推論される型は`never[]`です。
配列ということは推論できるけど中身までは分からないので `never型`になっています。

```ts:推論される型
// const todos: never[];
```

# never型によるエラーを解決する

以下はTodoを追加する関数です。
※便宜上、追加するTodoはベタ書きです。

```ts:index.tsx
const [todos, setTodos] = useState([]);

const addTodo = () => {
  setTodos((prevTodos) => {
    return [...prevTodos, { task: "買い物", isDone: false }];
  });
};
```

あとは`addTodo`を`onClick`等のイベントハンドラーに入れれば一応動きますが、エディター上では`(prevTodos) =>`に波線が引かれ、以下のような警告が表示されます。

```ts
(parameter) prevList: never[]
型 '(prevList: never[]) => { task: string; isDone: boolean; }[]' の引数を型 'SetStateAction<never[]>' のパラメーターに割り当てることはできません。
  型 '(prevList: never[]) => { task: string; isDone: boolean; }[]' を型 '(prevState: never[]) => never[]' に割り当てることはできません。
    型 '{ task: string; isDone: boolean; }[]' を型 'never[]' に割り当てることはできません。
      型 '{ task: string; isDone: boolean; }' を型 'never' に割り当てることはできません。
```

要するに `never型`に値を入れることはできないということです。

`never型`についてはこちら↓
https://typescriptbook.jp/reference/statements/never

# useStateに型をつける

上記のエラーを解決するには `useState`に明示的に型を付与して`never型`を避ける必要があります。
`useState`の型を見てみると以下のようにジェネリクスが使われていました。

```ts:index.d.ts
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];
```

つまり、以下のように書くと型エラーは消えます。

```ts:index.ts
type Todo = {
  task: string;
  isDone: boolean;
};

const [todos, setTodos] = useState<Todo[]>([]);

const addTodo = () => {
  setTodos((prevTodos) => {
    return [...prevTodos, { task: "買い物", isDone: false }];
  });
};
```

ちなみにプリミティブ型でも同じように型をつけることができます。

```ts:index.tsx
const [text, setText] = useState<string>("");
const [count, setCount] = useState<number>(0);
const [isDone, setIsDone] = useState<boolean>(false);
/**
 * 推論される型
 * const text: string;
 * const count: number;
 * const isDone: boolean;
 */
```
