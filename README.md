# NovelAILocalGUI — 更新情報

NovelAI の画像生成を Windows で行うデスクトップアプリ「NovelAILocalGUI」の、更新情報と公開資料を置くリポジトリです。
アプリ本体とそのソースコードはここにはありません。

NovelAILocalGUI は NovelAI の非公式クライアントです。NovelAI を運営する Anlatan, Inc. とは関係がありません。

## 使い方

[使い方ガイド](guide/README.md)をご覧ください。

## 入手方法

製品版・体験版とも、BOOTH の販売ページから入手できます: https://zero0milk.booth.pm/items/8889836

## このリポジトリにあるもの

| ファイル | 内容 |
|---|---|
| [`guide/`](guide/README.md) | 使い方ガイド(インストール、各機能の使い方、よくある質問) |
| [`EULA.md`](EULA.md) | 使用許諾契約書(利用規約)。製品版・体験版の両方に適用されます |
| [`latest.json`](latest.json) | 最新バージョンの情報。アプリの「更新を確認」が参照します |
| [Releases](../../releases) | アプリが使用しているオープンソースライブラリ(Qt / Qt for Python)のソースコード |

### Qt / Qt for Python のソースコード

NovelAILocalGUI は Qt と Qt for Python(PySide6)を GNU LGPL v3 に基づいて使用しています。
使用しているバージョンと同一のソースコードを [Releases](../../releases) に置いています
(Qt 公式の配布物をそのまま再配布したもので、改変していません)。

- [Qt 6.11.2 / Qt for Python 6.11.2](../../releases/tag/qt-6.11.2-sources)(NovelAILocalGUI v0.10.1 以降)

## 不具合の報告・要望

[Issues](../../issues) で受け付けています。

- **API キーや、個人を特定できる情報は書き込まないでください。** Issue は誰でも閲覧できます。
- アプリのバージョン(設定画面の「アプリ情報」に表示されます)と、再現手順を添えてください。
