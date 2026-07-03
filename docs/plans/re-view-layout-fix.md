# Re:VIEW PDF レイアウト修正計画

## Context

InDesign 版（`InDesign/iosdc-2025-article-indesign.pdf`）は 4 ページ、Re:VIEW 版（`ReVIEW/review/book.pdf`）は現状 5 ページ。同じ原稿内容なのにページ数が合わない。原因は以下の 3 点：

1. **チャプター見出しが巨大すぎる**  
   jsbook.cls の `\@makeschapterhead` は `\Huge`（≒ 22pt at 10pt base）＋前後 `2\Cvs + 3\Cvs` の余白 → タイトル全体で約 40mm（≒ 5 行分の縦スペース）消費。

2. **本文フォントが 10pt**  
   InDesign 版は目視で 8.5〜9pt 相当。10pt のまま二段組では文字密度が低い。

3. **セクション見出しの前後余白が大きい**  
   `\section` が前 `1\Cvs`・後 `0.5\Cvs` で合計 1.5\Cvs ≈ 24pt/見出し。記事中に `==` が 8 個、`===` がいくつかあり積み重なる。

## 修正方針

### ページ数計算の根拠

| 設定 | baselineskip | textheight | bottom margin |
|------|-------------|-----------|---------------|
| 10pt / 46行 | 16pt | 730pt ≈ 258mm | ≈19mm ✓ |
| 9pt / 49行 | 15pt (推定) | 729pt ≈ 258mm | ≈19mm ✓ |

`fontsize=9pt` にしても `number_of_lines=49` にすれば同じ textheight を維持できる（ページ下部 17mm を確保）。

コンテンツ密度の向上：  
- 1行あたりの文字数が 10% 増（9pt vs 10pt）  
- 行数が 49/46 ≈ 6.5% 増  
- 合計 ≈ **18% 多く**のテキストが 1 ページに入る

チャプター見出し削減で 1 ページ分に相当するスペースを節約：  
- 現在: 5\Cvs のパディング＋\Huge テキスト ≈ 100pt 消費  
- 修正後: 1.5\Cvs＋\LARGE テキスト ≈ 35pt  
- 節約: ≈ 65pt = 4〜5 行分

合計効果: 5 ページ → ≈ 3.9〜4.0 ページ → **4 ページ** ✓

---

## 変更ファイル一覧

### 1. `ReVIEW/review/config.yml`（1 箇所）

- `fontsize=10pt` → `fontsize=9pt`（InDesign の本文サイズに合わせる）
- `number_of_lines=46` → `number_of_lines=49`（9pt/15pt-baselineskip で同じ textheight を維持）

### 2. `ReVIEW/review/sty/review-custom.sty`

- チャプター見出し `\Huge + 5\Cvs` → `\LARGE + 1.5\Cvs`
- `\section`（==）: フォント `\Large` → `\large`、前後余白縮小
- `\subsection`（===）: 前後余白縮小

---

## 検証方法

```sh
cd ReVIEW/review
docker run --rm -v `pwd`:/work vvakame/review:5.9 /bin/sh -c "cd work && rake pdf"
```

生成された `book.pdf` が **4 ページ**になっているか確認する。

ページ数が合わない場合のチューニング：
- 5 ページのまま → `number_of_lines` をさらに増やす（50、51…）か、`\LARGE` を `\Large` に下げる
- 3 ページになった → `number_of_lines` を減らす（48、47…）

---

## 注意点

- `\Cvs` は jsbook.cls で `\baselineskip` に設定されており、`fontsize=9pt` 時は ≈ 15pt になる見込み
- `\headfont` は `review-style.sty` で `\gtfamily\sffamily\bfseries` として定義済み
- InDesign 版にある色付きタイトルバナーは Re:VIEW/LaTeX では再現しない（ページ数・フォントサイズのみ合わせる）
