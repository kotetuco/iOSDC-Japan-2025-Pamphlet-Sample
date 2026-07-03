# iOSDC　Japan　2025　Pamphlet　Sample

iOSDC Japan 2025 パンフレット記事「[実体験から学ぶ！ iOSDC Japan パンフレット記事入稿のコツ](https://fortee.jp/iosdc-japan-2025/proposal/c1ac1019-07fd-466b-899e-c2f48bc1ceaf)」で使用したサンプルプロジェクト置き場です。

同じ記事を Adobe InDesign、Affinity Publisher、Re:VIEW で作成したサンプルを含んでいます。主に、パンフレット記事の入稿用 PDF を作る際のレイアウト設定や出力手順を確認するためのリポジトリです。

## フォルダ構成

| フォルダ | 内容 |
| --- | --- |
| `InDesign/` | Adobe InDesign 版の原稿データと出力済み PDF を配置しています。 |
| `AffinityPublisher/` | Affinity Publisher 版の原稿データと出力済み PDF を配置しています。InDesign から書き出した `.idml` も含みます。 |
| `ReVIEW/` | Re:VIEW 版の原稿、設定ファイル、画像、LaTeX スタイルを配置しています。 |

## Re:VIEW 版について

Re:VIEW 版の作業ディレクトリは `ReVIEW/review/` です。

主なファイルは以下です。

| ファイル | 内容 |
| --- | --- |
| `ReVIEW/review/review.re` | PDF 出力に使う Re:VIEW 原稿です。 |
| `ReVIEW/review/review.md` | Markdown で編集したい場合の元原稿です。 |
| `ReVIEW/review/config.yml` | PDF の用紙サイズ、段組、フォントサイズ、行数などを設定します。 |
| `ReVIEW/review/catalog.yml` | PDF に含める章ファイルを指定します。 |
| `ReVIEW/review/sty/review-custom.sty` | ページ番号非表示や見出し余白など、LaTeX 側の調整を行います。 |
| `ReVIEW/review/images/` | Re:VIEW ビルドで参照する画像を配置しています。 |

現在の設定では、A4・二段組の PDF を出力します。生成される PDF は `ReVIEW/review/book.pdf` です。

## Re:VIEW で PDF を出力する

Docker Desktop が利用できる場合は、以下のコマンドで PDF を生成できます。

```sh
cd ReVIEW/review
docker run --rm -v `pwd`:/work vvakame/review:5.9 /bin/sh -c "cd work && rake pdf"
```

生成後、以下のファイルが作成されます。

```text
ReVIEW/review/book.pdf
```

`book.pdf` は生成物のため、`ReVIEW/review/.gitignore` で Git の追跡対象外にしています。

## Markdown から Re:VIEW 原稿を生成する

`review.md` を編集してから `review.re` を生成し、そのまま PDF まで出力する場合は以下のコマンドを使います。

```sh
cd ReVIEW/review
docker run --rm -v `pwd`:/work vvakame/review:5.9 /bin/sh -c "cd work && pandoc2review review.md > review.re && rake pdf"
```

`review.re` を直接編集している場合は、前述の `rake pdf` のみで PDF を再生成できます。

## ローカル Ruby 環境で出力する場合

Docker を使わずにローカル環境で出力する場合は、Ruby、Bundler、Re:VIEW が必要です。

```sh
cd ReVIEW/review
bundle install
bundle exec rake pdf
```

Re:VIEW のバージョンは `Gemfile` で `5.9.0` を指定しています。

## CMYK PDF を出力する場合

`Rakefile` には CMYK 変換用の `pdf_cmyk` タスクもあります。Ghostscript が利用できる環境では以下のコマンドで `book_cmyk.pdf` を生成できます。

```sh
cd ReVIEW/review
docker run --rm -v `pwd`:/work vvakame/review:5.9 /bin/sh -c "cd work && rake pdf_cmyk"
```

生成後、以下のファイルが作成されます。

```text
ReVIEW/review/book_cmyk.pdf
```
