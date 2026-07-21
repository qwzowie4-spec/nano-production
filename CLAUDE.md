# CLAUDE.md — Nano Production 公式サイト プロジェクトルール

このファイルは、Nano Production 公式サイトの開発・運用における唯一のルールブックです。
AI・人間を問わず、このリポジトリに手を入れる際は必ず本ファイルに従ってください。

---

## 1. プロジェクト概要

- **サイト名:** Nano Production(ナノ・プロダクション)
- **事業内容:** YouTubeエンタメ動画 / ビジネス横動画 / 縦型ショート広告の映像制作。TikTok・Instagram(希望に応じて)対応・ディレクション。
- **ターゲット:** 企業の広報担当者、マーケター、YouTuber、TikToker、中小企業の社長・広報。
- **サイトの役割:** 実績を見せて問い合わせにつなげる1ページ完結のポートフォリオ + 窓口。

## 2. ファイル構成

```
/
├── CLAUDE.md        ← 本ファイル(ルール)
├── index.html       ← 本体(HTML / Tailwind CDN / インラインJS)
├── videos.json      ← 実績動画データ(運用者が編集するのは基本ここだけ)
└── assets/          ← OGP画像・ロゴ等(必要になったら作成)
```

- ビルド工程なし。**静的ファイルのみ**で完結させる(サーバー要件を作らない)。
- JSは `index.html` 内にインラインで保持する(HTTPリクエスト数削減のため)。

## 3. デザイントークン(変更時は必ずここを更新)

| トークン | Tailwind | 用途 |
|---|---|---|
| 背景(最奥) | `bg-zinc-950` (#09090b) | ページ全体の漆黒背景 |
| サーフェス | `bg-zinc-900` (#18181b) | ヘッダー・カード・フォーム枠。**背景より必ず一段明るく** |
| ボーダー | `border-zinc-800` / `border-white/10` | カード・セクション境界。境目を明確にする |
| 本文 | `text-zinc-100` | 標準テキスト |
| 補足 | `text-zinc-400` / `text-zinc-500` | サブテキスト |
| アクセント | `text-sky-400` / `bg-blue-600` | リンク・ボタン・ラベル(ロゴの青) |
| グラデーション | `#7dd3fc → #38bdf8 → #2563eb` | ロゴ・見出しの `grad-text` |
| グロー | `shadow-[0_0_15px_rgba(56,189,248,0.2)]` | ホバー/フォーカス時の発光。カスタム `shadow-glow` / `shadow-glow-lg` |

- **ロゴ:** NPモノグラム(PROPOSAL 04)をブランドブルーのグラデーションで使用。ヘッダー・ファビコン(インラインSVG)に実装済み。変更時は両方を同期する。
- **タイポグラフィ:** 英字見出し・ロゴ = Plus Jakarta Sans(`font-extrabold` + `tracking-wider`)。日本語 = システム標準ゴシック(Hiragino / Yu Gothic スタック、webフォント読込なし=軽量)。Google Fontsの追加読込は Plus Jakarta Sans 1ファミリーのみ。
- **シグネチャ:** アスペクト比「16:9 / 9:16」をラベル・レイアウトのデザイン言語として使う。グラデーションはロゴ由来のブルー系のみ許可。
- モーションは控えめに。`prefers-reduced-motion` を必ず尊重する。

## 4. 動画の追加・入れ替え(運用ルール)

1. `videos.json` を開く。
2. 横動画は `horizontal`、縦動画(ショート)は `vertical` の配列に追記する。
3. 1件のフォーマット:

```json
{ "id": "YouTubeの動画ID", "title": "表示タイトル", "client": "クライアント名や種別(任意)", "thumb": "サムネイルURL(任意・省略可)" }
```

- `id` は YouTube URL の `watch?v=◯◯` / `shorts/◯◯` の ◯◯ 部分。
- `thumb` を省略すると `https://i.ytimg.com/vi/{id}/hqdefault.jpg` を自動使用。縦動画で見栄えが悪い場合のみ `thumb` で差し替える。
- 並び順 = 配列の順。上に書いたものが先に表示される。
- **index.html を触らずに動画運用が完結すること**が本サイトの機能要件。崩す変更は不可。

### 大判サムネイル(1280×720)の設置

- WORKS一覧の下部に、1280×720の画像を1枚置ける専用スペースがある(「16:9 — 1280×720サムネイル」)。
- 画像ファイルを `assets/works-banner.jpg` という名前・パスで配置するだけで自動的に表示される(コード変更不要)。
- 画像が存在しない間は、点線枠のプレースホルダーが表示される(壊れた画像アイコンにはならない)。

## 5. SEO・内部対策ルール(最重要)

- `<title>` / `<meta name="description">` には必ず「Nano Production」+ 主要キーワード(動画制作 / YouTube編集 / ショート動画広告)を含める。
- OGP(`og:*`)と Twitter Card(`twitter:*`)タグを常に維持する。公開時に `og:url` と `og:image` を本番URL・本番画像に差し替えること(現在はダミー)。
- `<h1>` はページに1つだけ(サイト名+主要キーワード)。セクションは `<section>` + `<h2>`、実績カードは `<article>`。この構造を壊さない。
- 構造化データ(JSON-LD, `Organization` + `VideoObject` は将来検討)を維持する。
- **速度規約:** 外部リソースは Tailwind CDN と Google Fonts(2ファミリー)のみ。画像は必ず `loading="lazy"`。YouTube の iframe は**モーダルを開いた瞬間に生成**し、閉じたら破棄する(初期表示で iframe を1つも読み込まない)。

## 6. コーディング規約

- インデント2スペース。日本語コンテンツは全角、英数字は半角。
- Tailwind のユーティリティを第一選択とし、独自CSSは `<style>` 内に最小限(トークン定義・モーダル等)。
- JSは素のES2015+。ライブラリ追加禁止。`fetch('videos.json')` が失敗した場合(file:// 直開き等)は、HTML内のフォールバックJSON(`#videos-fallback`)を使う二重化を維持する。
- フォームは Formspree(fetch方式)で送信。エンドポイントは index.html 内の `FORMSPREE_ENDPOINT` 定数で管理し、送信失敗時は qwzowie@gmail.com への直接連絡を案内する。

## 7. 公開前チェックリスト

- [ ] `og:url` / `canonical` を本番URLに変更
- [ ] `og:image` を実画像(1200×630)に差し替え
- [x] Formspree設定済み(https://formspree.io/f/xnjedkny → qwzowie@gmail.com)
- [ ] `videos.json` を実案件に差し替え
- [ ] スマホ実機(375px幅)で表示確認
