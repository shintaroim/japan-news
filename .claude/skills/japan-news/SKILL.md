---
name: japan-news
description: 日本の主要ニュース（日経・Yahoo!ニュース）を取得し、カテゴリ別に日本語で要約して news/ に保存する。「日本の主要ニュース」「今日のニュース」と頼まれたとき、または /japan-news で呼ばれたときに使う。
---

# 日本の主要ニュース取得

日経新聞スキル（`C:\VS Code workspace\日経新聞\.claude\skills\nikkei-news\SKILL.md`）の手順・出力形式をベースに、ソースを日本の主要ニュース全般に広げたもの。

## 手順

1. WebFetch が未ロードなら `ToolSearch` で `select:WebFetch` を実行してロードする。
2. 次を **並列で** WebFetch する。
   - `https://www.nikkei.com/` — 「トップページに掲載されている主要ニュースの見出しを、記事URLと短い要約（リード文があれば）付きで、掲載順に最大20件列挙。カテゴリが分かれば併記。ページの日付も。」
   - `https://assets.wor.jp/rss/rdf/nikkei/news.rdf`（日経速報RSS）
   - `https://news.yahoo.co.jp/rss/topics/top-picks.xml`（Yahoo!主要）
   - `https://news.yahoo.co.jp/rss/topics/domestic.xml`（国内）
   - `https://news.yahoo.co.jp/rss/topics/world.xml`（国際）
   - `https://news.yahoo.co.jp/rss/topics/business.xml`（経済）
   - RSS のプロンプト:「RSSに含まれる記事の見出し・URL・日時・概要をすべて列挙してください。」
   - NHK（www3.nhk.or.jp）は WebFetch 不可なので使わない。
3. 統合する。Yahoo!主要と日経トップの両方に載る記事・掲載順の上位を「主要ニュース」として優先。重複は統合し、直近24時間以外は除外。
4. 取得に失敗したソースがあれば残りで報告し、出典欄にその旨を書く。全滅なら `WebSearch` で「今日 主要ニュース 日本」を検索して代替。
5. 下記の出力形式で表示し、`news/YYYY-MM-DD_HHMM.md`（取得時刻）に保存する。末尾に `取得日時: YYYY-MM-DD HH:MM` を付ける。
6. 保存先パスをリンク形式で示す。

## 出力形式

```
## 日本の主要ニュース（YYYY年M月D日 朝/夜）

### 主要ニュース
1. **[カテゴリ] 見出し** — 1行要約 ([記事](URL))
...（10件程度）

### カテゴリ別の注目記事
- **マーケット・金利**: ...
- **企業**: ...
- **国際**: ...
- **社会・暮らし**: ...
- **スポーツ**: ...

### 今日のポイント
- 全体を通した傾向を2〜3行で

出典: ...
```

## 注意

- 日経の記事本文は有料会員限定が多い。見出しとリード文の範囲で要約し、本文を推測で補わない。
- 要約は事実ベースで簡潔に。URL は取得できたもののみ記載する。
