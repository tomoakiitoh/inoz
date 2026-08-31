# inoz

国土地理院と Project PLATEAU の公開データだけで、日本のどこにでも立てる WebXR ビューアです。

地名か座標を渡すと、その場所の地形・空中写真・道路・3D都市モデルを**実行時に**組み立てて、歩ける状態にします。**サーバを持ちません。**単一の HTML がブラウザの中で、国の配信元から直接データを受け取って描画します。

**https://inoz.jp**

> 名前は伊能図(いのうず)から。伊能忠敬は50歳で隠居してから天文と測量を学び、17年かけて日本を歩いて測りました。

---

## できること

- **地形** — 国土地理院の標高タイルから約4km四方の地形を組み、その外側に遠景を足します。遠景は約12km四方から約190km四方まで、5段で広げられます
- **歩き続けられます** — 端に近づくと次の約4km四方を裏で組み立てて差し替えます。止まりません
- **空中写真** — 白模型／空中写真／写真+道路の3つの見え方。空中写真のときは**建物の屋根にも同じ写真**がかかります(壁は白のまま)
- **建物** — Project PLATEAU の3D都市モデル(3D Tiles)を、整備済みの都市で立てます。画面上の誤差を見て近くは細かく遠くは粗く読み、屋根の形が入っている街(LOD2)ではそれを使います
- **水位** — 指定した標高に水面を1枚置いて、「水位◯mのとき、この街はどう見えるか」を確かめます
- **持ち込みデータ** — 点と線を地形の上に置けます
- **共有** — 見ている景色を、そのまま渡せる URL にします
- **VR** — WebXR 対応の端末でそのまま入れます

## URL の作り

景色は URL だけで表せます。`?place=秩父市` のように場所を渡すこともできます。

```
https://inoz.jp/?lat=35.99170&lon=139.08570&dir=164&tilt=6&photo=1&label=秩父市
```

| | 意味 |
|---|---|
| `lat` `lon` | 立っている位置 |
| `place` / `label` | 地名(検索する) / 表示名(検索しない) |
| `dir` `tilt` `eye` | 方位角 / 仰角 / 目の高さ(地面から m) |
| `photo` `bldg` `water` `far` | 地表の描き方 / 建物 / 水位(絶対標高 m) / 遠景の幅(`0`=なし、`1`=既定、`2`〜`5` で約24・47・95・190km四方) |

パラメータは**追加のみ**行います。削除も意味の変更もしません。今日作った URL は、将来も同じ場所を指します。

建物の細かい指定もできます。`lod`(1〜4。既定は「LOD2があればLOD2、無ければLOD1」)/ `bldgtex=1`(テクスチャ付きの配信を許す)/ `sse`(画面上の誤差の上限px。小さいほど細かく、重い)/ `roof=0`(屋根に写真を貼らない)。

> **屋根の写真について。** 貼っているのは国土地理院の空中写真を**真上から落としたもの**で、屋根を測って撮ったものではありません。写真は地面に正射化されているので、高い建物ほど写真の中で傾いて写ります。低い街ではほぼ合いますが、高層ビルの屋上には脇の地面が写り込みます。「屋根の写真」ではなく「屋根の位置に地面の写真を落としたもの」です。

### `?` と `#` の境界

```
?  国の公開データで再現できるもの(座標・視点・表示状態)   … サーバに届く
#  持ち込んだデータ                                        … サーバに届かない
```

ブラウザはフラグメント(`#` より後ろ)をサーバに送りません。**「貼り付けたデータは送信されません」は、約束ではなく仕様です。**

## データを載せる

手元の表やメモを地形の上に置けます。形式は **[llms.txt](llms.txt)** に書いてあります。お使いの LLM にこのファイルを読ませると、この形式の JSON を作ってくれます。

できた JSON を画面の「データ」欄に貼ると、点と線が描かれ、そのまま渡せる URL が発行されます。

`source`(出典)は必須扱いです。書かれていない場合、画面に「出典の記載がありません」と表示します。規約で縛るのではなく、UI に言わせる方針です。

## やらないこと、謳わないこと

| 言わない | 言う |
|---|---|
| 浸水予測 / 氾濫シミュレーション / 被害想定 | 水位◯mのときの見え方 |
| ここは安全 / ここは危険 | (判断を書かない) |
| ルート案内 / 最短経路 | 見るための道すじ |
| 測量結果 / 正確な標高 | 国土地理院の標高データに基づく表示 |

水位表示は、指定した標高より低い場所を機械的に塗るだけのものです。水がどこから来るか、時間とともにどう動くか、排水がどう効くかを一切考えていません。川とつながっていない窪地も塗られます。堤防は標高データに含まれていません。**低い土地が塗られていないことは、そこが安全であることを意味しません。**

**避難の判断は、お住まいの自治体のハザードマップをご覧ください。**

## 出典

- 標高・空中写真・道路中心線・地名検索: **国土地理院**
- 3D都市モデル: **Project PLATEAU(国土交通省)**
- 地名検索の一部: **OpenStreetMap contributors**

データは開くたびに、それぞれの配信元から直接受け取ります。**このリポジトリも、公開しているページも、データを保持しません。**

inoz を複製・改変して動かす場合、取得先のデータには**それぞれの提供元の利用条件**が適用されます。各提供元の規約をご確認ください。

## ライセンス

**現時点ではライセンスを定めていません。**

したがって著作権はすべて留保されます(All rights reserved)。ソースを読むことは自由ですが、複製・改変・再配布・商用利用の許諾は行っていません。

ライセンスは検討中です。決まり次第、このリポジトリに `LICENSE` を置きます。

### 同梱しているオープンソースソフトウェア

これは inoz 自身のライセンスとは別の話で、**こちらが守る側**です。

| | ライセンス | |
|---|---|---|
| [three.js](https://threejs.org/) r160 | MIT | 描画 |
| [Draco](https://google.github.io/draco/) (glTFデコーダ) | Apache-2.0 | PLATEAU の 3D Tiles の展開。wasm を base64 で埋め込み。改変なし |

著作権表示と許諾条件の全文は **[THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt)** に置いてあり、
配布物である `index.html` の冒頭にも同じものを埋め込んでいます（単一HTMLで配る以上、
ファイルが一枚で完結している必要があるため）。

## 構成

| | |
|---|---|
| `index.html` | 本体(ビルド済みの単一 HTML) |
| `terra.html` | 同内容(旧リンク維持用) |
| `llms.txt` | データ形式の仕様 |
| `inou_baseline.json` | データの実例(伊能忠敬が最初に測った基線) |
| `akiba.html` `kenashi.html` | 初期の実験(秋葉原・毛無峠) |
| `THIRD-PARTY-NOTICES.txt` | 同梱しているOSSの著作権表示と許諾条件(全文) |
| `_headers` `robots.txt` `.assetsignore` | 配信の設定 |

ビルド前のソースはこのリポジトリには含めていません。

## 状態

開発中です。広くの公開はまだしていません(検索避けをかけています)。

---

## English

**inoz** is a WebXR viewer that lets you stand anywhere in Japan, built only from open government data — elevation, aerial imagery and road centrelines from the **Geospatial Information Authority of Japan (GSI)**, and 3D city models from **Project PLATEAU (MLIT)**.

It has no server. A single HTML file assembles the terrain in your browser, fetching tiles directly from the government endpoints at runtime. Nothing is stored, by inoz or by this repository.

The whole view is expressible as a URL. Query parameters (`?`) carry what can be reproduced from public data; the fragment (`#`) carries data you bring yourself — and browsers never send fragments to servers, so pasted data stays on your machine by specification rather than by promise.

**No licence has been chosen yet, so all rights are reserved.** You are welcome to read the source; copying, modification, redistribution and commercial use are not granted at this time. Data fetched at runtime remains subject to the terms of each provider.

Bundled open-source software — three.js (MIT) and the Draco decoder (Apache-2.0) — is covered by [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt); the same notices are embedded at the top of the distributed `index.html`.

The name comes from *Inō-zu* — the maps of Inō Tadataka, who retired at fifty, took up astronomy and surveying, and spent seventeen years walking Japan to measure it.
