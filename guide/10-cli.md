# 10. コマンドライン版

[目次に戻る](README.md)

zip には、画面を使わずにコマンドで操作する `NovelAILocalGUI-cli.exe` も入っています(v0.13.0 から)。
同じ設定でたくさん生成したいときや、ほかのツールやスクリプトから呼び出したいときに使えます。

- API キー・保存先フォルダ・日付フォルダの設定は GUI と共通です。GUI で設定済みなら、そのまま使えます。
- 保存される画像は GUI と同じ形式(生成設定のメタデータ付き)です。GUI にドラッグ&ドロップすれば設定を読み込めます。
- 体験版では、GUI と同じ制限(透かし、一度に処理できるのは 5 件まで、Anlas を消費する送信の前の確認)がかかります。

## 使い始める

エクスプローラーで `NovelAILocalGUI-cli.exe` のあるフォルダを開き、アドレスバーに `powershell` と入力して Enter を押すと、そのフォルダで PowerShell が開きます。

```powershell
.\NovelAILocalGUI-cli.exe --help
.\NovelAILocalGUI-cli.exe generate --help
```

GUI で API キーを設定していない場合は、先に設定します(入力した文字は表示されません)。

```powershell
.\NovelAILocalGUI-cli.exe apikey set
```

## コマンドの一覧

| コマンド | 内容 |
|---|---|
| `generate`(`gen`) | 画像を生成して保存します |
| `queue` | 設定ファイルに並べたジョブをまとめて生成します |
| `postprocess`(`pp`) | Director Tools・アップスケールをかけて保存します |
| `info` | 画像に埋め込まれた生成設定を表示・保存します |
| `account` | Anlas 残高・プラン・V5 生成の残量を表示します |
| `suggest` | タグの候補を表示します(日本語も可) |
| `apikey` | API キーを設定・削除します(`set` / `delete` / `status`) |

## 画像を生成する

```powershell
# プロンプトだけで 1 枚
.\NovelAILocalGUI-cli.exe gen -p "1girl, smile, park"

# V5 で横長、4 枚をブーストで
.\NovelAILocalGUI-cli.exe gen -m v5 --size landscape -p "1girl, smile" -c 4 --boost

# キャラクター 2 人を左右に配置(A1〜E5 は英字が横、数字が縦。C3 が中央)
.\NovelAILocalGUI-cli.exe gen -p "2girls, park" --char "hatsune miku" --char-pos B3 --char "megurine luka" --char-pos D3
```

保存した画像のパスが 1 行に 1 つ表示されます。

### 送信する前に確かめる

`--dry-run` を付けると、内容と消費 Anlas の見積りを表示するだけで、送信しません。
`--max-anlas` を付けると、見積りの合計がその値を超える場合は送信しません(`--max-anlas 0` なら無料の範囲だけ)。

```powershell
.\NovelAILocalGUI-cli.exe gen -p "1girl" -c 8 --boost --dry-run
.\NovelAILocalGUI-cli.exe gen -p "1girl" -c 8 --max-anlas 0
```

### 前に生成した画像の設定を使う

`--from` に画像を指定すると、その画像の生成設定を読み込みます(GUI のドラッグ&ドロップと同じ)。
ほかの引数を付けると、その項目だけ変えられます。

```powershell
# 同じ設定で、シードだけランダムにして 4 枚
.\NovelAILocalGUI-cli.exe gen --from .\前の画像.png --seed random -c 4
```

### img2img / Infill・参照画像

```powershell
# img2img(幅・高さは元画像に合わせます)
.\NovelAILocalGUI-cli.exe gen -p "1girl, winter coat" --image .\元画像.png --strength 0.6

# Infill(マスクは白が描き直す範囲、黒がそのまま残す範囲。透過 PNG なら不透明な部分)
.\NovelAILocalGUI-cli.exe gen -p "1girl, red dress" --image .\元画像.png --mask .\マスク.png

# Vibe Transfer(繰り返し指定で複数枚)/ Precise Reference(V4.5 のみ)
.\NovelAILocalGUI-cli.exe gen -p "1girl" --vibe .\vibe1.png --vibe .\vibe2.png --vibe-strength 0.5
.\NovelAILocalGUI-cli.exe gen -p "1girl" --precise .\キャラ.png --precise-type character
```

### キャラ名置換(バッチ)

プロンプトの `{CHARACTER}` を名前ごとに置き換えて、1 件ずつ生成します。

```powershell
.\NovelAILocalGUI-cli.exe gen -p "{CHARACTER}, smile" --name "hatsune miku" --name "kagamine rin"
.\NovelAILocalGUI-cli.exe gen -p "{CHARACTER}, smile" --names-file .\names.txt
```

## 設定ファイル

細かい設定は、ファイル(TOML または JSON)に書いて `--config` で渡せます。引数を付けると、その項目はファイルより優先されます。
画像のパスは、設定ファイルのある場所からの相対パスで書けます。

```toml
# job.toml
model = "v4.5"
prompt = "2girls, park, daytime"
negative_prompt = "lowres"
width = 832
height = 1216
steps = 28
scale = 5.0
seed = "random"
count = 2
ai_position = false          # キャラクターの配置を指定する(true なら AI におまかせ)

[[characters]]
prompt = "hatsune miku, smile"
position = "B3"

[[characters]]
prompt = "megurine luka, smile"
position = [0.7, 0.5]        # 座標(0〜1)でも書けます

[[vibes]]
image = "vibe.png"
strength = 0.6
information_extracted = 1.0
```

```powershell
.\NovelAILocalGUI-cli.exe gen --config .\job.toml
```

書ける項目は、`model` `prompt` `negative_prompt` `width` `height` `steps` `scale` `cfg_rescale` `sampler` `noise_schedule`
`skip_cfg_diversity`(スケール多様性) `seed` `count` `boost` `ai_position` `image` `mask` `strength` `noise`、
一覧の `characters`(`prompt` `negative_prompt` `position`)・`vibes`(`image` `strength` `information_extracted`)・
`precise_references`(`image` `type` `strength` `fidelity`)、V3 の `controlnet`(`image` `type` `strength`)です。
知らない項目(書き間違い)があるとエラーになります。

画像の設定を、そのまま設定ファイルにすることもできます。

```powershell
.\NovelAILocalGUI-cli.exe info .\前の画像.png --save .\job.json
```

## まとめて生成する(queue)

共通の設定と、ジョブの一覧(`[[jobs]]`)を書いたファイルを `queue` に渡すと、順番に生成します。
各ジョブに書いた項目だけが、共通の設定より優先されます。`[replace]` を書くと、各ジョブをキャラ名置換で展開します。

```toml
# queue.toml
model = "v4.5"
negative_prompt = "lowres"
steps = 28

[replace]
word = "{CHARACTER}"
names = ["hatsune miku", "kagamine rin"]

[[jobs]]
prompt = "{CHARACTER}, smile, park"

[[jobs]]
prompt = "{CHARACTER}, angry, classroom"
count = 2
```

```powershell
.\NovelAILocalGUI-cli.exe queue .\queue.toml --dry-run
.\NovelAILocalGUI-cli.exe queue .\queue.toml
```

- すべてのジョブの内容を送信前に確かめ、1 つでも誤りがあれば何も送信しません。
- 途中でエラーになったジョブは飛ばして続けます。止めたいときは `--stop-on-error` を付けます。

## 後処理

```powershell
.\NovelAILocalGUI-cli.exe pp upscale .\画像.png --scale 4
.\NovelAILocalGUI-cli.exe pp bg-removal .\a.png .\b.png
.\NovelAILocalGUI-cli.exe pp emotion .\画像.png --emotion happy --defry 0
.\NovelAILocalGUI-cli.exe pp colorize .\線画.png --prompt "blue dress"
```

ツールは `bg-removal`(背景除去)`lineart`(線画)`sketch`(スケッチ)`colorize`(彩色)`emotion`(表情変更)`declutter`(ノイズ除去)`upscale` です。
感情の一覧は `pp --help` で表示されます。後処理は消費 Anlas の見積りを表示できません。

## 保存先

| オプション | 内容 |
|---|---|
| (指定なし) | GUI の設定と同じ保存先・日付フォルダ |
| `-o フォルダ` | 保存先を変える(日付フォルダは GUI の設定に従います) |
| `--date-folder YYYY/MM-DD` | 日付フォルダの書式を指定する |
| `--no-date-folder` | 日付フォルダを作らない |

## ほかのツールから使う

- `--json` を付けると、結果(保存したファイル、シード、エラー)を JSON で出力します。
- API キーは環境変数 `NOVELAI_API_KEY` でも渡せます(設定済みのキーより優先)。
- 終了コード: `0` 成功 / `1` エラー / `2` 引数の誤り / `3` 確認で中止・上限を超えた・途中で停止
- 体験版で、確認が必要な送信を画面なしで実行するときは `--yes` を付けます。
