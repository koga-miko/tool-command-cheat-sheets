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

# 画質を下げて軽量化
ffmpeg -i input.mp4 -b:v 1000k output.mp4
```

## Notes

- `-i` の後に入力ファイル、続けて出力ファイルを置くのが基本です。
- 圧縮品質や解像度の指定が重要です。
- 変換前後のファイル形式を確認すると安心です。

## Related Links

- Download: https://www.ffmpeg.org/download.html
- Install: https://www.ffmpeg.org/download.html
- Docs: https://ffmpeg.org/documentation.html
- License: https://www.ffmpeg.org/legal.html
