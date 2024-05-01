---
title: "ApexChartsのrangeBarで値が同じでもグラフを表示させる方法"
emoji: "📊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["apexcharts", "react", "nextjs"]
published: true
---

# ApexCharts とは？

ApexCharts は、以下のような特徴を持つ JavaScript チャートライブラリです。

1. 豊富なチャートタイプ: ラインチャート、バーチャート、円グラフ、レーダーチャートなど、多様なチャートタイプを提供しており、多くのデータ視覚化ニーズに対応します。
2. インタラクティブ: ユーザーがデータをより深く理解できるよう、ズーム、パン、ツールチップなどのインタラクティブな機能が組み込まれています。
3. レスポンシブデザイン: デバイスに関係なく最適な表示を実現するため、自動的にサイズ調整が行われます。
4. カスタマイズ可能: スタイルや動作の詳細設定が可能で、CSS を使ったスタイリングや JavaScript API を通じて、グラフの外観と機能を細かく調整できます。

https://apexcharts.com/

## いろいろなチャートタイプで表示可能

ApexCharts では以下の種類からチャートタイプを指定することができます。

- line
- area
- bar
- pie
- donut
- radialBar
- scatter
- bubble
- heatmap
- candlestick
- boxPlot
- radar
- polarArea
- rangeBar
- rangeArea
- treemap

https://apexcharts.com/docs/options/chart/type/
それぞれがどのように表示されるかの詳細は [ApexCharts のデモページ](https://apexcharts.com/javascript-chart-demos/)で確認できます。

# 通常時の表示結果

今回は[rangeBar](https://apexcharts.com/react-chart-demos/timeline-charts/basic/)という、**値の範囲を視覚的に表現するのに適してるもの**を選択しました。
`rangeBar`グラフを表示したい場合は以下のように書きます。

```tsx:Chart.tsx
import ReactApexChart from "react-apexcharts";

const Chart = () => {
  const options = {};

  const series = [
    {
      name: "Points",
      data: [
        { x: "TEAM A", y: [20, 27] },
        { x: "TEAM B", y: [25, 25] },
        { x: "TEAM C", y: [23, 30] },
      ],
    },
  ];

  return (
    <>
      <h1>ポイントの範囲</h1>
      <ReactApexChart
        options={options}
        series={series}
        type="rangeBar"
        height={350}
      />
    </>
  );
};

export default Chart;
```

![通常時の表示結果](/images/apexcharts-same-value/option-off.png)
_表示結果_

ちなみにホバーするとツールチップがでます。

![ツールチップの表示結果](/images/apexcharts-same-value/option-off-tooltip.png)
_TEAM A にカーソルを合わせた状態_

簡単に説明すると、
`options`プロパティは**グラフの見た目**を定義し、
`series`プロパティは**グラフに表示するデータ**を定義します。

## 発生している問題

画像を見て分かるように、TEAM B が表示されていません。

```tsx
const series = [
  {
    name: "Points",
    data: [
      { x: "TEAM A", y: [20, 27] },
      { x: "TEAM B", y: [25, 25] }, // 値は入っているのに表示されない
      { x: "TEAM C", y: [23, 30] },
    ],
  },
];
```

しかし、開発者ツールで確認したところ、要素は存在していることが確認できました。
どうやら**範囲の最小値と最大値が同じであるため、要素の高さが 0 となり、表示されていないように見えていた**ようです。
（範囲値の差に基づいて要素の高さ（または幅）を計算していると思われます。）

![開発者ツールで確認した表示結果](/images/apexcharts-same-value/option-off-devtool.png)
_開発者ツールで確認_

# オプションを設定して表示させる

`stroke` というオプションを定義します。
https://apexcharts.com/docs/options/stroke/

役割としては、グラフの線や境界線に関連するスタイリングの設定を行うために使用されます。
このオプションを利用することで、線の幅、色、線の種類（点線など）、曲線の滑らかさなどをカスタマイズできます。

今回は `width`プロパティを設定してグラフの線の太さを出すことで、要素の境界線が強調され、**実際には高さや幅が 0 でも視覚的には線として表示されるように**しています。

```tsx
const options = {
  stroke: {
    width: 1,
  },
};
```

:::details 最終的なコード

```tsx:Chart.tsx
import ReactApexChart from "react-apexcharts";

const Chart = () => {
  const options = {
    stroke: {
      width: 1,
    },
  };

  const series = [
    {
      name: "Points",
      data: [
        { x: "TEAM A", y: [20, 27] },
        { x: "TEAM B", y: [25, 25] },
        { x: "TEAM C", y: [23, 30] },
      ],
    },
  ];

  return (
    <>
      <h1>ポイントの範囲</h1>
      <ReactApexChart
        options={options}
        series={series}
        type="rangeBar"
        height={350}
      />
    </>
  );
};

export default Chart;
```

:::

![オプションを設定した結果](/images/apexcharts-same-value/option-on.png)
_stroke を設定した結果_

![オプションを設定して開発者ツールで確認した結果](/images/apexcharts-same-value/option-on-devtool.png)
_要素の高さは 0 のまま_

## 注意点

この方法で実装する場合、要素の高さが 0 のままであることに注意する必要があります。
`width` の値を大きくする場合は、実際のデータ値と表示内容が乖離しないように気をつけましょう。

# 参考

https://stackoverflow.com/questions/73235992/how-to-display-range-apex-chart-when-we-have-start-end-are-same-at-some-time
