# 1. はじめに(インストールと API キーの設定)

[目次に戻る](README.md)

## 必要なもの

- Windows 10 / 11(64bit)
- NovelAI のサブスクリプション(Tablet / Scroll / Opus のいずれか)
- NovelAI の API キー(下で取得方法を説明します)
- インターネット接続

## インストール

1. [BOOTH の販売ページ](https://zero0milk.booth.pm/items/8889836)から zip をダウンロードします。
   - 体験版: `NovelAILocalGUI-Trial-<バージョン>-win64.zip`(無料)
   - 製品版: `NovelAILocalGUI-<バージョン>-win64.zip`(購入後、BOOTH の購入履歴からダウンロード)
2. zip を右クリックして「すべて展開」を選び、好きな場所に展開します。
3. 展開したフォルダの中の `NovelAILocalGUI.exe` を起動します。

> フォルダの中の `_internal` などのファイルも起動に必要です。`NovelAILocalGUI.exe` だけを別の場所に移さず、フォルダごと使ってください。
> デスクトップから起動したい場合は、`NovelAILocalGUI.exe` のショートカットを作ってください。

### 「Windows によって PC が保護されました」と表示された場合

初回の起動時に、Windows の SmartScreen の警告が出る場合があります。
「詳細情報」を押し、表示された「実行」を押すと起動できます。

## API キーの設定

### API キーを取得する

1. NovelAI の Web サイトにログインし、ユーザー設定(User Settings)を開きます。
2. 「Account」を開き、「Get Persistent API Token」を押します。
3. 表示されたトークン(`pst-` で始まる文字列)をコピーします。

> 新しいトークンを発行すると、それまでのトークンは使えなくなります。ほかのツールで同じトークンを使っている場合は、そちらも更新してください。

### アプリに設定する

1. 画面右上の歯車を押して、設定画面を開きます。
2. 「API キー」の「設定」を押し、コピーしたトークンを貼り付けて OK を押します。
3. 上部に Anlas の残高とプラン(Tier)が表示されれば完了です。

API キーは Windows の資格情報マネージャーに保存されます。設定ファイルには書き込まれず、NovelAI 以外には送信されません。

次は [画像を生成する](02-generate.md) に進んでください。
