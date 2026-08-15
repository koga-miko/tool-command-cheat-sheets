# ffmpeg Cheat Sheet

## Overview

`ffmpeg` は、音声・動画・画像を変換・抽出・結合するための多機能ツールです。実務ではメディア処理の現場で頻繁に使います。

## When to Use

- 動画を変換したいとき
- 音声を抽出したいとき
- 画面キャプチャやサムネイルを作りたいとき

## Basic Syntax

```bash
ffmpeg [global_options] [input_options] -i input_file [output_options] output_file
```

## Common Examples

```bash
# 動画を MP4 に変換
ffmpeg -i input.mov output.mp4

# 音声だけを抽出
ffmpeg -i input.mp4 -vn -acodec mp3 output.mp3

# 10 秒だけ切り出す
ffmpeg -ss 00:00:10 -i input.mp4 -t 10 output.mp4

# 解像度を変更
ffmpeg -i input.mp4 -resize 1280x720 output.mp4

# GIF に変換
ffmpeg -i input.mp4 -vf "fps=10,scale=480:-1" output.gif

# サムネイルを作成
ffmpeg -ss 00:00:05 -i input.mp4 -vframes 1 thumbnail.jpg

# 1 秒ごとに画像を出力
ffmpeg -i input.mp4 -vf fps=1 frame_%03d.png

# ヘッダーの無い RAW PCM 音声（16bit/22.05kHz/モノラル）を WAV に変換
# RAW PCM はファイル自体にフォーマット情報が無いため、入力側で明示的に指定する
ffmpeg -f s16le -ar 22050 -ac 1 -i input.pcm output.wav
```

## Frequently Used Options

```bash
# 画質を指定
ffmpeg -i input.mp4 -crf 23 output.mp4

# 音声を無効化
ffmpeg -i input.mp4 -an output.mp4

# ビデオの長さを制限
ffmpeg -i input.mp4 -t 30 output.mp4

# 60fps でエンコード
ffmpeg -i input.mp4 -r 60 output.mp4

# 画質を下げて軽量化（映像ストリームの目標ビットレートを 1000kbps に指定）
ffmpeg -i input.mp4 -b:v 1000k output.mp4
```

## Notes

- `-i` の後に入力ファイル、続けて出力ファイルを置くのが基本です。
- 圧縮品質や解像度の指定が重要です。
- 変換前後のファイル形式を確認すると安心です。
- RAW PCM（拡張子 `.pcm` や `.raw` など）はファイル自体にサンプルレート・チャンネル数・ビット深度・エンディアンの情報を持たないため、`-f`（入力フォーマット）・`-ar`（サンプルレート）・`-ac`（チャンネル数）を入力側オプションとして明示する必要があります。`s16le` は符号あり16bit・リトルエンディアンを表し、ビッグエンディアンの場合は `s16be` を使います（値を間違えるとノイズや再生速度のズレとして現れます）。この変換は実際に `ffmpeg -f s16le -ar 22050 -ac 1 -i input.pcm output.wav` で動作確認済みです。
- `-b:v` の `:v` は「対象のストリーム種別」を指定するストリーム指定子で、`v` は映像（video）を意味します（音声には `-b:a` を使います）。`-b:v 1000k` は「映像ストリームの目標ビットレートを 1000kbps（=1,000,000 bit/秒）にする」という指定で、ビットレートが高いほど高画質・大容量、低いほど低画質・小容量になります。
- `-crf`（品質ベース）と `-b:v`（ビットレートベース）は画質・サイズ調整の異なるアプローチです。`-crf` は「一定の品質」を保ちつつシーンに応じてビットレートを自動で増減させるのに対し、`-b:v` は「目標の平均ビットレート」に近づけるよう品質側を調整します。ファイルサイズの上限を厳密に決めたい場合は `-b:v`、見た目の品質を優先したい場合は `-crf` が向いています。

## Related Links

- Download: https://www.ffmpeg.org/download.html
- Install: https://www.ffmpeg.org/download.html
- Docs: https://ffmpeg.org/documentation.html
- License: https://www.ffmpeg.org/legal.html
